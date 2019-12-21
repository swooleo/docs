# Channel->pop

向`Channel`中获取出数据。

## 方法原型

```php
Swoole\Coroutine\Channel::pop

<?php
public function pop($timeout = null) { }
@param double $timeout
[optional]

@return mixed | false
```

## 参数

### mixed $timeout

等待的超时时间，单位秒（`s`）

在通道为空的情况下，pop会挂起当前协程，在约定的时间内，如果没有任何生产者生产数据，将发生超时，底层会恢复当前协程，`pop`调用立即返回`false`，取出失败。

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->pop(1);
        var_dump($ret);
    });
});
```

```shell
bool(false)
```

## 返回值

因为`Channel`调用`pop`的时候，如果失败会返回`false`，所以为避免产生歧义，请勿向通道中写入`false`。

取出`Channel`里面的`false`：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->push(false);
        var_dump($ret);
    });

    go(function () use ($chan) {
        $ret = $chan->pop();
        var_dump($ret);
    });
});
```

```shell
bool(true)
bool(false)
```

因为失败，导致返回`false`：

```shell
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->pop(1);
        var_dump($ret);
    });
});
```

```shell
bool(false)
```

如果必须要往`Channel`里面`push`一个`false`，那么必须通过`Channel`的`errCode`错误码来判断是否发生了错误：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->push(false);
        var_dump($ret);
    });

    go(function () use ($chan) {
        $ret = $chan->pop();
        if (($ret === false) && ($chan->errCode < 0)) {
            var_dump("error");
        } else {
            var_dump("success");
        }
    });
});
```

```shell
bool(true)
string(7) "success"
```

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->pop(1);
        if (($ret === false) && ($chan->errCode < 0)) {
            var_dump("error");
        } else {
            var_dump("success");
        }
    });
});
```

```shell
string(5) "error"
```
