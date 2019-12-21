# Channel->push

向`Channel`中写入数据。

## 方法原型

```php
Swoole\Coroutine\Channel::push

<?php
public function push($data, $timeout = null) { }
@param mixed $data

@param double $timeout
[optional]

@return bool
```

## 参数

### mixed $data

往`Channel`中存放的数据（可以是任意类型的数据）。例如：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(2);

    go(function () use ($chan) {
        $chan->push("swoole");
        $chan->push(1024);
        $chan->push((object)[
            'object',
        ]);
    });

    go(function () use ($chan) {
        $data = $chan->pop();
        var_dump($data);
        $data = $chan->pop();
        var_dump($data);
        $data = $chan->pop();
        var_dump($data);
    });
});
```

```shell
string(6) "swoole"
int(1024)
object(stdClass)#5 (1) {
  ["0"]=>
  string(6) "object"
}
```

### mixed $timeout

等待的超时时间，单位秒（`s`）

在通道已满的情况下，push会挂起当前协程，在约定的时间内，如果没有任何消费者消费数据，将发生超时，底层会恢复当前协程，push调用立即返回false，写入失败。

#### 不设置timeout

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->push("swoole");
        var_dump($ret);
        $ret = $chan->push(1024);
        var_dump($ret);
    });

    var_dump("here");
});
```

```shell
bool(true)
string(4) "here"
```

此时`1024`没有被`push`进`Channel`里面，并且协程被`yield`出来了。

#### 设置timeout

```php
<?php

use Swoole\Coroutine;
use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->push("swoole");
        var_dump($ret);
        $ret = $chan->push(1024, 1);
        var_dump($ret);
    });

    var_dump("here");
});
```

```shell
bool(true)
string(4) "here"
bool(false)
```

此时，因为`timeout`的时间到了，但是依然没有消费者协程去从这个`Channel`里面`pop`出数据，所以第二个`push`返回`false`。

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
