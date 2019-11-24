# Coroutine::defer

`Coroutine::defer`用来在协程关闭之前(即协程函数执行完毕时)调用被`defer`注册的代码。

> 注意，因为`defer`一般用于资源的释放，所以它的调用顺序是先注册defer的后执行。逆序符合资源释放的正确逻辑, 后申请的资源可能是基于先申请的资源的, 如先释放先申请的资源, 后申请的资源可能就难以释放。另外需要注意的是，在协程内抛出了异常，已注册的defer也会被执行。

## 方法原型

```php
Swoole\Coroutine::defer

<?php
public static function defer($callback) { }
@param callable $func

@return mixed
```

这个方法有一个别名，`defer`：

```php
defer

<?php
function defer($callback) { }
@param callable $func

@return mixed
```

## 参数

### callable $func

代表一个`PHP`函数或者方法。

#### 普通函数

```php
<?php

function task1()
{
    echo 'task1' . PHP_EOL;
}

function task2()
{
    echo 'task2' . PHP_EOL;
}

Swoole\Coroutine\run(function () {
    go(function () {
        echo '1' . PHP_EOL;
        Swoole\Coroutine::defer('task1');
        Swoole\Coroutine::defer('task2');
        echo '2' . PHP_EOL;
    });
});
```

```shell
1
2
task2
task1
```

#### 匿名函数

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        echo '1' . PHP_EOL;
        Swoole\Coroutine::defer(function () {
            echo 'task1' . PHP_EOL;
        });
        Swoole\Coroutine::defer(function () {
            echo 'task2' . PHP_EOL;
        });
        echo '2' . PHP_EOL;
    });
});
```

```shell
1
2
task2
task1
```

#### 非静态方法

```php
<?php

class Codinghuang
{
    public function task1()
    {
        echo 'task1' . PHP_EOL;
    }

    public function task2()
    {
        echo 'task2' . PHP_EOL;
    }
}

Swoole\Coroutine\run(function () {
    go(function () {
        echo '1' . PHP_EOL;
        $obj = new Codinghuang;
        Swoole\Coroutine::defer([$obj, 'task1']);
        Swoole\Coroutine::defer([$obj, 'task2']);
        echo '2' . PHP_EOL;
    });
});
```

```shell
1
2
task2
task1
```

#### 静态方法

```php
<?php

class Codinghuang
{
    public static function task1()
    {
        echo 'task1' . PHP_EOL;
    }

    public static function task2()
    {
        echo 'task2' . PHP_EOL;
    }
}

Swoole\Coroutine\run(function () {
    go(function () {
        echo '1' . PHP_EOL;
        Swoole\Coroutine::defer([Codinghuang::class, 'task1']);
        Swoole\Coroutine::defer([Codinghuang::class, 'task2']);
        echo '2' . PHP_EOL;
    });
});
```

```shell
1
2
task2
task1
```

#### 在类的内部调用非静态方法

```php
<?php

class Codinghuang
{
    public function run ()
    {
        Swoole\Coroutine\run(function () {
            go(function () {
                echo '1' . PHP_EOL;
                Swoole\Coroutine::defer([$this, 'task1']);
                Swoole\Coroutine::defer([$this, 'task2']);
                echo '2' . PHP_EOL;
            });
        });
    }

    public function task1()
    {
        echo 'task1' . PHP_EOL;
    }

    public function task2()
    {
        echo 'task2' . PHP_EOL;
    }
}

$obj = new Codinghuang;
$obj->run();
```

```shell
1
2
task2
task1
```

#### 在类的内部调用静态方法

```php
<?php

class Codinghuang
{
    public function run ()
    {
        Swoole\Coroutine\run(function () {
            go(function () {
                echo '1' . PHP_EOL;
                Swoole\Coroutine::defer([self::class, 'task1']);
                Swoole\Coroutine::defer([self::class, 'task2']);
                echo '2' . PHP_EOL;
            });
        });
    }

    public static function task1()
    {
        echo 'task1' . PHP_EOL;
    }

    public static function task2()
    {
        echo 'task2' . PHP_EOL;
    }
}

$obj = new Codinghuang;
$obj->run();
```

```shell
1
2
task2
task1
```

## 使用协程的返回值作为参数

```php
<?php

Swoole\Coroutine\run(function () {
    go(function () {
        echo '1' . PHP_EOL;
        Swoole\Coroutine::defer(function ($param) {
            var_dump($param);
        });
        echo '2' . PHP_EOL;
        return "swoole";
    });
});
```

```shell
1
2
string(6) "swoole"
```
