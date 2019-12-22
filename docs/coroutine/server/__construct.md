# Server->__construct

`Server`构造方法。

## 方法原型

```php
Swoole\Coroutine\Server

<?php
class Server { }
Swoole\Coroutine\Server::__construct

Server constructor.

<?php
public function __construct(
    string $host,
    int $port = 0,
    bool $ssl = false,
    bool $reuse_port = false
) { }
@param string $host

@param int $port
[optional]

@param bool $ssl
[optional]

@param bool $reuse_port
[optional]

@return void
```

## 参数

### string $host

监听的地址，支持`3`种格式。

#### IPv4地址

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server("0.0.0.0");
});
```

#### IPv6地址

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server("::");
});
```

#### UnixSocket地址

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server("unix:/test.sock");
});
```

### string $port

端口范围：`port >= 0 && port <= 65535`

#### 指定端口

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server("0.0.0.0", 9501);
    var_dump($server->port);
});
```

```shell
int(9501)
```

#### 随机分配端口

监听的端口，默认（即`port`为`0`）将由操作系统随机分配一个端口。

例子一：

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server("0.0.0.0");
    var_dump($server->port);
});
```

```shell
int(39415)
```

例子二：

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server("0.0.0.0", 0);
    var_dump($server->port);
});
```

```shell
int(49382)
```

### string $ssl

是否开启`SSL`加密。

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server("0.0.0.0", 9501, true);
    $server->set([
        'ssl_cert_file' => dirname(__DIR__) . '/server.crt',
        'ssl_key_file' => dirname(__DIR__) . '/server.key',
    ]);
});
```

### string $reuse_port

是否开启端口重用，仅在`Linux-3.10`或更高版本内核可用。开启后可以在多个进程中绑定监听同一个端口，配合`Process\Pool`可以实现多进程并行处理同一个端口的网络请求，实现更高的吞吐能力。

```php
<?php

use Swoole\Coroutine\Server;
use Swoole\Constant;
use Swoole\Coroutine\Server\Connection;
use Swoole\Process\Pool;

$pool = new Pool(2);
$pool->set(['enable_coroutine' => true]);
$pool->on(Constant::EVENT_WORKER_START, function ($pool, $id) {
    $server = new Server('127.0.0.1', 9501, false, true);
    $server->handle(function (Connection $conn) {
        $data = $conn->recv();
        var_dump($data);
        $conn->close();
    });
    $server->start();
});
$pool->start();
```
