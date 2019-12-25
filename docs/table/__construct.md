# Table->__construct

`Table`构造方法，创建内存表。

## 方法原型

```php
Swoole\Table

<?php
class Table implements
    Iterator,
    Traversable,
    ArrayAccess,
    Countable { }
Swoole\Table::__construct

<?php
public function __construct($table_size, $conflict_proportion = null) { }
@param long $table_size

@param double|null $conflict_proportion
[optional]

@return void
```

## 参数

### long $table_size

指定表格的行数，最小是`64`行，最大是`2147483648`行。并且`table_size`要是`2`的倍数，如果不是`2`的倍数，那么`table_size`会往上取到`2`的倍数。例如`65`会变成`128`。

```php
<?php

use Swoole\Table;

$table = new Table(1024);
```

### double|null $conflict_proportion

暂略。
