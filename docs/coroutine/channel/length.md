# Channel->length

获取`Channel`中的数据数量。

## 方法原型

```php
Swoole\Coroutine\Channel::length

<?php
public function length() { }
@return long
```

## 无参数

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(3);

    go(function () use ($chan) {
        $chan->push('a');
    });

    go(function () use ($chan) {
        $chan->push('a');
    });

    var_dump($chan->length());
});
```

```shell
int(2)
```
