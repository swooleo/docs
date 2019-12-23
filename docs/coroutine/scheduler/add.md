# Scheduler->add

添加协程任务函数。

## 方法原型

```php
Swoole\Coroutine\Scheduler::add

<?php
public function add(callable $func, ...$params) { }
@param callable $func

@param mixed ...$params

@return mixed
```

## 参数

### callable $func

需要被执行的协程任务函数：

```php
<?php

use Swoole\Coroutine\Scheduler;

use function Co\run;

run(function () {
    $sch = new Scheduler;
    $sch->add(function () {
        var_dump("swoole");
    });
});
```

### mixed ...$params

传递给协程任务函数的参数：

```php
<?php

use Swoole\Coroutine\Scheduler;

use function Co\run;

run(function () {
    $sch = new Scheduler;
    $sch->add(function ($param) {
        var_dump($param);
    }, "swoole");
});
```
