### Redis 缓存过期机制
过期字典表保存着数据库中所有键的过期时间。
#### 缓存过期策略：    
- 定时删除：在设置键的过期时间的同时，创建一个定时器，让定时器在键的过期时间来临时，立即执行对键的删除操作
- 惰性删除：放任键的过期时间不管，但是每次从键空间获取键的时，都检查取得的键是否过期，如果过期的话，就删除改建；如果没有过期，就返回该键。
- 定期删除：每隔一段时间，就对数据库进行一次检查，删除里面的过期键，至于要删除多少过期键，以及要检查多少个数据库，都有算法决定。
##### 定期删除
优点：内存友好，尽快的释放内存。有定时的线程在监测键的过期时间，在键马上过期的时候就会删除该键。
缺点：cpu时间不友好，影响系统的吞吐量。
##### 惰性删除
优点：cpu友好
缺点：内存不友好，若系统中有大量的过期键，且无法被访问到的话就会一直存留在内存中，占用内存空间。除非手动执行FLUSHDB。
##### 定期删除
优点：对内存及cpu都相对友好，不会占用太多cpu时间而且对内存友好。
缺点：如何确定删除的频率和删除时长。如果删除的时间持续比较长，则会占用过多的CPU时间，导致系统的吞吐量下降，如果操作执行的次数过少
那就会导致内存的浪费。    
#### Redis中的过期实现
redis使用定期删除+惰性删除来实现键的过期策略。    
在定期删除的时候，会在规定给的时间内，分多次遍历各个数据库，从数据库的过期字典表中随机检查一部分键的过期时间。每次执行的时候，
redis会有个一全局变量current_db来记录当前activie_ExpireCycle函数检查的进度，并且在下次进行检查的时候接着上次的进度进行处理。
当所有的服务器都变检查过一遍，则current_db就会重置为0，然后开始新一轮的检查。

#### 数据淘汰策略
- volatile-lru：(last recently used)从已经设置过期的数据集中挑选最近使用最少的数据进行淘汰。
- volatile-lfu：(last frequently used)从已经设置过期的数据集中，挑选使用频率最少的数据进行淘汰。
- volatile-ttl：从已经设置过期的数据集中挑选即将过期的数据进行淘汰。
- volatile-random：从已经设置过期的数据集中，随机挑选数据进行淘汰。
- allkeys-lru：从所有的数据集中挑选最近使用最少的数据进行淘汰。
- allkeys-random：从所有的数据集中随机挑选数据进行淘汰。
- no-enviction:禁止驱逐策略。
###### redisObject中都有24位的bits空间用来记录LRU/LFU的信息。
##### volatile-lru的实现：
在LRU中，redisObject的24位bits记录的时一个24位的时钟数。
在redis内部对对象的访问有个时钟来记录，每次访问一次就会将其更新,在进行lru的时候redis首先拿到全部时钟的时间，然后和当前键的时钟值进行对比，淘汰掉差距最大的键。
使用linkedHashMap来实现：
```
public class LRUCache<K, V> extends LinkedHashMap<K, V> {

    private final int MAX_CACHE_SIZE;

    public LRUCache(int maxCacheSize) {
        super((int) Math.ceil(maxCacheSize / 0.75) + 1, 0.75f, true);
        this.MAX_CACHE_SIZE = maxCacheSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > MAX_CACHE_SIZE;
    }
    
}
```
#### volatile-lfu的实现：
在LFU中24位bits分为两部分：
1、前16位记录键的访问时间。。
2、后8位记录键的访问次数(counter)。
counter中，可以随着访问次数的增加逐渐的增加，但其一直趋向于255。若在一段的时间内没有被访问到，则counter会衰减。    
其增加因子和衰减因子：
- lfu-log-factor:10 增长因子，默认值位10
- lfu-decay-time:1  衰减因子，默认值位1。

#### 如何发现热点数据：
redis 提供了命令 object freq 来获取热点数据，但时首先由注意将驱逐策略设置位 allkeys-lfu/volatile-lfu,只有在该策略下才生效。
使用scan命令遍历键，然后时用object freq 获取访问频率的排序    
在新版的redisClient中提供了  redis-cli --hotkeys就可以列出热点数据。

#### RDB、AOF对过期数据的处理
##### RDB 
- 在生成RDB文件的时候对于已经过期的键并不会写入RDB文件中去。
- 在加载RDB文件的时候，redis会对键进行检测，对于为过期的数据会加载进内存中，若键值已过期则忽略。
##### AOF
- 在生成AOF文件的时候过期的键，而没有被删除则这个键会被写入该文件，在该键被删除后会向AOF文件追加一条删除命令。来显示的记录该键已经被删除掉了。
- AOF重写 在重写AOF文件的时候 ，redis会对键进行检查，不将过期的键写入AOF.
#### 复制对过期键的处理
- 在主服务器删除掉一个过期键后，会显示的向所有的从服务器发送一条DELl命令，告知从服务器该键过期了，从服务器从而删除该键。
- 从服务器在执行客户端的请求的时候，遇到过期的键也不会删出，会像继续处理为过期键的一样来处理。
- 从服务器只有接收到主服务器发来的DELL命令后才会删除键。
