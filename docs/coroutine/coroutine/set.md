# Coroutine::set

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

`hook`的`unix`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_UNIX,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        $socket = stream_socket_server(
            'unix://swoole.sock', $errno, $errstr, STREAM_SERVER_BIND | STREAM_SERVER_LISTEN
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

没有`hook`的`unix`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        $socket = stream_socket_server(
            'unix://swoole.sock', $errno, $errstr, STREAM_SERVER_BIND | STREAM_SERVER_LISTEN
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

### SWOOLE_HOOK_UDG

`hook`的`udg`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_UDG,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        $socket = stream_socket_server(
            'udg://swoole.sock', $errno, $errstr, STREAM_SERVER_BIND
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

没有`hook`的`udg`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        $socket = stream_socket_server(
            'udg://swoole.sock', $errno, $errstr, STREAM_SERVER_BIND
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

### SWOOLE_HOOK_SSL

TODO

### SWOOLE_HOOK_TLS

TODO

### SWOOLE_HOOK_STREAM_FUNCTION

TODO

### SWOOLE_HOOK_FILE

`hook`的`file`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_FILE,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        $fp = fopen(__FILE__, 'r');
        while (true) {
            fread($fp, 1024);
        }
    });
    echo "here" . PHP_EOL;
});
```

```shell
here

```

没有`hook`的`file`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        $fp = fopen(__FILE__, 'r');
        while (true) {
            fread($fp, 1024);
        }
    });
    echo "here" . PHP_EOL;
});
```

此时不会打印出`here`字符串。

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

`hook`的`proc`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_PROC,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        $descriptorspec = array(
            0 => array("pipe", "r"),  // stdin, child process read from it
            1 => array("pipe", "w"),  // stdout, child process write to it
        );
        $process = proc_open('php', $descriptorspec, $pipes);
        if (is_resource($process)) {
            fwrite($pipes[0], '<?php echo "I am process\n" ?>');
            fclose($pipes[0]);

            while (true) {
                echo fread($pipes[1], 1024);
            }

            fclose($pipes[1]);
            $return_value = proc_close($process);
            echo "command returned $return_value" . PHP_EOL;
        }
    });

    echo "here" . PHP_EOL;
});
```

打印出`here`字符串：

```shell
here
I am process
```

没有`hook`的`proc`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        $descriptorspec = array(
            0 => array("pipe", "r"),  // stdin, child process read from it
            1 => array("pipe", "w"),  // stdout, child process write to it
        );
        $process = proc_open('php', $descriptorspec, $pipes);
        if (is_resource($process)) {
            fwrite($pipes[0], '<?php echo "I am process\n" ?>');
            fclose($pipes[0]);

            while (true) {
                echo fread($pipes[1], 1024);
            }

            fclose($pipes[1]);
            $return_value = proc_close($process);
            echo "command returned $return_value" . PHP_EOL;
        }
    });

    echo "here" . PHP_EOL;
});
```

此时不会打印出`here`字符串：

```shell
I am process

```

### SWOOLE_HOOK_BLOCKING_FUNCTION

这里的`blocking function`包括了：`gethostbyname`、`exec`、`shell_exec`。

`hook`的`blocking function`：

```php
<?php

Swoole\Coroutine::set([
    'hook_flags' => SWOOLE_HOOK_BLOCKING_FUNCTION,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        while (true) {
            exec("cat");
        }
    });

    echo "here" . PHP_EOL;
});
```

```shell
here

```

没有`hook`的`blocking function`：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        while (true) {
            exec("cat");
        }
    });

    echo "here" . PHP_EOL;
});
```

此时不会打印出`here`字符串。

### SWOOLE_HOOK_CURL

TODO

## c_stack_size

设置单个协程初始栈的内存尺寸，单位是`B`，默认为`2 * 1024 * 1024B`，即`2MB`。范围：`256 * 1024B ～ 16 * 1024 * 1024`。

> 注意，这里设置的是协程初始C栈的大小，而不是协程初始PHP栈的大小。
> 协程初始PHP栈的大小目前是固定的8 * 1024B。

## socket_connect_timeout

建立`socket`连接超时时间, 单位：`s`，默认为`1s`。设置为`-1`，表示永不超时。

> 注意，这里不能设置为0。设置为0的话，会取默认值。

## socket_timeout

设置`socket`读和写的操作超时时间, 单位：`s`，默认为`-1`, 即永不超时。

> 注意，这里不能设置为0。设置为0的话，会取默认值。

## socket_read_timeout

只设置`socket`读的操作超时时间，单位`s`，默认为`-1`, 即永不超时。

> 注意，这里不能设置为0。设置为0的话，会取默认值。

## socket_write_timeout

只设置`socket`写的操作超时时间，单位`s`，默认为`-1`, 即永不超时。

> 注意，这里不能设置为0。设置为0的话，会取默认值。

## log_level

设置日志等级。大于等于这个日志等级的日志**才**会被打印出来。

> 注意，这里配置的不是PHP层的日志级别，而是C层的日志级别，后面会有一个例子进行说明。

可配置的日志等级从低到高为：

```shell
SWOOLE_LOG_DEBUG
SWOOLE_LOG_TRACE
SWOOLE_LOG_INFO
SWOOLE_LOG_NOTICE
SWOOLE_LOG_WARNING
SWOOLE_LOG_ERROR
```

> 注意，如果在编译`Swoole`的时候开启了`debug`，那么`log_level`默认是`SWOOLE_LOG_DEBUG`；否则`log_level`默认是`SWOOLE_LOG_INFO`。

没有设置`log_level`的例子：

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        $client = new Swoole\Coroutine\Client(SWOOLE_TCP);
        $client->connect('127.0.0.1');
    });
});
```

此时会打印`PHP`层`Warning`级别的警告：

```shell
PHP Warning:  Swoole\Coroutine\Client::connect(): The port is invalid in /root/codeDir/phpCode/swoole/coroutine/coroutine/set.php on line 6
```

设置`log_level`的例子：

```php
<?php

Swoole\Coroutine::set([
    'log_level' => SWOOLE_LOG_ERROR,
]);

Swoole\Coroutine\run(function () {
    go(function () {
        $client = new Swoole\Coroutine\Client(SWOOLE_TCP);
        $client->connect('127.0.0.1');
    });
});
```

此时依然会打印出`PHP`层`Warning`级别的警告：

```shell
PHP Warning:  Swoole\Coroutine\Client::connect(): The port is invalid in /root/codeDir/phpCode/swoole/coroutine/coroutine/set.php on line 10
```

## trace_flags

TODO

## dns_cache_expire

设置`swoole` `dns`缓存失效时间，单位`s`，默认`60s`。

## dns_cache_capacity

设置`swoole` `dns`缓存容量，默认`1000`。

## dns_server

TODO

## display_errors

TODO

## aio_core_worker_num

TODO

## aio_worker_num

TODO

## aio_max_wait_time

TODO

## aio_max_idle_time

TODO
