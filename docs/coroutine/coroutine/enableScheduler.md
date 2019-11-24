# Coroutine::enableScheduler

让当前协程支持`CPU`密集型调度，默认是开启的。

> 注意，enableScheduler生效的前提是扩展本身开启协程CPU密集型调度。也就是说，当扩展开启了协程CPU密集型调度的时候，会专门起一个调度协程去对占用CPU资源过久的协程进行中断的操作。

## 方法原型

```php
Swoole\Coroutine::enableScheduler

<?php
public static function enableScheduler() { }
@return mixed
```

## 无参数

### 开启扩展的协程CPU密集型调度

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

ini_set("swoole.enable_preemptive_scheduler", "1");

run(function () {
    go(function () {
        Coroutine::enableScheduler();
        while (true) {
        }
    });
    var_dump("swoole");
});
```

```shell
string(6) "swoole"

```

此时，会跳出死循环。（注意，还会回到循环里面）

实际上，协程默认是开启`CPU`密集型调度的。例如：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

ini_set("swoole.enable_preemptive_scheduler", "1");

run(function () {
    go(function () {
        while (true) {
        }
    });
    var_dump("swoole");
});
```

```shell
string(6) "swoole"

```

依然会跳出死循环。

### 关闭扩展的协程CPU密集型调度

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        Coroutine::enableScheduler();
        while (true) {
        }
    });
    var_dump("swoole");
});
```

可以发现，是不会跳出死循环的。
