## SeaweedFS的几个概念

Node：系统抽象的节点，抽象为DataCenter、Rack、DataNode

DataCenter：数据中心，对应现实中的不同机房

Rack：机架，对应现实中的机柜

Datanode：存储节点，用于管理、存储逻辑卷

Volume：逻辑卷，存储的逻辑结构，逻辑卷下存储Needle

Needle：逻辑卷中的Object，对应存储的文件

Collection：文件集，可以分布在多个逻辑卷上

## 启动 SeaweedFS

```shell
# 启动 master
nohup ./weed master -mdir="./data/master" > ./logs/master/master.log 2>&1 &

# 配置 volume 逻辑卷
nohup ./weed volume -dir="./data/volume/vol1" -mserver="test.com:9333" -port=8081 > ./logs/volume/vol1.log 2>&1 &
```

## 访问 SeaweedFS

```shell
# 请求 master，分配一个逻辑卷和 fid
curl http://127.0.0.1:9333/dir/assign
# 返回结果
{"fid":"3,01ed433ef4","url":"127.0.0.1:8081","publicUrl":"127.0.0.1:8081","count":1}

# 使用返回的 url 和 fid 上传文件
curl -F file=@/root/test.txt http://127.0.0.1:8081/3,01ed433ef4
# 返回结果
{"name":"test.txt","size":23,"eTag":"cdbb2ef7","mime":"text/plain"}

# 下载文件
wget http://127.0.0.1:8081/3,01ed433ef4
# 删除文件
curl -X DELETE http://127.0.0.1:8081/3,01ed433ef4

# master 自动生成 fid 并上传至 volume
curl -F file=@/root/report.js http://127.0.0.1:9333/submit
```

## Filer Server

Filer Server 是将文件以文件目录的方式上传到图片服务，然后你根据文件目录的方式访问。

```shell
# 生成 filer.toml 配置文件
./weed scaffold -config=filer -output=.
# 修改 filer.toml 配置文件
[leveldb2]
dir = "./ftmp" # leveldb2 数据库存储位置

# 启动 filer 服务
nohup ./weed filer > ./logs/filer/filer.log 2>&1 &

curl -F file=@report.js "http://localhost:8888/javascript/new_name.js" # 上传文件
curl  -H "Accept: application/json" "http://localhost:8888/javascript/?pretty=y"
curl -X DELETE http://localhost:8888/path/to/file # 删除文件
curl -X DELETE http://localhost:8888/path/to/dir?recursive=true # 删除文件夹
```

## 参考文章

- https://blog.wangqi.love/articles/seaweedfs/seaweedfs搭建与使用.html
- https://www.jianshu.com/p/5e8dfb98fd86
- http://blog.zollty.com/b/archive/summary-of-SeaweedFS-for-distributed-file-storage.html
- https://github.com/bingoohuang/blog/issues/57
- https://ghostsf.com/seaweedfs/
- https://gschaos.club/2019/09/19/SeaweedFS/

