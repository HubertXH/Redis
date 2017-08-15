## SADD
sadd key member\[member ... \]
添加一个或者多个指定的元素到key集合中,如果member已经在集合key中则忽略不添加，若key不存在则新建集合key并添加member;
返回成功添加的元素的个数
#### 事例：
sadd myset one 将one元素添加进myset的集合之中去。
## SCARD
scard key
获取集合key中的元素个数
返回集合中元素的个数
#### 事例：
scard myset
## SDIFF
sdiff key \[key1 ...\]
返回出现在key集合中，没有出现在key1,key2等集合并集中的元素。也就是key集合中有，而key1,key2等等集合中没有的元素。
#### 事例： myset = \[1,2,3,4\] set1 = \[3,4,6,7\] set2 = \[1,4,6\]
sdiff myset set1 set2 = 2
## SIDFFSTORE
sdiffstore destination key \[key1,key2 ....\]
语义与sdiff 相同，不过sdiffstore将返回的元素存放在集合destination中，若destination已经存在则覆盖。
## SINTER
sinter key \[key1,key2 ,...\]
返回指定集合的交集
#### 事例: myset = \[1,2,3,4\] set1 = \[3,4,6,7\] set2 = \[1,3,4,6\]
sinter myset set1 set2 = 3
## SINTERSTORE
sinterstore destination key \[key1,key2, ...\]
予以同sinter，不过sinterstore将返回的元素存放在集合destination中，若destination已经存在则覆盖。
## SISMEMBER
SISMEMBER key member
判断集合member是否是集合key的成员，若member是key的成员则返回1,若不是则返回0,若key不存在也返回0
## SMEMBERS
SMEMBERS key
获取存储在key集合中的所有成员（元素）
## SMOVE
SMOVE source destination member
将souce集合中的member元素剪贴到destination中，如果集合source不存在或者source集合中没有member元素则不进行任何操作并且返回0，如果在destination集合中已经存在member则只从source集合中移除member元素，不向destination集合中添加。
#### 事例 : myset = \[1,2,3,4\] set1 = \[1,3,5,6\]
smove myset set1 2;  myset = \[1,3,4\] set1 = \[1,2,3,5,6\]
## SPOP
SPOP key \[count\]
从集合key中移除并返回一个或者随机多个元素
可选参数count在3.2以后的版本可用
## SRANDMEMBER
SRANDMEMBER key \[count\]
从集合key中获取随机个数的元素，获取元素并不将元素从集合key中移除。参数count，当参数count为正整数时，返回一个大小为count的数组，若count的值大于set集合的大小则返回集合的所有元素;当count的值为负数时，返回一个大小为count绝对值的数组，数组中的元素可以重复。
#### 事例：myset = \[1,4,5,6\]
srandmember myset 3 = 5 1 6;
srandmember myset 6 = 1 4 5 6;
srandmember myset -2 = 4 4;
sandmember myset -6 = 5 1 1 6 1 4;
## SREM
SREM key member \[member ...\]
从集合key中移除元素member，若元素不存则忽略，若集合为不存在则当作空集合来处理并且返回0;
返回值为从集合key中所移除的元素的个数;
## SUNION
SUNION key \[key ...\]
获取集合key...等集合的并集，返回的集合中无重复数据
#### 事例：myset = \[1,4,5,6\] set1 = \[1,2,3\]
sunion myset set1 = 1,2,3,4,5,6;
## SUNIONSTORE
SUNIONSTORE destination key \[key ...\]
语义同sunion，sunionstore将返回值存放于destination集合中，若集合已经存在这覆盖原集合数据









