## ZADD
ZADD key [NX|XX] [CH] [INCR] score member [score member ...]
* 将指定的元素添加到有序集合中，添加的元素为score/member 分值对，score为String 类型双精度浮点数，用于member排序比较。使用score进行升序排序
* 若集合中member已经存在则更新其score，并将member重新插入到合适的位置。
* 若集合不存在则创建新的有序集合
* 当不同元素用于相同score时先按score进行升序排序，后按字典顺序进行排序
* 返回值为成功添加之集合中元素的数量
### 参数：
* XX：只更新已经存在的元素，不添加任何新元素
* NX：只添加新元素，不更新任何已经存在的元素
* CH：修改返回的结果为-集合中更新的元素数量
* INCR：添加该参数后其语义同ZINCRBY，请参考ZINCRBY
#### 事例：
zadd myset 1 "one"
## ZCARD 
ZCARD key
返回存储在key中集合的元素总数
zcard myset
## ZCOUNT
ZCOUNT key min max
返回存储于key中集合中元素的score处于min和max之间的元素个数。即返回在集合中min<score<max的元素个数
#### 事例：
zcount myset 1 3;
## ZINCRBY
ZINCRBY key increment member
* 将集合中元素member的score增加increment
* 若元素member不存在按照increment member将元素添加进集合中
* 若集合不存在则创建新集合
* 返回member的新的score
## ZINTERSTORE
ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]
计算给定数量（numkeys）集合（key\[key ...\]）的交集,并将结果存储与destination集合中，numkeys必须在key集合给定之前给予
默认情况下，交集中每个元素的score为有个key集合中该元素的总和。若过destination已经存在则覆盖。
关于WEIGHTS和AGGREGATE参数请参考ZUNIONSTORE
#### 事例 myset = \[1-one,2-two,3-three\]  zset1 = \[1-one,2-two,3-three,4-four\]
zinterstore out 2 myset zset1 = 2-one,4-two,6-three
## ZLEXCOUNT
ZLEXCOUNT key min max
计算有序集合中指定成员之间的成员数量,即计算位于min和max之间的成员数量
可以使用 - 和 + 表示得分最小值和最大值 
#### 事例： myzset = 0-a 0-b 0-c 0-d 0-e
zlexcount myzset - + = 5
zlexcount myzset [b [d = 3
## ZRANGE
ZRANGE key start stop [WITHSCORES]
返回下标start stop之内的集合元素，默认的为升序排序，若想使用降序排序请使用ZREVRABGE
下标的起始值为0;若使用负数则表示从尾部开始，-1代表最后一位
如果起始值大于终止值则返回一个空列表，如果stop大于集合的大小则，Redis会认为是取最大值
参数：WITHSCORES 在返回的时候添加元素的score
#### 事例：myset = \[1-one,2-two,3-three\]
zrange myset 1 2 withscores = 2-two,3-three






