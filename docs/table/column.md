# Table->column

为内存表增加一个字段。

## 方法原型

```php
Swoole\Table::column

<?php
public function column($name, $type, $size = null) { }
@param string $name

@param long $type

@param long|null $size
[optional]

@return bool
```

## 参数

### string $name

字段的名称。

```php
<?php

use Swoole\Table;

$table = new Table(1024);

$table->column('age', Table::TYPE_INT);
$table->create();
```

此时内存表有一个名字为`age`的字段。

### long $type

指定字段类型。支持3种类型，`Table::TYPE_INT`, `Table::TYPE_FLOAT`, `Table::TYPE_STRING`。

如果字符串类型没有指定最大长度，那么会报警告。例如：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('name', Table::TYPE_STRING, 8);
$table->column('age', Table::TYPE_INT);
$table->column('height', Table::TYPE_FLOAT);
$table->create();
```

此时，创建了一个用户表，这个表有三个字段，分别是`name`、`age`、`height`。

### long|null $size

设置字段最大的字节数。

#### TYPE_INT

默认为`4`个字节，可以设置`1`，`2`，`4`，`8`一共`4`种长度。

实际大小超出`4`字节情况：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 2147483647]);
$table->set('2', ['age' => 2147483648]);

foreach ($table as $key => $value) {
    var_dump($value);
}
```

```shell
array(1) {
  ["age"]=>
  int(2147483647)
}
array(1) {
  ["age"]=>
  int(-2147483648)
}
```

因为`4`字节最大的`INT`值是`2147483647`，所以当`age`的值超过`2147483647`的时候，就会溢出。

修改默认大小：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT, 8);
$table->create();

$table->set('1', ['age' => 2147483647]);
$table->set('2', ['age' => 2147483648]);

foreach ($table as $key => $value) {
    var_dump($value);
}
```

```shell
array(1) {
  ["age"]=>
  int(2147483647)
}
array(1) {
  ["age"]=>
  int(2147483648)
}
```

此时不会溢出。

#### TYPE_STRING

指定字段的最大长度，单位为字节。字符串类型的字段必须指定`size`。

例如：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('name', Table::TYPE_STRING);
$table->create();
```

```shell
PHP Warning:  Swoole\Table::column(): the length of string type values has to be more than zero in /root/codeDir/phpCode/swoole/table/column.php on line 7

Warning: Swoole\Table::column(): the length of string type values has to be more than zero in /root/codeDir/phpCode/swoole/table/column.php on line 7
```

实际大小超出配置的大小例子：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('name', Table::TYPE_STRING, 8);
$table->create();

$table->set('1', ['name' => '123456789']);

foreach ($table as $key => $value) {
    var_dump($value);
}
```

```shell
[2019-12-25 11:06:46 @14361.0]  WARNING swTableRow_set_value: [key=1,field=name�]string value is too long
array(1) {
  ["name"]=>
  string(8) "12345678"
}
```

此时会报警告，并且字符串会被截断。

#### TYPE_FLOAT

占用`8`个字节的内存。
