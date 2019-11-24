# Coroutine::resume

让指定的协程继续运行。

## 方法原型

```php
Swoole\Coroutine::resume

<?php
public static function resume($cid) { }
@param long $cid

@return bool
```

## 参数

### long $cid

需要继续运行的协程`cid`。

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
