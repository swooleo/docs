# Server->set

设置协议处理参数。

> 注意，可以多次调用`set`方法，底层会对调用的`set`方法进行`array_merge`的操作

## 方法原型

```php
Swoole\Coroutine\Server::set

<?php
public function set(array $setting): void { }
@param array $setting

@return void
```

## 参数

### array $setting

#### open_ssl

`bool`类型。表示是否开启`ssl`加密：

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501, true);
    $server->set([
        'open_ssl' => true,
    ]);
});
```

#### open_eof_check

`bool`类型。打开`EOF`检测，此选项将检测客户端连接发来的数据，当数据包结尾是指定的字符串时才会结束读取数据，否则会一直读取数据包，直到超过缓存区或者超时才会中止：

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        'open_eof_check' => true,
    ]);
});
```

#### open_eof_split

`bool`类型。启用`EOF`自动分包。当设置`open_eof_check`后，底层检测数据是否以特定的字符串结尾来进行数据缓冲。但默认只截取收到数据的末尾部分做对比。这时候可能会产生多条数据合并在一个包内。

`EOF`切割需要遍历整个数据包的内容，查找`EOF`，因此会消耗大量`CPU`资源。假设每个数据包为`2M`，每秒`10000`个请求，这可能会产生`20G`条`CPU`字符匹配指令。

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        'open_eof_split' => true,
    ]);
});
```

#### package_eof

`string`类型。与`open_eof_check`或者`open_eof_split`配合使用，设置`EOF`字符串。

> 注意，不可以为空字符串，并且EOF字符串的最大长度是8

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        'open_eof_check' => true,
        'package_eof' => "\r\n",
    ]);
});
```

这里需要特别注意的是，那些会被转义的`EOF`字符串例如`\r\n`，在单引号和双引号的含义下是不一样的。至少来说，长度就是不一样的。

```php
<?php

var_dump(strlen('\r\n'));
var_dump(strlen("\r\n"));
```

```shell
int(4)
int(2)
```

#### open_mqtt_protocol

`bool`类型。启用`mqtt`协议处理，启用后会解析`mqtt`包头。

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        'open_mqtt_protocol' => true
    ]);
});
```

#### open_length_check

`bool`类型。打开包长检测特性。包长检测提供了固定包头+包体这种格式协议的解析。

> 长度检测协议，只需要计算一次长度，数据处理仅进行指针偏移，性能非常高，推荐使用。

#### package_length_type

长度值的类型，接受一个字符参数，与`php`的`pack`函数一致。目前`Swoole`支持`10`种类型：

- c：有符号、1字节
- C：无符号、1字节
- s ：有符号、主机字节序、2字节
- S：无符号、主机字节序、2字节
- n：无符号、网络字节序、2字节
- N：无符号、网络字节序、4字节
- l：有符号、主机字节序、4字节（小写L）
- L：无符号、主机字节序、4字节（大写L）
- v：无符号、小端字节序、2字节
- V：无符号、小端字节序、4字节

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        "open_length_check" => true,
        "package_length_type" => 'N',
    ]);
});
```

#### package_length_offset

`length`长度值在包头的第几个字节。

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        "open_length_check" => true,
        "package_length_type" => 'N',
        "package_length_offset" => 0,
    ]);
});
```

#### package_body_offset

从第几个字节开始计算长度，一般有`2`种情况：

`length`的值包含了整个包（包头`+`包体），`package_body_offset`为`0`
包头长度为`N`字节，`length`的值不包含包头，仅包含包体，`package_body_offset`设置为`N`

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        "open_length_check" => true,
        "package_length_type" => 'N',
        "package_body_offset" => 4,
    ]);
});
```

#### package_length_func

设置长度解析函数，支持C++或PHP的2种类型的函数。长度函数必须返回一个整数。

- 返回`0`，长度数据不足，需要接收更多数据
- 返回`-1`，数据错误，底层会自动关闭连接
- 返回包长度值（包括包头和包体的总长度），底层会自动将包拼好后返回给回调函数

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        'open_length_check' => true,
        'package_length_func' => function ($data) {
            if (strlen($data) < 8) {
                return 0;
            }
            $length = intval(trim(substr($data, 0, 8)));
            if ($length <= 0) {
                return -1;
            }
            return $length + 8;
        },
    ]);
});
```

#### package_max_length

> 默认值为`2M`, 即`2 * 1024 * 1024`

设置最大数据包尺寸，单位为字节。开启`open_length_check`/`open_eof_check`等协议解析后。`swoole`底层会进行数据包拼接。这时在数据包未收取完整时，所有数据都是保存在内存中的。

所以需要通过`package_max_length`来设置一个数据包最大允许占用的内存尺寸。如果同时有`1`万个TCP连接在发送数据，每个数据包`2M`，那么最极限的情况下，就会占用`20G`的内存空间。

`open_length_check`，当发现包长度超过`package_max_length`，将直接丢弃此数据，并关闭连接，不会占用任何内存。包括`websocket`、`mqtt`、`http2`协议。
`open_eof_check`，因为无法事先得知数据包长度，所以收到的数据还是会保存到内存中，持续增长。当发现内存占用已超过`package_max_length`时，将直接丢弃此数据，并关闭连接。

```php
<?php

use Swoole\Coroutine\Server;

use function Co\run;

run(function () {
    $server = new Server('127.0.0.1', 9501);
    $server->set([
        'open_eof_check' => true,
        'package_eof' => "\r\n\r\n",
        'package_max_length' => 1024 * 1024 * 2, //2M
    ]);
});
```
