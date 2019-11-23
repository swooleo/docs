# Coroutine::Set

`Coroutine::set`用来设置协程相关选项。

```php
Swoole\Coroutine::set

<?php
public static function set($options) { }
@param array $options

@return mixed
```

## max_coroutine

设置最大协程数，超过限制后底层将无法创建新的协程。

> 注意，这个最大协程数指的是这个线程目前存活的协程数，而不是创建的协程个数。因为死亡的协程是不记录在协程个数里面的。

例子：

```php
<?php

Swoole\Coroutine::set([
    'max_coroutine' => 5,
]);

for ($i=0; $i < 6; $i++) {
    go(function() {
        echo 'swoole' . PHP_EOL;
    });
}
```

```shell
swoole
swoole
swoole
swoole
swoole
swoole
```

依然可以创建出`6`个协程。因为协程创建完之后，执行`echo`就会立马死亡，接着创建下一个协程。

但是，下面这个例子是会失败的：

```php
<?php

Swoole\Coroutine::set([
    'max_coroutine' => 5,
]);

go(function() {
    echo 'swoole' . PHP_EOL;
    go(function() {
        echo 'swoole' . PHP_EOL;
        go(function() {
            echo 'swoole' . PHP_EOL;
            go(function() {
                echo 'swoole' . PHP_EOL;
                go(function() {
                    echo 'swoole' . PHP_EOL;
                    go(function() {
                        echo 'swoole' . PHP_EOL;
                    });
                });
            });
        });
    });
});
```

```shell
php set.php
swoole
swoole
swoole
swoole
swoole
PHP Warning:  go(): exceed max number of coroutine 5 in /root/codeDir/phpCode/swoole/coroutine/coroutine/set.php on line 19
```

## hook_flags

可以在运行时动态将基于`php_stream`实现的扩展、`PHP`网络客户端代码一键协程化。`Swoole`创建协程的时候，默认是不会`hook`的。

可以`hook`的`flag`有：

### SWOOLE_HOOK_TCP

例子：

`hook`的`tcp`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_TCP,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        $ctx = stream_context_create(['socket' => ['so_reuseaddr' => true, 'backlog' => 128]]);
        $socket = stream_socket_server(
            'tcp://0.0.0.0:6666',
            $errno, $errstr, STREAM_SERVER_BIND | STREAM_SERVER_LISTEN, $ctx
        );
        if (!$socket) {
            echo "$errstr ($errno)" . PHP_EOL;
            exit(1);
        }
        while (stream_socket_accept($socket)) {
        }
    });
    echo "here" . PHP_EOL;
});
```

```shell
here

```

没有`hook`的`tcp`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        $ctx = stream_context_create(['socket' => ['so_reuseaddr' => true, 'backlog' => 128]]);
        $socket = stream_socket_server(
            'tcp://0.0.0.0:6666',
            $errno, $errstr, STREAM_SERVER_BIND | STREAM_SERVER_LISTEN, $ctx
        );
        if (!$socket) {
            echo "$errstr ($errno)" . PHP_EOL;
            exit(1);
        }
        while (stream_socket_accept($socket)) {
        }
    });
    echo "here" . PHP_EOL;
});
```

此时不会打印出`here`字符串。

### SWOOLE_HOOK_UDP

`hook`的`udp`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_UDP,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        $socket = stream_socket_server(
            'udp://0.0.0.0:6666',
            $errno, $errstr, STREAM_SERVER_BIND
        );
        if (!$socket) {
            echo "$errstr ($errno)" . PHP_EOL;
            exit(1);
        }
        while (stream_socket_recvfrom($socket, 1, 0)) {
        }
    });
    echo "here" . PHP_EOL;
});
```

```shell
here

```

没有`hook`的`udp`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        $socket = stream_socket_server(
            'udp://0.0.0.0:6666',
            $errno, $errstr, STREAM_SERVER_BIND
        );
        if (!$socket) {
            echo "$errstr ($errno)" . PHP_EOL;
            exit(1);
        }
        while (stream_socket_recvfrom($socket, 1, 0)) {
        }
    });
    echo "here" . PHP_EOL;
});
```

此时不会打印出`here`字符串。

### SWOOLE_HOOK_UNIX

### SWOOLE_HOOK_UDG

### SWOOLE_HOOK_SSL

### SWOOLE_HOOK_TLS

### SWOOLE_HOOK_STREAM_FUNCTION

### SWOOLE_HOOK_FILE

### SWOOLE_HOOK_SLEEP

例子：

`hook`的`sleep`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_SLEEP,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        sleep(1);
        echo '1' . PHP_EOL;
    });
    go(function () {
        echo '2' . PHP_EOL;
    });
});
```

```shell
2
1
```

没有`hook`的`sleep`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        sleep(1);
        echo '1' . PHP_EOL;
    });
    go(function () {
        echo '2' . PHP_EOL;
    });
});
```

```shell
1
2
```

### SWOOLE_HOOK_PROC

### SWOOLE_HOOK_BLOCKING_FUNCTION

### SWOOLE_HOOK_CURL

## c_stack_size

设置单个协程初始栈的内存尺寸，单位是`B`，默认为`2 * 1024 * 1024B`，即`2MB`。范围：`256 * 1024B ～ 16 * 1024 * 1024`。

> 注意，这里设置的是协程初始C栈的大小，而不是协程初始PHP栈的大小。
> 协程初始PHP栈的大小目前是固定的8 * 1024B。

## log_level

日志等级。

## trace_flags

跟踪标签。

## socket_connect_timeout

建立`socket`连接超时时间, 默认为`1s`。

## socket_timeout

`socket`读写操作超时时间, 默认为`-1`, 即永不超时。

## dns_cache_expire

设置`swoole` `dns`缓存失效时间，单位`s`，默认`60s`。

## dns_cache_capacity

设置`swoole` `dns`缓存容量，默认`1000`。

## enable_preemptive_scheduler

设置打开写成抢占式调度，协程最大执行时间为`10ms`。
