# Coroutine::disableScheduler

关闭**当前协程**的`CPU`密集型调度。

## 方法原型

```php
Swoole\Coroutine::disableScheduler

<?php
public static function disableScheduler() { }
@return mixed
```

## 无参数

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

ini_set("swoole.enable_preemptive_scheduler", "1");

run(function () {
    go(function () {
        Coroutine::disableScheduler();
        while (true) {
        }
    });
    var_dump('swoole');
});
```

可以发现，是不会跳出死循环的。
