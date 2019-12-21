# Channel->errCode

- 默认成功 `0` (`SWOOLE_CHANNEL_OK`)
- 超时`pop`失败时(超时)会置为`-1` (`SWOOLE_CHANNEL_TIMEOUT`)
- `Channel`已关闭,继续操作`Channel`，设置错误码 `-2` (`SWOOLE_CHANNEL_CLOSED`)

## SWOOLE_CHANNEL_OK

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel();
    go(function () use ($chan) {
        $chan->push('hello');
        var_dump($chan->errCode);
    });
});
```

```shell
int(0)
```

## SWOOLE_CHANNEL_TIMEOUT

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel();
    go(function () use ($chan) {
        $chan->pop(1);
        var_dump($chan->errCode);
    });
});
```

```shell
int(-1)
```

## SWOOLE_CHANNEL_CLOSED

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel();
    go(function () use ($chan) {
        $chan->pop();
        var_dump($chan->errCode);
    });

    $chan->close();
});
```

```shell
int(-2)
```
