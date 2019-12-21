# Channel->stats

获取通道的状态。

## 方法原型

```php
Swoole\Coroutine\Channel::stats

<?php
public function stats() { }
@return array
[
  ["consumer_num"]=>
  int
  ["producer_num"]=>
  int
  ["queue_num"]=>
  int
]
```

## 无参数

### consumer_num

代表调用`pop`后被`yield`的协程数量（也就意味着，这些协程在等其他协程生产数据）。

例如：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $chan->pop();
    });

    go(function () use ($chan) {
        $chan->pop();
    });

    go(function () use ($chan) {
        $chan->pop();
    });

    var_dump($chan->stats());
});
```

```shell
array(3) {
  ["consumer_num"]=>
  int(3)
  ["producer_num"]=>
  int(0)
  ["queue_num"]=>
  int(0)
}
```

### producer_num

代表调用`push`后被`yield`的协程数量（也就意味着，这些协程在等其他协程消费数据）。

例如：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);

    go(function () use ($chan) {
        $chan->push("a");
    });

    go(function () use ($chan) {
        $chan->push("b");
    });

    go(function () use ($chan) {
        $chan->push("c");
    });

    var_dump($chan->stats());
});
```

```shell
array(3) {
  ["consumer_num"]=>
  int(0)
  ["producer_num"]=>
  int(2)
  ["queue_num"]=>
  int(1)
}
```

此时，`producer_num`的个数为`2`。因为这个`Channel`的容量是`1`，所以，第一个生产者协程可以往这个`Channel`里面`push`数据。后面的两个生产者协程则需要等待。

### queue_num

代表`Channel`中数据的数量。

例如：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(3);

    go(function () use ($chan) {
        $chan->push("a");
    });

    go(function () use ($chan) {
        $chan->push("b");
    });

    var_dump($chan->stats());
});
```

```shell
array(3) {
  ["consumer_num"]=>
  int(0)
  ["producer_num"]=>
  int(0)
  ["queue_num"]=>
  int(2)
}
```
