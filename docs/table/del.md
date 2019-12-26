# Table->del

删除数据。

## 方法原型

```php
Swoole\Table::del

<?php
public function del($key) { }
@param mixed $key

@return mixed
```

## 参数

### string $key

标志`Table`中某一行的`key`。

例子：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

$table->set("1", ['age' => 10]);
var_dump($table->count());
$table->del("1");
var_dump($table->count());
```

```shell
int(1)
int(0)
```

> 请勿在foreach的过程中删除行记录

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

for ($i = 0; $i < 5000; $i++) {
    $table->set($i, ['age' => $i]);
}

foreach ($table as $key => $value) {
    $table->del($key);
}

var_dump($table->count());
```

```shell
int(838)
```

此时并没有删除所有的行记录。
