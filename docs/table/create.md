# Table->create

创建内存表。

> 1、调用create之前不能使用set、get等数据读写操作方法。
>
> 2、调用create之后不能使用column方法添加新字段。
>
> 3、Table使用共享内存来保存数据，在创建子进程前，务必要执行Table->create()。
>
> 4、Server中使用Table，Table->create() 必须在Server->start()前执行。

## 方法原型

```php
Swoole\Table::create

<?php
public function create() { }
@return bool
```

## 无参数

```php
<?php

use Swoole\Table;

$table = new Table(10000);

$table->column('age', Table::TYPE_INT);
$table->create();
```
