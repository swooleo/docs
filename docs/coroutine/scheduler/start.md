# Scheduler->start

启动调度起去调度协程。

## 方法原型

```php
Swoole\Coroutine\Scheduler::start

<?php
public function start() { }
@return mixed
```

## 无参数

```php
<?php

use Swoole\Coroutine\Scheduler;

$sch = new Scheduler;
$sch->parallel(5, function () {
    var_dump("swoole");
});
$sch->start();
```

```shell
string(6) "swoole"
string(6) "swoole"
string(6) "swoole"
string(6) "swoole"
string(6) "swoole"
```
