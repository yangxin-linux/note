### 1.概述（V6.8.0）

> Elasticsearch是一个基于[Lucene](https://baike.baidu.com/item/Lucene/6753302)的搜索服务器。它提供了一个分布式多用户能力的[全文搜索引擎](https://baike.baidu.com/item/全文搜索引擎/7847410)，基于RESTful web接口。Elasticsearch是用Java语言开发的，并作为Apache许可条款下的开放源码发布，是一种流行的企业级搜索引擎。Elasticsearch用于[云计算](https://baike.baidu.com/item/云计算/9969353)中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。官方客户端在Java、.NET（C#）、PHP、Python、Apache Groovy、Ruby和许多其他语言中都是可用的。根据DB-Engines的排名显示，Elasticsearch是最受欢迎的企业搜索引擎，其次是Apache Solr，也是基于Lucene
>
> ---
>
> Elasticsearch 是一个分布式、高扩展、高实时的搜索与[数据分析](https://baike.baidu.com/item/数据分析/6577123)引擎。它能很方便的使大量数据具有搜索、分析和探索的能力。充分利用Elasticsearch的水平伸缩性，能使数据在生产环境变得更有价值。Elasticsearch 的实现原理主要分为以下几个步骤，首先用户将数据提交到Elasticsearch 数据库中，再通过分词控制器去将对应的语句分词，将其权重和分词结果一并存入数据，当用户搜索数据时候，再根据权重将结果排名，打分，再将返回结果呈现给用户。
>
> Elasticsearch是与名为Logstash的数据收集和日志解析引擎以及名为Kibana的分析和可视化平台一起开发的。这三个产品被设计成一个集成解决方案，称为“Elastic Stack”（以前称为“ELK stack”）。
>
> Elasticsearch可以用于搜索各种文档。它提供可扩展的搜索，具有接近实时的搜索，并支持多租户。Elasticsearch是分布式的，这意味着索引可以被分成分片，每个分片可以有0个或多个副本。每个节点托管一个或多个分片，并充当协调器将操作委托给正确的分片。再平衡和路由是自动完成的。相关数据通常存储在同一个索引中，该索引由一个或多个主分片和零个或多个复制分片组成。一旦创建了索引，就不能更改主分片的数量。
>
> Elasticsearch使用Lucene，并试图通过JSON和Java API提供其所有特性。它支持facetting和percolating，如果新文档与注册查询匹配，这对于通知非常有用。另一个特性称为“网关”，处理索引的长期持久性；例如，在服务器崩溃的情况下，可以从网关恢复索引。Elasticsearch支持实时GET请求，适合作为NoSQL数据存储，但缺少[分布式](https://baike.baidu.com/item/分布式/7316617)事务。

### 2.ES安装

#### 1.安装es

```linux
1.docker pull elasticsearch:6.8.0
sysctl -w vm.max_map_count=262144
sysctl -a|grep vm.max_map_count
docker run --name elasticsearch -p 9200:9200 -p 9300:9300 -e "ES_JAVA_OPTS=-Xms256m -Xmx256m" -d elasticsearch:6.8.0

docker exec -it elasticsearch /bin/bash

# 进入容器，更改文件elasticsearch.ym，并安装ik分词器 在elasticsearch.ym最后面添加
http.cors.enabled: true
http.cors.allow-origin: "*"

cd /usr/share/elasticsearch/plugins/
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.8.0/elasticsearch-analysis-ik-6.8.0.zip

# 查看安装插件列表
# 或者通过API 查看 kibana   GET /_cat/plugins
cd /bin
elasticsearch-plugin liste

# 退出容器 重启服务
exit
docker restart elasticsearch
访问 ip:9200
```

#### 2.安装kibana

```linux
1.docker pull kibana:6.8.0
2.docker run --name kibana -e ELASTICSEARCH_HOSTS=http://43.128.111.99:9200 -p 5601:5601 -d kibana:6.8.0

docker exec -it 容器ID bash
vim config/kibana.yaml
		#
		# ** THIS IS AN AUTO-GENERATED FILE **
		#
		# Default Kibana configuration for docker target
		server.name: kibana
		server.host: "0"
		elasticsearch.hosts: [ "http://43.128.111.99:9200" ]
		xpack.monitoring.ui.container.elasticsearch.enabled: true

docker restart kibana

#访问:
http://43.128.111.99:5601/app/kibana
```

### 3.概念

> 索引（index）
>
> 一个索引就是一个拥有几分相识特征的文档集合，比如说你可以有一个客户数据的索引 另一个产品目录的索引，一个订单数据的索引，一个索引由一个名字标识（必须全是小写字母）并且当我们对这个索引的中文文档进行索引 搜索更新和删除时候  都需要用到这个名字  索引类似关系型数据库database的概念 ，在一个集群中可以定义任意多的索引
>
> ---
>
> 类型（type）
>
> 在一个索引中 你可以定义一种或多种类型，一个类型是你做因的一个逻辑的分类或者分区，其语义完全由你来定，通常会为具有一组共同字段的文档定义一个类型 比如我们运营一个博客平台并且将你所有的数据存储到一个索引中 在这个索引中  你可以为用户数据定义一个类型 为博客数据定义一个类型  当然也可以为评论定义一个类型  类型类似于关系型数据库中table的概念
>
> ---
>
> 映射（mapping）
>
> 映射是ES中很重要的一个内容 类似关系型数据库的table中的schema(约束)  用来定义一个索引中类型的数据结构，在es中可以手动创建type和mapping也可以默认创建方式  在默认配置中 es可以根据插入数据自动创建type及其mapping ，mapping中主要包括字段名 字段数据类型和字段索引类型
>
> ---
>
> 文档（document）
>
> 一个文档是一个可被索引的基础信息单元 类似表中的一条记录 比如你可以拥有某一个员工的文档 可以拥有某个商品的一个文档，文档采用了轻量级数据交换格式JSON来表示

### 4.查询

#### 1.索引的操作

```json
PUT /dangdang  创建索引
DELETE /dangdang  删除索引
DELETE /*   删除所有索引  慎用
GET /_cat/indices?v  查看索引信息
```

>health status index                    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
>green  open   .kibana_task_manager_1   3cheM6JWT-WxM51PtOTS7A   1   0          2            0     12.4kb         12.4kb
>green  open   .apm-agent-configuration bgGMaEY2Tyq39ZLGVepeeQ   1   0          0            0       283b           283b
>yellow open   ems                      vnI7jxvGQbCKbC-aExulCA   1   1          0            0       230b           230b
>green  open   .kibana_1                nVCXXxk4TDy65sT7wXD_NA   1   0         10            2     31.8kb         31.8kb
>
>---
>
>health  索引状态 ggreen 健壮的  yellow可用但不健壮  red 不可用
>
>status open  默认打开  关闭情况下索引不可用
>
>uuid  索引唯一标识
>
>pri  主分片数量  rep  副分片数量
>
>docs.count 文档总数量
>
>docs.deleted 删除的文档数量
>
>store.size  文档存储大小
>
> pri.store.size 主分片存储大小



#### 2.文档的操作

```json
PUT /ems
{
  "mappings": {
    "emp":{
      "properties":{
        "id":{"type":"keyword"},
        "name":{"type":"text"},
        "age":{"type":"integer"},
        "bir":{"type":"date"}
      }
    }
  }
}

# 查看索引对应的类型
GET /ems/_mapping

# 插入文档  PUT /类型/映射/文档ID
# 插入文档  POST /类型/映射  （ID自动生成）
PUT /ems/emp/1
{
  "id":1,
  "name":"linux",
  "age":18,
  "date":"2008-12-12"
}

# 查看文档
GET /ems/emp/1

# 删除文档
DELETE /ems/emp/1

# 更新文档 覆盖所有信息
POST /ems/emp/1
{
  "name":"java"
}

# 更新文档 只更新指定部分信息
POST /ems/emp/1/_update
{
  "doc": {
    "name":"java",
    "age":20
  }
}

# 更新同时 新增字段
POST /ems/emp/1/_update
{
  "doc": {
    "name":"python",
    "age":21,
    "gender":"man"
  }
}


# 文档的批量操作  index（新增）delete（删除）update（添加）
# 新增
PUT /ems/emp/_bulk
{"index":{"_id":2}}
  {"name":"linux","age":20,"bir":"2011-11-11","gender":"woman"} # 命令只能写在一行
{"index":{"_id":3}}
  {"name":"java","age":21,"bir":"2012-12-11","gender":"man"} # 命令只能写在一行 
{"index":{}}  # 不写ID 默认自动生成
{"name":"java","age":21,"bir":"2012-12-11","gender":"man"} # 命令只能写在一行
  
# 删除
PUT /ems/emp/_bulk
{"delete":{"_id":2}}   # 命令只能写在一行
{"delete":{"_id":3}}

PUT /ems/emp/_bulk
{"update":{"_id":1}}
  {"doc":{"name":"java","gender":"woman","age":30}}  # 命令只能写在一行
```

#### 3.查询操作

```json
# URL方式查询
# 查询所有
GET /ems/emp/_search?q=*

# 按年龄逆序排列 不加:desc 升序
GET /ems/emp/_search?q=*&sort=age:desc

# 只显示前5个，从0开始  默认10个
GET /ems/emp/_search?q=*&sort=age:desc&size=5

# 从第6个开始 只显示5个 可用于分页
GET /ems/emp/_search?q=*&sort=age:desc&size=5&from=6

# 只显示 age, name 字段名
GET /ems/emp/_search?q=*&sort=age:desc&size=5&from=6&_source=age,name
```

---

```json
# DSL方式查询
# 查询所有
GET /ems/emp/_search
{
  "query": {
    "match_all": {
      
    }
  }
}

# 查询所有 排序  排序字段不能被分词处理
GET /ems/emp/_search
{
  "query": {
    "match_all": {
      
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    },
    {
      "bir": {
        "order": "asc"
      }
    }
  ]
}


# 分页查询
GET /ems/emp/_search
{
  "query": {
    "match_all": {
      
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    },
    {
      "bir": {
        "order": "asc"
      }
    }
  ],
  "size": 5,
  "from": 6
}

# 返回指定字段
GET /ems/emp/_search
{
  "query": {
    "match_all": {
      
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    },
    {
      "bir": {
        "order": "asc"
      }
    }
  ],
  "size": 5,
  "from": 6,
  "_source": ["age","name"]
}
```

---

```json
# term关键字查询 
GET /ems/emp/_search
{
  "query": {
    "term": {
      "name":{
        "value": "叶"
      }
    }
  }
}

# 映射类型是text时, 插入文本会进行分词,默认使用标准分词器 对于中文是单字分词 你好分成你,好
# 对于英文的分词是 单词的分词 helloworld 分词 hello, world

# keyword, integer, long, date 等类型的数据不会被分词, 只有text类型数据会被分词


# 查看一段文本的分词效果
GET /_analyze
{
  "text": "china is the best country"
}

GET /_analyze
{
  "text": "中国是最好的国家"
}
```

---

```json
# range 范围查询 一般操作的对象是 integer类型
# 查询年龄大于等于25 小于30的文档
GET /ems/emp/_search
{
  "query": {
    "range": {
      "age": {
        "gte": 25,
        "lt": 30
      }
    }
  }
}

# 基于前缀查询  假设分词之后存在词 叶子  可以通过查询前缀 叶 查询叶子这个词对应的文档
GET /ems/emp/_search
{
  "query": {
    "prefix": {
      "name": {
        "value": "叶"
      }
    }
  }
}

# 通配符查询  ? 代表一个字符  * 代表任意个字符 
# 不建议这种写法 *m 导致查询混乱
GET /ems/emp/_search
{
  "query": {
    "wildcard": {
      "gender": {
        "value": "m*"
      }
    }
  }
}

# 查询多个id对应的文档
GET /ems/emp/_search
{
  "query": {
    "ids": {
      "values": ["26MZYHsBDLGZY1EPOBnb","56MZYHsBDLGZY1EPQRmS"]
    }
  }
}

# 模糊查询 允许查询的词 出现少许错误 如pythee , pythom
# 最大模糊的错误 必须在0-2之间  
# 关键词长度小于2 不允许错误 关键词长度3-5允许一个错误 大于5允许2个错误
GET /ems/emp/_search
{
  "query": {
    "fuzzy": {
      "name": "pythee"
    }
  }
}
```

---

```json
# 布尔查询
# must &&   should ||    must_not !

# 查询年龄大于21 且名称为python的文档
GET /ems/emp/_search
{
  "query": {
    "bool": {
      "must": [
        {"range": {
          "age": {
            "gte": 21
          }
        }},
        {"term": {
          "name": {
            "value": "python"
          }
        }}
      ]
    }
  }
}



GET /ems/emp/_search
{
  "query": {
    "bool": {
      "should": [
        {"range": {
          "age": {
            "gte": 21
          }
        }},
        {"term": {
          "name": {
            "value": "python"
          }
        }}
      ]
    }
  },
  "size": 20
  , "from": 20
}
```

---

```json
# 高亮查询
# 将包含man的关键字  用 pre_tags,post_tags对应的标签括起来  <span style='color:red'>man</span>
GET /ems/emp/_search
{
  "query": {
    "term": {
      "gender":{
        "value": "man"
      }
    }
  },
  "highlight": {
    "pre_tags": ["<span style='color:red'>"],
    "post_tags": ["</span>"],
    "fields": {
      "*":{}
    }
  }
}
```

---

```json
# 多字段查询
# 查询 字段name或者content包含python的文档
GET /ems/emp/_search
{
  "query": {
    "multi_match": {
      "query": "python",
      "fields": ["name","content"]
    }
  }
}

# 会先将linux哈python 分词成  linux、 哈 、 python
# 然后依次用关键词去文档对应的字段查询是否存在
GET /ems/emp/_search
{
  "query": {
    "multi_match": {
      "query": "linux哈python",
      "fields": ["name","gender"]
    }
  }
}

# 多字段分词查询
# python is 2, java is 1,linux is 0  分词之后去文档对应的name字段查找
GET /ems/emp/_search
{
  "query": {
    "query_string": {
      "default_field": "name",
      "query": "python is 2, java is 1,linux is 0"
    }
  }
}

# 也可以写成类似multi_match的方式
GET /ems/emp/_search
{
  "query": {
    "query_string": {
      "query": "python man",
      "fields": ["name","gender"]
    }
  }
}


# 指定特定的分词器  IK
GET /ems/emp/_search
{
  "query": {
    "query_string": {
      "default_field": "name",
      "query": "python is 2, java is 1,linux is 0",
      "analyzer": "ik_max_word"
    }
  }
}
```

#### 4.IK分词器

>上述步骤已经安装完成了IK分词器，通过kibana  API检测分词结果
>
>GET /_analyze
>{
>"text": "中华人民共和国",
>"analyzer": "ik_max_word"
>}
>
>---
>
>在创建类型的时候 必须指定分词的类型  默认是标准类型 不适合中国人习惯
>
>PUT /ems
>{
>  "mappings": {
>    "emp":{
>      "properties":{
>        "id":{"type":"keyword"},
>        "name":{
>          "type":"text",
>          "analyzer": "ik_max_word",       # 指定索引分词器
>          "search_analyzer":"ik_max_word"  # 指定搜索分词器 默认与创建索引的相同
>        },
>        "age":{"type":"integer"},
>        "bir":{"type":"date"},
>        "gender":{"type":"keyword"},
>        "content":{
>          "type":"text",
>          "analyzer": "ik_max_word",
>          "search_analyzer":"ik_max_word"
>        }
>      }
>    }
>  }
>}

#### 5.本地扩展、停用词典

`cat /usr/share/elasticsearch/config/analysis-ik/IKAnalyzer.cfg.xml` IK分词器扩展配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<comment>IK Analyzer 扩展配置</comment>
	<!--用户可以在这里配置自己的扩展字典 -->
	<entry key="ext_dict"></entry>
	 <!--用户可以在这里配置自己的扩展停止词字典-->
	<entry key="ext_stopwords"></entry>
	<!--用户可以在这里配置远程扩展字典 -->
	<!-- <entry key="remote_ext_dict">words_location</entry> -->
	<!--用户可以在这里配置远程扩展停止词字典-->
	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```

> 自定义扩展词语
>
> 在IKAnalyzer.cfg.xml同级目录下创建一个自定义的dic文件  my.dic 然后写入扩展词语 xhss、晓辉等等
>
> 自定义分词文件 必须是UTF-8格式的   file  filename可以查看文件格式
>
> 然后将扩展文件信息写入IK配置文件中
>
> ```xml
> <comment>IK Analyzer 扩展配置</comment>
> <!--用户可以在这里配置自己的扩展字典 -->
> <entry key="ext_dict">my.dic</entry>
>  <!--用户可以在这里配置自己的扩展停止词字典-->
> <entry key="ext_stopwords"></entry>
> <!--用户可以在这里配置远程扩展字典 -->
> <!-- <entry key="remote_ext_dict">words_location</entry> -->
> <!--用户可以在这里配置远程扩展停止词字典-->
> <!-- <entry key="remote_ext_stopwords">words_location</entry> -->
> ```
>
> ---
>
> 停用词语词典的方式与扩展词语方式相同
>
> 修改这个位置即可
>
> ```xml
> <entry key="ext_stopwords"></entry>
> ```

#### 6.远程扩展、停用词典

```xml
<!--用户可以在这里配置远程扩展字典 -->
<entry key="remote_ext_dict">http://wwwxh-ss.cn/es/ext.txt</entry>
<!--用户可以在这里配置远程扩展停止词字典-->
<entry key="remote_ext_stopwords">url</entry>
```





### 5.过滤查询

>Query与Filter
>查询在Query查询上下文和Filter过滤器上下文中，执行的操作是不一样的：
>
>查询上下文：
>
>在查询上下文中，查询会回答这个问题——“这个文档匹不匹配这个查询，它的相关度高么？”
>
>如何验证匹配很好理解，如何计算相关度呢？之前说过，ES中索引的数据都会存储一个_score分值，分值越高就代表越匹配。另外关于某个搜索的分值计算还是很复杂的，因此也需要一定的时间。
>
>查询上下文 是在 使用query进行查询时的执行环境，比如使用search的时候。
>
>---
>
>过滤器上下文：
>
>在过滤器上下文中，查询会回答这个问题——“这个文档匹不匹配？”
>
>答案很简单，是或者不是。它不会去计算任何分值，也不会关心返回的排序问题，因此效率会高一点。
>
>过滤上下文 是在使用filter参数时候的执行环境，比如在bool查询中使用Must_not或者filter
>
>另外，经常使用过滤器，ES会自动的缓存过滤器的内容，这对于查询来说，会提高很多性能。

```json
# 基于范围过滤
# 查询content包含中国  且年龄大于等于20小于100的文档
GET /ems/emp/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {
          "content": {
            "value": "中国"
          }
        }}
      ],
      "filter": {
        "range": {
          "age": {
            "gte": 20,
            "lt": 100
          }
        }
      }
    }
  }
}

# 基于关键词过滤
# 查询gender是women  且content包含美国的文档
GET /ems/emp/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {
          "content": {
            "value": "美国"
          }
        }}
      ],
      "filter": {
        "term": {
          "gender": "women"
        }
      }
    }
  }
}

# 多个关键词过滤
GET /ems/emp/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {
          "age": {
            "value": "28"
          }
        }}
      ],
      "filter": {
        "terms": {
          "content": [
            "美",
            "美国"
          ]
        }
      }
    }
  }
}


# 获取某个字段不为空的文档
# 获取content字段不为空 q
GET /ems/emp/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {
          "age": {
            "value": "28"
          }
        }}
      ],
      "filter": {
        "exists": {
          "field": "content"
        }
      }
    }
  }
}
```





### 6.java客户端操作ES

#### 1.创建删除索引

```xml
<dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>6.8.0</version>
</dependency>

<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>transport</artifactId>
    <version>6.8.0</version>
</dependency>

<dependency>
    <groupId>org.elasticsearch.plugin</groupId>
    <artifactId>transport-netty4-client</artifactId>
    <version>6.8.0</version>
</dependency>
```

```java
@SpringBootTest
public class AlphaTest {

    private TransportClient client;

    @BeforeEach
    public void init() throws UnknownHostException {
        // 配置集群节点名称  通过IP:9200查看
        Settings settings = Settings.builder().put("cluster.name", "docker-cluster").build();
        client = new PreBuiltTransportClient(settings);
        client.addTransportAddress(new TransportAddress(InetAddress.getByName("43.128.111.99"), 9300));
    }

    /**
     * 索引的创建与删除
     */
    @Test
    public void indexCreateAndDel() {
        // 创建索引
        CreateIndexResponse response = client.admin().indices().prepareCreate("user").get();
        System.out.println(response.isAcknowledged());

        // 删除索引
        AcknowledgedResponse result = client.admin().indices().prepareDelete("user").get();
        System.out.println(result.isAcknowledged());
    }

    @AfterEach
    public void end() {
        client.close();
    }
}
```



#### 2.创建文档

```json
PUT /ems/
{
  "mappings": {
    "book":{
      "properties":{
        "id":{"type":"long"},
        "author":{
          "type":"text",
          "analyzer": "ik_max_word"
        },
        "name":{
          "type":"text",
          "analyzer": "ik_max_word"
        },
        "resume":{
          "type":"text",
          "analyzer": "ik_max_word"
        },
        "pubdate":{
          "type":"date"
        }
      }
    }
  }
}
```

```java
@Test
public void create() {
    Book book = new Book(1L, "张大炮", "海贼王", "这是一本关于大炮的书", new Date());
    String s = JSONObject.toJSONString(book);
    IndexResponse response = client.prepareIndex("ems", "book")
        .setSource(s, XContentType.JSON).get();

    System.out.println(response.status());
}
```





#### 3.更新文档

```java
@Test
public void update() {
    Book book = new Book(1L, "张大炮", "火影忍者", "这是一本关于大炮的书", new Date());
    String s = JSONObject.toJSONStringWithDateFormat(book,"yyyy-MM-dd");

    // 使用es自动创建的_id
    UpdateResponse response = client.prepareUpdate("ems", "book", "IaNdZXsBDLGZY1EPJRrm")
        .setDoc(s, XContentType.JSON).get();

    System.out.println(response.status());
}
```





#### 4.删除文档

```java
@Test
public void del() {
    DeleteResponse response = client.prepareDelete("ems", "book", "IaNdZXsBDLGZY1EPJRrm").get();
    System.out.println(response.status());
}
```





#### 5.查询

##### 1.单个查询

```java
@Test
public void queryOne() throws Exception {
    GetResponse response = client.prepareGet("ems", "book", "I6N_ZXsBDLGZY1EPERp3").get();
    System.out.println(response.toString());
    System.out.println(response.getSource().toString());

    Map<String, Object> map = response.getSourceAsMap();
    Date date = new Date(Long.parseLong(map.get("pubdate").toString()));
    Book book = new Book(Long.parseLong(map.get("id").toString()), map.get("author").toString(), map.get("name").toString(), map.get("resume").toString(), date);
    System.out.println(book);
}
```



##### 2.查询所有

```java
@Test
public void queryAll() {
    QueryBuilder queryBuilder = new MatchAllQueryBuilder();
    SearchResponse response = client.prepareSearch("ems")
        .setTypes("book")
        .setQuery(queryBuilder)
        .get();

    // 总条数
    long totalHits = response.getHits().getTotalHits();
    // 总得分
    float score = response.getHits().getMaxScore();

    // 查询的结果集
    SearchHit[] hits = response.getHits().getHits();

    for (SearchHit hit : hits) {
        System.out.println(hit.getSourceAsMap().toString());
    }
}
```





##### 3.批量操作

```java
@Test
public void queryBulk() {

    // 创建文档
    Book book = new Book(3L, "hxh", "仁王", "搞笑的故事", new Date());
    String json = JSONObject.toJSONString(book);
    IndexRequest indexRequest = new IndexRequest("ems", "book", book.getId().toString()).source(json, XContentType.JSON);

    // 更新文档
    UpdateRequest updateRequest = new UpdateRequest("ems", "book", book.getId().toString()).doc(JSONObject.toJSONString(book.setAuthor("晓辉")), XContentType.JSON);

    // 删除文档
    DeleteRequest deleteRequest = new DeleteRequest("ems", "book", book.getId().toString());

    // 批量操作
    BulkRequestBuilder prepareBulk = client.prepareBulk();
    BulkResponse responses = prepareBulk
        .add(indexRequest)
        .add(updateRequest)
        .add(deleteRequest)
        .get();

    for (BulkItemResponse respons : responses) {
        // 返回每一次操作对应的结果
        // 批量操作不具有原子性
        System.out.println(respons.status());
    }
}
```





##### 4.复合查询

```java
@Test
public void queryCom() {
    // 查询全部
    MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();
    // 通配符查询 查询名称包含王的文档
    WildcardQueryBuilder wildcardQueryBuilder = QueryBuilders.wildcardQuery("name", "王*");
    // 区间查询 查询年龄大于20小于50的文档
    RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("id").gt(20).lt(50);
    // 前缀查询 查名称分词中  前缀是仁的文档
    PrefixQueryBuilder prefixQueryBuilder = QueryBuilders.prefixQuery("name", "仁");

    // 多字段分词匹配查询
    MultiMatchQueryBuilder multiMatchQueryBuilder = QueryBuilders.multiMatchQuery("搞笑", "resume", "name");

    // id查询
    IdsQueryBuilder idsQueryBuilder = QueryBuilders.idsQuery().addIds("28").addIds("29", "25");

    // 模糊查询
    // 2个字得分词不允许错误  3-5允许一个错误 大于5允许2个错误
    FuzzyQueryBuilder fuzzyQueryBuilder = QueryBuilders.fuzzyQuery("resume", "搞笑得");

    // bool查询
    BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery().must(QueryBuilders.termQuery("author", "仁王"));

    SearchResponse response = client.prepareSearch("ems").setTypes("book")
        // .setQuery(wildcardQueryBuilder)
        // .setQuery(matchAllQueryBuilder)
        // .setQuery(rangeQueryBuilder)
        // .setQuery(prefixQueryBuilder)
        // .setQuery(idsQueryBuilder)
        // .setQuery(fuzzyQueryBuilder)
        .setQuery(multiMatchQueryBuilder)
        .setFrom(0)  // 设置起始位置
        .setSize(5)  // 设置返回结果的数量
        .addSort("pubdate", SortOrder.DESC)  // 设置排序
        // 返回指定的字段
        .setSource(SearchSourceBuilder.searchSource().fetchSource("id", "name"))
        .get();

    System.out.println(response.getHits().getTotalHits());
    System.out.println(response.getHits().getMaxScore());

    for (SearchHit hit : response.getHits().getHits()) {
        System.out.println(hit.getSourceAsMap());
    }

}
```





##### 5.过滤查询

```jade
@Test
public void queryByFilter() {

RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("id").gt(25).lte(40);
TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("resume", "28");
// 过滤查询 查询之前对 数据进行筛选
SearchResponse response = client.prepareSearch("ems").setTypes("book")
.setPostFilter(rangeQueryBuilder)
.setPostFilter(termQueryBuilder)
.setQuery(QueryBuilders.matchAllQuery())
.get();

for (SearchHit hit : response.getHits().getHits()) {
System.out.println(hit);
	}
}
```







### 7.spirngBoot操作ES （V7.6.2）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

##### 1.ES设置密码

> `vi /usr/share/elasticsearch/config`
>
> > 最后一行添加  完成后要重启ES
> >
> > xpack.security.enabled: true
>
> ---
>
> 
>
> 进入bin目录 执行
>
> `elasticsearch-setup-passwords interactive`
>
> > Enter password for [elastic]: 
> > Reenter password for [elastic]: 
> > Enter password for [apm_system]: 
> > Reenter password for [apm_system]: 
> > Enter password for [kibana]: 
> > Reenter password for [kibana]: 
> > Enter password for [logstash_system]: 
> > Reenter password for [logstash_system]: 
> > Enter password for [beats_system]: 
> > Reenter password for [beats_system]: 
> > Enter password for [remote_monitoring_user]: 
> > Reenter password for [remote_monitoring_user]: 
> > Changed password for user [apm_system]
> > Changed password for user [kibana]
> > Changed password for user [logstash_system]
> > Changed password for user [beats_system]
> > Changed password for user [remote_monitoring_user]
> > Changed password for user [elastic]
>
> ---
>
> 
>
> 修改kibana 账户密码
>
> `vi config/kibana.yml`
>
> > elasticsearch.username: "elastic" 
> >
> > elasticsearch.password: "123456"
