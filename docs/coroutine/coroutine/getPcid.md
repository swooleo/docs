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

## 注意

协程之间并没有实质上的父子关系，协程之间是相互隔离、独立运作的。此`Pcid`可理解为是哪个协程创建了当前协程。

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        var_dump("parent coroutine start");

        Coroutine::defer(function () {
            var_dump("parent coroutine dead");
        });

        go(function () {
            var_dump("child coroutine start");
            Coroutine::sleep(0.01);
            var_dump("child coroutine still running");
        });
    });
});
```

```shell
string(22) "parent coroutine start"
string(21) "child coroutine start"
string(21) "parent coroutine dead"
string(29) "child coroutine still running"
```

父协程死亡之后，子协程依然还在运行。
