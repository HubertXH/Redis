### RDB
RDB存储的是Redis服务器中内存中的数据的状态，即数据库中内容，存储与硬盘之上，就算断电宕机，数据也不会丢失。
在服务器载入RDB文件的时候，会一直处于阻塞状态，直到载入完成为止。Redis没有专门用于载如RDB文件的命令，RDB文件的载入是在服务器启动的时候。
##### 执行的命令：
- save: 由主线程执行，在执行期间会阻塞服务器，服务器无法向外界提供服务。
- bgsave: 由子线程执行，执行期间不阻塞服务器，服务器任然可以继续处理客户端请求。

##### 间隔自动保存：
Redis允许通过save命令来配置自动保存条件。
例如：save 20 5000 服务器再20秒内，对数据库进行了至少5000此修改（对数据的修改更新操作，而非命令的条数，若一条命令中有3次数据的修改则统计为3次），那就会自动执行一次BGSAVE。
同时可以配置多条执行条件，只要满足一条就可以执行BGSAVE命令。Redis会维持一个saveparam。数组来记录配置的条件。同时Redis也会维持dirty和lastsave两个属性来协助自动保存命令的执行。
- dirty：记录服务器距离上次执行save或者bgsave命令后对数据进行了多少次的修改操作。
- lastsave：记录服务器上一次save或者bgsave命令的执行时间。

### AOF(Append Only File)
AOF通过保存数据库执行过的命令来记录数据库的状态。
再服务器启动的时候，服务器会先判断是否开起了AOF持久化，若开启了则优先加载AOF文件，若没有则加载RDB文件。
##### Append
服务器在执行完一个写命令后，会以协议格式将被执行的写命令追加到aof_buf的缓冲区末尾。
##### WirteAndSynch
服务器在执行一次写命令后，都会调用flushAppendOnlyFile函数，考虑是否需要将aof_buf中的数据写入AOF文件中。flushAppendOnlyFile根据服务器配置的appendfsync的只来决定是否进行同步写入。
- always 将aof_buf缓冲区的所有内容写入并同步到AOF文件。
- everysec(默认配置) 将aof_buf缓冲区的所有内容写入，如果距离上次的同步AOF文件超过1秒钟，则将aof_buf缓冲区的内容同步到AOF文件中。
- no 将aof_buf缓冲去中的内容写入到AOF文件中，但是不进行同步。
##### 载入
服务器需要读入并重新执行一边AOF文件中保存的所有命令。就可以还原服务器关闭之前的数据库的状态。    
 1、服务器启动载入程序    
 2、创建伪客户端    
 3、从AOF文件中读取一条写命令    
 4、使用伪客户端执行写命令    
 5、循环3、4步骤，知道所有的命令执行为止    
 ##### AOF重写
 重新构建AOF文件，减少AOF文件的体积(因为随着命令的越来越多，AOF文件会越来越大)。
 AOF重写是对现有数据库状态的分析来进行，不会对现有的AOF文件进行分析。读取现有数据库中的键值，然后用一条命令去记录键值对。在这个期间，若数据库中的键值过期，则会直接忽略不进行记录。如果键值对带有过期时间，那么过期的时间也会被重写。
 - aof_rewrite:执行文件的重写，但是在这个期间会阻塞线程
 - bgrewriteaof:后台利用子线程对AOF文件进行重写，开启子线程就行重写，不阻塞服务器，服务器可对外提供服务。
在利用bgrewriteaof进行AOF重写的时候，服务器会维护一个AOF重写缓冲区，将在着一段时间内服务器的写命令记录进去，等AOF对现有数据库分析重建完后，将重写缓冲区内的命令也添加进重写后的AOF文件中。
 
