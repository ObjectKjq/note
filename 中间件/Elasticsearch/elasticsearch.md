## Elasticsearch入门

### 使用Http对es进行操作

https://www.elastic.co/cn/downloads/elasticsearch去官网下载elasticsearch

点击.\elasticsearch-7.8.0\bin\elasticsarch.bat启动

注意：9300端口为Elasticsarch集群间组建的通讯端口，9200端口为浏览器访问的http协议端口

http://localhost:9200/在浏览器中检测是否启动成功。支持RESTful风格、数据的发送和接收都是以json的形式访问的

![](.\图片\Snipaste_2022-04-09_13-12-24.png)

https://www.getpostman.com官网



创建一个索引就等于创建一个数据库，shopping表示创建的索引名称

![](.\图片\Snipaste_2022-04-09_13-28-58.png)

put - http://127.0.0.1:9200/shopping创建一个以索引名为shopping

get - http://127.0.0.1:9200/shopping获取索引的一条数据

get - http://127.0.0.1:9200/_cat/indices?v查看所有索引

delete - http://127.0.0.1:9200/shopping删除索引

es中是没有表的概念的

put - http://127.0.0.1:9200/shopping/_doc/1001添加数据id为1001，此时如果id是存在的就是修改（这种修改是完全覆盖性的）

get - http://127.0.0.1:9200/shopping/_doc/1001

get - http://127.0.0.1:9200/shopping/_search获取shopping索引中的所有数据

post-http://127.0.0.1:9200/shopping/_update/1001对局部进行修改

```json
//data中的数据
{
    "doc":{
        "kjq":"45"
    }
}
```

delete-http://127.0.0.1:9200/shopping/_doc/1001删除1001中数据



条件查询

get - http://127.0.0.1:9200/shopping/_search

```json
{
    "query":{//表示一个查询
        "match":{//表示一个匹配查询
            "kjq":"001"
        }
    }
}
//分页查询
{
    "query" : {//表示一个查询
        "match_all" : {//表示查询全部
            
        }
    },
    "from" : 0,//表示起始位置
    "size" : 2//表示每页查询两条数据
    "_source":["kjq"],//表示想看哪些字段
    "sort": {//表示排序
        "kjq":{
            "order": "desc"
        }
    }
}
//多条查询和范围查询
{
    "query" : {//表示一个查询
        "bool" : {//表示条件
            "must": [//表示多个条件同时成立，如果改成should就表示条件中有一个成立就行
                {
                    "match" : {//表示查询
                        "kjq":"001"
                    }
                } //这里可以添加别的条件
            ],
            "filter" : {//过滤
                "range":{//表示对范围进行过滤
                    "price" : {
                        "gt":5000//表示price大于5000
                    }
                }
            }
        }
    }
}
//全文检索和完全匹配和高亮查询
{
    "query":{
        "match":{//如果把match该成match_phrase表示完全匹配
            "category":"小华"//他会把小和华拆成数组，对小进行全文检索，对华进行全文检索
        },
        "highlight":{//表示对查询的结果进行高亮显示
            "fields":{
                "category":{}
            }
        }
    }
}
//聚合查询
{
    "aggs":{//聚合操作
        "price_group": {//统计结果的名称（相当于对相同价格的商品进行数量上的统计）
            "terms":{//分组操作，如果改成avg表示平局值
                "field":"price"//分组的字段
            }
        }
    },
    "size":0//把原始数据去掉
}
```

```json
{
	"properties":{//创建映射
        "name":{//对name创建映射
            "type":"text",//表示可以分词查询
            "index":true//可以以索引查询
        },
        "sex":{
            "type":"keyword",//必须完全匹配
            "index":true
        },
        "tel":{
            "type":"keyword",
            "index":false//不可以以索引查询
        }
    }
}
```

### 使用JavaApi的形式进行造作

首先引入两个依赖

```xml
<!--操作elasticsearch的依赖-->
<dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>7.8.0</version>
</dependency>
<!--elasticsearch的客户端-->
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.8.0</version>
</dependency>
```

连接数据库

```java
//创建es客户端
RestHighLevelClient esClient = new RestHighLevelClient(
        RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
);
//关闭连接
esClient.close();
```

创建索引

```java
@Test
public void text01() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );

    //创建索引
    CreateIndexRequest request = new CreateIndexRequest("user");//请求
    CreateIndexResponse response = esClient.indices().create(request, RequestOptions.DEFAULT);

    //相应状态
    boolean acknowledged = response.isAcknowledged();
    System.out.println(acknowledged);
    //关闭连接
    esClient.close();
}
```

向索引中添加数据

```java
@Test
public void test02() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );

    //像索引中插入数据
    IndexRequest request = new IndexRequest();//创建请求
    request.index("user").id("1001");//表示对那个索引进行插入，插入的id是1001
    //准备一个对象，转换成json
    User user = new User("小米", 32, "123123");
    ObjectMapper objectMapper = new ObjectMapper();
    String userJson = objectMapper.writeValueAsString(user);
    request.source(userJson, XContentType.JSON);

    IndexResponse response = esClient.index(request, RequestOptions.DEFAULT);

    DocWriteResponse.Result result = response.getResult();
    System.out.println(result);

    //关闭连接
    esClient.close();
}
```

//局部修改

```java
@Test
public void test02() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );

    //修改数据
    UpdateRequest request = new UpdateRequest();//创建请求

    request.index("user").id("1001");
    //局部修改
    request.doc(XContentType.JSON, "sex", "女");

    UpdateResponse response = esClient.update(request, RequestOptions.DEFAULT);
    System.out.println(response.getResult());
    //关闭连接
    esClient.close();
}
```

//查询

```java
@Test
public void test03() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //查询数据
    GetRequest request = new GetRequest();
    request.index("user").id("1001");


    GetResponse response = esClient.get(request, RequestOptions.DEFAULT);

    System.out.println(response.getSourceAsString());

    //关闭连接
    esClient.close();
}
```

//删除

```java
@Test
public void test03() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //删除数据
    DeleteRequest request = new DeleteRequest();
    request.index("user").id("1001");

    DeleteResponse response = esClient.delete(request, RequestOptions.DEFAULT);
    System.out.println(response.toString());

    //关闭连接
    esClient.close();
}
```

//批量添加

```java
@Test
public void test03() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //批量插入数据
    BulkRequest request = new BulkRequest();
    IndexRequest source1 = new IndexRequest().index("user").id("1001").source(XContentType.JSON, "name", "小明");
    IndexRequest source2 = new IndexRequest().index("user").id("1002").source(XContentType.JSON, "name", "小明");
    IndexRequest source3 = new IndexRequest().index("user").id("1003").source(XContentType.JSON, "name", "小明");
    request.add(source1);
    request.add(source2);
    request.add(source3);

    BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
    System.out.println(response.getTook());
    System.out.println(response.getItems());

    //关闭连接
    esClient.close();
}
```

//批量删除

```java
@Test
public void test03() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //批量插入数据
    BulkRequest request = new BulkRequest();
    DeleteRequest source1 = new DeleteRequest().index("user").id("1001");
    DeleteRequest source2 = new DeleteRequest().index("user").id("1001");
    DeleteRequest source3 = new DeleteRequest().index("user").id("1001");
    request.add(source1);
    request.add(source2);
    request.add(source3);

    BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
    System.out.println(response.getTook());
    System.out.println(response.getItems());

    //关闭连接
    esClient.close();
}
```

//查询全部

```java
@Test
public void test03() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //查询数据
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //查询条件,全部数据
    SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());//查询对象
    request.source(query);

    SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

//条件查询

```java
@Test
public void test03() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //查询数据
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //条件查询
    SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.termQuery("age", 30));//查询对象
    request.source(query);

    SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

分页查询

```java
@Test
public void test05() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //分页查询
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //条件查询
    SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());//查询对象
    query.from(0);//从哪里查询
    query.size(2);//每页显示两条
    query.sort("age", SortOrder.ASC);//对查询进行排序，升序
    String[] excludes = {};//排除
    String[] includes = {"name"};//包含
    query.fetchSource(includes, excludes);//对显示字段排序
    request.source(query);

    SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

多条件查询

```java
@Test
public void test06() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    //分页查询
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //条件查询
    SearchSourceBuilder builder = new SearchSourceBuilder();//查询对象
    BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
    boolQueryBuilder.must(QueryBuilders.matchQuery("age", 30));
    boolQueryBuilder.mustNot(QueryBuilders.matchQuery("sex", "女"));
    builder.query(boolQueryBuilder);

    request.source(builder);

    SearchResponse response = esClient
            .search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

范围查询

```java
@Test
public void test06() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //条件查询
    SearchSourceBuilder builder = new SearchSourceBuilder();//查询对象
    RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("age");//范围查询
    rangeQuery.gte(30);//大于等于30
    rangeQuery.lte(40);//大于等于30
    builder.query(rangeQuery);

    request.source(builder);

    SearchResponse response = esClient
            .search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

模糊查询

```java
@Test
public void test07() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //模糊查询
    SearchSourceBuilder builder = new SearchSourceBuilder();//查询对象

    builder.query(QueryBuilders.fuzzyQuery("name", "小").fuzziness(Fuzziness.ONE));//差一个能查询得到

    request.source(builder);

    SearchResponse response = esClient
            .search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

高亮查询

```java
@Test
public void test08() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //高亮查询
    SearchSourceBuilder builder = new SearchSourceBuilder();//查询对象
    TermsQueryBuilder termsQueryBuilder = QueryBuilders.termsQuery("name", "小明");

    HighlightBuilder highlightBuilder = new HighlightBuilder();

    highlightBuilder.preTags("<font color='red'>");//前缀标签
    highlightBuilder.postTags("</font>");//后缀标签
    highlightBuilder.field("name");//对name做高亮显示

    builder.highlighter(highlightBuilder);
    builder.query(termsQueryBuilder);

    request.source(builder);

    SearchResponse response = esClient
            .search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

聚合查询

```java
@Test
public void test09() throws IOException {
    //创建es客户端
    RestHighLevelClient esClient = new RestHighLevelClient(
            RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
    );
    SearchRequest request = new SearchRequest();
    request.indices("user");

    //聚合查询
    SearchSourceBuilder builder = new SearchSourceBuilder();//查询对象
    AggregationBuilder aggregationBuilder = AggregationBuilders.max("maxAge").field("age");//对年龄取最大值

    builder.aggregation(aggregationBuilder);
    request.source(builder);

    SearchResponse response = esClient
            .search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println(hits.getTotalHits());
    System.out.println(response.getTook());

    for (SearchHit hit :hits) {
        System.out.println(hit.getSourceAsString());
    }

    //关闭连接
    esClient.close();
}
```

## Elasticsearch环境

以上讲述都是单机服务器

单机服务器，往往都有最大的负载能力，超过这个值，服务器性能就会大大降低甚至不可用，所以1生产环境中，一般都是运行在指定服务器集群中

单机存储容量有限，单机服务容易出现单点故障，无法实现高可用、单机处理并发能力有限

### 集群

一个集群有一个或多个服务器节点组成，一个es集群有一个唯一的名字表示，这个名字默认是“elasticsrarch”

节点Node、

​	集群中包含很多服务器中

集群部署配置、

打开.\elasticsearch-7.8.0\config\elasticsearch.yml

```python
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
# 集群名称
cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
# 每个节点有自己的名称，不能重复
node.name: node-1001
# 当前节点可以是master，也可以是数据节点
node.master: true
node.data: true
#
# Add custom attributes to the node:
#
# node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
# path.data: /path/to/data
#
# Path to log files:
#
# path.logs: /path/to/logs
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
# bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
# 主机名称
network.host: localhost
#
# Set a custom port for HTTP:
# 浏览器访问端口
http.port: 1001
# 通讯端口
transport.tcp.port: 9301
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
# discovery.seed_hosts: ["host1", "host2"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
# cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
# action.destructive_requires_name: true
# 跨域配置
http.cors.enabled: true
http.cors.allow-origin: "*"
```

node-1002的配置文件

```python
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
# 集群名称
cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
# 每个节点有自己的名称，不能重复
node.name: node-1002
# 当前节点可以是master，也可以是数据节点
node.master: true
node.data: true
#
# Add custom attributes to the node:
#
# node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
# path.data: /path/to/data
#
# Path to log files:
#
# path.logs: /path/to/logs
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
# bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
# 主机名称
network.host: localhost
#
# Set a custom port for HTTP:
# 浏览器访问端口
http.port: 1002
# 通讯端口
transport.tcp.port: 9302
# 配置通讯端口去找已经启动的端口
discovery.seed_hosts: ["localhost:9301"]
discovery.zen.fd.ping_timeout: 1m
discovery.zen.fd.ping_retries: 5
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
# discovery.seed_hosts: ["host1", "host2"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
# cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
# action.destructive_requires_name: true
# 跨域配置
http.cors.enabled: true
http.cors.allow-origin: "*"
```

## Elasticsearch进阶

索引的概念

es索引的精髓，一切设计都是为了索引

![](.\图片\Snipaste_2022-04-09_21-19-43.png)

![](.\图片\Snipaste_2022-04-09_21-22-21.png)

字段Field

相当于是数据表的字段，对文档数据根据不同属性进行的分类表示

![](.\图片\Snipaste_2022-04-09_21-25-20.png)

把一张表进行分片,比如把男的表与女的表分片

![](.\图片\Snipaste_2022-04-09_21-25-51.png)

![](.\图片\Snipaste_2022-04-09_21-27-38.png)

当主服务器坏了，就从副本上取数据

![](.\图片\Snipaste_2022-04-09_21-28-53.png)

![](.\图片\Snipaste_2022-04-09_21-29-51.png)



**系统架构**

![](.\图片\Snipaste_2022-04-09_21-32-54.png)

使用master管理节点。r0,r1,r2：是分片、p0,p1,p2是副本



### 创建集群

![](.\图片\Snipaste_2022-04-09_21-36-25.png)

单节点创建分片，三个分片是可以正常运行的，但副本都是不能用的。这样的分片很危险。

多节点集群

当再次重启一个节点时，副本就会重新分配。就能使用了

![](.\图片\Snipaste_2022-04-09_22-02-29.png)

### 水平扩容

当启动了第三个节点，我们的集群会拥有第三个节点的集群：为了分散负载均衡而对分片进行重新分配

![](.\图片\Snipaste_2022-04-09_22-07-43.png)

最好不要用6个以上的节点，因为会导致资源的浪费。主分片是在创建索引的时候就定下来的，但是副本分片是可以修改

put-http://127.0.0.1:1001/users/_settings

![](.\图片\Snipaste_2022-04-09_22-15-18.png)



当主分片停掉，1002就会变成主分片，服务依然可以用。当再此启动停掉的分片，此时就会变成副分片



### 路由计算and分片控制

就是计算数据应该存放到那个分片

![](.\图片\Snipaste_2022-04-09_22-32-36.png)

查询

![](.\图片\Snipaste_2022-04-09_22-35-24.png)



数据写的流程

![](.\图片\Snipaste_2022-04-09_22-40-00.png)

consistency设置成one表示只要分片保存好数据，就能进行查询，all是当所有副本都保存号才能进行查询，quorum都是中间

timeout设置一个超时时间，默认十一分钟

查询数据的流程

![](.\图片\Snipaste_2022-04-09_22-48-42.png)

更新流程

![](.\图片\Snipaste_2022-04-09_22-50-26.png)

### 倒排索引

它适用于快速的全文搜索

![](.\图片\Snipaste_2022-04-09_22-56-09.png)

分词器 keyword不能分词，text可以分词。ik_max_word能拆分就拆了，ik_smart

![](.\图片\Snipaste_2022-04-09_23-00-55.png)

![](.\图片\Snipaste_2022-04-10_08-21-07.png)



先写入内存，让后再写入日志文件，再向磁盘中添加数据，最后保存到磁盘日志文件。只有当保存到磁盘中用户才能查询

![](.\图片\Snipaste_2022-04-10_08-27-40.png)



先保存到系统的缓冲区，让后再写入磁盘。磁盘中的文件段还要考虑是否需要合并问题

![](.\图片\Snipaste_2022-04-10_08-34-18.png)

**文档分析**

![](.\图片\Snipaste_2022-04-10_08-37-37.png)

![](.\图片\Snipaste_2022-04-10_08-38-09.png)

![](.\图片\Snipaste_2022-04-10_08-38-49.png)

简单测试

![](.\图片\Snipaste_2022-04-10_08-46-18.png)

​	指定IK中文分词器。下载地址：https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.8.0

![](.\图片\Snipaste_2022-04-10_08-53-32.png)

​	测试

![](.\图片\Snipaste_2022-04-10_08-54-33.png)

​	ik_max_word: 会将文本做最细的拆分

​	ik_smart: 会将文本做最粗力度的拆分



​	我们还可以告诉ik分词器指定的名词

![](.\图片\Snipaste_2022-04-10_09-02-21.png)

```xml
<!--用户可以在这里配置自己的扩展字典 -->
<entry key="ext_dict">custom.dic</entry>
```

也可以自定义分词器

**文档处理**

为了防止出现文档修改失败

![](.\图片\Snipaste_2022-04-10_09-25-34.png)

es也是一个乐观锁，指定版本

![](.\图片\Snipaste_2022-04-10_09-28-16.png)

拿到新版本更新

![](.\图片\Snipaste_2022-04-10_09-32-19.png)

![](.\图片\Snipaste_2022-04-10_09-34-32.png)

### Kibana的使用

打开Kibana的配置文件

```xml
# 数据展示服务端口
server.port: 5601
# ES服务端口地址
elasticsearch.hosts: ["http://127.0.0.1:9200"]
# 索引名
kibana.index: ".kibana"
# 支持中文
i18n.locale: "zh-CN"
```

双击bin/kibana.bat文件，打开浏览器访问http://127.0.0.1:5601

打开控制台如下界面

![](.\图片\Snipaste_2022-04-10_10-04-55.png)

## Elasticsearch集成

### Spring Data框架的集成。

简化非关系型数据库操作的框架，他是基于springboot的框架

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.6.RELEASE</version>
    <relativePath/>
</parent>
<dependencies>
	<dependency>
    	<groupId>org.projectlombok</groupId>
    	<artifactId>lombok</artifactId>
    </dependency>
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

增加配置文件

在resources中添加application.properties文件

```properties
# es服务地址
elasticsearch.host=127.0.0.1
# es服务端口
elasticsearch.part=9200
# 配置日志级别，开启debug日志
logging.level.com.atguigu.es=debug
```

主程序类

![](.\图片\Snipaste_2022-04-10_10-23-30.png)

实体类

![](.\图片\Snipaste_2022-04-10_10-22-33.png)
