# Server->handle

设置连接处理函数。

## 方法原型

```php
Swoole\Coroutine\Server::handle

<?php
public function handle(callable $fn): void { }
@param callable $fn

@return void
```

## 参数

### callable $fn

其中，这个回调函数可以接收一个参数，类型为`Swoole\Coroutine\Server\Connection`。`Swoole`底层自动会去传递这个参数，开发者只需要对这个参数进行接收和发送等常规操作即可。

```php
<?php

use Swoole\Coroutine\Server;
use Swoole\Coroutine\Server\Connection;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501, false, true);
    $server->handle(function (Connection $conn) {
        $data = $conn->recv();
        var_dump($data);
        $conn->close();
    });
    $server->start();
});
```

#### Connection类

`Connection`类提供了`recv`、`send`、`close`方法。并且，`Connection`有一个`public`的属性`$socket`，它是`Swoole\Coroutine\Socket`类型的，如果`recv`、`send`、`close`这三个方法无法满足开发者，开发者可以直接操作`$socket`。
