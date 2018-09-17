# 使用Redis作为LRU缓存

LRU(Least Recently Used)最近最少使用:[内存管理](https://baike.baidu.com/item/%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86/5633616)的一种页面置换算法，对于在内存中但又不用的[数据块](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E5%9D%97/107672)（内存块）叫做LRU

当Redis用作缓存时，通常可以让它在添加新数据时自动逐出旧数据。

LRU实际上只是支持的驱逐方法之一。

从Redis 4.0版开始，引入了新的LFU（最少使用）驱逐策略。

## Maxmemory配置指令

以便用于Redis的配置为使用的存储器的指定量的数据集。可以使用该`redis.conf`文件设置配置指令，或稍后在运行时使用[CONFIG SET](https://redis.io/commands/config-set)命令。

例如，为了配置100兆字节的内存限制，可以在`redis.conf`文件中使用以下指令。

```
maxmemory 100mb
```

设置`maxmemory`为零会导致无内存限制。在64位系统的默认行为，在32位系统下默认限制为3GB。

达到指定内存后，可以选择不同的处理方式。这称之为`策略`



## 驱逐政策

`maxmemory`使用`maxmemory-policy`配置指令配置达到限制时Redis遵循的确切行为。

* **noeviction**: 当达到内存限制时，客户端执行可能导致内存增多的命令时返回错误(大多数写命令，但[DEL](https://redis.io/commands/del)和一些例外)。
* **allkeys-lru** : 删除最近最少使用(LRU)的key
* **volatile-lru**:在**设置过期时间(expire set)**的key中删除最近使用较少的（LRU）key
* **allkeys-random**:随机删除key
* **volatile-random**:在**设置过期时间(expire set)**的key中随机删除
* **volatile-ttl**:在**设置过期时间(expire set)**，尝试清楚`生存时间较短(TTL)`的key

### 经验法则

* 当您期望在请求的流行度中使用幂律分布时，使用`allkeys-lru`策略，也就是说，您希望访问元素的子集的频率远远高于其他元素。
* 如果有连续扫描所有key的循环访问，或者希望分布均匀。使用**allkeys-random**
* 在创建缓存对象时通过使用不同的TTL值向Redis提供有关到期的最佳候选者的提示，请使用**volatile-ttl**。

## 驱逐程序如何运作

- 客户端运行新命令，导致添加更多数据。
- Redis检查内存使用情况，如果它大于`maxmemory`限制，它会根据策略驱逐密钥。
- 执行新命令，依此类推。

因此，我们不断跨越内存限制的边界，通过检查，然后通过驱逐密钥返回限制之下。

## 新的LFU模式

从Redis 4.0开始，可以使用新的[最少使用的逐出模式](http://antirez.com/news/109)。

要配置LFU模式，可以使用以下策略：

- `volatile-lfu` 使用过期集在密钥中使用近似LFU进行驱逐。
- `allkeys-lfu` 使用近似LFU逐出任何键。

