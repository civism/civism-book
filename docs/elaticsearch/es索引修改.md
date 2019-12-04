#ES索引修改


#背景



#实战经验
假如ES中存在索引item_index，需要添加一个嵌套对象并实现搜素,并且 item_index 是item_index_v1的别名
1. 查询item_index_v1的mapping
```
GET 查询item_index_v1的mapping/_mapping?pretty
```
2. 修改步骤1中的mapping结构体，（需要懂得ES的基础语法）

3. 创建修改过后的mapping，并命名为item_index_v2

4. 同步item_index_v1到item_index_v2中
```
POST _reindex
{
  "source": {
    "index": "item_index_v1"
  },
  "dest": {
    "index": "item_index_v2"
  }
}
```
5. 更新别名
```
POST _aliases
{
  "actions": [
    {
      "add": {
        "alias": "item_index",
        "index": "item_index_v2"
      }
    }
  ]
}
POST _aliases
{
  "actions": [
    {
      "remove": {
        "alias": "item_index",
        "index": "item_index_v1"
      }
    }
  ]
}

```
6. 验证数据
