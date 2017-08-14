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


