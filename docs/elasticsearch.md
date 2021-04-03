# ElasticSearch

## 1.简介

​	elasticsearch是一个基于lucence的服务器，它提供了一个分布式全文搜索引擎，对外基于restful web接口提供服务，它是基于java开发的应用。

## 2.功能分类

​	elasticsearch具备两个主要功能：

​	1.搜索。功能和solr类似

​	2.分析。结合LogStach使用

## 3.相关概念

### 	3.1.cluster

​	集群由一个或多个节点组成，其中有一个主节点，主节点可以通过选举产生，主节点是对于集群内部而言。对于外部来说，集群是一个整体，访问任何一个节点就等价于与整个集群通信。所以，主节点的存在不会产生单点安全隐患和并发访问瓶颈等问题。（阈值：(n/2)+1）

### 	3.2.shards

​	（1）primary shard：代表索引的主分片。es可以把一个完整的索引分成多个primary shard，这样的好处是可以把一个大的索引拆分成多个分片，分别存储在不同的es节点上，从而形成分布式存储，并为搜索提供分布式服务，提高并发处理能力。分片的数量只能在索引创建时指定。索引创建后不能再更改primary shard的数量。

​	（2）replica shard：代表索引主分配的副本（从分片）。es可以设置多个replica shard。作用是：1.提高系统的容错性，当某个节点某个primary shard损坏或者丢失时可以从副本中恢复。2.提高es的查询效率，es会自动对搜索请求进行负载均衡，将并发的搜索请求发送给合适的节点，增强并发处理能力。

*注：主分片和从分片不能放在一个节点上，每一个节点上不能有重复的从分片。*

​		*主分片读和写，从分片只读。*

​		*一般主分片比从分片多1个。如10个主分片9个从分片。*

​		*主分片一旦设置不可更改，从分片可以。*

### 3.3.Index

​	索引。相当与关系型数据库中的表。其中

### 3.4.Type

类型，是一个抽象的概念。底层还是存的一个，只是逻辑区分，从不同角度看的而已。每个索引中都必须有一个Type。Type时index中的一个逻辑分类。es中的数据Document是存储在索引下的Type中的。

*注意：es5.x及更低版本中，一个index中可以有多个Type。es6.x版本之后，Type被弱化，一个Index中只能有唯一的一个Type。在es7.x版本后，删除Type。*

### 3.5.Document

文档。es中的最小数据单元。一个Document就是一条数据，一般使用json数据结构表示。每一个Index下的Type中都可以存储多个Document。一个Document中可以定义多个field，field就是数据字段。

### 3.6.元数据

在es中所有以"_"开头的属性都称为元数据，都有着自己特定的含义。例如：“_index”表示索引

### 3.7.倒排索引|反向索引

对数据进行分析，抽取出数据中的词条，以词条作为key，对应数据的存储位置作为value，实现索引的存储。这种索引称为倒排索引。倒排索引是Document写入es时分析维护的。

## 4.docker安装es

### 4.1.修改linux系统限定配置

`vim /etc/security/limits.conf`

新增：

`* soft nofile 65536`

`* hard nofile 65536`

`* soft nproc 4096`
`root soft nproc unlimited`

如图：

![es-config](./images/es-config.PNG)

注：

soft nofile ：可打开的文件描述符的最大数(超过会警告);
hard nofile ：可打开的文件描述符的最大数(超过会报错);

soft nproc ：单个用户可用的最大进程数量(超过会警告);

65536和4096时es启动时的要求信息，否则es启动不了

### 4.2.修改系统控制文件（该文件是管理系统中的各种资源控制的配置文件）

`vim /etc/sysctl.d/99-sysctl.conf`

新增:

`vm.max_map_count=655360`

该文件不会立即生效，使用命令立即刷新生效：`sysctl -p`

### 4.3.安装es

`docker pull elasticsearch:6.8.4`

`docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 elasticsearch:6.8.4`

注：

由于es是java编写，默认启动会占用2G堆内存，内存不足则无法启动。可以启动时添加设置内存大小：`-e ES_JAVA_OPTS="-Xms256m -Xmx256m"`

启动后访问如图所示则安装成功：curl http://192.168.91.20:9200

![es-success](./images/es-success.PNG)

## 5.安装es管理工具Kibana

`docker pull kibana:6.8.4`

`docker run -it -d -e ELASTICSEARCH_URL=http://192.168.91.20:9200 --name kibana --link elasticsearch:elasticsearch -p 5601:5601 kibana:6.8.4`

注：

启动后浏览器访问：http://192.168.91.20:5601

## 6.常用es管理操作

### 6.1.查看信息

#### 6.1.1.查看集群健康状态    

?v表示带有标题头

`GET /_cat/health?v`

#### 6.1.2.查看索引信息

`GET /_cat/indices?v`

#### 6.1.3.查看集群中节点信息

`GET /_cat/nodes?v`

#### 6.1.4.查看分片信息

`GET /_cat/shards?v`

### 6.2.索引操作

#### 6.2.1.创建

命令语法：PUT 索引名[索引配置参数]

index名称**<u>必须小写</u>**，且不能以下划线'_',	'-',	'+'开头，不能包含字符	,	\	*	\\\	<	>	|	/	?	""

每个索引默认5个主分片，5个从分片（索引的主分片数5，每个主分片的副本数1）

`PUT my_index`

#### 6.2.2.创建索引并指定分片

注意：编写时大括号不能和put命令在一行（不能把{放在索引名后，kibana会把{当成索引的一部分）

`PUT second_index`
`{`
  `"settings": {`
    `"number_of_shards": 2,`
    `"number_of_replicas": 0`
  `}`
`}`

注：磁盘空间不足15%时从分片不再分配，不足5%时主分片不在分配

#### 6.2.3.修改索引

命令：PUT 索引名/_setting{索引配置参数}

注意：索引一旦创建，primary shards数量不可变化，可以改变replica shards的数量

`PUT my_index/_settings`
`{`
  `"number_of_replicas": 0`
`}`

#### 6.2.4.删除索引

命令：DELETE 索引名

支持删除多个，DELETE index1,index2,index3...

`DELETE my_index,second_index`

### 6.3.文档操作

es中向一个不存在的索引中添加数据自动创建索引，索引的主分片数5，每个主分片的副本数1

`PUT my_index/my_type/1`
`{`
  `"name":"zhangjianhua",`
  `"age":27,`
  `"gender":"man",`
  `"info": {`
    `"card_id":"342923199410253110"`
  `},`
  `"hobbies":["eat","java","sleep","game","study"]`
`}`

`POST my_index/my_type`
`{`
  `"name":"zhangsan",`
  `"age":25,`
  `"gender":"man",`
  `"info": {`
    `"card_id":"342923199410253110"`
  `},`
  `"hobbies":["eat","java","sleep","game","study"]`
`}`

注：POST和PUT的区别是POST能够保证在分布式环境下id是唯一的，而PUT使用的是uuid