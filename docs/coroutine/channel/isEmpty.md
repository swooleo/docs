# Channel->isEmpty

判断`Channel`是否为空。

## 方法原型

```php
Swoole\Coroutine\Channel::isEmpty

<?php
public function isEmpty() { }
@return bool
```

## 无参数

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(1);
    var_dump($chan->isEmpty());
    $chan->push('a');
    var_dump($chan->isEmpty());
});
```

```shell
bool(true)
bool(false)
```
