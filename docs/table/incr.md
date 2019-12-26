# Table->incr

原子自增操作。

## 方法原型

```php
Swoole\Table::incr

<?php
public function incr($key, $column, $incrby = null) { }
@param string $key

@param string $column

@param mixed|null $incrby
[optional]

@return mixed
```

## 参数

### string $key

指定数据的key：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
$table->incr('1', 'age');

var_dump($table->get('1'));
```

```shell
array(1) {
  ["age"]=>
  int(11)
}
```

此时自增`key`为`'1'`的记录。

### string $column

指定列名，仅支持浮点型和整型字段。

#### 浮点型

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->column('height', Table::TYPE_FLOAT);
$table->create();

$table->set('1', ['age' => 10, 'height' => 180.1]);
$table->incr('1', 'height', 1.1);

var_dump($table->get('1'));
```

```shell
array(2) {
  ["age"]=>
  int(10)
  ["height"]=>
  float(181.2)
}
```

此时对`height`(浮点数类型)的数据进行了自增操作。

#### 整型

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->column('height', Table::TYPE_FLOAT);
$table->create();

$table->set('1', ['age' => 10, 'height' => 180.1]);
$table->incr('1', 'age', 2);

var_dump($table->get('1'));
```

```shell
array(2) {
  ["age"]=>
  int(12)
  ["height"]=>
  float(180.1)
}
```

此时对`age`(整型)的数据进行了自增操作。

### mixed|null $incrby

增量，默认为`1`。如果列为整形，`$incrby`必须为`int`型，如果列为浮点型，`$incrby`必须为`float`类型。

#### 默认值

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
$table->incr('1', 'age');

var_dump($table->get('1'));
```

```shell
array(1) {
  ["age"]=>
  int(11)
}
```

#### 指定值

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
$table->incr('1', 'age', 8);

var_dump($table->get('1'));
```

```shell
array(1) {
  ["age"]=>
  int(18)
}
```
