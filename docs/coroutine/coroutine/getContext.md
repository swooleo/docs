# Coroutine::getContext

获取当前协程的上下文对象。

## 方法原型

```php
Swoole\Coroutine::getContext

<?php
public static function getContext($cid = null) { }
@param long|null $cid

@return long
```

## 参数

### long|null $cid

某个协程的`cid`或者`null`。

如果为`null`，代表获取当前协程的`pcid`。

```php
<?php

use Swoole\Coroutine;

use function Swoole\Coroutine\run;

function func(callable $fn, ...$args)
{
    go(function () use ($fn, $args) {
        $fn(...$args);
        echo 'Coroutine#' . Coroutine::getCid() . ' exit' . PHP_EOL;
    });
}

/**
 * Compatibility for lower version
 * @param object|Resource $object
 * @return int
 */
function php_object_id($object)
{
    static $id = 0;
    static $map = [];
    $hash = spl_object_hash($object);
    return $map[$hash] ?? ($map[$hash] = ++$id);
}

class Resource
{
    public function __construct()
    {
        echo __CLASS__ . '#' . php_object_id((object)$this) . ' constructed' . PHP_EOL;
    }

    public function __destruct()
    {
        echo __CLASS__ . '#' . php_object_id((object)$this) . ' destructed' . PHP_EOL;
    }
}

run(function () {
    $context = new Coroutine\Context();
    assert($context instanceof ArrayObject);
    assert(Coroutine::getContext() === null);
    func(function () {
        $context = Coroutine::getContext();
        assert($context instanceof Co\Context);
        $context['resource1'] = new Resource;
        $context->resource2 = new Resource;
        func(function () {
            Coroutine::getContext()['resource3'] = new Resource;
            Coroutine::yield();
            Coroutine::getContext()['resource3']->resource4 = new Resource;
            Coroutine::getContext()->resource5 = new Resource;
        });
    });
    Coroutine::resume(3);
});
```

```shell
Resource#1 constructed
Resource#2 constructed
Resource#3 constructed
Coroutine#2 exit
Resource#2 destructed
Resource#1 destructed
Resource#4 constructed
Resource#5 constructed
Coroutine#3 exit
Resource#5 destructed
Resource#3 destructed
Resource#4 destructed
```
