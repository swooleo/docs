# Coroutine::exists

判断指定协程是否存在。

## 方法原型

```php
Swoole\Coroutine::exists

<?php
public static function exists($cid) { }
@param long $cid

@return bool
```

## 参数

### long $cid

协程`id`。

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    $cid = go(function () {
        go(function () {
            var_dump(Coroutine::exists(Coroutine::getCid()));
        });
    });

    var_dump(Coroutine::exists($cid));
});
```

```shell
bool(true)
bool(false)
```

因为，当执行到

```php
var_dump(Coroutine::exists($cid));
```

这一行的时候，`$cid`对应的那个协程已经死亡了，所以返回`false`。
