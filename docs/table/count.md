# Table->count

返回`table`中存在的条目数

## 方法原型

```php
Swoole\Table::count

<?php
public function count() { }
@return long|false
```

## 无参数

往`table`中插入`5000`条记录：

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

for ($i = 0; $i < 5000; $i++) {
    $table->set($i, ['age' => $i]);
}

var_dump($table->count());
```

```shell
int(5000)
```
