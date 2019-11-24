# Coroutine::getBackTrace

获取协程函数调用栈。

## 方法原型

```php
Swoole\Coroutine::getBackTrace

<?php
public static function getBackTrace(
    $cid = 0,
    $options = DEBUG_BACKTRACE_PROVIDE_OBJECT,
    $limit = 0
) { }
@param long|null $cid

@param long|null $options

@param long|null $limit

@return array|false
```

## 参数

### cid

#### cid不为null

获取`cid`这个协程的函数调用栈。

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    $cid = go(function () {
        Coroutine::yield();
    });
    var_dump(Coroutine::getBackTrace($cid));
    Coroutine::resume($cid);
});
```

```shell
array(1) {
  [0]=>
  array(6) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(9)
    ["function"]=>
    string(5) "yield"
    ["class"]=>
    string(16) "Swoole\Coroutine"
    ["type"]=>
    string(2) "::"
    ["args"]=>
    array(0) {
    }
  }
}
```

可以看到`$cid`这个协程的函数调用栈信息。可以发现，`$cid`这个协程当前执行的函数是`yield`。

#### cid为null

获取当前协程的函数调用栈。

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        var_dump(Coroutine::getBackTrace());
    });
});
```

```shell
array(1) {
  [0]=>
  array(6) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(9)
    ["function"]=>
    string(12) "getBackTrace"
    ["class"]=>
    string(16) "Swoole\Coroutine"
    ["type"]=>
    string(2) "::"
    ["args"]=>
    array(0) {
    }
  }
}
```

可以发现，当前协程执行的函数是`getBackTrace`。

### options

options可以取值`DEBUG_BACKTRACE_PROVIDE_OBJECT`和`DEBUG_BACKTRACE_IGNORE_ARGS`。

#### options不为null

##### DEBUG_BACKTRACE_PROVIDE_OBJECT

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        var_dump(Coroutine::getBackTrace(Coroutine::getCid(), DEBUG_BACKTRACE_PROVIDE_OBJECT));
    });
});
```

```shell
array(1) {
  [0]=>
  array(6) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(9)
    ["function"]=>
    string(12) "getBackTrace"
    ["class"]=>
    string(16) "Swoole\Coroutine"
    ["type"]=>
    string(2) "::"
    ["args"]=>
    array(2) {
      [0]=>
      int(2)
      [1]=>
      int(1)
    }
  }
}
```

可以得到传递给`getBackTrace`函数的参数。

##### DEBUG_BACKTRACE_IGNORE_ARGS

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        var_dump(Coroutine::getBackTrace(Coroutine::getCid(), DEBUG_BACKTRACE_IGNORE_ARGS));
    });
});
```

```shell
array(1) {
  [0]=>
  array(5) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(9)
    ["function"]=>
    string(12) "getBackTrace"
    ["class"]=>
    string(16) "Swoole\Coroutine"
    ["type"]=>
    string(2) "::"
  }
}
```

会省略传递给`getBackTrace`的参数。

#### options为null

实际上就是`DEBUG_BACKTRACE_PROVIDE_OBJECT`。例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

run(function () {
    go(function () {
        var_dump(Coroutine::getBackTrace(Coroutine::getCid()));
    });
});
```

```shell
array(1) {
  [0]=>
  array(6) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(9)
    ["function"]=>
    string(12) "getBackTrace"
    ["class"]=>
    string(16) "Swoole\Coroutine"
    ["type"]=>
    string(2) "::"
    ["args"]=>
    array(1) {
      [0]=>
      int(2)
    }
  }
}
```

可以得到传递给`getBackTrace`的参数。

### limit

此参数可用于限制返回的函数调用栈数目。默认情况下(`limit=0`)返回所有函数调用栈。

#### limit不为null

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

function task1()
{
    task2();
}

function task2()
{
    task3();
}

function task3()
{
    var_dump(Coroutine::getBackTrace(Coroutine::getCid(), DEBUG_BACKTRACE_IGNORE_ARGS, 2));
}

run(function () {
    go(function () {
        task1();
    });
});
```

```shell
array(2) {
  [0]=>
  array(5) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(19)
    ["function"]=>
    string(12) "getBackTrace"
    ["class"]=>
    string(16) "Swoole\Coroutine"
    ["type"]=>
    string(2) "::"
  }
  [1]=>
  array(3) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(14)
    ["function"]=>
    string(5) "task3"
  }
}
```

返回了两层函数调用栈。

#### limit为null

例子：

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

function task1()
{
    task2();
}

function task2()
{
    task3();
}

function task3()
{
    var_dump(Coroutine::getBackTrace(Coroutine::getCid(), DEBUG_BACKTRACE_IGNORE_ARGS));
}

run(function () {
    go(function () {
        task1();
    });
});
```

```shell
array(4) {
  [0]=>
  array(5) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(19)
    ["function"]=>
    string(12) "getBackTrace"
    ["class"]=>
    string(16) "Swoole\Coroutine"
    ["type"]=>
    string(2) "::"
  }
  [1]=>
  array(3) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(14)
    ["function"]=>
    string(5) "task3"
  }
  [2]=>
  array(3) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(9)
    ["function"]=>
    string(5) "task2"
  }
  [3]=>
  array(3) {
    ["file"]=>
    string(78) "/Users/codinghuang/codeDir/phpCode/swoole/coroutine/coroutine/getBackTrace.php"
    ["line"]=>
    int(24)
    ["function"]=>
    string(5) "task1"
  }
}
```

返回了所有的函数调用栈。
