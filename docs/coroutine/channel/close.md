# Channel->close

关闭通道。并唤醒所有等待读写的协程。

## 方法原型

```php
Swoole\Coroutine\Channel::close

<?php
public function close() { }
@return bool
```

## 无参数

- 唤醒所有生产者协程，push方法返回false
- 唤醒所有消费者协程，pop方法返回false

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $ret = $chan->push('a');
        var_dump($ret);
    });

    go(function () use ($chan) {
        $ret = $chan->push('a');
        var_dump($ret);
    });

    go(function () use ($chan) {
        $ret = $chan->push('a');
        var_dump($ret);
    });

    $chan->close();
});
```

```shell
bool(true)
bool(false)
bool(false)
```

因为一个协程默认会占用`8 * 1024B`的`PHP`内存和`2MB`的`C`内存，如果协程一直被挂起，那么会造成内存泄漏，所以及时调用`Channel`的`close`方法可以尽可能的避免内存泄漏：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $chan->push('a');
    });

    go(function () use ($chan) {
        $chan->push('a');
    });

    go(function () use ($chan) {
        $chan->push('a');
    });

    var_dump(memory_get_usage());
    $chan->close();
    var_dump(memory_get_usage());
});
```

```shell
int(914128)
int(896352)
```

此时，因为调用了`Channel`的`close`方法，会唤醒被挂起的`2`个协程，所以内存使用量减少了。
