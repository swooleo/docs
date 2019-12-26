# Table->exist

检查table中是否存在某一个key。

## 方法原型

```php
Swoole\Table::exist

<?php
public function exist($key) { }
@param string $key

@return bool
```

## 参数

### string $key

指定记录的`key`。

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
var_dump($table->exist('1'));
```

```shell
bool(true)
```

## 返回值

记录存在返回`true`，不存在返回`false`。

### 记录存在

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
var_dump($table->exist('1'));
```

```shell
bool(true)
```

### 记录不存在

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set('1', ['age' => 10]);
var_dump($table->exist('2'));
```

```shell
bool(false)
```
