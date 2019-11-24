# Coroutine::create

`Coroutine::create`用来创建一个协程，并且立马执行这个协程。

## 方法原型

```php
<?php
public static function create(
    callable $func,
    ...$params
) { }
@param callable $func

@param mixed ...$params

@return mixed
```

这个方法有一个别名，`go`：

```php
<?php
function go(
    $func,
    $params = null
) { }
@param callable $func

@param mixed ...$params

@return mixed
```

## 参数

### callable $func

代表一个`PHP`函数或者方法。

#### 普通函数

```php
<?php

function task() {
    echo 'swoole' . PHP_EOL;
}

go('task');
```

```shell
swoole
```

#### 匿名函数

```php
<?php

go(function () {
    echo 'swoole' . PHP_EOL;
});
```

```shell
swoole
```

#### 非静态方法

```php
<?php

class Codinghuang
{
    public function task()
    {
        echo 'swoole' . PHP_EOL;
    }
}

$obj = new Codinghuang;
go([$obj, 'task']);
```

```shell
swoole
```

#### 静态方法

```php
<?php

class Codinghuang
{
    public static function task()
    {
        echo 'swoole' . PHP_EOL;
    }
}

go([Codinghuang::class, 'task']);
```

```shell
swoole
```

#### 在类的内部调用非静态方法

```php
<?php

class Codinghuang
{
    public function test()
    {
        go([$this, 'task']);
    }

    public function task()
    {
        echo 'swoole' . PHP_EOL;
    }
}

$obj = new Codinghuang;
$obj->test();
```

```shell
swoole
```

#### 在类的内部调用静态方法

```php
<?php

class Codinghuang
{
    public function test()
    {
        go([self::class, 'task']);
    }

    public static function task()
    {
        echo 'swoole' . PHP_EOL;
    }
}

$obj = new Codinghuang;
$obj->test();
```

```shell
swoole
```

### mixed ...$params

传递给协程任务函数的参数，参数个数是可变的。

#### 给普通函数传递参数

```php
<?php

function task($param1, $param2)
{
    var_dump($param1);
    var_dump($param2);
}

go('task', 1, 'swoole');
```

```shell
int(1)
string(6) "swoole"
```

#### 给匿名函数传递参数

```php
<?php

go(function ($param1, $param2) {
    var_dump($param1);
    var_dump($param2);
}, 1, 'swoole');
```

```shell
int(1)
string(6) "swoole"
```

#### 使用闭包

```php
<?php

$param1 = 1;
$param2 = 'swoole';

go(function () use ($param1, $param2) {
    var_dump($param1);
    var_dump($param2);
});
```

```shell
int(1)
string(6) "swoole"
```
