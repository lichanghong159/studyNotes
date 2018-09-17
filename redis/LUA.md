# EVAL script numkeys key [key ...] arg [arg ...]

## EVAL简介

[EVAL](https://redis.io/commands/eval)和[EVALSHA](https://redis.io/commands/evalsha)用于使用从2.6.0版开始内置于Redis中的Lua解释器来评估脚本。

[EVAL](https://redis.io/commands/eval)的第一个参数是Lua 5.1脚本。该脚本不需要定义Lua函数（也不应该）。它只是一个将在Redis服务器上下文中运行的Lua程序。

[EVAL](https://redis.io/commands/eval)的第二个参数是脚本后面的参数数量（从第三个参数开始），表示Redis键名称。参数可以通过Lua中使用来访问`KEYS`全局变量在基于一个阵列（这样的形式`KEYS[1]`，`KEYS[2]`...）。

所有其他参数不应该代表键名称，可以通过Lua的使用来访问`ARGV`全局变量，非常相似与什么发生了键（所以`ARGV[1]`，`ARGV[2]`...）。

以下示例应阐明上述内容：

```
> eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
1) "key1"
2) "key2"
3) "first"
4) "second"
```

可以使用两个不同的Lua函数从Lua脚本调用Redis命令：

- `redis.call()`
- `redis.pcall()`

`redis.call()`类似于`redis.pcall()`，唯一的区别是，如果Redis命令调用将导致错误，`redis.call()`将引发Lua错误，反过来将强制[EVAL](https://redis.io/commands/eval)向命令调用者返回错误，同时`redis.pcall`将陷阱错误并返回Lua表代表错误。

## Lua和Redis数据类型之间的转换



**Redis to Lua** conversion table.

- Redis integer -> Lua number
- Redis bulk -> Lua string
- Redis multi bulk -> Lua table (may have other Redis data types nested)
- Redis status -> Lua table with a single `ok` field containing the status
- Redis error -> Lua table with a single `err` field containing the error
- Redis Nil bulk  and Nil multi bulk reply -> Lua false boolean type

**Lua to Redis** conversion table.

- Lua number -> Redis integer (the number is converted into an integer)
- Lua string -> Redis bulk 
- Lua table (array) -> Redis multi bulk (truncated to the first nil inside the Lua array if any)
- Lua table with a single `ok` field -> Redis status 
- Lua table with a single `err` field -> Redis error 
- Lua boolean false -> Redis Nil bulk .

- Lua boolean true - > Redis integer ，值为1。

还有两个重要的规则需要注意：

- Lua有一个数字类型，Lua数字。整数和浮点数之间没有区别。因此，我们总是将Lua数转换为整数，如果有的话，删除数字的小数部分。**如果你想从Lua返回一个浮点数，你应该将它作为一个字符串返回**，就像Redis本身一样（参见例如[ZSCORE](https://redis.io/commands/zscore)命令）。
- 有[没有简单的方法有lua阵内尼尔斯](http://www.lua.org/pil/19.1.html)，这是的Lua表语义的结果，所以当Redis的一个Lua阵列转换成Redis的协议如果遇到零的转换停止。

```
> eval "return 10" 0
(integer) 10

> eval "return {1,2,{3,'Hello World!'}}" 0
1) (integer) 1
2) (integer) 2
3) 1) (integer) 3
   2) "Hello World!"

> eval "return redis.call('get','foo')" 0
"bar"
```

## 脚本的原子性

Redis使用相同的Lua解释器来运行所有命令。Redis还保证脚本以原子方式执行：在执行脚本时不会执行其他脚本或Redis命令。这种语义类似于[MULTI](https://redis.io/commands/multi) / [EXEC](https://redis.io/commands/exec)。从所有其他客户端的角度来看，脚本的效果仍然不可见或已经完成。

## EVALSHA

[EVAL](https://redis.io/commands/eval)命令迫使你一次又一次地发送脚本主体。Redis不需要每次重新编译脚本，因为它使用内部缓存机制，但是在许多情况下支付额外带宽的成本可能不是最佳的。

另一方面，使用特殊命令或通过定义命令`redis.conf` 将是一个问题，原因如下：

- 不同的实例可能具有不同的命令实现。
- 如果我们必须确保所有实例都包含给定命令，特别是在分布式环境中，则部署很难。
- 读取应用程序代码时，由于应用程序调用服务器端定义的命令，因此完整的语义可能不明确。

为了在避免带宽损失的同时避免这些问题，Redis实现了[EVALSHA](https://redis.io/commands/evalsha)命令。

[EVALSHA的](https://redis.io/commands/evalsha)工作方式与[EVAL](https://redis.io/commands/eval)完全相同，但不是将脚本作为第一个参数，而是具有脚本的SHA1摘要。行为如下：

- 如果服务器仍记得具有匹配的SHA1摘要的脚本，则执行该脚本。
- 如果服务器不记得带有此SHA1摘要的脚本，则会返回一个特殊错误，告知客户端使用[EVAL](https://redis.io/commands/eval)。

```
> set foo bar
OK
> eval "return redis.call('get','foo')" 0
"bar"
> evalsha 6b1bf486c81ceb7edf3c093f4c48582e38c0e791 0
"bar"
> evalsha ffffffffffffffffffffffffffffffffffffffff 0
(error) `NOSCRIPT` No matching script. Please use [EVAL](/commands/eval).
```

## 脚本缓存语义

保证执行的脚本永远位于Redis实例的给定执行的脚本缓存中。这意味着如果针对Redis实例执行[EVAL，](https://redis.io/commands/eval)则所有后续[EVALSHA](https://redis.io/commands/evalsha)调用都将成功。

刷新脚本缓存的唯一方法是显式调用[SCRIPT FLUSH](https://redis.io/commands/script-flush)命令，该命令将*完全刷新*脚本缓存，删除到目前为止执行的所有脚本。

## SCRIPT命令

Redis提供了一个SCRIPT命令，可用于控制脚本子系统。SCRIPT目前接受三种不同的命令：



* [SCRIPT FLUSH](https://redis.io/commands/script-flush)

  强制redis刷新脚本缓存的唯一方法。在云环境中最有用，开可以将同一实例重新分配给其他用户

* SCRIPT EXISTS sha1 sha2 ... shaN

  检查脚本是否存在缓存中。1：脚本缓存中已经存在该脚本、

* SCRIPT LOAD script

  在Redis脚本缓存中注册指定脚本。

* [SCRIPT KILL](https://redis.io/commands/script-kill)

  中断长时间运行脚本的唯一方法。

## 脚本作为纯函数

*注意：从Redis 5开始，脚本始终作为效果复制，而不是逐字发送脚本。因此，以下部分主要适用于Redis版本4或更早版本。*

