## 相关命令

### 前台启动

docker-compose up

### 后台启动

docker-compose up -d

### 启动工程中所有服务的容器

docker-compose start

### 停止工程中所有服务的容器

docker-compose stop

### 停止并删除工程中所有服务的容器、网络、数据卷

docker-compose down -v

### 停止并删除工程中所有服务的容器、网络、镜像

docker-compose down --rmi all

## 对虚拟内存配置错误进行故障排查
当首次启动 Elasticsearch 节点时，许多用户会遇到虚拟内存配置方面的问题，并收到错误消息，例如：
```
{"@timestamp":"2023-04-14T13:16:22.148Z", "log.level":"ERROR", "message":"node validation exception\n[1] bootstrap checks failed. You must address the points described in the following [1] lines before starting Elasticsearch.\nbootstrap check failure [1] of [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]", "ecs.version": "1.2.0","service.name":"ES_ECS","event.dataset":"elasticsearch.server","process.thread.name":"main","log.logger":"org.elasticsearch.bootstrap.Elasticsearch","elasticsearch.node.name":"es01","elasticsearch.cluster.name":"docker-cluster"}
```
这里的关键点是 max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]。

最终，需要在容器所在的位置运行命令 sysctl -w vm.max_map_count=262144。

## es异常退出

```
es01-1        | ERROR: Elasticsearch exited unexpectedly
es01-1 exited with code 137    
```
此报错是内存不足，增加es内存限制。
## es01 Your kernel does not support swap limit capabilities or the cgroup is not mounted. Memory limited without swap.
关闭swap
一、临时关闭

swapoff -a
二、查看效果

free -h
三、启用swap分区（不必操作）

swapon -a
四、永久关闭

vim /etc/sysctl.conf
#修改vm.swappiness为0
vm.swappiness=0
#保存退出后使配置生效
sysctl -p