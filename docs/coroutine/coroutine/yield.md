# Coroutine::yield

让出当前协程的执行权。

## 方法原型

```php
Swoole\Coroutine::yield

<?php
public static function yield() { }
@return mixed
```

## 无参数

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    $cid = go(function () {
        var_dump(Coroutine::getCid());
        Coroutine::yield();
        var_dump(Coroutine::getCid());
    });

    go(function () use ($cid) {
        var_dump(Coroutine::getCid());
        Coroutine::resume($cid);
        var_dump(Coroutine::getCid());
    });
});
```

```shell
int(2)
int(3)
int(2)
int(3)
```

## 注意

必须与`Coroutine::resume()`方法成对使用。该协程`yield`以后，必须由其他外部协程`resume`，否则将会造成协程泄漏，被挂起的协程永远不会执行，从而造成内存泄露。所以**不推荐**开发者使用。

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    $cid = go(function () {
        var_dump(Coroutine::getCid());
        Coroutine::yield();
        var_dump(Coroutine::getCid());
    });

    go(function () use ($cid) {
        var_dump(Coroutine::getCid());
    });
});
```

```shell
int(2)
int(3)
```

此时，`cid`为`2`的协程不会重新执行。
