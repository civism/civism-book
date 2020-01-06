# ES基本操作

**1.创建mapping**

```json
PUT guava
{
  "settings":{
     "index.number_of_replicas":1,
      "index.number_of_shards":5,
      "index.refresh_interval": "10s",
      "analysis": {
        "analyzer": {
          "comma":{
            "type": "pattern",
            "pattern":","
          }
        }
      }
  },
  "mappings": {
    "_doc":{
      "properties":{
        "distributor_scope":{
           "type":"text",
           "analyzer": "comma", 
           "search_analyzer": "comma" 
        },
        "buyer_nick":{
          "type":"keyword"
        },
        "pay_time":{
          "type":"date",
          "format":"yyyy-MM-dd HH:mm:ss"
        },
        "receiver_address":{
          "type":"text",
          "analyzer":"ik_max_word",
          "fields":{
            "address":{
              "type": "keyword",
              "ignore_above" : 256
            }
          }
        },
        "payment":{
          "type":"double"
        },
        "tid":{
          "type":"long"
        }
      }   
    }
  }
}
```



>distributor_scope: 用了自定义分词器，搜索也会用comma分词，comma分词是自定义逗号分词
>
>buyer_nick：keyword类型
>
>receiver_address：申明为text类型，并且采用了IK分词器,同时申明了一个address字段别名，申明了keyword类型
>
>pay_time: 申明为date类型，格式化时间方式为：y y y y-MM-dd HH:mm:ss

**2.查看结构**

> GET guava/_mapping

**3.新增数据**

```json
POST guava/_doc
{
  "buyer_nick":"civism",
  "distributor_scope":"12,22,33,44,55,1,2",
  "pay_time":"2020-01-01 22:00:00",
  "payment":"99.98",
  "receiver_address":"浙江省杭州市余杭区好运街",
  "tid":1213030893145321475
}

POST guava/_doc
{
  "buyer_nick":"guava",
  "distributor_scope":"1,2,3,4,5,6",
  "pay_time":"2020-01-01 10:00:00",
  "payment":"12.98",
  "receiver_address":"浙江省杭州市余杭区IT公园",
  "tid":1213036652945625089
}
```

**4.查询数据**

 * 查询所有

   ```json
   GET guava/_search
   {
     "query": {
       "match_all": {}
     }
   }
   ```

* 精准查询

  ```json
  GET guava/_search
  {
    "query": {
      "term": {
        "buyer_nick": {
          "value": "guava"
        }
      }
    }
  }
  ```

* 模糊类查询

  ```json
  GET guava/_search
  {
    "query": {
      "match": {
        "receiver_address": "IT公园"
      	}
      }
    }
  }
  ```

* 当用term与match分别对distributor_scope查询

  >GET guava/_search
  >{
  >  "query": {
  >    "term": {
  >      "distributor_scope": {
  >        "value": "1"
  >      }
  >    }
  >  }
  >}
  >GET guava/_search
  >{
  >  "query": {
  >    "match": {
  >      "distributor_scope": "1"
  >    }
  >    }
  >  }
  >}
  >出现结果一样,虽然term是精准查询，但是在distributor_scope由于指明了搜索分词，所以也被查询出来了
  >
  >* **term查询**
  >
  >  term是代表完全匹配，也就是精确查询，**搜索前不会再对搜索词进行分词**，所以我们的**搜索词必须是文档分词集合中的一个**。比如说我们要找标题为北京奥运的所有文档
  >
  >* **Match类查询**
  >
  >  match查询会先对搜索词进行分词,分词完毕后再逐个对分词结果进行匹配，因此相比于term的精确搜索，match是分词匹配搜索

* 修改数据

  * 部分修改

    ```json
    POST guava/_doc/需要修改字段的ID/_update
    { 
      "doc": {
        "payment":"24.98"
      }
    }
    ```

  * 全部修改

    ```json
    POST guava/_doc/d3XZeW8BTHFK4TZn1ZWg
    { 
      "doc": {
        "payment":"24.98"
      }
    }
    ```

    

**5.删除数据**

 * 根据主键删除数据

   >Delete 索引名称/文档名称/主键编号

 * 根据匹配条件删除数据

   ```json
   POST 索引名称/文档名称/_delete_by_query   
   {
     "query":{
       "term":{
         "_id":"d3XZeW8BTHFK4TZn1ZWg"
       }
     }
   }
   ```

* 删除所有数据

  ```json
  POST 索引名称/文档名称/_delete_by_query?pretty
  {
      "query": {
          "match_all": {
          }
      }
  }
  ```

**6.删除索引**

> 删除单个索引：delete 索引名称
>
> 删除多个索引：delete 索引名称1 索引名称2
>
> 删除所有：delete _all   (对数据安全来说，能够使用单个命令来删除所有的数据可能会带来很可怕的后果，所以，为了避免大量删除，可以在**elasticsearch.yml** 配置文件中修改 **action.destructive_requires_name: true**设置之后只限于使用特定名称来删除索引，使用_all 或者通配符来删除索引无效)

**查看IK分词结果**

```json
POST guava/_analyze 
{
   "analyzer": "ik_max_word",
 	 "text":     "浙江省杭州市余杭区IT公园"
}
```

