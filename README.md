# ElasticSearch

## 基本的CRUD

### 增加

```
指定ID创建新文档，如果已经存在，会报错：
POST/PUT users/_create/3
{
  "context":"小蕾蕾获取了今年的奥数冠军",
  "age":19
}

指定ID创建新文档，如果已经存在，会报错：
POST/PUT users/_doc/1?op_type=create
{
  "age":11,
  "user":"chencheng"
}


不指定ID创建(只能使用POST)
POST users/_doc
{
  "context":"张三捡到了5元",
  "age":19
}

```

### 删除

```
删除文档：DELETE index/_doc/1
删除索引：DELETE index
```

### 修改

```
Index = 删除再创建 版本号+1
POST/PUT users/_doc/1?op_type=index  (默认就是index，可以不用显示的写出来)
{
  "age":11,
  "user":"chencheng"
}

Update = 会对文档做增量的更新  更新成功 版本号+1 更新不成功(没有更新的内容) 版本号不+1
POST users/_update/1
{
  "doc":{
    "age":22 
  }
}
```

### 查询

```
查询文档：GET index/_doc/1
查询索引配置：GET index
```

### 注意：

**Index 和 Create 不一样的地方：如果文档不存在，就索引新的文档。否则现有文档会被删除，新的文档被索引。版本信息+1**

## 批量操作

### bulk 

批量操作，在一次请求中多次操作索引

```
POST _bulk
{"create":{"_index":"users","_id":18}}
{"doc":{"name":"王武","age":"36"}}
{"delete":{"_index":"users","_id":3}}
{"update":{"_index":"users","_id":4}}
{"doc":{"name":"小蕾"}}
{"index":{"_index":"users","_id":1}}
{"doc":{"context":"李思想去考研!!!"}}
```

### mget

只能根据ID批量获取


```
GET _mget
{
  "docs":
  [
    {
      "_index":"users",
      "_id":1
    },
     {
      "_index":"users",
      "_id":2
    },
     {
      "_index":"users",
      "_id":3
    }
  ]
}

如果index都一样可以简写成下面：

GET users/_mget
{
  "docs":
  [
    {
      "_id":1
    },
     {
      "_id":2
    },
     {
      "_id":3
    }
  ]
}
```

### msearch

根据条件批量获取

```
GET/POST users/_msearch
{}
{"query":{"match_all":{}},"from":4,"size":1}
{}
{"query":{"match":{"_id":1}}}
```

### 注意

**mget 是通过文档ID列表得到文档信息。msearch 是根据查询条件，搜索到相应文档。**

## 倒排索引

**[倒排索引](https://blog.csdn.net/andy_wcl/article/details/81631609)(Inverted Index)**：倒排索引是实现“单词-文档矩阵”的一种具体存储形式，通过倒排索引，可以根据单词快速获取包含这个单词的文档列表。倒排索引主要由两个部分组成：“单词词典”和“倒排文件”。

Elasticsearch的JSON文档中的每个字段，都有自己的倒排索引

可以指定对某些字段不做索引

  -  优点：节省存储空间
  -  缺点：字段无法被搜索

## 分词

- Analysis-文本分析是把全文本转换一系列单词（term/token）的过程，也叫分词

- Analysis 是通过Analyzer来实现的I 

- - 可使用Elasticsearch内置的分析器或者按需定制化分析器

- 除了在数据写入时转换词条，匹配Query 语句时候也需要用相同的分析器对查询语句进行分析
