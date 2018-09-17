# Key

## DEL

**DEL key [key ...]**

删除给定的一个或多个 `key` 。

不存在的 `key` 会被忽略。

- **可用版本：**

  >= 1.0.0

- **时间复杂度：**

  O(N)， `N` 为被删除的 `key` 的数量。删除单个字符串类型的 `key` ，时间复杂度为O(1)。删除单个列表、集合、有序集合或哈希表类型的 `key` ，时间复杂度为O(M)， `M` 为以上数据结构内的元素数量。

- **返回值：**

  被删除 `key` 的数量。

```clike
#  删除单个 key

redis> SET name huangz
OK

redis> DEL name
(integer) 1


# 删除一个不存在的 key

redis> EXISTS phone
(integer) 0

redis> DEL phone # 失败，没有 key 被删除
(integer) 0


# 同时删除多个 key

redis> SET name "redis"
OK

redis> SET type "key-value store"
OK

redis> SET website "redis.com"
OK

redis> DEL name type website
(integer) 3
```

## DUMP

**DUMP key**

序列化给定 `key` ，并返回被序列化的值，使用 [*RESTORE*](http://redisdoc.com/key/restore.html) 命令可以将这个值反序列化为 Redis 键。

序列化生成的值有以下几个特点：

- 它带有 64 位的校验和，用于检测错误， [*RESTORE*](http://redisdoc.com/key/restore.html) 在进行反序列化之前会先检查校验和。
- 值的编码格式和 RDB 文件保持一致。
- RDB 版本会被编码在序列化值当中，如果因为 Redis 的版本不同造成 RDB 格式不兼容，那么 Redis 会拒绝对这个值进行反序列化操作。

序列化的值不包括任何生存时间信息。

- **可用版本：**

  >= 2.6.0

- **时间复杂度：**

  查找给定键的复杂度为 O(1) ，对键进行序列化的复杂度为 O(N*M) ，其中 N 是构成 `key` 的 Redis 对象的数量，而 M 则是这些对象的平均大小。如果序列化的对象是比较小的字符串，那么复杂度为 O(1) 。

- **返回值：**

  如果 `key` 不存在，那么返回 `nil` 。否则，返回序列化之后的值。

```
redis> SET greeting "hello, dumping world!"
OK

redis> DUMP greeting
"\x00\x15hello, dumping world!\x06\x00E\xa0Z\x82\xd8r\xc1\xde"

redis> DUMP not-exists-key
(nil)
```

## EXISTS

**EXISTS key**

检查给定 `key` 是否存在。

- **可用版本：**

  >= 1.0.0

- **时间复杂度：**

  O(1)

- **返回值：**

  若 `key` 存在，返回 `1` ，否则返回 `0` 。

```
redis> SET db "redis"
OK

redis> EXISTS db
(integer) 1

redis> DEL db
(integer) 1

redis> EXISTS db
(integer) 0
```