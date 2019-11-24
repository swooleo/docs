# Coroutine::getCid

获取当前协程的唯一`ID`, 它的别名为`getUid`, 是一个进程内唯一的正整数。

## 方法原型

```php
Swoole\Coroutine::getCid

<?php
public static function getCid() { }
@return mixed
```

## 无参数

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    var_dump(Coroutine::getCid());
});
```

```shell
int(1)
```
