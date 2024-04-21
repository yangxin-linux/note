## mongoDB

### 1.1 简介

> MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。
>
> 在高负载的情况下，添加更多的节点，可以保证服务器性能。
>
> MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。
>
> MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组



### 1.2 安装

```bash
docker pull mongo

docker run -d --name mongo -p 27017:27017 mongo --auth

docker run -d --name mongo -v ~/docker/data/mongoDB:/data/db -p 27017:27017 mongo  # 不需要密码

# ===============================================================================
# 进入 mongoDB  使用 admin 数据库
docker exec -it mongo mongo admin

# 创建账户
db.createUser({ user:'admin',pwd:'root',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});

# 测试连接
db.auth('admin', 'root')

# 查看所在数据库
db

# 查看当前数据库所有集合
show dbs
# ===============================================================================
```



### 1.2 特点

> - MongoDB 是一个面向文档存储的数据库，操作起来比较简单和容易。
> - 你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。
> - 你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。
> - 如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。
> - Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
> - MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。
> - Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。
> - Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。
> - Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。
> - GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。
> - MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。
> - MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言



### 1.3 基本操作

```bash
show dbs  # 查看所有数据库

db # 查看当前正在使用的数据库

use test # 切换到test数据库,如果test数据库不存在,则自动创建，然后插入一些数据到，test数据库才能查看出来

show collections | show tables # 查看当前数据库下面所有的集合

db.dropDatabase()  # 删除当前数据库

db.createCollection("ips")  # 创建一个名为 ips 的集合

db.ips.insertOne({"http":"127.0.0.1:8080"}) # 往ips集合中新增一条数据,如果ips不存在，则自动创建ips集合
```



### 1.4 数据类型

>- null
>null 类型用于表示空值或不存在的字段。
>
>
>-布尔类型
>布尔类型的值可以为 true 或者 false
>
>
>- 数值类型
>shell 默认使用 64 位的浮点数来表示数值类型。因此，下面的数值在 shell 中看起来是“正常”的
>
>对于整数，可以使用 NumberInt 或 NumberLong 类，它们分别表示 4 字节和 8 字节的有符号整数。
>
>
>- 字符串类型
>任何 UTF-8 字符串都可以使用字符串类型来表示。
>
>
>- 日期类型
>MongoDB 会将日期存储为 64 位整数，表示自 Unix 纪元（1970 年 1 月 1日）以来的毫秒数，不包含时区信息
>
>
>- 正则表达式
>查询时可以使用正则表达式，语法与 JavaScript 的正则表达式语法相同。
>
>
>- 数组
>集合或者列表可以表示为数组
>
>
>- 内嵌文档
>文档可以嵌套其他文档，此时被嵌套的文档就成了父文档的值
>
>
>- ObjectId
>Object ID 是一个 12 字节的 ID，是文档的唯一标识。
>
>
>- 二进制数据
>二进制数据是任意字节的字符串，不能通过 shell 操作。如果要将非 UTF-8 字符串存入数据库，那么使用二进制数据是唯一的方法
>
>- 代码
>MongoDB 还可以在查询和文档中存储任意的 JavaScript 代码



### 1.5 CRUD

```bash
db.collectionName.insertOne()  # 新增一条数据
db.collectionName.insertMany() # 新增多条数据

db.collectionName.findOne()
db.collectionName.find()

db.collectionName.updateOne()
db.collectionName.updateMany()

db.collectionName.deleteOne()
db.collectionName.deleteMany()

#----------------------------------------

db.students.insertOne({"name":"hxh","age":"18"})

# 新增带有objectID的文档
db.students.insertOne({_id:123,"name":"hxh","age":"18"}) 

# 如果新增多条数据，objectID重复的数据无法新增,其他无影响
db.students.insertMany([{"name":"hxh","age":"18"},{"name":"mss","age":"18"}]) 

#----------------------------------------

# db.students.find(query,projection)  query查询条件  projection 可选参数，明确文档中包含的字段
# 查询students集合, {} 表示查询当亲集合所有文档, name:1 显示name字段,_id:0 不显示id字段
db.students.find({},{"name":1,_id:0})

# 查询 满足 字段 name:"hxh"  的一个文档
db.students.findOne({name:"hxh"})

# 查询 满足 字段 name:"hxh"  的多个文档
db.students.find({name:"hxh"})

#----------------------------------------

# db.student.updateOne(filter,update,options)
# fliter 过滤条件  update 重新设置的值  options :{upsert:true} 如果没匹配到则插入文档
# 修改一个满足 "name":"hxh"条件的文档,将字段age值修改成20   $set 修改字段值
db.students.updateOne({"name":"hxh"},{$set:{age:20}})

# 修改一个满足 "name":"hxh"条件的文档,将字段 name 名称修改成 myname   $rename 修改字段名
db.students.updateOne({"name":"hxh"},{$rename:{"name":"myname"}})

# 修改一个满足 "name":"hxh"条件的文档,删除age 这个字段  $unset 删除某个字段
db.students.updateOne({"name":"hxh"},{$unset:{"age":""}})

# 未匹配到就新增这条数据  upsert:true
db.students.updateOne({"name":"xjp","age":20},{$unset:{"age":""}},{upsert:true})

# 替换一条文档的数据,原文档除了 _id未被替换,其他字段全被新的文档替换
db.students.replaceOne({"name":"hxh"},{"name":"HXH"})

#----------------------------------------

# 删除匹配到的第一条数据, 
# 精确删除的话，可以匹配 _id字段
db.students.deleteOne({"name":"HXH"})

# 删除多条数据
db.students.deleteMany({"name":"HXH"})

```



### 1.6 高级查询

```bash
# 查询结果格式化
db.students.find().pretty()

#--------------------------------

<<'EOF'
$eq		等于
$gt		大于
$gte	大于等于
$lt		小于
$lte	小于等于
$ne		不等于

{filed:{$gte:value}}
EOF
# 查询 age>18 的所有文档,不显示id字段
db.students.find({"age":{"$gte":10}},{_id:0})

#--------------------------------


<<'EOF'
$in		匹配数组中任意值
$nin	不匹配数据组任意值

{filed:{$in:[v1,v2]}}
EOF
# 匹配 age 等于 [18,28] 任意一个值的文档
db.students.find({"age":{"$in":[18,28]}})

# 匹配 name 以 x,m开头的所有文档
db.students.find({name:{"$in":[/^x/,/m/]}})

# 查询包含 name 字段的所有文档
db.students.find({name:{"$in":[/./]}})
db.students.find({name:{$regex:/^m/}})

#--------------------------------

<<'EOF'
$and	与
$not	取反
$or		或

{$and:[{exp1},{exp2}]}
EOF
# 同时满足两个条件
db.students.find({$and:[{name:"mss"},{age:"18"}]})

# 满足其中一个条件
db.students.find({$or:[{name:"mss"},{age:"18"}]})

# $regex:/正则表达式/    $options:"i" 忽略大小写
db.students.find({name:{$regex:/^m/,$options:"i"}})

#--------------------------------

# 只返回2条文档数据
db.students.find().limit(2)

# 跳过3个文档数量
db.students.find().skip(3)

# 跳过3个 只取4个
db.students.find().skip(3).limit(4)

#--------------------------------
```



### 1.7 pymongo

```python
import pymongo


def main():
    client = pymongo.MongoClient(host="127.0.0.1", port=27017, username="root", password="root")

    db_test = client["test"]  # 使用test数据库

    collection_stu = db_test["students"]  # 使用test数据库中的 students 集合

    # 新增一条数据
    collection_stu.insert_one({"name": "java", "age": 20})

    # 修改一条数据
    collection_stu.update_one(
        {"name": "java"},
        {"$set": {"age": 100}}
    )

    # 查询一条数据
    result = collection_stu.find_one({
        "$and": [{"name": "java"}, {"age": {"$gte": 90}}]
    })
    print(result)

    # no_cursor_timeout 不允许游标超过10分钟未活动就关闭服务器
    list_item = collection_stu.find(no_cursor_timeout=True)
    for item in list_item:
        # item是一个游标对象,如果在遍历之前被使用,如print,则 for循环可能拿不到值
        print(item)

    # 删除一条数据
    collection_stu.delete_one({
        "name": "java"
    })

    client.close()


if __name__ == '__main__':
    main()

```



## redis

### 2.1 简介

> Redis 是完全开源的，遵守 BSD 协议，是一个高性能的 key-value 数据库。Redis 与其他 key - value 缓存产品有以下三个特点：
>
> Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
> Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
> Redis支持数据的备份，即master-slave模式的数据备份
>
> ---
>
> **Redis 优势**
> 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
> 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
> 原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
> 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。
>
> ---
>
> **Redis与其他key-value存储有什么不同？**
> Redis有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。Redis的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
>
> Redis运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，因为数据量不能大于硬件内存。在内存数据库方面的另一个优点是，相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样Redis可以做很多内部复杂性很强的事情。同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。



### 2.2 安装

```bash
docker pull redis:5.0.4-alpine3.9
# 源码下载 :  wget http://download.redis.io/releases/redis-5.0.4.tar.gz

docker run -d -p 6379:6379 --name redis01  redis:5.0.4-alpine3.9

# 创建数据卷, 开启数据持久化 --appendonly yes
docker run -d -p 6380:6379 -v redisData:/data --name redis02  redis:5.0.4-alpine3.9 redis-server --appendonly yes

# 创建密码 --requirepass "root"
docker run -d --name redis -p 6379:6379 redis:5.0.4-alpine3.9 --requirepass "root" --appendonly yes

# 自定义redis.conf文件,修改redis.conf一些变量的值,否则启动容器会失败
# ----------- 
<< EOF
protected-mode no
#bind 127.0.0.1
daemonize no
EOF
#------------
docker run -p 6379:6379 --name redis_5.0.4 -v /data/docker/redis/redis.conf:/etc/redis/redis.conf -v /data/docker/redis/data:/data -d redis:5.0.4-alpine3.9 redis-server /etc/redis/redis.conf --appendonly yes --requirepass "root"


# 登录redis客户端
docker exec -it 容器ID /bin/sh
redis-cli -h 127.0.0.1 -p 6379 -a root 
```



### 2.3 基本数据类型

> -redis 是k-v 的数据结构，每条数据对应一个键值对
> -redis 的key是字符串  value包含5种基本数据类型
>
> >    string (字符串)
> >
> > string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。
> >
> > string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 
> >
> > string类型是Redis最基本的数据类型，一个键最大能存储512MB
>
> ---
>
> > Hash（哈希）
> > Redis hash 是一个键值对集合。
> >
> > Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象
>
> ---
>
> > List（列表）
> > Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）
>
> ---
>
> > Set（集合）
> > Redis的Set是string类型的无序集合,不允许元素重复
> >
> > 集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)
>
> ---
>
> > zset(sorted set：有序集合)
> > Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
> > 不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
> >
> > zset的成员是唯一的,但分数(score)却可以重复



### 2.4 字符串CRUD

```bash
# string 相关操作,命令不区分大小写
SET key value # SET 命令用于设置给定 key 的值。如果 key 已经存储其他值， SET 就覆写旧值，且无视类型
SET key value ex 5 # 设置key的过期时间为5s
SET key value px 1000  # 设置key的过期时间为1000毫秒, px与ex不能同时设置
SET key vaule nx # 当key不存在才能设置

# Mset 命令用于同时设置一个或多个 key-value 对
MSET key1 value1 key2 value2 .. keyN valueN 

# Msetnx 命令用于所有给定 key 都不存在时，同时设置一个或多个 key-value 对，原子操作
MSETNX key1 value1 key2 value2 .. keyN valueN 

# Redis Setnx（SET if Not eXists） 命令在指定的 key 不存在时，为 key 设置指定的值
SETNX key value

# Setex 命令为指定的 key 设置值及其过期时间。如果 key 已经存在， SETEX 命令将会替换旧的值。
SETEX key timeout value  # 默认单位 秒

# 查看key的剩余过期时间
ttl key

# Redis Append 命令用于为指定的 key 追加值。
#如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾。
# 如果 key 不存在， APPEND 就简单地将给定 key 设为 value ，就像执行 SET key value 一样
APPEND KEY_NAME NEW_VALUE

#Redis Get 命令用于获取指定 key 的值。如果 key 不存在，返回 nil 。如果key 储存的值不是字符串类型，返回一个错误。
GET KEY_NAME

# Mget 命令返回所有(一个或多个)给定 key 的值。 如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 nil 
MGET KEY1 KEY2 .. KEYN

# 查看键的类型
type key_name

# 删除键
del key_name1  key_name2
```



### 2.5 列表CRUD

```bash
# Lpush 命令将一个或多个值插入到列表头部。 如果 key 不存在，一个空列表会被创建并执行 LPUSH 操作。 当 key 存在但不是列表类型时，返回一个错误
LPUSH KEY_NAME VALUE1.. VALUEN # RPUSH KEY_NAME VALUE1.. VALUEN

# Redis Lpushx 将一个或多个值插入到已存在的列表头部，列表不存在时操作无效
LPUSHX KEY_NAME VALUE1.. VALUEN

# Redis Lindex 命令用于通过索引获取列表中的元素
lindex key_name  1 # 获取第2个元素
lindex key_name  -1 # 获取倒数第r1个元素

# Redis Llen 命令用于返回列表的长度。 如果列表 key 不存在，则 key 被解释为一个空列表，返回 0 。 如果 key 不是列表类型，返回一个错误
llen key_name

# Redis Lpop 命令用于移除并返回列表的第一个元素
lpop key_name

# Redis Lset 通过索引来设置元素的值 。当索引参数超出范围，或对一个空列表进行 LSET 时，返回一个错误
lset key_name index  value

# Redis Lrange 返回列表中指定区间内的元素，区间以偏移量 START 和 END 指定。 其中 0 表示列表的第一个元素， 1 表示列表的第二个元素，以此类推。 你也可以使用负数下标，以 -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推
LRANGE KEY_NAME START END

# Blpop 命令移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
BLPOP Key_name TIMEOUT
# 实例中，操作会被阻塞，如果指定的列表 key list1 存在数据则会返回第一个元素，否则在等待100秒后会返回 nil 
BLPOP list1 100 # BLPOP list1 0  一直阻塞,直到列表添加新的值

# Redis Rpoplpush 命令用于移除列表的最后一个元素，并将该元素添加到另一个列表并返回,这个操作是具有原子性的po
RPOPLPUSH SOURCE_KEY_NAME DESTINATION_KEY_NAME
# 当目标的key_name与源key_name相同,则实现了元素旋转操作   RPOPLPUSH nums nums
# 数据旋转操作爬取数据时，存放多个cookie在一个列表, 原列表取出一个，将用过的cookie存入列表表头,达到循环使用cookie的目的
```



### 2.6 哈希CRUD

```bash
# hash类型的 value里面的类型的 key,value都是string类型的
# Hset 命令用于为哈希表中的字段赋值 。如果哈希表不存在，一个新的哈希表被创建并进行 HSET 操作。如果字段已经存在于哈希表中，旧值将被覆盖
HSET KEY_NAME FIELD1 VALUE1 FIELD1 VALUE1 # hset person name linux age 18

# Hget 命令用于返回哈希表中指定字段的值。
HGET KEY_NAME FIELD_NAME 

# Hmget 命令用于返回哈希表中，一个或多个给定字段的值。如果指定的字段不存在于哈希表，那么返回一个nil 值
HMGET KEY_NAME FIELD1...FIELDN 

# Hgetall 命令用于返回哈希表中，所有的字段和值。在返回值里，紧跟每个字段名(field name)之后是字段的值(value)，所以返回值的长度是哈希表大小的两倍
HGETALL KEY_NAME

# Hkeys 命令用于获取哈希表中的所有字段名
HKEYS KEY_NAME

# Hdel 命令用于删除哈希表 key 中的一个或多个指定字段，不存在的字段将被忽略
HDEL KEY_NAME FIELD1.. FIELDN 

# Hvals 命令返回哈希表所有字段的值
HVALS KEY_NAME FIELD VALUE 

# Hexists 命令用于查看哈希表的指定字段是否存在
HEXISTS KEY_NAME FIELD_NAME

# Hlen 命令用于获取哈希表中字段的数量
HLEN KEY_NAME
```



### 2.7 集合

```bash
# 集合元素唯一,元素类型是string

# Sadd 命令将一个或多个成员元素加入到集合中，已经存在于集合的成员元素将被忽略。假如集合 key 不存在，则创建一个只包含添加的元素作成员的集合。当集合 key 不是集合类型时，返回一个错误
SADD KEY_NAME VALUE1..VALUEN

# Scard 命令返回集合中元素的数量
SCARD KEY_NAME

# Spop 命令用于移除并返回集合中的一个随机元素,被移除的随机元素。 当集合不存在或是空集时，返回 nil
SPOP KEY

# 返回  从key1中去除key1和key2存在交集的元素 (不会删除key1的内容,只做结果返回)
SDIFF KEY1 KEY2

# 返回 两个集合的交集中的元素
SINTER KEY1 KEY2

#  Srem 命令用于移除集合中的一个或多个成员元素，不存在的成员元素会被忽略
SREM KEY MEMBER1..MEMBERN

# Smembers 命令返回集合中的所有的成员。 不存在的集合 key 被视为空集合
SMEMBERS KEY

# Srandmember 命令用于返回集合中的2个随机元素
SRANDMEMBER KEY 2

# Sismember 命令判断成员元素是否是集合的成员
SISMEMBER KEY VALUE 

# Smove 命令将指定成员 member 元素从 source 集合移动到 destination 集合。SMOVE 是原子性操作
SMOVE SOURCE DESTINATION MEMBER

# Sunion 命令返回给定集合的并集。不存在的集合 key 被视为空集
SUNION KEY KEY1..KEYN

# Sunionstore 命令将给定集合的并集存储在指定的集合 destination 中
SUNIONSTORE DESTINATION KEY KEY1..KEYN
```



### 2.8 有序集合

```bash
# Redis 有序集合和 Redis 集合类似，元素唯一,元素类型是string
# 每个有序集合的成员都关联着一个 double 类型评分(score),代表权重,这个 score 用于把有序集合中的成员按最低分到最高分排列 
# zset 的成员是唯一的，但分数（score）却可以重复。

# Redis Zadd 命令用于将一个或多个成员元素及其分数值加入到有序集当中。如果某个成员已经是有序集的成员，那么更新这个成员的分数值，并通过重新插入这个成员元素，来保证该成员在正确的位置上。分数值可以是整数值或双精度浮点数。如果有序集合 key 不存在，则创建一个空的有序集并执行 ZADD 操作
ZADD KEY_NAME SCORE1 VALUE1.. SCOREN VALUEN

# Zcard 命令用于计算集合中元素的数量
ZCARD KEY_NAME

# Zscore 命令返回有序集中，成zz员的分数值。 如果成员元素不是有序集 key 的成员，或 key 不存在，返回 nil
ZSCORE key member

# Zrange 返回有序集中，指定区间内的成员。其中成员的位置按分数值递增(从小到大)来排序
ZRANGE key start stop   # start stop 指的是元素索引,并不是分数   zrange temp 0 -1 

# 获取在指定分数区间的元素
ZRANGEBYSCORE key min max # min max 指的是分数 

#  Zrem 命令用于移除有序集中的一个或多个成员，不存在的成员将被忽略
ZREM key member

# ZREMRANGEBYSCORE key min max  移除指定分数区间的元素
ZREMRANGEBYSCORE key_name min_score max_score
```



### 2.9 key相关命令

```bash
#  DEL 命令用于删除已存在的键。不存在的 key 会被忽略
del key_name

# TTL 命令以秒为单位返回 key 的剩余过期时间
ttl key_name

# 查看数据库所有key
keys *

# 查看key类型
type key_name

# MOVE 命令用于将当前数据库的 key 移动到给定的数据库 db 当中
MOVE KEY_NAME DESTINATION_DATABASE

# 选择数据库,默认 0-15
select 14 s

# RANDOMKEY 命令从当前数据库中随机返回一个 key
RANDOMKEY

# Rename 命令用于修改 key 的名称
Rename OLD_KEY_NAME NEW_KEY_NAME

# Renamenx 命令用于在新的 key 不存在时修改 key 的名称
RENAMENX OLD_KEY_NAME NEW_KEY_NAME

# 清空当前数据库
flushdb

# 清空所有数据库
flushall

# 给任意类型的 key 设置过期时间
EXPIRE key_name 10  # 10s后过期
```



### 2.10 python-redis

```python
import redis

"""
连接 redis 4种方式：
#   the first Connection redis way
import redis
redis_conn = redis.Redis(host="localhost", port=6379, decode_responses=True)

#------------------------------------------------------------

from redis import StrictRedis
redis_conn = StrictRedis(host="localhost", port=6379, password="123456")

#------------------------------------------------------------

from redis import StrictRedis, ConnectionPool
pool = ConnectionPool(host="localhost", port=6370, db=0, password="2342")
redis = StrictRedis(connection_pool=pool)

#------------------------------------------------------------

url = 'redis://:foobared@localhost:6379/0'
pool = ConnectionPool.from_url(url)
redis = StrictRedis(connection_pool=pool)

#------------------------------------------------------------

# redis-py默认在执行每次请求都会创建（连接池申请连接）和断开（归还连接池）一次连接操作，
# 如果想要在一次请求中指定多个命令，则可以使用pipline实现一次请求指定多个命令，
# 并且默认情况下一次pipline 是原子性操作
import redis
 
pool = redis.ConnectionPool(host='192.168.22.132', port=6379)
r = redis.Redis(connection_pool=pool)
 
pipe = r.pipeline(transaction=True)
pipe.set('name', 'root')
pipe.set('role', 'root')
pipe.execute()

"""


def main():
    conn = redis.Redis(host="localhost", port=6379, decode_responses=True)
    result = conn.lpush("names", "hxh", "mss")
    print(result)

    l = conn.lrange("names", 0, -1)
    print(l)
    conn.close()


if __name__ == '__main__':
    main()
```







---

## 附录

### redis 主从复制配置

```bash
# redis-master  ip:port -> 192.168.0.254 
# 主服务器的 bind 需要修改成 从服务器可以访问的或者修改成所有人主机都可以访问
# bind 127.0.0.1
bind 192.168.0.253 # 从服务器ip

# 修改 redis-slave 从服务器的配置文件  redis.conf
#---------------
# 如果主从服务器在同一个docker宿主机,且处于同一个虚拟网络 ,那么可以直接访问docker构建的容器(redis主服务器)所在的虚拟地址,如 replicaof 172.17.0.2 6379
replicaof 192.168.0.254 6379  
# 如果主服务器有密码则配置主服务器的认证密码
masterauth root
#---------------

# 配置完成后,主服务器可写可读,从服务器只允许读取数据
```



