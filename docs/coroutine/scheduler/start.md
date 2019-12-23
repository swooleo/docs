# Scheduler->start

启动调度器去调度协程。遍历`add`和`parallel`方法添加的协程任务并执行。

- 启动成功，会执行所有添加的任务，所有协程退出时`start`会返回`true`
- 启动失败返回`false`，原因可能是已经启动了或者已经创建了其他调度器无法再次创建

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
$sch->add(function () {
    var_dump("add");
});
$sch->parallel(2, function () {
    var_dump("parallel");
});
$sch->add(function () {
    var_dump("add");
});
$sch->parallel(3, function () {
    var_dump("parallel");
});
$sch->start();
```

```shell
string(3) "add"
string(8) "parallel"
string(8) "parallel"
string(3) "add"
string(8) "parallel"
string(8) "parallel"
string(8) "parallel"
```
