## 极客时间-Elasticsearch核心技术与实战 学习笔记

[TOC]

# 前言

笔记记录了跟随课程学习时遇到的问题，和一些疑问点。

[课程课件GitHub地址](https://github.com/onebirdrocks/geektime-ELK/)，个人学习可以直接跟着这个课件看。

> 个人使用平台与软件版本：

> > Win10：19042.685
> >
> > Docker Desktop for Windows:  docker engine version = 20.10.0
> >
> > WSL2: Debian
> >
> > ES,Kibana,Logstash: version 统一 7.10.1
> >
> > 内存：建议16G 起，起ES集群时尤其费内存





## 第一部分：初识 Elasticsearch

### 第 1 章：概述
#### 01 | 课程介绍

>阮一鸣，eBay Pronto平台负责人。管理上百es集群。作用：订单搜索、商品推荐、日志管理、风险控制、IT运维、安全监控等。
>集合 kibana、logstash、beats， elastic stack还被广泛使用在大数据近实时分析的领域，包括日志分析，指标监控，信息安全等多个领域。帮助你探索海量的结构化的、非结构化的数据，按需创建可视化报表，对监控数据设置报警阈值，甚至使用机器学习技术，自动识别异常的状况，及时发出警报。
>
>
>
>为什么要学习ES？
>
>> 使用广泛，功能强大，Elastic 公司也推出了Elastic工程师认证，含金量较高，去学就对了。（本课程包含考点）

#### 02 | 课程综述及学习建议

***

**为什么要学习Elasticsearch**

- **主要功能**
  - 分布式搜索引擎
  - 大数据近实时分析引擎
- **产品特性**
  - 高性能，和T+1[^T+n]说不（这里提了Hadoop[^Hadoop]，说了ES性能更好，应该也要分情况吧。。）
  - 容易使用 / 容易扩展

***

**学习目标：三个层面**

- **开发**
  - 产品基本功能，底层工作原理，数据建模最佳实践
- **运维**
  - 容量规划；性能优化；问题诊断；滚动升级
- **方案**
  - 搜索与如何解决搜索的相关性问题
  - 大数据分析实践与项目实战，理论知识运用到实际场景

***

**课程实战**

- 电影搜索
- Stack Overflow 调查问卷数据分析

***

**课程内容与结构**

- **Elasticsearch 入门与深入**
  - 环境搭建、搜索与聚合、架构原理、数据建模
- **Elasticsearch 集群管理**
  - 水平扩展及性能优化、最佳实践
- **ELK [^ELK]进行大数据分析**
  - 可视化分析、时序型数据、异常检测
- **项目实战和知识点回顾**
  - 电影搜索、问卷分析、Elastic认证

***

**学习建议**

- **勤动手**
  - 本地搭建多节点的集群环境，理解分布式工作原理，执行教程中的每个范例
- **多思考**
  - 结合实际的业务场景，深入思考，学会查阅相关的技术文档
- **定目标**
  - 做一次分享，开发一个具体项目，参加Elastic认证考试

***



#### 03 | Elasticsearch 简介及其发展历史

>ES用户很多。搜索引擎中排名第一。 
>提供功能相似的产品，Solr（Apache开源项目）、Splunk（商业上市公司）。
>都是起源于 Lucene（基于Java语言开发的搜索引擎类库）
>"Search is something that any application should have."		-- Shay Banon （Elasticsearch 创始人）



#### 04 | Elastic Stack 家族成员及其应用场景

https://www.elastic.co/guide/index.html

**云服务**：搜索、日志分析、指标分析、安全分析

- 开源
  - 可视化：Kibana
  - 存储/计算：Elasticsearch
  - 数据抓取：Logstash，Beat
- 商业
  - X-Pack: 安全、告警、监控、图查询、机器学习

***

**Logstash:数据处理管道**

https://www.elastic.co/guide/en/logstash/current/introduction.html

> **开源的服务器端数据处理管道，支持从不同源采集数据，转换数据，并将数据发送到不同的存储中**

**特性**

- **实时解析和转换数据**
  - 从IP地址破译出地理坐标
  - 将PII [^PII] 数据匿名化，完全排除敏感字段
- **可扩展**
  - 200 多个插件（日志、数据库、[ArcSight](https://www.elastic.co/guide/en/logstash/current/arcsight-module.html)、~~[Netflow](https://www.elastic.co/guide/en/logstash/current/netflow-module.html)~~）
- **可靠性安全性**
  - Logstash 会通过持久化队列来保证至少将运行中的事件送达一次
  - 数据传输加密
- **监控**



***

**Beats- 轻量数据采集器**

https://www.elastic.co/guide/en/beats/libbeat/current/beats-reference.html

**beats作为代理安装在服务器上，捕获相应数据后发送到Elasticsearch。针对不同的数据有不同的方案。**

- Auditbeat: Audit data
- Filebeat: Log files
- Functionbeat: Cloud data
- Heartbeat: Availability
- Journalbeat: Systemed journals
- Metricbeat: Metrics
- Packetbeat: Network traffic
- Winlogbeat: Windows event logs



***

**日志管理**

- 日志搜集
- 格式化分析
- 全文检索
- 风险告警

***

**Elasticsearch 与数据库集成**

- **单独使用Elasticsearch存储**
  - 架构简单
- **与数据库集成**
  - 需要与现有系统集成
  - 需要考虑事务性
  - 数据更新频繁



### 第 2 章：安装上手
#### 05 | Elasticsearch 的安装与简单配置

[Download Elasticsearch](https://www.elastic.co/cn/downloads/elasticsearch)

根据自己的环境自行选择不同的安装方式。我这里用了docker。[Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)

```powershell
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.10.1
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.10.1

# 版本自行选择
```

***

**脚本启动**

​	上面直接在终端 `docker run` 也可以，只是自定义参数时内容较长，所以尝试添加shell脚本。这里只多加了个自定义 name。

​	windows下创建个 xxx.sh 文件，在终端 `.\xxx.sh` 执行（这需要安装了git。如果不行可以搜索 Win10下如何执行`.sh`文件）

```sh
docker run \
    --name es-single \
    -p 9200:9200 -p 9300:9300 \
    -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.10.1
```

***

**插件安装**

```powershell
# 进入容器中
docker exec -it es-single bash

# 通过 bin目录下的 elasticsearch-plugin 可以很方便的处理插件
# 查看已有插件
bin/elasticsearch-plugin list 
# 安装分词插件
bin/elasticsearch-plugin install analysis-icu
```

***

**集群启动**

> docker-compose.yml

```yml
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
    name: elastic  # 这里需要指定name，才能保证创建的网络名是elastic
```



按照文档给的 `docker-compose.yml` 文件执行，遇到了几个问题：

1 | 自动创建的网络名称多了个文件夹名称的前缀，需要添加`network.elastic.name`指定名字
2 | data  挂载目录，`/usr/share/elasticsearch/data` 是 Linux 目录，我的 Windows 环境需要换成 `/D/docker/elasticsearch/data`,我是在d盘里建的文件夹
3 | max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

```powershell
# 我使用的docker desktop ，settings -> General ,勾选了 Use the WSL2 based engine, 在powershell中通过以下命令修改配置
wsl -d docker-desktop
sysctl -w vm.max_map_count=262144
```

> <font color=red>wsl 重启后配置又变回了 65530，下面第二个链接提供了几种修改方式，但是好像重启后都失效了</font>

参考：

https://blog.csdn.net/weixin_35726962/article/details/109800916

https://github.com/docker/for-win/issues/5202



#### 06 | Kibana 的安装与界面快速浏览

[Run Kibana on Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)

```sh
docker run -d \
    --link es01:elasticsearch \
    --name kibana \
    -p 5601:5601 \
    --network elastic \
    docker.elastic.co/kibana/kibana:7.10.1
```





***

**界面预览**

- Home -> Add data -> Sample data 中给了三种简单数据，添加后可以自行 View data感受下效果。
- 搜索栏搜索 “dev tools” 可以找到调试工具，以后会用到





#### 07 | 在 Docker 容器中运行 Elasticsearch，Kibana 和 Cerebro

**使用 docker-compose 一次启动所有服务**

https://github.com/onebirdrocks/geektime-ELK/blob/master/docker-hot-warm-cold/docker-compose.yaml

> **执行前先把原来起得容器停掉（防止端口占用）或删掉（防止重名）。记得根据自己的版本修改文件。**

> docker-compose.yml
>
> docker-compose -f  xxxx.yml up

```yaml
version: '2.2'
services:
  cerebro:
    image: lmenezes/cerebro:0.8.3
    container_name: hwc_cerebro
    ports:
      - "9000:9000"
    command:
      - -Dhosts.0.host=http://elasticsearch:9200
    networks:
      - hwc_es7net
  kibana:
    image: docker.elastic.co/kibana/kibana:7.10.1
    container_name: hwc_kibana7
    environment:
      #- I18N_LOCALE=zh-CN
      - XPACK_GRAPH_ENABLED=true
      - TIMELION_ENABLED=true
      - XPACK_MONITORING_COLLECTION_ENABLED="true"
    ports:
      - "5601:5601"
    networks:
      - hwc_es7net
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es7_hot
    environment:
      - cluster.name=geektime-hwc
      - node.name=es7_hot
      - node.attr.box_type=hot
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.seed_hosts=es7_hot,es7_warm,es7_cold
      - cluster.initial_master_nodes=es7_hot,es7_warm,es7_cold
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - hwc_es7data_hot:/d/docker/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - hwc_es7net
  elasticsearch2:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es7_warm
    environment:
      - cluster.name=geektime-hwc
      - node.name=es7_warm
      - node.attr.box_type=warm
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.seed_hosts=es7_hot,es7_warm,es7_cold
      - cluster.initial_master_nodes=es7_hot,es7_warm,es7_cold
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - hwc_es7data_warm:/d/docker/elasticsearch/data
    networks:
      - hwc_es7net
  elasticsearch3:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es7_cold
    environment:
      - cluster.name=geektime-hwc
      - node.name=es7_cold
      - node.attr.box_type=cold
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.seed_hosts=es7_hot,es7_warm,es7_cold
      - cluster.initial_master_nodes=es7_hot,es7_warm,es7_cold
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - hwc_es7data_cold:/d/docker/elasticsearch/data
    networks:
      - hwc_es7net


volumes:
  hwc_es7data_hot:
    driver: local
  hwc_es7data_warm:
    driver: local
  hwc_es7data_cold:
    driver: local

networks:
  hwc_es7net:
    driver: bridge
    name: hwc_es7net
```



**localhost:9000  访问 Cerebro，查看简单的集群情况。。看效果能显示的内容挺详细，集群的各个节点使用情况都有。**



#### 08 | Logstash 安装与导入数据

[Running Logstash on Docker](https://www.elastic.co/guide/en/logstash/current/docker.html)

7.10.0 版本的`logstash`，文件结构和课程演示的就不同了。`logstash.conf` 文件不再在 `bin`目录下,新增了`pipeline`目录存储

```sh
docker run --rm -it --name logstash --network hwc_es7net \
-v /home/zhuht/docker/elastic/logstash/data/:/usr/share/logstash/data/ \
-v /home/zhuht/docker/elastic/logstash/conf/:/usr/share/logstash/pipeline/ \
docker.elastic.co/logstash/logstash:7.10.1
```

> 由于logstash仅仅用来导入数据，所以run 加了 --rm 参数，表示停止容器后就移除容器

> 挂载了两个文件夹，data存的是要导入的数据，这里是movies.csv; conf 存的是下面的 logstash.conf, 解析数据，导入指定ES中



> logstash.conf

```json
input {
  file {
    path => "/usr/share/logstash/data/movies.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}
filter {
  csv {
    separator => ","
    columns => ["id","content","genre"]
  }

  mutate {
    split => { "genre" => "|" }
    remove_field => ["path", "host","@timestamp","message"]
  }

  mutate {

    split => ["content", "("]
    add_field => { "title" => "%{[content][0]}"}
    add_field => { "year" => "%{[content][1]}"}
  }

  mutate {
    convert => {
      "year" => "integer"
    }
    strip => ["title"]
    remove_field => ["path", "host","@timestamp","message","content"]
  }

}
output {
   elasticsearch {
     hosts => "http://elasticsearch:9200"
     index => "movies"
     document_id => "%{id}"
   }
  stdout {}
}

```



### 第 3 章：Elasticsearch 入门
#### 09 | 基本概念（1）：索引，文档和 REST API

索引和文档属于逻辑概念，偏开发。分片更偏运维，属于物理概念。

***

[文档和索引](https://www.elastic.co/guide/en/elasticsearch/reference/7.10/documents-indices.html#documents-indices)

**文档**

- ​	**Elasticsearch 是面向文档的，文档是所有可搜索数据的最小单位**
  - 日志文件中的日志项
  - 一部电影的具体信息 
- **文档会被序列化成JSON格式，保存在Elasticsearch中**
  - JSON对象由字段组成
  - 每个字段都有对应的字段类型（字符串、数值、布尔、日期、二进制、范围类型）
- **每个文档都有一个unique ID**
  - 可以自己指定 id
  - 也可以通过 Elasticsearch自动生成



**一篇文档包含一系列字段，类似数据库表中的一条记录。**

JSON文档，格式灵活，不需要预先定义格式。

- 字段类型可以指定或者通过ES自动推算
- 支持数组 / 嵌套

上一节通过`logstash`导入的 movies.csv, 在ES中数据格式如下

```json
{
    "@version": "1",
    "id": "3",
    "title": "Grumpier Old Men",
    "year": 1995,
    "genre": [
      "Comedy",
      "Romance"
    ]
  }
```

上面是文档的存储数据，文档本身还有一些**元数据,用于标注文档的相关信息**

```json
{
  "_index": "movies",				// 文档所属的索引名
  "_type": "_doc",					// 文档所属的类型名
  "_id": "3",						// 文档唯一ID
  "_version": 1,					// 文档的版本信息。并发时可以解决文档冲突问题
  "_score": 0,						// 相关性打分
  "_source": {						// 文档的原始JSON数据
    "@version": "1",
    "id": "3",
    "title": "Grumpier Old Men",
    "year": 1995,
    "genre": [
      "Comedy",
      "Romance"
    ]
  }
}

// _all: 整合所有字段内容到该字段，7.0开始已被废除
```



***

**索引**

- **Index 索引是文档的容器，是一类文档的结合**
  - Index 体现了逻辑空间的概念：每个索引都有自己的Mapping定义，用于定义包含的文档的字段名和字段类型。
  - Shard 体现了物理空间的概念：索引中的数据分散在Shard上
- **索引的 Mapping 与 Settings**
  - Mapping 定义文档字段的类型
  - Settings 定义不同的数据分布

> tip: 可以在 Kibana > Manage > Data > Index Management 中选择索引，查看它的Mappings定义



**索引的不同语义**

- 名词：一个ES集群中，可以创建多个不同的索引
- 动词：保存一个文档到ES的过程也叫索引（indexing）
  - ES中，创建一个倒排索引的过程
- 名词：一个B树索引，一个倒排索引

> 例句：索引（动词）文档到ES 的索引（名词）中

***

**抽象与类比**

| RDBMS(关系型数据库) | Elasticsearch |
| ------------------- | ------------- |
| Table               | Index(Type)   |
| Row                 | Document      |
| Column              | Field         |
| Schema(表定义)      | Mapping       |
| SQL                 | DSL           |

**优势**

- RDBMS：注重事务
- Elasticsearch：全文检索



***

**REST API - 很容易被各种语言调用**

> Kibana 中使用 Dev-Tools 调试API

[REST APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html)

```apl
# 查看索引相关信息
GET movies

# 查看索引的文档总数
GET kibana_sample_data_ecommerce/_count

# 查看前10条文档，了解文档格式。没指定排序规则，，也不知道按什么排的
GET movies/_search
{
  "from": 0, 
  "size": 10
}

# 查看index。使用索引的名字进行通配符查询
GET /_cat/indices/kibana*?v&s=index

# 查看状态为green的索引
GET /_cat/indices?v&health=green

# 按照文档个数排序
GET /_cat/indices?v&s=docs.count:desc

# 查看具体的字段
GET /_cat/indices/kibana*?pri&v&h=health,index,pri,rep,docs.count,mt

# 查看每个索引使用的内存
GET /_cat/indices?v&h=i,tm&s=tm:desc
```



#### 10 | 基本概念（2）：节点，集群，分片及副本

**分布式系统的可用性与扩展性**

- **高可用**
  - 服务可用性 - 允许有节点停止服务
  - 数据可用性 - 部分节点丢失，不会丢失数据
- **可扩展性**
  - 请求量提升  / 数据的不断增长（将数据分布到所有节点上）

***

**分布式特性**

- Elasticsearch 的分布式架构的好处
  - 存储的水平扩容
  - 提高系统的可用性，部分节点停止服务，整个集群的服务不受影响
- Elasticsearch 的分布式架构
  - 不同的集群通过不同的名字来区分，默认名字“elasticsearch”
  - 通过配置文件修改，或者在命令行中 `-E cluster.name=customName` 进行设定
  - 一个集群可以有一个或多个节点

***



[节点官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html#modules-node)

**节点**

- 节点是一个 Elasticsearch 的实例
  - 本质上就是一个 Java进程
  - 一个机器上可以运行多个 Elasticsearch 进程，但是生产环境一般建议一台机器上只运行一个 Elasticsearch 实例 
- 每一个节点都有名字，通过配置文件，或者启动时候 `-E node.name=name`指定
- 每一个节点在启动之后，会分配一个UID，保存在 data目录下

***

**Node roles**

节点可以有不同的角色，每种角色对应不同的属性或功能。

可以通过 `node.roles` 来定义节点的角色。如果未特别指定，则该节点默认具有以下角色：

- master
- data
- data_content
- data_hot
- data_warm
- data_cold
- ingest
- ml
- remote_cluster_client

***

**Master-eligible node[^Master-eligible node] 和 Master Node**

- 每个节点启动后，默认就是一个 Master eligible 节点
  - 可以设置 `node.master: false` 禁止
- Master-eligible 节点可以参加选主流程，成为 Master节点
- 当第一个节点启动时，它会将自己选举成 Master 节点
- 每个节点上都保存了集群状态，只有Master 节点才能修改集群的状态信息
  - 集群状态（Cluster State），维护了一个集群中，必要的信息
    - 所有节点信息
    - 所有的索引和其相关的 Mapping 与 Settings 信息
    - 分片的路由信息
  - 任意节点都能修改集群信息的话，会导致数据的不一致性

***

**Data Node & Coordinating Node(协调节点)**

- Data Node

  - 可以保存数据的节点。负责保存分片数据。在数据扩展上起到了至关重要的作用

- Coordinating Node

  - 负责接受Client的请求，将请求分发到合适的节点，最终把结果汇集到一起
  - 每个节点默认都起到了 Coordinating Node 的职责

  

> NOTE Coordinating Node
>
> >
> > Requests like search requests or bulk-indexing requests may involve data held on different data nodes | A search request, for example, is executed in two phases which are coordinated by the node which receives the client request — the *coordinating node*.
> >
> > In the *scatter* phase, the coordinating node forwards the request to the data nodes which hold the data | Each data node executes the request locally and returns its results to the coordinating node | In the *gather* phase, the coordinating node reduces each data node’s results into a single global result set.
> >
> > Every node is implicitly a coordinating node | This means that a node that has an explicit empty list of roles via `node.roles` will only act as a coordinating node, which cannot be disabled | As a result, such a node needs to have enough memory and CPU in order to deal with the gather phase.



***

**其它节点**

- Hot & Warm Node
  - 不同硬件配置的 Data Node, 用来实现 Hot & Warm 架构，降低集群部署的成本
- Machine Learning Node
  - 负责跑机器 学习的Job，用来做异常检测
- Tribe Node
  - （5。3 开始使用 Cross Cluster Search） Tribe Node 连接到不同的Elasticsearch集群，并且支持将这些集群当成一个独立的集群处理。



***

**配置节点类型**

- 开发环境中一个节点可以承担多种角色
- <font color=red>生产环境中，应该设置单一角色的节点（dedicated node 专用节点）</font>
  - 更好的性能
  - 职责明确，可为不同节点配置不同硬件



***

**分片（Primary Shard & Replica Shard）**

- 主分片，用以解决数据水平扩展的问题。通过主分片，可以将数据分布到集群内的所有节点上
  - 一个分片是一个运行的 Lucene 实例
  - 主分片数在索引创建时指定，后续不允许修改。除非 Reindex
- 副本，用以解决数据高可用的问题。分片是主分片的拷贝
  - 副本分片数，可以动态调整
  - 增加副本数，还可以在一定程度上提高服务的可用性（读取的吞吐）

![image-20210114113416371](C:\Users\zhuht\AppData\Roaming\Typora\typora-user-images\image-20210114113416371.png)



**分片的设定**

- <font color=red>对于生产环境中分片的设定，需要提前做好容量规划</font>
  - 分片数设置过小
    - 导致后续无法增加节点实现水平扩展
    - 单个分片的数据量太大，导致数据重新分配耗时
  - 分片数设置过大，7.0 开始，默认主分片从 5 改成了 1，解决了 `oversharding`（过渡分片） 的问题
    - 影响搜索结果的相关性打分，影响统计结果的准确性
    - 单个节点上过多的分片，会导致资源浪费，同时也会影响性能



#### 11 | 文档的基本 CRUD 与批量操作

- **type**:约定都用_doc,如 `PUT my_index/_doc/1` ,`GET my_index/_doc/1`, `DELETE my_index/_doc/1` | 注意 create 用 `PUT my_index/_create/1`, update 用 `POST my_index/_update/1`
- **create**: 如果id 已存在，会失败。 如果使用 POST 方法，可以自动生成id
- **index**: 如果id不存在，会创建新的文档。否则，先删除现有文档，再创建新文档，版本会增加
- **update**: 文档必须已经存在，更新只会对相应字段做增量修改



***

**Bulk API**

> 支持在一次API调用中，对不同的索引进行操作
>
> 支持4中操作类型
>
> > index
> >
> > create
> >
> > update
> >
> > delete
>
> 可以在 URI 中指定index，也可以在请求的 Payload中进行
>
> 操作中单条操作失败，并不影响其它操作
>
> 返回结果包括了每一条操作执行的结果



***

**常见错误返回**

| 问题         | 原因               |
| ------------ | ------------------ |
| 无法连接     | 网络故障或集群挂了 |
| 连接无法关闭 | 网络故障或节点出错 |
| 429          | 集群过于繁忙       |
| 4xx          | 请求体格式有错     |
| 500          | 集群内部错误       |



#### 12 | 倒排索引入门

**正排与倒排索引**

文档id指向文档内容即为**正排索引**

文档内容指向文档id即为**倒排索引**

**倒排索引的核心组成**

- 倒排索引包含两个部分
  - 单次词典（Term Dictionary）, 记录所有文档的单词，记录单词到倒排列表的关联关系
    - 单次词典一般比较大，可以通过 B+ 树或哈希拉链法实现，以满足高性能的插入与查询
  - 倒排列表（Posting List）记录单词对应文档的结合，由倒排索引项组成
    - 倒排索引项（Posting）
      - 文档Id
      - 词频 TF - 该单词在文档中出现的次数，用于相关性评分
      - 位置 Posting - 单词在文档中分词的位置。用于语句搜索
      - 偏移 offset - 记录单词的开始结束位置，实现高亮显示



***

**Elasticsearch 的倒排索引**

- Elasticsearch 的 JSON 文档中的每个字段，都有自己的倒排索引
- 可以执行对某些字段不做索引
  - 优点：可以节省存储空间
  - 缺点：字段无法被搜索



#### 13 | 通过分析器进行分词

**Analysis 与 Analyzer**

- Analysis - 文本分析是把全文本转换为一系列单次（term / token） 的过程，也叫分词
- Analysis 是通过 Analyzer 实现的
  - 可使用 Elasticsearch 内置的分析器， 或者按需定制分析器
- 除了在写入数据时转换词条，匹配Query语句的时候也需要相同的分析器对查询语句进行分析

***

**Analyzer 的组成**

- 分词器是专门处理分词的组件，Analyzer由三部分组成
  - Character Filters: 针对原始文本处理，例如去除html
  - Tokenizer：按照规则切分文本为单次
  - Token Filter：将切分的单词进行加工，如大小写转换、删除停用词、添加同义词

***

**Elasticsearch 的内置分词器**

- Standard Analyzer - 默认分词器，按词切分，小写处理
- Simple Analyzer - 按照非字母切分（符号被过滤），小写处理
- Stop Analyzer - 小写处理，停用词过滤（过滤掉那些出现频率高但是对检索负面影响不大的词，例如英文的停用词有 a, an, and, no, not, this 等）
- Whitespace Analyzer - 按照空格切分，不转小写
- Keyword Analyzer - 不分词，直接将输入当做输出
- Patter Analyzer - 正则表达式，默认 \W+(非字符分隔)
- Language - 提供30多种常见语言的分词器
- Customer Analyzer 自定义分词器

***

**使用 _analyzer API**

```shell
Get /_analyze
{
	"analyzer":"standard",
	"text":"xxx,xxxxxxxxx"
}
```

***

**更多的中文分词器**

- IK
- THULAC



##### 安装IK分词器插件

https://github.com/medcl/elasticsearch-analysis-ik

```shell
# 注意与es版本对应，
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.10.1/elasticsearch-analysis-ik-7.10.1.zip

# 重启es

# 测试效果
GET family_beta/_analyze
{
  "analyzer": "ik_smart",
  "text": "测试ik分词器的效果"
}

# 重建旧索引。暂时只能创建个新的
POST _reindex
{
  "source": {"index": "family_beta"},
  "dest": {"index": "family_beta2"}
}

```

**注意**

> 因为中文的分词问题，在没有安装ik分词插件的情况下，查询结果可能会与预期不符。



#### 14 | Search API 概览

- URI Search
  - 在URL 中使用查询参数
- Request Body Search
  - 使用Elasticsearch 提供的，基于JSON格式的更加完备的 Query Domain Specific Language （DSL）

***

**指定查询的索引**

| 语法                   | 范围              |
| ---------------------- | ----------------- |
| /_search               | 集群上所有索引    |
| /index1/_search        | index1            |
| /index1,index2/_search | index1和index2    |
| /index*/_search        | 以index开头的索引 |



***

**搜索的相关性 Relevance**

- 搜索是用户和搜索引擎的对话
- 用户关系的是搜索结果额相关性
  - 是否可以找到所有相关的内容
  - 有多少不相关的内容被返回了
  - 文档的打分是否合理
  - 结合业务需求，平衡结果排名

**Web 搜索**

- Page Rank 算法
  - 不仅仅是内容
  - 更重要的是内容的可信度

**电商搜索**

- 搜索引擎扮演-**销售**的角色
  - 提高用户购物体验
  - 提升网站销售业绩
  - 去库存



***

**衡量相关性**

- Information Retrieval
  - Precision（查准率）- 尽可能返回较少的无关文档
  - Recall（查全率）- 尽量返回较多的相关文档
  - Ranking - 是否能够按照相关度进行排序



#### 15 | URI Search 详解

**Query String Syntax（1）**

- 指定字段 vs 泛查询
  - 指定字段: `q=title:2012` 或 `q=2012&df=title`, 两种写法都是指定查询title字段中包含 2012 的文档。
  - 泛查询：`q=2012`, 不指定查询字段，会把所有字段中包含2012的文档都查出来
- Term vs Phrase
  - `str1 str2`: 查询两个字符串，字段中包含任意一个就行
  - `"str1 str2"`:查询两个用引号包起来的字符串，就要求字段中要有这两个字符串才行。其中 Phrase 还要求字符串顺序保持一致。
- 分组与引号
  - title:(str1 and str2)
  - title="str1 str2"

```shell
GET _search
{
  "query": {
    "match_all": {}
  }
}

GET family_beta/_search?q=钱*

GET family_beta/_search?q=name:张
{
  "profile": "true"
}

GET family_beta/_search?q=name:(张 三)
{
  "profile": "true"
}
```





#### 16 | Request Body 与 Query DSL 简介

- 将查询语句通过HTTP Request Body 发送给 Elasticsearch
- Query DSL

```shell
# 查询全部。 es默认查询最大数是 10000，可在配置文件中修改
POST family_beta/_search?
{
  "profile": "true",
  "query": {
    "match_all": {}
  }
}

# 分页, 排序
POST family_beta/_search
{
  "profile": "true",
  "from": 0,
  "size": 2,
  "sort": [
    {
      "followTime": {
        "order": "desc"
      }
    },
    {
      "historyAction.actionTime": {
        "order": "desc"
      }
    }
  ], 
  "query": {
    "match_all": {}
  }
}

# 过滤 _source .支持使用通配符
GET family_beta/_search
{
  "from": 0,
  "size": 5,
  "_source": [
    "id","name","owner","*Time"
    ],
    "query": {"match_all": {}}
}
```



##### 脚本字段

类似于MySQL内置函数

``` shell
# 拼接字符串
GET family_beta/_search
{
  "script_fields": {
    "newScriptField": {
      "script": {
        "lang": "painless",
        "source": "doc['name'].value + ' ，你好'"
      }
    }
  },
  "query": {"match_all": {}}
}
```

***

##### 条件查询

```shell
POST family_beta/_search
{
  "query": {
    "match": {
      "name": "热巴 扎提"
    }
  },
  "profile": "true"
}

POST family_beta/_search
{
  "query": {
    "match": {
      "name": {
        "query": "热巴 扎提",
        "operator": "and"
      }
    }
  },
  "profile": "true"
}

# 短语匹配
POST family_beta/_search
{
  "query": {
    "match_phrase": {
      "name": {
        "query": "热巴扎提"
      }
    }
  }
}
# slop 允许短语中多出的字符数
POST family_beta/_search
{
  "query": {
    "match_phrase": {
      "name": {
        "query": "热巴扎提",
        "slop": 4
      }
    }
  }
}

```







#### 17 | Query String & Simple Query String 查询

##### Query String

类似 URI Query

```shell
POST family_beta/_search
{
  "query": {
    "query_string": {
      "default_field": "name",
      "query": "热巴 AND 扎提 OR 伊索尔"
    }
  }
}
# 虽然这里 遥字时错的，但是根据路字依然能查出结果
POST family_beta/_search
{
  "query": {
    "query_string": {
      "fields": ["name","owner.name"],
      "query": "(热巴 AND 迪丽) AND 路遥 "
    }
  }
}
```



##### Simple Query String Query

- 类似Query String, 但是会忽略错误的语法，同时只支持部分查询语法
- 不支持 AND OR NOT ，会当做字符串处理
- Term 之间默认关系是 OR， 可以指定OPerator
- 支持部分逻辑
  - +替代AND
  - |替代OR
  - -替代NOT

```shell
POST family_beta/_search
{
  "query": {
    "simple_query_string": {
      "query": "家长 -测试",
      "fields": ["name"],
      "default_operator": "AND"
    }
  },
  "profile": "true"
}
```







#### 18 | Dynamic Mapping 和常见字段类型

##### 什么是Mapping

- Mapping 类似于数据库中的schema 的定义，作用如下
  - 定义索引中的字段名称
  - 定义字段的数据类型，例如字符串，数字，布尔.....
  - 字段，倒排索引的相关配置（Analyzed or Not Analyzed，Analyzer）
- Mapping会把JSON文档映射成Lucene所需要的扁平格式
- 一个Mapping属于索引的type
  - 每个文档都属于一个 Type
  - 一个Type有一个 Mapping定义
  - 7.0开始，不需要在Mapping定义中指定type信息

***

##### 字段的数据类型

- 简单类型
  - Text / Keyword
  - Date
  - Integer / Floating
  - Boolean
  - IPV4 & IPV6
- 复杂类型 - 对象和嵌套对象
  - 对象类型 / 嵌套类型
- 特殊类型
  - geo_point & geo_shape / percolator

***

#####  什么是 Dynamic Mapping

- 在写入文档时，如果索引不存在，会自动创建索引
- Dynamic Mapping 的机制，使得我们无需手动定义Mapping。 Elasticsearch 会自动根据文档信息推算出字段类型
- 但有时候会推算的不对，例如地理位置信息
- 当类型如果设置不对时，会导致一些功能无法正常运行。例如 Range 查询

***

##### 能否更改 Mapping的字段类型

- 两种情况
  - 新增字段
    - Dynamic 设为 true时，一旦有新增字段的文档写入，Mapping也同时被更新
    - Dynamic 设为false，Mapping 不会被更新，新增字段的数据无法被索引，但是信息会出现在 _source中
    - Dynamic 设置为 Strict， 文档写入失败
  - 对已有字段，一旦已经有数据写入，就不再支持修改字段定义
    - Lucene 实现的倒排索引，一旦生成后，就不允许修改
  - 如果希望改变字段类型，必须 Reindex API， 重建索引
- 原因
  - 如果修改了字段的数据类型，会导致已被索引的属性无法被搜索
  - 但是如果是新增字段，就不会有这样的影响

***

##### 控制 Dynamic Mappings

|                | true | false | strict |
| -------------- | ---- | ----- | ------ |
| 文档可索引     | YES  | YES   | NO     |
| 字段可索引     | YES  | NO    | NO     |
| Mapping 被更新 | YES  | NO    | NO     |



##### 示例

```shell
# 创建一个新的测试文档
PUT dynamic_mapping_test/_doc/1
{
  "newFiled": "someValue"
}

GET dynamic_mapping_test/_mapping

# 目前可以查到结果
POST dynamic_mapping_test/_search
{
  "query": {
    "match": {
      "newFiled": "someValue"
    }
  }
}

# 修改Dynamic 为 false
PUT dynamic_mapping_test/_mapping
{
  "dynamic": false
}

# 新增anotherField
PUT dynamic_mapping_test/_doc/10
{
  "anotherField": "somValue"
}
# 该字段不可以被搜索，因为 dynamic已经被设置为false
POST dynamic_mapping_test/_search
{
  "query": {
    "match": {
      "anotherField": "somValue"
    }
  }
}

GET dynamic_mapping_test/_doc/10

# 修改 dynamic 为 strict
PUT dynamic_mapping_test/_mapping
{
  "dynamic": "strict"
}

# 写入数据出错
PUT dynamic_mapping_test/_doc/12
{
  "lastField": "someValue"
}

DELETE dynamic_mapping_test
```





#### 19 | 显式 Mapping 设置与常见参数介绍

 ##### 自定义 Mapping 的一些建议

- 可以参考 API 手册，纯手写
- 为了减少输入的工作量，减少出错概率，可以依照一下步骤
  - 创建一个临时的index，写入一些样本数据
  - 通过访问 Mapping API 获取该临时文件的动态 Mapping 定义
  - 修改后，使用该配置创建索引
  - 删除临时索引



> 对于某些不想被搜索的字段，可以指定index为false，这样就不会创建倒排索引，还可以节省空间



mappings 中定义的属性，如果允许为null，可以使用 `null_value`属性定义null的代表值，之后就可以通过 field = "Null" 来查询 null值。

```shell
"null_value": "Null"
```



如果需要将某些字段组合在一起，可以使用 `copy_to` 属性，定义一个组合字段名，但是此字段名只能在查询时用，返回的查询结果文档中没有此字段

```shell
 {
   "firstName":{
     "type": "text",
     "copy_to": "fullname"
   },
   "lastName":{
     "type": "text",
     "copy_to": "fullname"
   }
 }
```





#### 20 | 多字段特性及 Mapping 中配置自定义 Analyzer

##### 多字段类型

```shell
PUT products
{
  "mappings":{
    "properties": {
      "company":{
        "type": "text",
        "fields": {
          "keyword":{
            "type": "keyword",
            "ignore_above":256
          }
        }
      },
      "comment":{
        "type": "text",
        "fields":{
          "english_comment":{
            "type": "text",
            "analyzer": "english",
            "search_analyzer": "english"
          }
        }
      }
    }
  }
}
```

- 厂商名字实现精确匹配
  - 增加一个 keyword 字段
- 使用不同的analyzer
  - 不同语言
  - pinyin 字段的搜索
  - 还支持为 搜索 和 索引 指定不同的 analyzer

***

##### Exact Values VS Full Text

- Exact Value: 包括数字 / 日期 / 具体一个字符串（例如"Apple Store"），不需要被分词
  - Elasticsearch 中的 keyword
- 全文本，非结构化的文本数据
  - Elasticsearch 中的 text



***

#####  Character Filters

- 在 Tokenizer 之前对文本进行处理，例如增加删除及替换字符。可以配置多个 Character Filter。 会影响 Tokenizer 的 position 和offset 信息
- 一些自带的 Character Filters
  - html_strip：去除 html标签
  - Mapping：字符串替换
  - Pattern replace：正则匹配替换



***

##### Tokenizer

- 将原本的文本按照一定的规则切分为词（trem or token）
- Elasticsearch 内置的 Tokenizer
  - whitespace / standard / uax_url_email / pattern / keyword（不做处理） / path hierarchy（例如文件路径）
- 可以用Java开发插件，实现自己的 Tokenizer

***

##### Token Filters

- 将 Tokenizer 输出的单词（term），进行增加，修改，删除
- 自带的 Token Filters
  - Lowercase / stop / synonym(添加近义词)

***

##### 示例

```shell
# html_strip 去除html标签
POST _analyze
{
  "tokenizer": "keyword",
  "char_filter": ["html_strip"],
  "text": "<b>Hello World</b>"
}

# mapping 替换字符
POST _analyze
{
  "tokenizer": "standard",
  "char_filter": [
    {
      "type": "mapping",
      "mappings": ["- => _"]
    }
    ],
    "text":"123-456, I-test!"
}

# 正则
GET _analyze
{
  "tokenizer": "standard",
  "char_filter": [
    {
      "type": "pattern_replace",
      "pattern": "http://(.*)",
      "replacement":"$1"
    }
    ],
    "text": "http://www.elastic.co"
}

# 路径拆分
POST _analyze
{
  "tokenizer": "path_hierarchy",
  "text": "/user/local/etc"
}

# 由于 开头的 The 首字母是大写，所以只有加入 lowercase 先转小写后，the才会被当做 stopword 删除
GET _analyze
{
  "tokenizer": "whitespace",
  "filter": ["lowercase","stop"],
  "text":"The gilrs in China are playing this game!"
}
```



#### 21 | Index Template 和 Dynamic Template

##### 什么是 Index Template

- index Templates，帮助你设定mappings 和 Settings，并按照一定规则，自动匹配到新创建的索引上
  - 模板仅在一个索引被创建时，产生作用。修改模板不会影响已创建的索引
  - 你可以设置多个索引模板，这些设置会被 merge 在一起
  - 你可以指定 order 的数值，控制 merging 的过程

##### Index Template 的工作方式

- 当一个索引被创建时
  - 应用Elasticsearch 默认的 settings 和 mappings
  - 应用 order 值低的 Index Template 中的设定
  - 应用 order 值高的 Index Template 中的设定，之前的设定会被覆盖
  - 应用创建索引时，用户所指定的 Settings 和 Mappings，并覆盖之前模板中的设定



***

##### 什么是 Dynamic Template

- 根据 Elasticsearch 识别的数据类型，综合字段名称，来动态设定字段类型
  - 所有字符串类型都设定成 keyword， 或者关闭 keyword 字段
  - is 开头的字段都设置成 boolean
  - long 开头都设置成 long 类型
- Dynamic Template 是定义在某个索引的Mapping中
- Template有一个名称
- 匹配规则是一个数组
- 为匹配到的字段设置 Mapping



```shell
PUT dynamic_template_test
{
  "mappings":{
    "dynamic_templates":[
      {
        "full_name":{
          "path_match": "name.*",
          "path_unmatch": "*.middle",
          "mapping": {
            "type": "text",
            "copy_to": "full_name"
          }
        }
      }
      ]
  }
}
```









#### 22 | Elasticsearch 聚合分析简介

##### 什么是聚合 Aggregation

- Elasticsearch 出搜索以外，提供的针对ES数据进行统计分析的功能
  - 实时性高
  - Hadoop（T+1）
- 通过聚合，我们会得到一个数据的概览，是分析和总结全套的数据，而不是寻找单个文档
  - 尖沙咀和香港岛的客房数量
  - 不同的加个区间，可预订的酒店数量
- 高性能，只需要一条语句，就可以从 Elasticsearch 得到分析结果
  - 无需在客户端自己去实现分析逻辑



> Kibana 可视化报表大多是通过ES聚合实现



##### 聚合的分类

- Bucket Aggregation：一些列满足特定条件的文档的聚合
  - 类似 SQL中的 group
- Metric Aggregation：一些数学允许，可以对文档字段进行统计分析
  - 类似SQL中的 count
- Pipeline Aggregation：对其他聚合结果进行二次聚合
- Matrix Aggregation：支持对多个字段操作并返回一个结果矩阵



```shell
GET family_beta/_search
{
  "size": 0,
  "aggs": {
    "tenant_count": {
      "terms": {
        "field": "tenantId"
      }
    }
  }
}



GET family_beta/_mapping


GET _cat/indices

DELETE products

# 字段需要排序，聚合或脚本访问时，需要设置fielddata=true，需要注意它可能会消耗大量堆空间
POST family_beta/_mapping
{
  "properties":{
    "tenantId":{
      "type": "text",
      "fielddata": true
    }
  }
}


GET kibana_sample_data_flights/_mapping

# 聚合航班目的地
GET kibana_sample_data_flights/_search
{
  "size": 0, 
  "aggs":{
    "flight_dest":{
      "terms": {
        "field": "DestCountry"
      }
    }
  }
}

# 查看航班目的地统计信息，增加均价，最高最低价
GET kibana_sample_data_flights/_search
{
  "size": 0,
  "aggs": {
    "flight_dest": {
      "terms": {
        "field": "DestCountry"
      },
      "aggs":{
      "average_price":{
        "avg": {
          "field": "AvgTicketPrice"
        }
      },
      "max_price":{
        "max": {
          "field": "AvgTicketPrice"
        }
      },
      "min_price":{
        "min": {
          "field": "AvgTicketPrice"
        }
      }
    }
    }
  }
}
```



#### 23 | 第一部分总结

##### 产品与使用场景

- Elasticsearch是一个开源的分布式搜索与分析引擎，提供了近实时搜索和聚合两大功能
- Elastic Stack 包括 Elasticsearch, Kibana, Logstash, Beats 等一系列产品。Elasticsearch是核心引擎，提供了海量数据存储，搜索和聚合的能力。Beats是轻量的
  数据采集器，Logstash用来做数据转换，Kibana则提供了丰富的可视化展现与分析的功能。
- Elastic Stack主要被广泛使用于：搜索,日志皆理,安全分析，指标分析，业务分析,
  应用性能监控等多个领域
- Elastic Stack开源了X-Pack在内的相关代码。作为商业解决方案，X-Pack的部分功能 
  需要收费。Elastic公司从6.8和7.1开始，Security功能也可以免费使用
- 相比关系型数据库，Elasticsearch提供了如模糊查询，搜索条件的算分第等关系型数据 
  库所不擅长的功能，但是在事务性等方面，也不如关系型数据库来的强大。因此，在实际 生产环境中，需要考虑具体业务要求，综合使用

***

##### 基本概念

- 一个Elasticsearch 集群可以运行在单节点上，也支持运行在多个服务器上，实现数据和服务的水平扩展
- 从逻辑角度看，索引是一些具有相似结构的文档集合
- 物理角度看，分片是一个 Lucene 的实例。分片存储了索引的具体数据，分片可以分布在不同的节点之上。副本分片除了提高数据的可靠性，还能一定程度上提高集群的查询性能
- Elasticsearch 的文档可以是任意的JSON格式数据
- 将文档写进Elasticsearch 的过程叫索引 indexing
- Elasticsearch 提供 REST API 和 Transport API 两种，建议使用 REST API

***

##### 搜索和聚合

- Precision 指除了相关的结果，还返回了多少不相关的结果
- Recall： 衡量有多少相关的结果，实际上并没有返回
- 精确值包括：数字，日期和某些具体的字符串
- 全文本：是需要被检索的非结构文本
- Analysis 是将文本转换成倒排索引中的 terms的过程
- Elasticsearch 中的 Analyzer 是 Char_filter --> Tokenizer --> Token Filter 的过程
- 要善于利用 _analyze API 去测试 Analyzer
- Elasticsearch 搜索支持 URI Search 和 REST Body 两种方式
- Elasticsearch 提供了Bucket、Metric、Pipeline、Matrix 四种方式的聚合

***

##### 文档CRUD 与 Index Mapping

- 除了CRUD操作外，Elasticsearch还提供了 bulk，mget 和 msearch等批量操作。批量操作可以有效的提高性能，但是单次操作的数据量不宜过大，以免引发性能问题。
- 每个索引都有一个 Mapping 定义。包含文档的字段及类型，字段的 Analyzer 的相关配置
- Mapping 可以被动态的创建，为了避免一些错误的类型推算，或者为了满足特定的需求，可以显式的定义Mapping
- 可以在Mapping中定制Analyzer
- 可以为字段指定自定义的 analyzer，也可以为查询字符串指定 search_analyzer
- Index Template 可以定义 Mapping 和 Settings，并自动的应用到新创建的索引上，建议要合理的使用 Index Template
- Dynamic Template 支持在具体索引上指定规则，为新增加的字段指定相应的Mappings

***



##### 第一部分自测（一）

1. **判断题：ES支持使用HTTP PUT 写入新文档，并通过 Elasticsearch 生成文档id**

   解析：错。需要使用POST

2. **判断题：Update 一个 文档，需要使用 HTTP PUT**

   解析：错。update文档也得用POST， PUT只能用来做 Index或者 Create

3. **判断题：Index 一个已存在的文档，旧的文档会先被删除，新的文档再被写入，同时版本号加1**

   解析：对

4. **尝试描述 创建一个新的文档到一个不存在的索引中，背后会发生一些什么？**

   解析：默认情况下，会创建相应的索引，并且自己设置Mapping，当然，实际情况还要看是否有合适的Index Template

5. **ES 7 中的合法的 type 是什么？**

   解析：_doc

6. **精确值和全文本的本质区别是什么？**

   解析：精确值不会被 Analyzer分词，全文本会

7. **Analyzer 由哪几个部分组成？**

   解析：Character Filter + Tokenizer + Token Filter



##### 第一部分自测（二）

1. **尝试描述 match 和 match_phrase 的区别**

   解析：match 中的 terms 之间是 or 的关系，match phrase 的 terms之间是 and 的关系，而且terms 之间的位置也会影响搜索结果

2. **如果你希望 match_phrase 匹配到更多的结果，你应该配置查询中的什么参数？**

   解析：slop。slop 允许短语中多出的字符数

3. **如果 Mapping 的 dynamic 设置成 "strict", 索引一个包含新增字段的文档时会发生什么？**

   解析：直接报错

4. **如果 Mapping 的 dynamic 设置成 "false", 索引一个包含新增字段的文档时会发生什么？**

   解析：文档可以被索引，_source中可以看到新字段，但是新字段无法被搜索

5. **判断题：可以把一个字段的类型从 integer 改成 long，因为这两个类型是兼容的**

   解析：错。修改字段类型，只能 reindex

6. **判断题：你可以在Mapping 文件中为 indexing 和 searching指定不同的analyzer**

   解析：对。

7. **判断题：字段类型为 text的字段，一定可以被全文搜索**

   解析：错。可以通过为 text 类型的字段指定 Not Indexed，使其无法被搜索











## 第二部分：深入了解 Elasticsearch
### 第 4 章：深入搜索
#### 24 | 基于词项和基于全文的搜索

##### 基于Term的查询

- Term 的重要性
  - Term 是表达语意的最小单位。搜索和利用统计语言模型进行自然语言处理都需要处理term
- 特点
  - Term Level Query：Term Query / Range Query / Exists Query / Prefix Query / Wildcard Query
  - 在 ES中，term查询，对输入不做分词。会将输入作为一个整体，在倒排索引中查找准确的词项，并且使用相关度算分公式，为每个包含该词项的文档进行 相关度算分
  - 可以通过 Constant Score 将查询转换成一个 Filtering， 避免算分，并利用缓存，提高性能

> Term 查询不会对输入项做分词，也不会将其处理为小写。如果想对字段进行全匹配查询，需要查询字段的 .keyword



##### 基于全文本查询

- Match Query / Match Phrase Query / Query String Query
- 特点
  - 索引和搜索时都会进行分词，查询字符串先传递到一个合适的分词器，然后生成一个供查询的词项列表
  - 查询对每个词项逐个进行底层查询，再将结果进行合并。并为每个文档生成一个算分







#### 25 | 结构化搜索

##### 结构化数据

- 结构化搜索（Structured search）是指对结构化数据的搜索
  - 日期，布尔类型和数字都是结构化的
- 文本也可以是结构化的
  - 如彩笔可以有离散的颜色集合
  - 博客被标记的标签



##### ES 中的结构化搜索

- 布尔、时间、日期、数字这类结构化的数据，有精确的格式，我们可以对这些格式进行逻辑操作。包括比较数字或时间的范围，或判定两个值的大小。
- 结构化的文本可以做精确匹配或部分匹配
  - Term查询 / Prefix 前缀查询
- 结构化结果只有 “是” 或 “否” 两个值
  - 根据场景需要，可以决定结构化搜索是否需要打分



**日期 Range**

| y    | 年   |
| ---- | ---- |
| M    | 月   |
| w    | 周   |
| d    | 天   |
| H/h  | 小时 |
| m    | 分钟 |
| s    | 秒   |





#### 26 | 搜索的相关性算分

##### 相关性和相关性算分

- 相关性 - Relevance
  - 搜索的相关性算分，描述了一个文档和查询语句的匹配程度。ES会对每个结果进行算分 _score
  - 打分的本质是排序，需要把最符合用户需求的文档排在前面。ES 5 之前，默认的相关性算分采用 TF-IDF, 现在采用 BM 25



**示例：搜索 “区块链的应用”**

| 查询语句被分词后的term | 文档（Doc Id）               |
| ---------------------- | ---------------------------- |
| 区块链                 | 1,2,3                        |
| 的                     | 2,3,4,5,6,7,8,10,15,18,19,20 |
| 应用                   | 2,3,8,9,10,13                |



##### 词频 TF

- Term Frequency: 检索词在一篇文档中出现的频率
  - 检索词出现的次数除以文档的总字数
- 度量一条查询和结果文档相关性的简单方法：简单将搜索中的每个词的TF相加
- Stop Word
  - 某些检索词在文档中出现了很多次，但是对共享相关度几乎没有用处，不应该考虑它们的TF

##### 逆文档频率 IDF

- DF：检索词在所有文档中出现的频率
  - "区块链"在相对较少的文档中出现
  - "应用"在相对较多的文档中出现
  - "的"在大量文档中出现
- Inverse Document Frequency：简单说 = log(全部文档数/检索词出现过的文档总数)
- TF-IDF 本质上就是将 TF 求和变成了加权求和



|        | 出现的文档数 | 总文档树 | IDF             |
| ------ | ------------ | -------- | --------------- |
| 区块链 | 200万        | 10亿     | log(500) = 8.96 |
| 的     | 10亿         | 10亿     | log(1) = 0      |
| 作用   | 5亿          | 10亿     | log(2) = 1      |



##### BM 25

- 从 ES 5 开始，默认算法改为 BM 25
- 和经典的 TF-IDF 相比，当 TF 无限增加时， BM25算分会趋于一个数值



##### Boosting Relevance

- Boosting 是控制相关度的一种手段
  - 索引，字段或查子条件
- 参数boost的含义
  - 当boost > 1时，打分的相关度相对性提高
  - 当 0 < boost < 1 时，打分的权重相对性降低
  - 当 boost < 0 时，贡献负分





#### 27 | Query & Filtering 与多字符串多字段查询

query context：相关性算分

filter context：不需要算分，yes or no



**bool 查询**

子查询可以任意顺序出现

可以嵌套多个查询



**boosting 查询**

通过 "boost" : 2 指定搜索字段的权重，进而影响结果排序



#### 28 | 单字符串多字段查询：Dis Max Query

**Disjunction Max Query**：最大化查询。*将任何与任一查询匹配的文档作为结果返回，但只将最佳匹配的评分作为查询的评分结果返回* 

通过 "tie_breaker": 0.1 影响字段评分



#### 29 | 单字符串多字段查询：Multi Match

##### 三种场景

- 最佳字段（Best Fields）
  - 当字段之间相互竞争，又相互关联。例如 博客中的 title 和 body 这样的字段。评分来自最匹配字段
- 多数字段（Most Fields）
  - 处理英文内容时：一种常见的手段是，在主字段（English Analyzer），抽取词干，加入同义词，已匹配更多的文档。相同的文本，加入子字段（Standard Analyzer），以提供更精确的匹配。其他字段作为匹配文档提高相关度的信号。匹配字段越多越好。
- 混合字段（Cross Field）
  - 对于某些实体，例如人名，地址，图书信息。需要在多个字段中确定信息，单个字段只能作为整体的一部分。希望在任何这些列出的字段中找出尽可能多的词。
  - 与 copy_to 类似,但是会省空间，还可以单独指定每个字段的权重





#### 30 | 多语言及中文分词与检索

##### 自然语言与查询 Recall

- 当处理人类自然语言时，有些情况，尽管搜索和原文不完全匹配，但是希望搜到一些内容
  - Quick brown fox 和 fast brown fox / Jumping fox 和 Jumped fox
- 一些可采取的优化
  - 归一化词元：清除变音符号
  - 抽取词根：清除单复数和时态的差异
  - 包含同义词
  - 拼写错误：拼写错误，或者同音异形词



##### 混合多语言的挑战

- 一些具体的多语言场景
  - 不同的索引使用不同的语言 、同一个索引中，不同的字段使用不同的语言、一个文档的一个字段内混合不同的语言
- 混合语言存在的一些挑战
  - 词干提取：以色列文档，包含希伯来语，阿拉伯语，俄语和英语
  - 不正确的文档频率：英文为主的文章中，德文算分高（稀有）
  - 需要判断用户搜索时使用的语言，语言识别（Compact Language Detector）
    - 例如根据语言查询不同的索引



##### 分词的挑战

没有统一的标准。

中文分词更麻烦，有时需要根据语义区分。



中文分词器的演化，【字典法】。但是无法解决 词的 “二义性”。之后演变为【基于统计法的机器学习算法】。目前可以认为中文分词的问题已基本解决。



##### HanLP

https://hanlp.hankcs.com/

##### IK

##### pinyin



#### 31 | Space Jam，一次全文搜索的实例

数据源：TMDB 数据库

分词器对搜索结果很重要。



#### 32 | 使用 Search Template 和 Index Alias 查询

#####  Search Template -- 解耦程序 & 搜索 DSL

- Elasticsearch 的查询语句
  - 对相关性算分 / 查询性能都至关重要
- 在开发初期，虽然可以明确查询参数，但是往往还不能最终定义查询的DSL的具体结构
  - 通过 Search Template 定义一个 Contract
- 各司其职，解耦
  - **开发人员 / 搜索工程师 / 性能工程师**



https://www.elastic.co/guide/en/elasticsearch/reference/7.13/create-stored-script-api.html

```shell
POST _scripts/family
{
  "script":{
    "lang":"mustache",
    "source": {
      "_source":[
        "name","phone","historyAction"
        ],
        "size":20,
        "query":{
          "multi_match":{
            "query":"{{q}}",
            "fields":["name","phone"]
          }
        }
    }
  }
}

GET _scripts/family

POST family_beta/_search/template
{
  "id":"family",
  "params":{
    "q":"18736012371"
  }
}
```



##### Index Alias 索引别名

https://www.elastic.co/guide/en/elasticsearch/reference/7.13/indices-aliases.html



- 实现零停机运维
- 





#### 33 | 综合排序：Function Score Query 优化算分

可以在查询结束后，对每一个匹配的文档进行一系列的重新算分，根据新生成的分数进行排序。

**几种默认计算分值函数**

- weight：为每一个文档设置一个简单而不被规范化的权重
- field value factor：使用该数值来修改 _score,例如将“热度”和“点赞数”作为算分的参考因素
- random score：为每一个用户使用一个随机的，不同算分结果
- 衰减函数：以某个字段的值为标准，距离某个值越近，得分越高
- script score：自定义脚本完全控制所需逻辑



**使用 Modifier 平滑算分**





#### 34 | Term & Phrase Suggester

搜索建议，将输入的文本分解为token，然后在索引的字典里查找相似的term并返回。

ES 设计了4中类别的 Suggester

- Term & Phrase Suggester
- Complete & Context Suggester



**几种 Suggestion Mode**

- Missing -- 如索引中已经存在，就不提供建议
- Popular -- 推荐出现频率更加高的词
- Always -- 无论是否存在，都提供建议





#### 35 | 自动补全与基于上下文的提示

##### The Completion Suggester

- Completion Suggester 提供了自动完成（Auto Complete）的功能。用户每输入一个字符就需要向后端发送一个查询请求查找匹配项
- 对性能要求比较苛刻。Elasticsearch 采用了不同的数据结构，并非通过倒排索引来完成。而是将 Analyze 的数据编码成FST 和索引一期存放。FST 会被整个加载进内存，速度很快。
- FST[^FST] 只能用于前缀查找



##### 什么是 Context Suggester

- Completion Suggester 的扩展
- 可以在搜索中加入更多的上下文信息，例如，输入 “star”
  - 咖啡相关：建议 “starbucks”
  - 电影相关：“star wars”



需要设置索引的mapping



#### 36 | 配置跨集群搜索

##### 水平扩展的痛点

- 单集群：当水平扩展时，节点数不能无线增加
  - 集群的 meta信息（节点，索引，集群状态）过多时，会导致更新压力变大，单个 active master 会成为性能瓶颈，导致整个集群无法正常工作
- 早起版本，通过 tribe node 可以实现多集群访问的需求，但是还存在一定的问题
  - Tribe Node 会以 Client Node 的方式加入每个集群。集群中 Master 节点的任务变更需要 tribe node 的回应才能继续
  - tribe node 不保存 cluster state 信息，一旦重启，初始化很慢
  - 当多个集群存在索引重名的情况时，只能设置一种 prefer 规则



**早期的 Tribe Node 方案存在问题，已过时。ES 5.3 引入了跨集群搜索的功能（Cross Cluster Search）,在集群做一些配置，就可以在一个集群搜索另一集群的数据**







### 第 5 章：分布式特性及分布式搜索的机制
#### 37 | 集群分布式模型及选主与脑裂问题

##### 节点

- 节点是一个 Elasticsearch 的实例
  - 其本质上就是一个 JAVA 进程
  - 一台机器上可以运行多个Elasticsearch 进程，但是生产环境上，建议一台机器只运行一个实例。
- 每个节点都有名字，通过配置文件配置，或者启动的时候 `-E node.name=custome_name`指定
- 每一个节点在启动之后，会分配一个UID，保存在data目录下



##### Coordinating Node

- 处理请求的节点，叫coordinating node
  - 路由请求到正确的节点，例如创建索引的请求，需要路由到Master节点
- 所有节点默认都是Coordinating Node
- 通过将其他类型设置成 False，使其成为 Dedicated Coordinating Node



##### Master Node

- master node 职责
  - 处理创建，删除索引等请求 / 决定分片被分配到哪个节点 / 负责索引的创建与删除
  - 维护并且更新 Cluster State
- master node 的最佳实践
  - master节点非常重要，在部署上需要考虑解决单点的问题
  - 为一个集群设置多个master 节点 / 每个节点只承担master的单一角色



##### 集群状态

- 集群状态信息（Cluster State），维护一个集群中必要的信息
  - 所有的节点信息
  - 所有的索引和其他相关的 Mapping 与 Setting 信息
  - 分片的路由信息
- 在每个节点上都保存了集群的状态信息
- 但是只有 master 节点才能修改集群的状态信息，并负责同步给其他节点
  - 因为任意节点都能修改信息的话，会导致 Cluster State 信息的不一致



##### Master Eligible Node & 选主过程

- 互相ping 对方，Node id 低的会成为被选举的节点
- 其他节点会加入集群，但是不承担 Master 节点的角色。一旦发现被选中的主节点丢失，就会选举出新的 master 节点



##### 脑裂问题

- Split-Brain，分布式系统的经典网络问题，当出现网络问题，一个节点和其他节点无法连接，假如原 Node1 是master，它与其它节点断开网络
  - Node2 和 Node3 会重新选举 master
  - Node1 自己还是作为 master，组成一个集群，同时更新 cluster state
  - 导致2个master，维护不同的 cluster state。当网络恢复时，无法选择正确恢复

**7.x 之后的ES，基本不用在意脑裂问题**





#### 38 | 分片与集群的故障转移

##### Primary Shard -- 提升系统存储容量

- 分片是 Elasticsearch 分布式存储的基石
  - 主分片 / 副本分片
- 通过主分片，将数据分布在所有节点上
  - Primary Shard，可以将一份索引的数据，分散在多个 Data Node 上，实现存储的水平扩展
  - 主分片数在索引创建时指定，后续默认不能修改。只能通过重建索引修改

##### Replica Shard -- 提高数据可用性

- 数据可用性
  - 通过引入副本分片提高数据的可用性。一旦主分片丢失，副本分片可以 promote 成主分片。副本分片数可以动态调整。每个节点上都有完备的数据。如果不设置副本分片，一旦节点硬件故障，就有可能数据丢失
- 提升系统的读取性能
  - 副本分片由主分片同步。通过支持增加 replica 个数，一定程度可以提高读取的吞吐量



##### 集群健康状态

- green：健康，所有主分片和副本分片都可用
- yellow：亚健康，所有主分片可用，部分副本分片不可用
- red：不健康，部分主分片不可用





#### 39 | 文档分布式存储

##### 文档存储在分片上

- 文档会存储在某个具体的主分片和副本分片上。例如 文档1，会存储在 P0 和 R0上
- 文档到分片的映射算法
  - 确保文档能均匀分布在所用分片上，充分利用硬件资源，避免部分机器空闲，部分机器繁忙
  - 潜在的算法
    - 随机 / Round Robin。 当查询 文档1时，如果分片数很多，需要多次查询
    - 维护文档到分片的映射关系，当文档数据量大的时候，维护成本高
    - 实时计算，通过 文档1，自动算出需要去哪个分片上获取文档

 

##### 文档到分片的路由算法

- shard  = hash(_routing) % number_of_primary_shards
  - Hash 算法确保文档均匀分散到分片中
  - 默认的_routing 值是文档 id
  - 可以自行指定 routing数值，例如相同国家的商品，都分配到指定的 shard
  - 设置 index settings 后， primary 数，不能随意修改的根本原因





#### 40 | 分片及其生命周期

##### 倒排索引不可变性

- 倒排索引采用 Immutable Design，一旦完成，不可更改
- 不可变性的优势
  - 无需考虑并发写文件的问题，避免了锁机制带来的性能问题
  - 一旦读入内核的文件系统缓存，便留在那里。只要文件系统存有足够的空间，大部分请求就会直接请求内存，不会命中磁盘，提升了很大的性能
  - 缓存容易生成和维护，数据可以被压缩
- 不可变性的问题
  - 如果要让一个新的文档可以被索引，需要重建整个索引



##### Lucene Index

- 在 Lucene 中，单个倒排索引文件被称为 Segment。 Segment 是自包含的，不可变更的。多个 Segment 汇总在一起，称为Lucene 的 Index，其对应的就是ES中的shard
- 当有新文档写入时，会生成新的 Segment，查询时会同时查询所有 Segment，并且对结果汇总。Lucene 中有一个文件，用来记录所有Segment信息，叫做 Commit Point
- 删除的文档信息，保存在 .del 文件中



##### 什么是 Refresh

- 将 index Buffer 写入 segment 的过程叫 refresh。 refresh 不执行 fsync 操作
- refresh 频率：默认 1 秒 1 次，可通过 `index.refresh_interval`配置。refresh后数据就可以被搜索。这也是为什么ES被称为近实时搜索
- 如果系统有大量的数据写入，那就会产生很多的segment
- index Buffer 被占满时，会触发 refresh，默认值是 JVM 的10%



##### 什么是 Transaction Log

- segment 写入磁盘的过程相对耗时，借助文件系统缓存， refresh 时，先将 segment写入缓存开放查询
- 为了保证数据不丢失。所以在 index 文档时，同时写 transaction log。高版本开始，transaction log 默认落盘。每个分片由一个 transaction log
- 在 ES refresh 时，index Buffer 会被清空， transaction log不会清空



##### 什么是 Flush

- 调用 refresh， index Buffer 清空并且 refresh
- 写入 fsync，将缓存中的 segment 写入磁盘
- 清空（删除） transaction log
- 默认 30 分钟 调用一次
- transaction log 满后自动触发（默认 512MB）



##### Merge

- segment 很多，需要被定期合并
  - 减少 segment / 删除已经删除的文档
- ES 和 Lucene 会自动进行 Merge 操作
  - 手动触发 `POST my_index/_forcemerge`





#### 41 | 剖析分布式查询及相关性算分

##### Query 阶段

- 用户发出搜搜请求到ES节点。节点在收到请求后，会以 coordinating 节点的身份，在 6 个 主副分片中随机选择 3 个 发送查询请求。
- 被选中的分片执行查询，进行排序。然后，每个分片都会返回 from + size 个排序后的文档id 和 排序值给 coordinating 节点

##### Fetch 阶段

- coordinating node 会将 query 阶段，从每个分片获取的排序后的文档id列表，重新进行排序。选取 from 到 from + size 个文档的id
- 以 multi get 请求的方式，到相应的分片获取详细的文档数据



##### Query Then Fetch 潜在的问题

- 性能问题
  - 每个分片上需要查询的文档个数 = from + size
  - 最终协调节点需处理：number_of_shard * (from + size)
  - 深度分页
- 相关性算分
  - 每个分片都基于自己的分片上的数据进行相关度计算，会导致打分偏离的情况，特别在数据量很少，且主分片很多的情况下。

##### 解决算分不准的问题

- 数据量不大时，将主分片数设置为1
  - 当数据量足够大时，只要保证文档均匀分散在各个分片上，结果一般就不会出现偏差
- 使用 DFS Query Then Fetch
  - 搜索的URL中指定参数 `_search?search_type=dfs_query_then_fetch`
  - 到每个分片把各个分片的词频和文档频率进行搜集，然后完整的进行一次相关性算分，耗费更多的CPU和内存，执行性能低下，一般不建议使用







#### 42 | 排序及 Doc Values & Fielddata

##### 排序过程

- 排序是针对文档原始内容进行的。倒排索引无法发挥作用
- 需要用到正排索引。通过文档id和字段，快速得到字段 原始内容
- ES 有两种实现方法
  - fielddata
  - Doc Values（列式存储，对 Text类型无效）

|          | Doc values                         | Field data                                      |
| -------- | ---------------------------------- | ----------------------------------------------- |
| 创建时间 | 索引创建时，和倒排索引一起创建     | 搜索时动态创建                                  |
| 创建位置 | 磁盘空间                           | JVM Heap                                        |
| 优点     | 避免占用大量内存                   | 索引速度快，不占用额外磁盘空间                  |
| 缺点     | 降低了索引速度，占用了额外磁盘空间 | 文档过多时，动态创建开销大，占用过多的 JVM Heap |
| 缺省值   | ES 2.x 之后                        | ES 1.x 及之前                                   |



##### 关闭 Doc Values

```shell
PUT family_beta/_mapping
{
  "properties":{
    "orgId":{
      "type": "keyword",
      "doc_values":false
    }
  }
}
```

- 默认启用，可以通过 mapping 设置关闭
  - 增加索引的速度
  - 减少磁盘空间的占用
- 如果需要重新打开，只能重建索引
- 什么时候关闭
  - 明确不需要做排序及聚合分析



#### 43 | 分页与遍历：From, Size, Search After & Scroll API

##### From / Size

- 默认情况下，查询按照相关度算分排序，返回前10条记录
- 容易理解的分页方案
  - From：开始位置
  - Size：期望获取文档的总数



##### 分布式系统中深度分页问题

- ES天生就是分布式的。查询信息，但是数据分别保存在多个分片，多台机器上，ES天生就需要满足排序的需求（按照相关性算分）
- 当一个查询：From = 990， Size = 10
  - 会在每个分片上都先获取1000个文档。然后通过 Coordinating Node 聚合所有结果。最后再通过排序选取前 1000 个文档。
  - 页数越深，占用内存越多。为了避免深度分页带来的内存开销，ES默认设定到 10000个文档。

```shell
 POST family_beta/_search
 {
   "from": 100,
   "size": 20000,
   "query": {"match_all": {}}
 }
```



##### Search After 避免深度分页的问题

- 避免深度分页的性能问题，可以实时获取下一页文档信息
  - 不支持指定页数
  - 只能往下翻
- 第一步搜索需要指定sort，并且保证值是唯一的（可以通过加入 _id 保证唯一性）
- 翻页时使用上一页查出的最后一个文档的 sort 值继续查询



##### 不同的搜索类型和使用场景

- Regular
  - 需要实时获取顶部的部分文档。例如查询最新的订单
- Scroll
  - 需要全部文档，例如导出数据
- Pagination
  - From 和 Size
  - 如果需要深度分页，则选用 Search After









#### 44 | 处理并发读写操作

##### ES的乐观并发控制

- ES 中的文档是不可变更的。如果你更新一个文档，会将文档标记为删除，同时增加一个全新的， version + 1的文档
- 内部版本控制
  - if_seq_no + if_primary_term
- 使用外部版本（使用其他数据库作为主要数据存储）
  - version + version_type=external











### 第 6 章：深入聚合分析
#### 45 | Bucket & Metric 聚合分析及嵌套聚合
#### 46 | Pipeline 聚合分析
#### 47 | 作用范围与排序
#### 48 | 聚合分析的原理及精准度问题
### 第 7 章：数据建模
#### 49 | 对象及 Nested 对象
#### 50 | 文档的父子关系
#### 51 | Update By Query & Reindex API
#### 52 | Ingest Pipeline & Painless Script
#### 53 | Elasticsearch 数据建模实例
#### 54 | Elasticsearch 数据建模最佳实践
#### 55 | 第二部分总结回顾
## 第三部分：管理 Elasticsearch 集群
### 第 8 章：保护你的数据
#### 56 | 集群身份认证与用户鉴权
#### 57 | 集群内部安全通信
#### 58 | 集群与外部间的安全通信
### 第 9 章：水平扩展 Elasticsearch 集群
#### 59 | 常见的集群部署方式
#### 60 | Hot & Warm 架构与 Shard Filtering
#### 61 | 如何对集群进行容量规划
#### 62 | 分片设计及管理
#### 63 | 在私有云上管理 Elasticsearch 集群的一些方法
#### 64 | 在公有云上管理与部署 Elasticsearch 集群
### 第 10 章：生产环境中的集群运维
#### 65 | 生产环境常用配置与上线清单
#### 66 | 监控 Elasticsearch 集群
#### 67 | 诊断集群的潜在问题  
#### 68 | 解决集群 Yellow 与 Red 的问题
#### 69 | 提升集群写性能
#### 70 | 提升进群读性能
#### 71 | 集群压力测试
#### 72 | 段合并优化及注意事项
#### 73 | 缓存及使用 Breaker 限制内存使用
#### 74 | 一些运维的相关建议
### 第 11 章：索引生命周期管理
#### 75 | 使用 Shrink 与 Rollover API 有效管理时间序列索引
#### 76 | 索引全生命周期管理及工具介绍   
## 第四部分：利用 ELK 做大数据分析
### 第 12 章：用 Logstash 和 Beats 构建数据管道
#### 77 | Logstash 入门及架构介绍

#### 78 | 利用JDBC插件导入数据到Elasticsearch 

#### 79 | Beats 介绍
### 第 13 章：用 Kibana 进行数据可视化分析
#### 80 | 使用 Index Pattern 配置数据
#### 81 | 使用 Kibana Discover 探索数据
#### 82 | 基本可视化组件介绍
#### 83 | 构建 Dashboard  
## 第 14 章：探索 X-Pack 套件
#### 84 | 用 Monitoring 和 Alerting 监控 Elasticsearch 集群
#### 85 | 用 APM 进行程序性能监控
#### 86 | 用机器学习实现时序数据的异常检测（上）
#### 87 | 用机器学习实现时序数据的异常检测(下）
#### 88 | 用 ELK 进行日志管理
#### 89 | 用 Canvas 做数据演示

## 第五部分：应用实战工作坊  
### 实战 1：电影搜索服务
#### 90 | 项目需求分析及架构设计
#### 91 | 将电影数据导入 Elasticsearch
#### 92 | 搭建你的电影搜索服务
### 实战 2：Stackoverflow 用户调查问卷分析
#### 93 | 需求分析及架构设计
#### 94 | 数据 Extract & Enrichment
#### 95 | 构建 Insights Dashboard
### 备战：Elastic 认证
#### 96 | Elastic 认证介绍
#### 97 | 考点梳理
#### 98 | 集群的数据备份

#### 99 | 基于Java和Elasticsearch构建应用

#### 100 | 结果测试&结束语



# ELK 相关下载资源



1 | ELK 7.x  推荐官网直接下载，如网速低，可使用以下链接 - 百度网盘下载（https://pan.baidu.com/s/1CRT3W4wEESglCBDnslk2AA）

   



# 相关链接

[Elastic Stack and Product Documentation](https://www.elastic.co/guide/index.html)

[课程课件GitHub地址](https://github.com/onebirdrocks/geektime-ELK/)



# 注释

**注释内容多为个人理解，或从网上搜索的说明，不排除有错误的部分**

[^T+n]:报表统计数据范围，T+1 为昨天，T+7 为上周，T+30为上个月。常规报表方案通常无法实现 T+0 ，即实时报表的效果
[^Hadoop]:Hadoop是由java语言编写的，在分布式服务器集群上存储海量数据并运行分布式分析应用的开源框架，其核心部件是HDFS与MapReduce。
[^ELK]:Elasticsearch + Logstash + Kibana
[^PII]:Personal Identifiable information 个人信息。在美国习惯用PII。在欧洲通常使用 Personal Data
[^Master-eligible node]: 具有主角色（默认）的节点，这使其有资格被选作控制集群的主节点
[^FST]:FST（Finite State Transducer），不但能**共享前缀**还能**共享后缀**。不但能判断查找的key是否存在，还能给出响应的输入output。 它在时间复杂度和空间复杂度上都做了最大程度的优化，使得Lucene能够将Term Dictionary完全加载到内存，快速的定位Term找到响应的output（posting倒排列表）。https://www.shenyanchao.cn/blog/2018/12/04/lucene-fst/