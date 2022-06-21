# ElasticSearch 的使用

## ElasticSearch 是什么？

ElasticSearch 是一个分布式､可扩展､实时搜索与数据分析的引擎。它能从项目一开始就赋予你的数据以搜索、分析和探索的能力，可用于实现全文搜索和实时数据统计。

ElasticSearch 建立在全文搜索引擎库 Apache Lucene 基础之上。 Lucene 可以说是当下最先进、高性能、全功能的搜索引擎库

但是 Lucene 仅仅只是一个库。为了充分发挥其功能，你需要使用 Java 并将 Lucene 直接集成到应用程序中。 更糟糕的是，您可能需要获得信息检索学位才能了解其工作原理。Lucene *非常* 的复杂。

Elasticsearch 也是使用 Java 编写的，它的内部使用 Lucene 做索引与搜索，但是它的目的是使全文检索变得简单， 通过隐藏 Lucene 的复杂性，取而代之的提供一套简单一致的 RESTful API。

然而，Elasticsearch 不仅仅是 Lucene，并且也不仅仅只是一个全文搜索引擎。 它可以被下面这样准确的形容：

- 一个分布式的实时文档存储，*每个字段* 可以被索引与搜索
- 一个分布式实时分析搜索引擎
- 能胜任上百个服务节点的扩展，并支持 PB 级别的结构化或者非结构化数据

Elasticsearch 将所有的功能打包成一个单独的服务，这样你可以通过程序与它提供的简单的 **RESTful API 进行通信**， 可以使用自己喜欢的编程语言充当 web 客户端，甚至可以使用命令行（去充当这个客户端）。

随着你知识的积累，你可以利用 Elasticsearch 更多的高级特性，它的整个引擎是可配置并且灵活的。 从众多高级特性中，挑选Elasticsearch 中需要的服务，使它能解决你本地遇到的问题。

## ElasticSearch 中的基本概念

ElasticSearch 的主要目标是索引、搜索和分析。

在 ElasticSearch 中，一个 ElasticSearch 运行实例就是一个节点(Node), 一个 ElasticSearch 集群由多个节点组成，这些节点可以在一台机器中运行也可以分布在不同的计算机中运行，通常是一台机器运行一个 ElasticSearch 实例。

根据节点的作用，又分为：

- master-eligible: 可以作为主节点，一旦成为主节点，它就可以管理整个集群(cluster)的设置和变化：创建、更新、删除索引(index)；为 node 分配 shard 以及应用的集群设置。 master 节点的角色通常不是非常的占用资源，并且可以共同位于集群中运行了其他角色的节点上。
- data: 数据节点
- ingest: 数据接入
- coordinating node: 协调节点，该节点是一种特殊的节点。它可以很容易的被 kill 掉或者从集群中删除，而不造成任何问题。它不是 master 服务器，因此不参与集群功能，也不包含数据，因此不会因故障而发生数据重定位或者复制 ; 它可以防止由于开发人员失误或者用户错误查询而导致集群不稳定。

在实际使用的过程中，我们可以把请求发送给 data/ingest/coordination 节点，而不能发送给 master 节点。

![img](/home/autmaple/Documents/Notes/Attachment/cf3b26d6938f4be1b0e1064340839940-16558180192663.png)

### 集群的三种状态

- Green: 表示最佳状态
- Yellow: 数据和集群可用，但是集群的备份是坏的
- Red: 数据和集群不可用

### 数据类型

- index: 索引，相当于关系型数据库中的一个 database
- type：类型，相当于关系型数据库中的一张表
- document: 文档，相当于关系性数据库中的一条记录，即一行数据
- field: 字段

### 分片(Shards)和复制(Replicas)

一个索引可以存储超出单个节点硬件限制的大量数据，比如，一个具有 10 亿文档的索引占据 1TB 的磁盘空间，然而任何一个节点都没有这么大的磁盘空间，或者是单个节点处理搜索请求的速度太慢，无法满足我们的需求。为了解决这个问题，Elasticsearch 提供了将索引划分成若干份的能力，这若干份就叫做*分片*。当你创建一个索引的时候，你可以指定你想要的分片的数量，每个分片本身也是一个功能完善并且独立的“索引”，这些“索引”可以被放置到集群中的任何节点上。在某一个运行环境中，失败随时都可能发生，比如某个分片或节点由于某些原因处于离线状态，或者由于某些原因消失不见了，那么此时就需要有一个**故障转移机制**。Elasticsearch 同样帮我们解决了这个问题，Elasticsearch 允许我们创建某个分片的一份或多份拷贝，这些拷贝叫做*复制分片*（即复制），一旦我们复制了分片，那么每个索引就有了*主分片*（源分片）和*复制分片*（复制后的分片）之别。

分片和复制是相当重要的，分片允许我们水平分割或扩展内容容量，复制可以提高可用性，最重要的是分片和复制都可以有效的帮助我们提高性能、提升吞吐量。

### 映射(Mapping)

mapping 是对处理数据的方式和规则方面做出的一些限制，如某个字段的数据类型、默认值、是否被索引等等，这些都是映射里面做出限制。在处理 Elasticsearch 里数据的一些使用规则的设置也叫做映射，建立出一个良好的映射，可以有效的提升我们在处理数据时的效率和性能。

## 分词器

中文分词器：IK

分词力度：

- ik_max_word：按最大粒度进行拆分(找到文本中所有可能的单词)
- ik_smart: 根据句子的意思进行分词，默认情况下这种模式的功能就已经足够了

### 分词器的安装

直接到官网 https://github.com/medcl/elasticsearch-analysis-ik/releases 去下载对应 elasticsearch 版本的中文分词器即可

注意：下载的时候不要下载源码，而是下载已经编译好的文件，如果下载源码，就需要自己编译。

下载好对应的文件之后，解压缩。将解压后的文件夹放到 elasticsearch 目录下的 plugins 目录

## 安装和启动

ElasticSearch 的安装非常简单，到官网下载相应的安装包，然后解压即可

运行的话直接运行 `bin/elastricsearch` 即可，注意，这东西很吃内存, 如果没有设置其所占内存的大小，那么有多少它就占多少。如果要设置具体占多少内存，可以在 `config/jvm.options` 中设置堆的大小

启动之后运行

```shell
curl 'http://localhost:9200/?pretty'
```

如果启动成功的话会有如下输出

```json
{
  "name" : "autmaple-pc",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "TTCdcNksRASrhQKlzUdtZg",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

单个 *节点* 可以作为一个运行中的 Elasticsearch 的实例。 而一个集群是一组拥有相同 `cluster.name` 的节点， 他们能一起工作并共享数据，还提供容错与可伸缩性。当然，一个单独的节点也可以组成一个集群, 你可以在 `elasticsearch.yml` 配置文件中 修改 `cluster.name` ，该文件会在节点启动时加载

ElasticSearch 启动之后监听的端口分别是：9300 和 9200。9300 是 ElasticSearch 集群间组件的通信接口； 9200 是使用浏览器访问的 Http 协议的 RESTful 端口

ElssticSearch 使用 Json 格式的字符串来传递数据, 传递给 ElasticSearch 的数据使用 Json 格式，它传回来的数据也是 Json 格式

ElasticSearch 是面向文档型的数据库,使用倒排索引, 简单的理解就是将文章内容来对应文章的 id。正排索引则是通过文章 id 来查询文章的内容

需要先创建 index（索引） 才能够查询数据，index 相当于数据库，创建索引使用 put 请求,数据库的名称就是请求的路径，例如要创建一个 shopping 的索引，则请求的路径为：`http://127.0.0.1:9200/shopping`, 本地测试的时候不能够是 htpps 的请求，会报错

| 请求路径                                            | 方法   | 描述                                                         |
| --------------------------------------------------- | ------ | ------------------------------------------------------------ |
| http://localhost:port/_cluster/health               | get    | 获取集群的状态，port 为自己配置文件中配置的端口              |
| http://127.0.0.1:9200/shopping                      | put    | 创建索引                                                     |
| http://127.0.0.1:9200/shopping                      | get    | 获取 shopping 索引的信息                                     |
| http://localhost:9200/_cat/indices?v                | get    | 获取所有的索引                                               |
| http://127.0.0.1:9200/shopping                      | delete | 删除 shopping 索引                                           |
| http://127.0.0.1:9200/shopping/_doc                 | post   | 添加数据，请求体中的数据使用 json 格式, 同样的请求，返回的 id 不一样， id 是 ES 随机生成的唯一标识 |
| http://127.0.0.1:9200/shopping/_doc/1001            | post   | 在 shopping 中添加 id 为 1001 的一条数据                     |
| http://127.0.0.1:9200/shopping/_doc/1001            | get    | 查询 shopping 索引中 id 为 1001 的数据                       |
| http://127.0.0.1:9200/shopping/_search              | get    | 查询 shopping 索引中的所有数据                               |
| http://127.0.0.1:9200/shopping/_doc/1001            | put    | 更新 shopping 索引中 id 为 1001 的数据，该方式为全量更新     |
| http://127.0.0.1:9200/shopping/_update/1001         | post   | 修改某条数据的局部更新                                       |
| http://127.0.0.1:9200/shopping/_doc/1001            | delete | 删除 shopping 索引中的 id 为 1001 的那条数据                 |
| http://127.0.0.1:9200/shopping/_search?q=title:小米 | get    | 条件查询,在地址栏拼接数据                                    |
| http://127.0.0.1:9200/shopping/_search              | get    | 在请求体中使用 json 格式设置查询数据,推荐使用这种方式        |
| http://127.0.0.1:9200/user/_mapping                 | put    | 添加映射关系，在 body 中定义映射关系                         |
| http://localhost:9200/_cluster/state                | get    | 获取集群的状态                                               |

添加数据是直接在索引中添加数据，不需要创建表

## java 中操作 ElasticSearch

### 连接客户端

```java
package com.autmaple.es;


import org.apache.http.HttpHost;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

public class EsTest_Client {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );
        
        // 关闭客户端
        client.close();
    }
}

```

### 创建索引

```java
public class EsTest_Create {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        CreateIndexRequest studentIndex = new CreateIndexRequest("student");
        CreateIndexResponse createIndexResponse = client.indices().create(studentIndex, RequestOptions.DEFAULT);
        // 判断操作是否成功
        boolean acknowledged = createIndexResponse.isAcknowledged();
        System.out.println("响应状态: " + acknowledged);

        // 关闭客户端
        client.close();
    }
}
```

### 查询索引

```java
public class EsTest_SearchIndex {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        GetIndexRequest user = new GetIndexRequest("shopping");

        GetIndexResponse getIndexResponse = client.indices().get(user, RequestOptions.DEFAULT);
        System.out.println(getIndexResponse.getAliases());
        System.out.println(getIndexResponse.getSettings());
        System.out.println(getIndexResponse.getMappings());
        System.out.println(getIndexResponse.getDefaultSettings());
        System.out.println(getIndexResponse.getDataStreams());
        // 关闭客户端
        client.close();
    }
}
```

### 删除索引

```java
public class EsTest_DeleteIndex {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        DeleteIndexRequest user = new DeleteIndexRequest("user");
        AcknowledgedResponse delete = client.indices().delete(user, RequestOptions.DEFAULT);
        boolean acknowledged = delete.isAcknowledged();
        System.out.println(acknowledged);
        // 关闭客户端
        client.close();
    }
}

```

### 往索引添加数据

```java
public class EsTest_InsertData {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 插入数据
        User user = new User();
        user.setAge(10);
        user.setName("Kevin");
        user.setSex("Male");
        ObjectMapper mapper = new ObjectMapper();
        String userStr = mapper.writeValueAsString(user);

        IndexRequest indexRequest = new IndexRequest();
        // indexRequest.index("user").id("1001");
        indexRequest.index("user").id("1002");
        // indexRequest.index("user").id("1003");
        indexRequest.source(userStr, XContentType.JSON);
        IndexResponse response = client.index(indexRequest, RequestOptions.DEFAULT);

        DocWriteResponse.Result result = response.getResult();
        System.out.println(result);

        // 关闭客户端
        client.close();
    }
}
```

### 批量添加数据

```java
// 批量添加数据
public class EsTest_InsertDataBatch {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        BulkRequest request = new BulkRequest();
        request.add(new IndexRequest().index("user").id("1001").source(XContentType.JSON, "name", "kevin") );
        request.add(new IndexRequest().index("user").id("1002").source(XContentType.JSON, "name", "irvin") );
        request.add(new IndexRequest().index("user").id("1003").source(XContentType.JSON, "name", "james") );
        request.add(new IndexRequest().index("user").id("1004").source(XContentType.JSON, "name", "kobe") );
        request.add(new IndexRequest().index("user").id("1005").source(XContentType.JSON, "name", "brant") );
        request.add(new IndexRequest().index("user").id("1006").source(XContentType.JSON, "name", "harden") );
        request.add(new IndexRequest().index("user").id("1007").source(XContentType.JSON, "name", "maple") );
        request.add(new IndexRequest().index("user").id("1008").source(XContentType.JSON, "name", "knight") );
        BulkResponse result = client.bulk(request, RequestOptions.DEFAULT);
        System.out.println(result.status());
        // 关闭客户端
        client.close();
    }
}
```

### 更新数据

```java
public class EsTest_UpdateData {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 插入数据
        User user = new User();
        user.setAge(10);
        user.setName("Kevin");
        user.setSex("Male");
        ObjectMapper mapper = new ObjectMapper();
        String userStr = mapper.writeValueAsString(user);

        UpdateRequest request = new UpdateRequest();
        request.index("user").id("1001");
        request.doc(XContentType.JSON, "sex", "female");
        UpdateResponse response = client.update(request, RequestOptions.DEFAULT);
        DocWriteResponse.Result result = response.getResult();
        System.out.println(result);

        // 关闭客户端
        client.close();
    }
}

```

### 查询数据

```java
public class EsTest_SearchData {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        GetRequest request = new GetRequest();
        request.index("user").id("1001");
        GetResponse response = client.get(request, RequestOptions.DEFAULT);
        String result = response.getSourceAsString();
        System.out.println(result);

        // 关闭客户端
        client.close();
    }
}
```

### 删除数据

```java
public class EsTest_DeleteData {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        DeleteRequest request = new DeleteRequest();
        request.index("user").id("1001");
        DeleteResponse result = client.delete(request, RequestOptions.DEFAULT);
        System.out.println(result.status());
        // 关闭客户端
        client.close();
    }
}

```

### 批量删除数据

```java
// 批量删除数据
public class EsTest_DeleteDataBatch {
    public static void main(String[] args) throws IOException {
        // 连接客户端
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );


        BulkRequest request = new BulkRequest();
        request.add(new DeleteRequest().index("user").id("1001"));
        request.add(new DeleteRequest().index("user").id("1002"));
        request.add(new DeleteRequest().index("user").id("1003"));
        BulkResponse result = client.bulk(request, RequestOptions.DEFAULT);
        System.out.println(result.status());
        // 关闭客户端
        client.close();
    }
}

```

### 全增量查询

```java
// 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
        RestClient.builder(new HttpHost("localhost", 9200, "http"))
);


// 1. 全增量查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
request.source(query);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
     hits){
    System.out.println(hit);
}
```

### 条件查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

// 2. 条件查询查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.termQuery("name", "irvin"));
request.source(query);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
     hits){
    System.out.println(hit);
}
```

### 分页查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

// 3. 分页查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
builder.from(1);
builder.size(3);
request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
     hits){
    System.out.println(hit);
}
```

### 排序

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

//4. 排序
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
builder.sort("age", SortOrder.DESC);
request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
     hits){
    System.out.println(hit);
}
```

### 过滤字段

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

//5. 过滤字段
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());

String[] includes = {"name"};
String[] excludes = {};

builder.fetchSource(includes, excludes);
request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
     hits){
    System.out.println(hit);
}
```

### 组合查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

// 6. 组合查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder();
BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
boolQueryBuilder.must(QueryBuilders.matchQuery("name", "irvin"));
boolQueryBuilder.must(QueryBuilders.matchQuery("sex", "Male"));
builder.query(boolQueryBuilder);

request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
     hits){
    System.out.println(hit);
}
```

### 范围查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

// 7. 范围查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder();
RangeQueryBuilder range = QueryBuilders.rangeQuery("age");
range.gte(20); // 大于等于
range.lt(30); // 小于
builder.query(range);

request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
        hits){
    System.out.println(hit);
}
```

### 模糊查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

//8. 模糊查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder();
FuzzyQueryBuilder fuzzyQueryBuilder = QueryBuilders
        .fuzzyQuery("name", "irvin")
        .fuzziness(Fuzziness.ONE); // 差一个字符就能弄被查询

builder.query(fuzzyQueryBuilder);

request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
        hits){
    System.out.println(hit);
}
```

### 高亮查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

// 9. 高亮查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder();
TermsQueryBuilder termsQueryBuilder = QueryBuilders.termsQuery("name", "irvin");

HighlightBuilder highlightBuilder = new HighlightBuilder();
highlightBuilder.preTags("<font color = 'red'>");
highlightBuilder.postTags("</font>");
highlightBuilder.field("name");

builder.highlighter(highlightBuilder);
builder.query(termsQueryBuilder);

request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
        hits){
    System.out.println(hit);
}
```

### 聚合查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

//10. 聚合查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder();
MaxAggregationBuilder maxAggregationBuilder = AggregationBuilders.max("maxAge").field("age");

builder.aggregation(maxAggregationBuilder);

request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
        hits){
    System.out.println(hit);
}
```

### 分组查询

```java
 // 连接客户端
RestHighLevelClient client = new RestHighLevelClient(
    RestClient.builder(new HttpHost("localhost", 9200, "http"))
);

// 11. 分组查询
SearchRequest request = new SearchRequest();
request.indices("user");

SearchSourceBuilder builder = new SearchSourceBuilder();
TermsAggregationBuilder aggregationBuilder = AggregationBuilders.terms("ageGroup").field("age");

builder.aggregation(aggregationBuilder);

request.source(builder);
SearchResponse result = client.search(request, RequestOptions.DEFAULT);

SearchHits hits = result.getHits();
for (SearchHit hit :
        hits){
    System.out.println(hit);
}
```

## 整合在 springboot 中

### pom.xml

```xml
<parent>
    <artifactId>spring-boot-starter-parent</artifactId>
    <groupId>org.springframework.boot</groupId>
    <version>2.3.7.RELEASE</version>
</parent>

<dependencies>
	 <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <!--<version>5.2.4.RELEASE</version>-->
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <!--<version>2.2.7.RELEASE</version>-->
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-elasticsearch -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
            <!--<version>2.2.5.RELEASE</version>-->
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <!--<version>2.5.5</version>-->
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <!--<version>4.13.1</version>-->
            <scope>test</scope>
        </dependency>
</dependencies>
```

### applicattion.yaml 配置文件

```yaml
elasticsearch:
  host: localhost
  port: 9200
```

### 创建ElasticSearch 的配置类

```java
package com.autmaple.es;

import org.apache.http.HttpHost;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder;
import org.elasticsearch.client.RestHighLevelClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.elasticsearch.config.AbstractElasticsearchConfiguration;

@Configuration
public class ElasticSearchConfig extends AbstractElasticsearchConfiguration {

    @Value("${elasticsearch.host}")
    private String host;
    @Value("${elasticsearch.port}")
    private Integer port;

    @Override
    public RestHighLevelClient elasticsearchClient() {
        RestClientBuilder builder = RestClient.builder(new HttpHost(host, port));
        return new RestHighLevelClient(builder);
    }
}
```

### 创建实体类对象

```java
package com.autmaple.es;

import net.bytebuddy.description.field.FieldList;
import org.junit.jupiter.params.provider.CsvFileSource;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;
import org.springframework.data.elasticsearch.annotations.FieldType;

import javax.lang.model.element.TypeElement;
import java.lang.annotation.Documented;

/**
 * indexName: 表示将数据存储到那一个索引中
 * shards: 分片
 * replicas: 副本
 */
@Document(indexName = "product", shards = 3, replicas = 1)
public class Product {
    @Id
    private Long id;

    /**
     * FieldType: 该字段在 ES 中的数据类型
     * analyzer：指定分词器，可以不用指定
     */
    @Field(type = FieldType.Text, analyzer = "ik_max_word")
    private String title;

    @Field(type = FieldType.Keyword)
    private String category;
    
    @Field(type = FieldType.Double)
    private Double price;
    
    /**
     * index = false: 表示不能够使用图片路径来查询
     */
    @Field(type = FieldType.Keyword, index = false)
    private String images;

    public Product(){}

    public Product(Long id, String title, String category, Double price, String images) {
        this.id = id;
        this.title = title;
        this.category = category;
        this.price = price;
        this.images = images;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getCategory() {
        return category;
    }

    public void setCategory(String category) {
        this.category = category;
    }

    public Double getPrice() {
        return price;
    }

    public void setPrice(Double price) {
        this.price = price;
    }

    public String getImages() {
        return images;
    }

    public void setImages(String images) {
        this.images = images;
    }

    @Override
    public String toString() {
        return "Product{" +
                "id=" + id +
                ", title='" + title + '\'' +
                ", category='" + category + '\'' +
                ", price=" + price +
                ", images='" + images + '\'' +
                '}';
    }
}
```

### 常见 Dao 接口

不需要实现

```java
package com.autmaple.es;

import org.springframework.data.elasticsearch.repository.ElasticsearchRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ProductDao extends ElasticsearchRepository<Product, Long> {
}
```



### 测试

```java
package com.autmaple.es;


import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.index.query.TermQueryBuilder;
import org.elasticsearch.index.query.TermsQueryBuilder;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.validator.PublicClassValidator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.data.elasticsearch.core.ElasticsearchRestTemplate;
import org.springframework.test.context.junit4.SpringRunner;

import javax.xml.stream.events.ProcessingInstruction;
import java.util.ArrayList;
import java.util.Optional;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTest {

    @Autowired
    private ElasticsearchRestTemplate elasticsearchRestTemplate;

    @Autowired
    private ProductDao productDao;

    /**
     *  创建索引, spring 项目启动之后会根据实体类(Product.class)的配置自动创建索引
     */
    @Test
    public void createIndex(){

        System.out.println("创建索引");
    }

    /**
     * 删除索引
     */
    @Test
    public void deleteIndex(){
        boolean status = elasticsearchRestTemplate.deleteIndex(Product.class);
        System.out.println(status);
    }

    /**
     * 新增数据
     */
    @Test
    public void insertData(){
        Product product = new Product();
        product.setId(1L);
        product.setCategory("phone");
        product.setPrice(2999.0);
        product.setTitle("华为手机");
        product.setImages("https://www.example.com");
        productDao.save(product);
    }

    /**
     * 更新数据
     * 如果 id 相同则会更新数据
     */
    @Test
    public void updateData(){
        Product product = new Product();
        product.setId(1L);
        product.setCategory("phone");
        product.setPrice(3999.0);
        product.setTitle("华为手机");
        product.setImages("https://www.example.com");
        productDao.save(product);
    }

    /**
     * 根据 id 查询数据
     * orElse 方法:
     *      如果 findById() 方法找到了对应的数据，就将封装的数据对象返回，否则返回传递的对象
     */
    @Test
    public void findById(){
        Product product = productDao.findById(1L).orElse(new Product());
        System.out.println(product);
    }

    /**
     * 查询索引中的所有数据
     */
    @Test
    public void findAll(){
        Iterable<Product> all = productDao.findAll();
        for(Product product: all){
            System.out.println(product);
        }
    }

    /**
     * 删除数据
     */
    @Test
    public void delete(){
        Product product = new Product();
        product.setId(1L);
        productDao.delete(product);
    }

    /**
     * 批量新增数据
     */
    @Test
    public void saveAll(){
        ArrayList<Product> products = new ArrayList<>();
        for (long i = 1; i < 10; i++) {
            Product product = new Product();
            product.setId(i);
            product.setCategory("phone");
            product.setPrice(3999.0 + i);
            product.setTitle("华为手机" + i);
            product.setImages("https://www.example.com/i");
            products.add(product);
        }
        productDao.saveAll(products);
    }

    /**
     * 分页查询
     */
    @Test
    public void findByPageable(){
        // 设置排序(正序还是倒序，使用 id 字段进行排序)
        Sort sort = Sort.by(Sort.Direction.DESC, "id");
        int from = 0; // 从第几页开始，页数从 0 开始
        int pageSize = 5; // 每页数据的大小
        PageRequest pageRequest = PageRequest.of(from, pageSize, sort);
        Page<Product> products = productDao.findAll(pageRequest);
        for(Product product: products) {
            System.out.println(product);
        }
    }

    /**
     * term 查询
     * search(termQueryBuilder) 调用搜索方法，参数查询构建器对象
     */

    @Test
    public void termQuery(){
        TermQueryBuilder builder = QueryBuilders.termQuery("category", "phone");
        Iterable<Product> products = productDao.search(builder);
        for (Product product: products){
            System.out.println(product);
        }
    }

    /**
     * term 查询 + 分页查询
     */
    @Test
    public void termQueryByPage(){
        int from = 0;
        int pageSize = 5;

        PageRequest pageRequest = PageRequest.of(from, pageSize);
        TermsQueryBuilder builder = QueryBuilders.termsQuery("category", "phone");
        Page<Product> products = productDao.search(builder, pageRequest);
        for(Product product: products) {
            System.out.println(product);
        }
    }
}
```

