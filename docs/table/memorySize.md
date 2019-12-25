# Table->memorySize

使用`create`方法创建表后，可以读取`$table->memorySize`属性获取实际占用内存的尺寸，单位为字节。

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();

var_dump($table->memorySize);
```

```shell
int(2018496)
```
