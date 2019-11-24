# Coroutine::stats

获取当前协程的状态。

## 方法原型

```php
Swoole\Coroutine::stats

<?php
public static function stats() { }
@return array
```

## 无参数

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    var_dump(Coroutine::stats());
});
```

```shell
array(8) {
  ["event_num"]=>
  int(0)
  ["signal_listener_num"]=>
  int(0)
  ["aio_task_num"]=>
  int(0)
  ["aio_worker_num"]=>
  int(0)
  ["c_stack_size"]=>
  int(2097152)
  ["coroutine_num"]=>
  int(1)
  ["coroutine_peak_num"]=>
  int(1)
  ["coroutine_last_cid"]=>
  int(1)
}
```

## 返回值

`array`类型。

### event_num

当前进程所注册的事件个数。例如，在网络服务器中，`accept`会产生一个`event`。例子：

```php
<?php

use Swoole\Coroutine;
use Swoole\Coroutine\Socket;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        $socket = new Socket(AF_INET, SWOOLE_TCP);
        $socket->bind("127.0.0.1", 9501);
        $socket->listen();
        while (true) {
            $conn = $socket->accept();
        }
    });
    var_dump(Coroutine::stats());
});
```

```shell
array(8) {
  ["event_num"]=>
  int(1)
  ["signal_listener_num"]=>
  int(0)
  ["aio_task_num"]=>
  int(0)
  ["aio_worker_num"]=>
  int(0)
  ["c_stack_size"]=>
  int(2097152)
  ["coroutine_num"]=>
  int(2)
  ["coroutine_peak_num"]=>
  int(2)
  ["coroutine_last_cid"]=>
  int(2)
}

```

### signal_listener_num

TODO

### aio_task_num

TODO

### aio_worker_num

TODO

### c_stack_size

> 注意，这里不是指所有协程加起来的C栈大小，而是当前配置好的每个协程初始`C`栈大小

可以在创建协程之前，配置好协程`C`栈的大小。例子：

```php
<?php

use Swoole\Coroutine;
use Swoole\Coroutine\Socket;

use function Swoole\Coroutine\run;

Coroutine::set([
    'c_stack_size' => 3 * 1024 * 1024,
]);

run(function () {
    go(function () {
        var_dump(Coroutine::stats());
    });
});
```

```shell
array(8) {
  ["event_num"]=>
  int(0)
  ["signal_listener_num"]=>
  int(0)
  ["aio_task_num"]=>
  int(0)
  ["aio_worker_num"]=>
  int(0)
  ["c_stack_size"]=>
  int(3145728)
  ["coroutine_num"]=>
  int(2)
  ["coroutine_peak_num"]=>
  int(2)
  ["coroutine_last_cid"]=>
  int(2)
}
```

### coroutine_num

当前进程中存活的协程个数，例子：

```php
<?php

use Swoole\Coroutine;
use Swoole\Coroutine\Socket;

use function Swoole\Coroutine\run;

Coroutine::set([
    'c_stack_size' => 3 * 1024 * 1024,
]);

run(function () {
    go(function () {
    });
    var_dump(Coroutine::stats());
});
```

```shell
array(8) {
  ["event_num"]=>
  int(0)
  ["signal_listener_num"]=>
  int(0)
  ["aio_task_num"]=>
  int(0)
  ["aio_worker_num"]=>
  int(0)
  ["c_stack_size"]=>
  int(3145728)
  ["coroutine_num"]=>
  int(1)
  ["coroutine_peak_num"]=>
  int(2)
  ["coroutine_last_cid"]=>
  int(2)
}
```

可以发现，我们创建了两个协程（`run`函数会创建一个协程），但是`coroutine_num`是`1`。

### coroutine_peak_num

当前进程中**存活**的协程最大个数。

例子：

```php
<?php

use Swoole\Coroutine;
use Swoole\Coroutine\Socket;

use function Swoole\Coroutine\run;

Coroutine::set([
    'c_stack_size' => 3 * 1024 * 1024,
]);

run(function () {
    for ($i=0; $i < 5; $i++) {
        go(function () {
        });
    }

    var_dump(Coroutine::stats());
});
```

```shell
array(8) {
  ["event_num"]=>
  int(0)
  ["signal_listener_num"]=>
  int(0)
  ["aio_task_num"]=>
  int(0)
  ["aio_worker_num"]=>
  int(0)
  ["c_stack_size"]=>
  int(3145728)
  ["coroutine_num"]=>
  int(1)
  ["coroutine_peak_num"]=>
  int(2)
  ["coroutine_last_cid"]=>
  int(6)
}
```

尽管在`run`函数里面创建了`5`个协程，但是协程每次创建完之后就立马死亡，所以峰值是`2`个。

### coroutine_last_cid

当前进程的最后一个协程`cid`。
