# Coroutine::list

返回一个迭代器，可以用来遍历所有**存活**的协程。对返回的对象进行迭代，可以得到所有协程的`cid`。

## 方法原型

```php
Swoole\Coroutine::list

<?php
public static function list() { }
@return Swoole\Coroutine\Iterator
```

## 无参数

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        go(function () {
            go(function () {
                go(function () {
                    $coros = Coroutine::list();
                    var_dump($coros);
                    foreach ($coros as $co) {
                        var_dump($co);
                    }
                });
            });
        });
    });
});
```

```shell
object(Swoole\Coroutine\Iterator)#7 (1) {
  ["storage":"ArrayIterator":private]=>
  array(5) {
    [0]=>
    int(5)
    [1]=>
    int(4)
    [2]=>
    int(3)
    [3]=>
    int(2)
    [4]=>
    int(1)
  }
}
int(5)
int(4)
int(3)
int(2)
int(1)
```
