# Table->size

使用`create`方法创建表后，可以读取`$table->size`属性获取实际的表最大行数。

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

var_dump($table->size);
```

```shell
int(16384)
```
