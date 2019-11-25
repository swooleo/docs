# Channel->__construct

通道构造方法。

## 方法原型

```php
Swoole\Coroutine\Channel::__construct

<?php
public function __construct($capacity = 1) { }
@param long|null $capacity

@return void
```

> 注意：
>
> 此Channel可以混合存储任意类型的数据。
>
> 底层使用PHP引用计数来保存变量，缓存区只需要占用 $capacity * sizeof(zval) 字节的内存。
>
> 传递的容量必须为大于或等于1的整数，否则使用默认的1。

## 参数

### long|null $capacity

指定`channel`可以容纳的最大数据个数。默认是`1`。

#### 小于1

例子：

```php
<?php

use Swoole\Coroutine\Channel;

use function Swoole\Coroutine\run;

run(function () {
    $chan1 = new Channel(0);
    var_dump($chan1);
    $chan2 = new Channel(-1);
    var_dump($chan2);
});
```

```shell
object(Swoole\Coroutine\Channel)#3 (2) {
  ["capacity"]=>
  int(1)
  ["errCode"]=>
  int(0)
}
object(Swoole\Coroutine\Channel)#4 (2) {
  ["capacity"]=>
  int(1)
  ["errCode"]=>
  int(0)
}
```

#### null

```php
<?php

use Swoole\Coroutine\Channel;

use function Swoole\Coroutine\run;

run(function () {
    $chan = new Channel();
    var_dump($chan);
});
```

```shell
object(Swoole\Coroutine\Channel)#3 (2) {
  ["capacity"]=>
  int(1)
  ["errCode"]=>
  int(0)
}
```

#### 大于1

```php
<?php

use Swoole\Coroutine\Channel;

use function Swoole\Coroutine\run;

run(function () {
    $chan = new Channel(2);
    var_dump($chan);
});
```

```shell
object(Swoole\Coroutine\Channel)#3 (2) {
  ["capacity"]=>
  int(2)
  ["errCode"]=>
  int(0)
}
```
