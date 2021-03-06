#### elasticsearch操作手册

##### 查看elasticsearch集群状态

```
curl -XGET http://172.18.44.81:9200/_cluster/health?pretty=true
```

##### 移动分片位置

```
curl -XPOST "http://172.17.14.149:9200/_cluster/reroute" -d -H 'Content-Type: application/json'  '{"commands" : [ {"move" : {"index" : "stkpooladjust","shard" : 1,"from_node" : "node-4","to_node" : "node-1"}}]}'
```

##### 关闭node-4节点

```
curl -XPOST 'http://172.17.14.149:9200/_cluster/nodes/node-4/_shutdown'
```

##### 暂停集群shard自动均衡

```
curl -XPUT 'http://172.16.15.140:9200/_cluster/settings' -H 'Content-Type: application/json'  -d '{"transient": {"cluster.routing.allocation.enable" : "none"}}'

curl -XPUT 'http://172.16.15.140:9200/_cluster/settings' -H 'Content-Type: application/json'  -d '{"transient": {"cluster.routing.allocation.enable" : "all"}}'
```

##### 设置最小主节点数，可防止脑裂

```
curl -XPUT http://172.17.14.58:9200/_cluster/settings {"persistent" : {"discovery.zen.minimum_master_nodes" : 2}}	
```

##### 复制index，将empchat复制为empchatnew

```
curl -XPOST http://172.17.14.229:9200/_reindex -H 'Content-Type: application/json' -d '{"source": { "index": "empchat"},"dest": {"index": "empchatnew" }}'
```

##### 设置索引副本数量

```
curl -XPUT "http://14.18.189.139:9200/_settings" -H 'Content-Type: application/json' -d '{"number_of_replicas": 1}'

curl -XPUT "http://14.18.189.140:9200/test/_settings" -H 'Content-Type: application/json' -d '{"number_of_replicas": 1}'
```

##### 添加快照仓库

```
curl -XPUT 'http://172.16.15.140:9200/_snapshot/backup' -H 'Content-Type: application/json' -d '{"type": "fs","settings": {"location": "/opt/backup","compress": true}'
```

##### 查询快照仓库

```
curl http://172.16.15.135:9200/_snapshot/
```

##### 查看my_backup的所有快照

```
curl http://172.17.14.150:9200/_snapshot/my_backup/_all
```

##### 新增快照

```
curl -XPUT 'http://172.17.14.150:9200/_snapshot/my_backup/backup20190123?wait_for_completion=true'
```

##### 查询快照进度

```
curl -XGET 'http://172.17.14.150:9200/_snapshot/my_backup/backup20190115/_status'
```

##### 删除快照

```
curl -XDELETE 'http://172.16.15.140:9200/_snapshot/backup/backup20190115'
```

##### 删除快照仓库

```
curl -XDELETE 'http://172.16.15.140:9200/_snapshot/backup'
```