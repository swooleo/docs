# Server->start

启动服务器。启动成功将进入循环，`accept`连接。`accept`新的连接后会创建一个新的协程，并在协程中调用`handle`方法指定的函数。

> 当`accept`发生`too many open file`错误、或者无法创建子协程时，将暂停`1`秒然后再继续`accept`

## 方法原型

```php
Swoole\Coroutine\Server::start

<?php
public function start(): bool { }
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
    $server->handle(function (Connection $conn) {
        $data = $conn->recv();
        var_dump($data);
        $conn->close();
    });
    $server->start();
});
```

## 返回值

启动失败会返回`false`，并设置`errCode`属性。
