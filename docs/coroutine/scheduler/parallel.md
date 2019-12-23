# Scheduler->add

批量添加多个协程任务函数。

## 方法原型

```php
Swoole\Coroutine\Scheduler::parallel

<?php
public function parallel($n, callable $func = null, ...$params) { }
@param long $n

@param callable|null $func

@param mixed ...$params

@return mixed
```

## 参数

### long $n

批量添加协程任务函数的个数：

```php
<?php

use Swoole\Coroutine;
use Swoole\Coroutine\Scheduler;

$sch = new Scheduler;
$sch->parallel(5, function () {
    var_dump("co: " . Coroutine::getCid());
});
$sch->start();
```

```shell
string(5) "co: 1"
string(5) "co: 2"
string(5) "co: 3"
string(5) "co: 4"
string(5) "co: 5"
```

### callable|null $func

需要被执行的协程任务函数。

### mixed ...$params

传递给协程任务函数的参数：

```php
<?php

use Swoole\Coroutine\Scheduler;

$sch = new Scheduler;
$sch->parallel(5, function ($param) {
    var_dump($param);
}, 1);
$sch->start();
```

```shell
int(1)
int(1)
int(1)
int(1)
int(1)
```
