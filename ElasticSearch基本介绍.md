## 一、ElasticSearch基本介绍
### 1.1ElasticSearch(简称ES)
Elasticsearch是用Java开发并且是当前最流行的开源的企业级搜索引擎。能够达到实时搜索，稳定，可靠，快速，安装使用方便。客户端支持Java、.NET（C#）、PHP、Python、Ruby等多种语言。
官方网站: https://www.elastic.co/
下载地址：https://www.elastic.co/cn/start

创始人:Shay Banon（谢巴农）

![image-20230427141327734](C:\Users\l30037108\AppData\Roaming\Typora\typora-user-images\image-20230427141327734.png)
### 1.2 Lucene
Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库（框架）但是想要使用Lucene，必须使用Java来作为开发语言并将其直接集成到你的应用中，并且Lucene的配置及使用非常复杂，你需要深入了解检索的相关知识来理解它是如何工作的。
Lucene缺点：
1）只能在Java项目中使用,并且要以jar包的方式直接集成项目中.
2）使用非常复杂-创建索引和搜索索引代码繁杂
3）不支持集群环境-索引数据不同步（不支持大型项目）
4）索引数据如果太多就不行，索引库和应用所在同一个服务器,共同占用硬
盘.共用空间少.
**ElasticSearch底层是基于Lucence运行的**

### 1.3 基本概念
1. **什么是全文检索**
    全文检索是指：
    通过一个程序扫描文本中的每一个单词，针对单词建立索引，并保存该单
    词在文本中的位置、以及出现的次数
    用户查询时，通过之前建立好的索引来查询，将索引中单词对应的文本位
    置、出现的次数返回给用户，因为有了具体文本的位置，所以就可以将具体内
    容读取出来了

2. **倒排索引**
    Elasticsearch 使用一种称为 倒排索引 的结构，它适用于快速的全文搜索。一个倒排索引由文档中所有不重复词的列表构成，对于其中每个词，有一个包含它的文档列表。

  ![image-20230427164805498](C:\Users\l30037108\AppData\Roaming\Typora\typora-user-images\image-20230427164805498.png)

3. **分词与分词器**
    **文本分析**（Analysis）就是把全文本转换成一系列单词的过程，这个过程也叫做**分词**。在ES中，分词是通过**分词器**（Analyzer）来完成的
    **分词器的组成**
    **Character** **Filters**：针对原始文本处理，比如去除 html 标签
    **Tokenizer**：按照规则切分为单词，比如按照空格切分
    **Token** **Filters**：将切分的单词进行加工，比如大写转小写，删除 stopwords（a,the,is），增加同义语
4. **ES内置分词器**
   Standard Analyzer - 默认分词器，按词切分，小写处理
   Simple Analyzer - 按照非字母切分（符号被过滤），小写处理
   Stop Analyzer - 小写处理，停用词过滤（the ，a，is）
   Whitespace Analyzer - 按照空格切分，不转小写
   Keyword Analyzer - 不分词，直接将输入当做输出
   Pattern Analyzer - 正则表达式，默认 \W+
   Language - 提供了 30 多种常见语言的分词器
   Customer Analyzer - 自定义分词器
5. **中文分词**
   ES内置的分词器对中文并不友好,对于中文分词需要我们下载对应的中文友好的分词器,目前市面上比较受欢迎的中文分词器有:
   IK：
     支持自定义词库，支持热更新分词字典
     https://github.com/medcl/elasticsearch-analysis-ik
   jieba：
     Python 中最流行的分词系统，支持分词和词性标注
     支持繁体分词、自定义词典、并行分词等
    https://github.com/sing1ee/elasticsearch-jieba-plugin

### 1.4 ES中的核心概念
1. **索引 index**
   索引就是存储相同特征文档的集合,它类似于关系型数据库中的表的概念。一个索引由一个名字来标识，必须是小写字母。
2. **文档 document**
   一个文档是一个可被索引的基础信息单元，类似一条记录。文档以JSON（Javascript Object
   Notation）格式来表示
3. **映射 mapping**
   映射是对处理数据的方式和规则方面定义的一些限制，它类似于关系型数据库中的scheme概念，比如，映射可以定义某个字段的名字，类型，默认值，分词器，是否被索引等
4. **字段 field**
   相当于关系型数据库中的column概念
5. **字段类型 type**
   每一个字段都有对应的类型，常用的类型有
   字符串：string，string类型包含 text 和 keyword。
   text：该类型被用来索引长文本，在创建索引前会将这些文本进行分词，转化为
   词的组合，建立索引；允许es来检索这些词，text类型不能用来排序和聚合。
   keyword：该类型不能分词，可以被用来检索过滤、排序和聚合，keyword类型不
   可进行分词模糊检索。
   数值型：long、integer、short、byte、double、float
   日期型：date
   布尔型：boolean
6. **集群 cluster**
   ES作为一个分布式搜索引擎，支持集群的搭建，一个集群就是由一个或多个节点组织在一起，他们共同持有整个数据，并一起提供索引和搜索功能
7. **节点 node**
   一个节点是集群中的一个服务器，作为集群的一部分，它存储数据，参与集群的索
   引和搜索功能。
   一个节点可以通过配置集群名称的方式来加入一个指定的集群。默认情况下，每
   个节点都会被安排加入到一个叫做“elasticsearch”的集群中。
   这意味着，如果在网络中启动了若干个节点，并假定它们能够相互发现彼此，它们
   将会自动地形成并加入到一个叫做“elasticsearch”的集群中。
   在一个集群里，可以拥有任意多个节点。而且，如果当前网络中没有运行任何
   Elasticsearch节点，这时启动一个节点，会默认创建并加入一个叫
   做“elasticsearch”的集群。
8. **分片 shards**
   假如某个索引有超过10亿个文档，所占的存储空间超过1TB，而任一节点都没有这样大的磁盘空间，或者单个节点处理搜索请求，响应太慢。为了解决这个问题，Elasticsearch提供了将索引划分成多份的能力，这就叫做分片
9. **副本 replicas**
   为了解决集群中某个节点突然宕机而造成数据的丢失，es同时也提供了备份的功能，为每个分片生成了一个或多个副本，分别存储在不同的节点中，同时副本与主分片不能存储在同一个节点中。

例子：
## 二、DSL语言高级查询
1. DSL概述
   Domain Specific Language, 领域专用语言,ElasticSearch 提供了基于JSON的DSL来定义查询， DSL由叶子查询子句和复合查询子句组成。![image-20230503111329988](C:\Users\LuoHongfei\AppData\Roaming\Typora\typora-user-images\image-20230503111329988.png)
   语法：
   
   ```json
   GET /es_db/_doc/_search {json请求体数据}
   可以简化为下面写法
   GET /es_db/_search {json请求体数据}
   ```
   
   示例数据
```json
#指定ik分词器
PUT /es_db
{ "settings" : {
"index" : {
 "analysis.analyzer.default.type": "ik_max_word"
 }
 }
 }
# 创建文档,指定id
 PUT /es_db/_doc/1
 {
 "name": "张三",
 "sex": 1,
 "age": 25,
 "address": "广州天河公园",
 "remark": "java developer"
 }
 PUT /es_db/_doc/2
 {
 "name": "李四",
 "sex": 1,
 "age": 28,
 "address": "广州荔湾大厦",
"remark": "java assistant"
}

 PUT /es_db/_doc/3
{
"name": "王五",
"sex": 0,
"age": 26,
"address": "广州白云山公园",
"remark": "php developer"
}
PUT /es_db/_doc/4
{
"name": "赵六",
"sex": 0,
"age": 22,
"address": "长沙橘子洲",
"remark": "python assistant"
}
PUT /es_db/_doc/5
{
"name": "张龙",
"sex": 0,
"age": 19,
 "address": "长沙麓谷企业广场",
"remark": "java architect assistant"
}
PUT /es_db/_doc/6
{
"name": "赵虎",
"sex": 1,
"age": 32,
"address": "长沙麓谷兴工国际产业园",
"remark": "java architect"
}
```
1. 查询所有 match_all
   使用match_all 默认返回10条数据，因为_search查询默认采用分页查询，每页大小默认值为10，如果想要显示更多，制定size
```json 
GET /es_db/_search
等同于
GET /es_db/_search
{
"query":{
"match_all":{}
}
}
返回指定条数size：
GET /es_db/_search
{
"query":{
"match_all":{}
},
 "size":100,
  "from":0
}
注意：size的值不能无限增加，es规定查询结果的窗口,from+size必须小于等于max_result_window，而这个值默认为10000，可以通过下面的语句改变默认值的大小：
PUT /es_db/_settings
{
"index.max_result_window" :"20000" }
```

2.  分页查询
   普通的分页查询可以通过from+size关键字来实现，但是如果要进行深分页，官方并不推荐这种做法，因为如果要查询from为10000，size为10的数据，那么ES也必须先把前10000条数据加载到内存中，再进行分页取值，可想而知，这会对内存造成多大的消耗。

   深分页查询 scroll

   当我们要查询全量数据时，es提供了一种滚动查询的方式，通过scroll api查询。

   ```json
   查询命令中新增scroll=1m,说明采用游标查询，保持游标查询窗口一分钟。
   这里由于测试数据量不够，所以size值设置为2。
   实际使用中为了减少游标查询的次数，可以将值适当增大，比如设置为1000。
   GET /es_db/_search?scroll=1m
   {
   "query": { "match_all": {}},
   "size": 2
   }
   查询结果会返回一个scroll id，后续的查询通过每次返回的scroll id查询。
   GET /_search/scroll
   {
   "scroll":"1m",
   "scroll_id":""
   }

但是在最新的版本中，官方已经不推荐使用scroll来进行深分页查询，更提倡我们使用search_after的方式来查询大量结果，想要更具体的信息可以查看官方文档 https://www.elastic.co/guide/en/elasticsearch/reference/7.14/paginate-search-results.html

3. 排序 sort
   ```json
   GET /es_db/_search
   {
       "query":{
           "match_all":{}
       },
       "sort":[
           {
               "age":"desc"
           }
       ]
   }
   ```

4. 返回指定字段
      ```json
      GET /es_db/_search
      {
          "query":{
              "match_all":{}
          },
          "_source":["name","address"]
      }

5. match
   match 在匹配时会对所查找的关键词进行分词，然后按分词匹配查找

   match支持以下参数：

   query : 指定匹配的值

   operator : 匹配条件类型

   and : 条件分词后都要匹配

   or : 条件分词后有一个匹配即可(默认)

   minmum_should_match : 最低匹配度，即条件在倒排索引中最低的匹配度
   ```json
   模糊匹配match分词后or的效果
   GET /es_db/_search
   {
   "query":{
   "match":{
   "address":"东莞溪流背坡村"
   }
   }
   }
   分词后and的效果
    GET /es_db/_search
   {
   "query": {
   "match": {
    "address": {
   "query": "东莞溪流背坡村",
   "operator": "AND"
    }
   }
   }
   }
   ```
   在match中的应用： 当operator参数设置为or时，minnum_should_match参数用来控制
   匹配的分词的最少数量。

   ```json
    # 最少匹配两个词
   GET /es_db/_search
   {
    "query": {
   "match": {
   "address": {
   "query": "东莞溪流",
    "minimum_should_match": 2
    }
   }
   }
   }
   ```

6. 短语查询match_phrase

        match_phrase查询分析文本并根据分析的文本创建一个短语查询。match_phrase 会将检
        
        索关键词分词。match_phrase的分词结果必须在被检索字段的分词中都包含，而且顺序必
        
        须相同，而且默认必须都是连续的。
        
        ```json
         GET /es_db/_search
         {
        "query": {
        "match_phrase": {
        "address": "广州白云山"
        }
         }
         }


​        
​        GET /es_db/_search
​        {
​        "query": {
​        "match_phrase": {
​         "address": "广州白云"
​        }
​        }
​        }
​        
​        查看分词结果
​        POST _analyze
​         {
​        "analyzer":"ik_max_word",
​        "text":"广州白云山"
​        }
​        ```
​        
​        如何解决词条间隔的问题？可以借助slop参数，slop参数告诉match_phrase查询词条能够相隔多远时仍然将文档视为匹配。
​        
        ```json
        广州云山分词后相隔为2，可以匹配到结果
         GET /es_db/_search
         {
         "query": {
         "match_phrase": {
         "address": {
         "query": "广州白云",
         "slop": 2
         }
         }
         }
         }
        ```

7. 多字段查询multi_match，可以根据字段类型，决定是否使用分词查询，得分最高的在前面

      ```json
       GET /es_db/_search
       {
       "query": {
       "multi_match": {
       "query": "广州李四",
       "fields": [
       "address",
       "name"
       ]
       }
       }
       }
      注意：字段类型分词,将查询条件分词之后进行查询，如果该字段不分词就会将查询条件作
      为整体进行查询。
      
      query_string
      允许我们在单个查询字符串中指定AND | OR | NOT条件，同时也和 multi_match query
      一样，支持多字段搜索。和match类似，但是match需要指定字段名，query_string是在所
      有字段中搜索，范围更广泛。
      注意: 查询字段分词就将查询条件分词查询，查询字段不分词将查询条件不分词查询
      
      未指定字段查询
      GET /es_db/_search
      {
      "query": {
       "query_string": {
      "query": "张三 OR 广州"
      }
      }
      }
      
      指定单个字段查询
      GET /es_db/_search
       {
      "query": {
       "query_string": {
       "default_field": "address",
       "query": "白云山 OR 橘子洲"
      }
      }
      }
      
      指定多个字段查询
      GET /es_db/_search
      {
      "query": {
      "query_string": {
      "fields": ["name","address"],
      "query": "张三 OR (广州 AND 王五)"
       }
      }
       }
      ```

8.  关键词查询term
      Term用来使用关键词查询(精确匹配),还可以用来查询没有被进行分词的数据类型。

      match在匹配时会对所查找的关键词进行分词，然后按分词匹配查找，而term会直接对关键词进行查找。一般模糊查找的时候，多用match，而精确查找时可以使用term。

      ES中默认使用分词器为标准分词器(StandardAnalyzer),标准分词器对于英文单

      词分词,对于中文单字分词。

      在ES的Mapping Type 中 keyword , date ,integer, long , double , boolean

      or ip 这些类型不分词，只有text类型分词。

      ```json
      采用term精确查询, 查询字段映射类型为keyword
      GET /es_db/_search
      {
      "query":{
       "term": {
      "address.keyword": {
      "value": "广州白云山公园"
      }
      }
      }
      }
      在ES中，Term查询，对输入不做分词。会将输入作为一个整体，在倒排索引中查找准确的
      词项，并且使用相关度算分公式为每个包含该词项的文档进行相关度算分。
      ```

9. 前缀查询prefix
它会对分词后的term进行前缀搜索。
它不会分析要搜索字符串，传入的前缀就是想要查找的前缀
默认状态下，前缀查询不做相关度分数计算，它只是将所有匹配的文档返回，然
后赋予所有相关分数值为1。
prefix的原理：需要遍历所有倒排索引，并比较每个term是否已所指定的前缀开头。

```json
 GET /es_db/_search
 {
"query": {
 "prefix": {
 "address": {
 "value": "广州"
 }
 }
 }
 }
```

10. 通配符查询wildcard

    通配符查询：工作原理和prefix相同，只不过它不是只比较开头，它能支持更为复杂的匹配

    模式。

```json
1 GET /es_db/_search
2 {
3 "query": {
4 "wildcard": {
5 "address": {
6 "value": "*白*"
7 }
8 }
9 }
10 }
```

11. 范围查询range

    ```json
    range：范围关键字
    gte 大于等于
    lte 小于等于
    gt 大于
    lt 小于
    now 当前时间
    
     POST /es_db/_search
     {
     "query": {
     "range": {
     "age": {
     "gte": 25,
     "lte": 28
     }
     }
     }
     }
    ```

12. 多id查询ids

    ids 关键字 : 值为数组类型,用来根据一组id获取多个对应的文档

    ```json
     GET /es_db/_search
     {
     "query": {
     "ids": {
     "values": [1,2]
     }
     }
     }
    ```

13. 模糊查询fuzzy
    在实际的搜索中，我们有时候会打错字，从而导致搜索不到。在Elasticsearch中，我们可以使用fuzziness属性来进行模糊查询，从而达到搜索有错别字的情形。
    fuzzy 查询会用到两个很重要的参数，fuzziness，prefix_length
    fuzziness：表示输入的关键字通过几次操作可以转变成为ES库里面的对应field
    的字段操作是指：新增一个字符，删除一个字符，修改一个字符，每次操作可以记做编辑距离为1，
    如广洲到广洲编辑距离就是1，只需要修改一个字符；该参数默认值为0，即不开启模糊查询。
    prefix_length：表示限制输入关键字和ES对应查询field的内容开头的第n个字符，必须完全匹配，不允许错别字匹配，如这里等于1，则表示开头的字必须匹配，不匹配则不返回，默认值也是0，加大prefix_length的值可以提高效率和准确率。

    ```json
     GET /es_db/_search
     {
     "query": {
     "match": {
     "address": {
     "query": "广洲",
     "fuzziness": 1
     }
    }
     }
     }
    注意: fuzzy 模糊查询 最大模糊错误 必须在0-2之间
    搜索关键词长度为 2，不允许存在模糊
    搜索关键词长度为3-5，允许1次模糊
    搜索关键词长度大于5，允许最大2次模糊
    ```

14. 高亮highlight
    highlight 关键字: 可以让符合条件的文档中的关键词高亮。
    highlight相关属性：
    pre_tags 前缀标签
    post_tags 后缀标签
    tags_schema 设置为styled可以使用内置高亮样式
    require_field_match 多字段高亮需要设置为false

    ```json
     GET /products/_search
     {
     "query": {
     "term": {
     "name": {
     "address": "广州"
     }
     }
     },
     "highlight": {
    "fields": {
     "*":{}
     }
     }
     }
    
    自定义高亮html标签
    可以在highlight中使用pre_tags和post_tags
    
     GET /products/_search
     {
     "query": {
     "term": {
     "name": {
     "address": "广州"
     }
     }
     },
     "highlight": {
    "post_tags": ["</span>"],
    "pre_tags": ["<span style='color:red'>"],
    "fields": {
     "*":{}
     }
     }
     }
    
    多字段高亮
     GET /products/_search
     {
     "query": {
     "term": {
     "name": {
     "address": "广州"
     }
     }
     },
     "highlight": {
    "post_tags": ["</span>"],
    "pre_tags": ["<span style='color:red'>"],
    "fields": {
     "name":{},
     "address":{}
     }
     }
     }
    ```

    

15. **相关性和相关性算分**

    搜索是用户和搜索引擎的对话，用户关心的是搜索结果的相关性

    是否可以找到所有相关的内容

    有多少不相关的内容被返回了

    文档的打分是否合理

    结合业务需求，平衡结果排名

    如何衡量相关性：

    Precision(查准率)―尽可能返回较少的无关文档

    Recall(查全率)–尽量返回较多的相关文档

    Ranking -是否能够按照相关度进行排序

    **相关性（Relevance）**

    搜索的相关性算分，描述了一个文档和查询语句匹配的程度。ES 会对每个匹配查询条件的

    结果进行算分_score。打分的本质是排序，需要把最符合用户需求的文档排在前面。ES 5之

    前，默认的相关性算分采用TF-IDF，现在采用BM 25。

    **Boosting**

    Boosting是控制相关度的一种手段。

    参数boost的含义：

    当boost > 1时，打分的权重相对性提升

    当0 < boost <1时，打分的权重相对性降低

    当boost <0时，贡献负分

    返回匹配positive查询的文档并降低匹配negative查询的文档相似度分。这样就可以在不排

    除某些文档的前提下对文档进行查询,搜索结果中存在只不过相似度分数相比正常匹配的要

    低;

    ```json
    GET /test_score/_search
    {
     "query": {
     "boosting": {
     "positive": {
     "term": {
     "content": "elasticsearch"
     }
     },
     "negative": {
     "term": {
    "content": "like"
     }
    },
     "negative_boost": 0.2
     }
     }
     }
    应用场景：希望包含了某项内容的结果不是不出现，而是排序靠后。
    ```

    

16. **布尔查询bool Query**

    一个bool查询,是一个或者多个查询子句的组合，总共包括4种子句，其中2种会影响算分，

    2种不影响算分。

    must: 相当于&& ，必须匹配，贡献算分

    should: 相当于|| ，选择性匹配，贡献算分

    must_not: 相当于! ，必须不能匹配，不贡献算分

    filter: 必须匹配，不贡献算分

    在Elasticsearch中，有Query和 Filter两种不同的Context

    Query Context: 相关性算分

    Filter Context: 不需要算分 ,可以利用Cache，获得更好的性能

    ```json
    bool查询语法
    子查询可以任意顺序出现
    可以嵌套多个查询
    如果你的bool查询中，没有must条件,should中必须至少满足一条查询
    
    ```

    

## 三、ElasticSearch聚合操作
