# Table->set

设置行的数据。

## 方法原型

```php
Swoole\Table::set

<?php
public function set($key, array $value) { }
@param string $key

@param array $value

@return mixed
```

## 参数

### string $key

唯一标识一条行记录。

相同的`key`对应同一行数据，如果`set`同一个`key`，会覆盖上一次的数据，`key`最大长度不得超过`63`字节。

重复设置同一行记录：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
var_dump($table->get('1'));
$table->set('1', ['age' => 20]);
var_dump($table->get('1'));
```

```shell
array(1) {
  ["age"]=>
  int(10)
}
array(1) {
  ["age"]=>
  int(20)
}
```

超出`key`最大长度：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$key1 = str_repeat('1', 63);
$key2 = str_repeat('1', 64);

$table->set($key1, ['age' => 10]);
$table->set($key2, ['age' => 20]);
var_dump($table->get($key1));
```

```shell
PHP Warning:  Swoole\Table::set(): key[1111111111111111111111111111111111111111111111111111111111111111] is too long in /root/codeDir/phpCode/swoole/table/set.php on line 14

Warning: Swoole\Table::set(): key[1111111111111111111111111111111111111111111111111111111111111111] is too long in /root/codeDir/phpCode/swoole/table/set.php on line 14
array(1) {
  ["age"]=>
  int(20)
}
```

因为`key2`被截断了，所以最终`key2`等价于`key1`。所以设置`key2`的时候会修改`key1`的行记录。

### array $value

设置行记录。必须是一个数组，必须与字段定义的$name完全相同。

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
```
