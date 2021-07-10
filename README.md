docker运行命令：
docker run -it --name eureka-qt-build -p 8761:8761 -d 50034486/eureka:v1

-it 互动模式
--name 容器名称
-p 端口映射

创建网络
docker network create -d bridge newnet
172.17.0.0/16

docker run --network testnet 指定网络

容器间访问可以使用容器名加端口
--network-alias可以配置别名

docker logs -f -t --tail=100 CONTAINER_ID

进入容器
docker exec -it mysql bash


