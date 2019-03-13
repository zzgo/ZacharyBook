### elasticsearch操作

#### 简单的查询

```java
[root@localhost ~]# curl -H 'Content-Type: application/json' -XGET 'localhost:9200/_count?pretty' -d '
> {
> "query": {
> "match_all": {}
> }
> }'
{
  "count" : 0,
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "skipped" : 0,
    "failed" : 0
  }
}
```

** curl -XPOST 'http://localhost:9200/shutdown' 关闭服务API命令**_ _~~_,_\_shutdown已被移除~~

