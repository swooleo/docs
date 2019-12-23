# Server->shutdown

终止服务器。

成功返回true，失败返回false。终止运行后start方法中返回。

## 方法原型

```php
Swoole\Coroutine\Server::shutdown

<?php
public function shutdown(): bool { }
@return bool
```

## 无参数

```php
<?php

use Swoole\Coroutine\Server;
use Swoole\Coroutine\Server\Connection;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->handle(function (Connection $conn) use ($server) {
        $data = $conn->recv();
        var_dump($data);
        $conn->close();
        $server->shutdown();
    });
    $server->start();
});
```
