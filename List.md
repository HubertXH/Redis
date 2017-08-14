# 阻塞队列
## BLPOP
*BlockLeftPop;*
一阻塞的形式从队列的头部取出地一个元素，若队列为空则，阻塞当前链接。
若给定多个参数key，则按照参数key给定的顺序一次检查各个列表，返回地一个非空列表的头元素。返回的数据为列表的key值和头元素的值
若所有给定的key中都不存在或者都为空，阻塞当前链接，直到另一个链接给列表lpush或者rpush进一个值或者等带超时之后返回*nil和等待时间*
当多个客户端同时被一个key所阻塞时，当key中有可用元素时，最先被处理的是等待时间最长的链接。BLPOP的阻塞处理是以key为单位来进行的，
使用语法：
blpop key\[key...\] timeout
#### 例如：
取出队列list1的头元素,超时时间为30秒：
blpop list1 30;
取出队列list1,list0,list2的头元素，超时时间不限，其中lsit0为空，list1 list2为非空列表：
blpop list0 list1 list2 
返回的数据为list1中的头元素。
## BRPOP
与BLPOP语义相同，BLPOP取出的为读列的头元素，而BRPOP取出的为队列的尾部元素。
## BRPOPLPUSH
语法：brpoplpush source destination timeout;
从source队列的尾部取出最后一个元素返回给调用者，并将其放入的destination读列的头元素位置上;在超时发生的时候会返回nil
消费者端取到消息的同时把该消息放入一个正在处理中的列表。 当消息被处理了之后，该命令会使用 LREM 命令来移除正在处理中列表中的对应消息。
另外，可以添加一个客户端来监控这个正在处理中列表，如果有某些消息已经在这个列表中存在很长时间了（即超过一定的处理时限）， 那么这个客户端会把这些超时消息重新加入到队列中。
若source和destination相同的话可以很好的逐渐循环该列表。
#### 例子：
brpoplpush list1 list2 20;
## LINDEX 
返回key列表中index下标的元素，时间复杂度为O(N)
下标的起始值为0;
当下标为负数的时候则表示从尾部开始取值。
#### 例子：
lindex list 2;取list列表中第三个元素。
## LINSERT
linsert key before|after point value;
将value的值插入到key列表point元素之前或之后;对于空的list不会发生任何操作。
返回值为插入之后的list的长度，若找不到piont则返回-1
#### 例子：
linsert list before word holler;
## LLEN  
llen key  获取key列表的长度，若key不存在则是为空返回0
## LPOP
lpop key 从队列头部头元素并从队列中移除该元素
## LPUSH
lpush list value\[value ... \] 向队列list中插入元素
## LRANGE 
lrange list start end 返回队列list中从start开始到end结束的下标值
## LAEM
laem list count value 移除读列list中前count个值为value的元素
若count大于0则从头开始向后找，若count小于0则从尾部开始向头部找的count个元素，若当count=0的时则移除队列全部该元素
## LSET
lset list index value 将队列list下标为index的元素设置为value
## LTRIM
ltrim list start stop 截取列表 从start开始到stop结束
## RPOP
## RPUSH
## RPUSHX
## RPOPLPUSH









