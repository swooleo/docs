# Channel->capacity

构造函数中设定的容量会保存在此，不过如果设定的容量小于`1`则此变量会等于`1`。

例子：

```php
<?php

use Swoole\Coroutine\Channel;

use function Co\run;

run(function () {
    $chan = new Channel(2);
    var_dump($chan->capacity);
});
```

```shell
int(2)
```
