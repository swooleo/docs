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