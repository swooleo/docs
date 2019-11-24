# Coroutine::getPcid

获取当前协程的父`cid`。

## 方法原型

```php
Swoole\Coroutine::getPcid

<?php
public static function getPcid($cid = null) { }
@param long|null $cid

@return long
```

## 参数

### long|null $cid

某个协程的`cid`或者`null`。

如果为`null`，代表获取当前协程的`pcid`。

#### cid不为null

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        var_dump(Coroutine::getPcid(2));
    });
});
```

```shell
int(1)
```

#### cid为null

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        var_dump(Coroutine::getPcid());
    });
});
```

```shell
int(1)
```
