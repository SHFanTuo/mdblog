

[toc]



#### 文件压缩

```
tar -czvf  /app/deploy/log/hs-sell-api2/app.log.tar.gz /app/deploy/log/hs-sell-api2/app.log
```



#### 查看某个目录的硬盘占用
```
 du -h --max-depth=1 /app/jenkins/jobs
```


#### nginx-transfer


```shell
docker run --name nginx-transfer --restart=always -v /app/nginx/transfer/conf.d:/etc/nginx/conf.d -v /app/nginx/transfer/html:/usr/share/nginx/html:ro -p 80:80 -p 443:443 -d nginx:1.23.2


docker run --name nginx-transfer --restart=always -v /app/nginx/transfer/conf.d:/etc/nginx/conf.d -v /app/nginx/transfer/html:/usr/share/nginx/html:ro -p 80:80 -p 443:443 -d nginx:1.23.2

# nginx配置文件拷贝至宿主机
docker cp d3bb2a3a831a:/etc/nginx/etc/nginx/conf.d/default.con
app/nginx/default.conf

docker exec -i nginx-transfer nginx -s reload

```


#### static
```shell
docker run --name nginx-static --restart=always -v /app/nginx/static/conf.d:/etc/nginx/conf.d -v /app/nginx/static/html:/usr/share/nginx/html:ro -p 23384:80  -d nginx:1.23.2

```


#### jenkins

- http://101.132.189.6:23330/
- admin/f5641038
- gitlab token: _KYpp2EA83KUjgc7sJAE
- 

#### redis
```
docker run -d --name redis-qa -p 23326:6379 redis:6.2.6 --requirepass "P8Y3aZGGbgZUMt5f2JV2"

docker run -d --name redis-prod -p 23328:6379 redis:6.2.6 --requirepass "P8Y3aZGGbgZUMt5f2JV2"
```

```shell
docker run -d --name jenkins -p 23330:8080 -p 23331:50000 --restart=always --privileged=true -u root -e JAVA_OPTS='-Duser.timezone=Asia/Shanghai' -e JAVA_OPTS='-Dfile.encoding=UTF8'  -v /etc/localtime:/etc/localtime:ro -v /app/jenkins:/var/jenkins_home jenkins/jenkins:2.277.1-lts-centos7
```

```shell
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum upgrade
sudo yum install jenkins java-1.8.0-openjdk-devel
sudo systemctl daemon-reload
```

```shell
[root@heysee-test down]# vi /etc/sysconfig/jenkins
# 修改端口
[root@heysee-test down]# systemctl restart jenkins


#maven配置
ll /var/lib/jenkins/hudson.tasks.Maven_MavenInstallation/
```



#### xxl-job

- admin / aGcaXZNzMpmdk4kKdrJx



```
docker pull xuxueli/xxl-job-admin:2.3.0

docker run -e PARAMS="--spring.datasource.url=jdbc:mysql://rm-uf6fq96fw0m8m7223.mysql.rds.aliyuncs.com:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=hs_prod --spring.datasource.password=XiJqsrcaSJ5eGYKxQBLU --xxl.job.accessToken=sXzjQA3QMy7iSyudGvj5" -p 29101:8080 -v /app/xxl-job-admin:/data/applogs --name xxl-job-admin_prod  -d xuxueli/xxl-job-admin:2.3.0
```

#### phpMyAdmin


```
docker run --name myadmin -d --restart always -e PMA_HOST=rm-uf6fq96fw0m8m7223.mysql.rds.aliyuncs.com -e PMA_PORTS=3306 -e MYSQL_USER=myadmin -e MYSQL_PASSWORD=tuYiHAKp5ESP7yf62Yiw -p 29101:80 phpmyadmin:5.1.1
```



#### postgresSql 9.6.19

-   数据库
-   支撑wiki
-   端口：23311


```shell
docker run -d     --name some-postgres     -p 23311:5432     -e POSTGRES_PASSWORD=5641038     -e PGDATA=/var/lib/postgresql/data/pgdata     -v /app/pgsql9.16/custom/mount:/var/lib/postgresql/data postgres:9.6.19
```



#### wiki

-   wiki站点
-   windinsky122@163.com / 2wsx@WSX


```shell
docker run -d -p 23355:3000 \
    --name wiki --restart unless-stopped \
    --link some-postgres:some-postgres \
    -e "DB_TYPE=postgres" -e "DB_HOST=some-postgres" -e "DB_PORT=5432" -e "DB_USER=postgres" -e "DB_PASS=5641038" -e "DB_NAME=wiki" \
requarks/wiki:2.5
```

#### EMQ（prod）

- dashboard: （公） http://101.132.189.6:18083/   
- tcp：（私）tcp://172.22.144.56:1883 （公）tcp://101.132.189.6:1883
- admin/5WqpIJqzb2DHcwPSjcEb
- emqProd/mDQf3oJVyimhHQbx2g7d
- emqMachine/zpZmpabKXSqRLvrjxfDk



#### kkfileview

```shell
docker run -d -it --name kkfileview -p 38012:8012 registry-vpc.cn-shanghai.aliyuncs.com/heysee/kkview:4.4.0
```



| 容器端口 | 宿主机端口 | 说明                    |
| -------- | ---------- | ----------------------- |
| 1883     | 27101      | MQTT 协议端口           |
| 8883     | 27102      | MQTT/SSL 端口           |
| 8083     | 27103      | MQTT/WebSocket 端口     |
| 8084     | 27104      | MQTT/WebSocket/SSL 端口 |
| 8081     | 27105      | 管理 API 端口           |
| 18083    | 27106      | Dashboard 端口          |

```shell
docker pull emqx/emqx:4.3.0
docker run -d --name emqx-new --restart=always -e TZ=Europe/Amsterdam -p 27101:1883 -p 27102:8883 -p 27103:8083 -p 27104:8084 -p 27105:8081    -p 27106:18083 emqx/emqx:4.3.0

# 更改默认用户可以订阅$SYS
docker exec -it emqx /bin/bash
vi /opt/emqx/etc/acl.conf

#更改
{allow, {user, "emqProd"}, subscribe, ["$SYS/#"]}.

./bin/emqx_ctl acl reload

emqx restart

# 关闭匿名

# etc/emqx.conf

## Value: true | false
allow_anonymous = true
```



#### EMQ（pre）

- dashboard: （公） https://emq-pre.heysee.cn
- tcp：（私）tcp://172.22.144.56:1883 （公）tcp://101.132.189.6:1883
- admin/5WqpIJqzb2DHcwPSjcEb
- emqProd/mDQf3oJVyimhHQbx2g7d
- emqMachine/zpZmpabKXSqRLvrjxfDk



| 容器端口 | 宿主机端口 | 说明                    |
| -------- | ---------- | ----------------------- |
| 1883     | 27101      | MQTT 协议端口           |
| 8883     | 27102      | MQTT/SSL 端口           |
| 8083     | 27103      | MQTT/WebSocket 端口     |
| 8084     | 27104      | MQTT/WebSocket/SSL 端口 |
| 8081     | 27105      | 管理 API 端口           |
| 18083    | 27106      | Dashboard 端口          |

```shell
docker pull emqx/emqx:4.3.0
docker run -d --name emqx-pre --restart=always -p 27201:1883 -p 27202:8883 -p 27203:8083 -p 27204:8084 -p 27205:8081    -p 27206:18083 emqx/emqx:4.3.0

# 更改默认用户可以订阅$SYS
docker exec -it emqx /bin/bash
vi /opt/emqx/etc/acl.conf

#更改
{allow, {user, "emqProd"}, subscribe, ["$SYS/#"]}.

./bin/emqx_ctl acl reload

emqx restart

# 关闭匿名

# etc/emqx.conf

## Value: true | false
allow_anonymous = true
```



#### bookstack

- admin@admin.com
- password

```
docker run -d \
  --name=bookstack \
  -e PUID=1000 \
  -e PGID=1000 \
  -e APP_URL= \
  -e DB_HOST=47.116.74.114:23320 \
  -e DB_USER=root \
  -e DB_PASS=5641038 \
  -e DB_DATABASE=bookstackapp \
  -e APP_URL=http://book.heysee.cn \
  -p 26875:80 \
  -v /app/bookstack/data:/config \
  --restart unless-stopped \
  linuxserver/bookstack:version-v21.05.2
```



```
docker run -d --net bookstack_nw  \

docker run -d --name=bookstack  \
-e DB_HOST=47.116.74.114:23320 \
-e DB_DATABASE=bookstack \
-e DB_USERNAME=root \
-e DB_PASSWORD=5641038 \
-e APP_URL=http://book.heysee.cn \
-p 26875:8080 \
 solidnerd/bookstack:0.31
```




#### Portainer 1.24

admin / f5641038

```shell
docker run -p 23900:9000 -p 23800:8000 --name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /app/portainer/data:/data \
-d portainer/portainer
```



```
docker run -d -p 9001:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent
```





#### Portainer 2.5

admin / f5641038

```shell
docker run -p 23901:9000 -p 23801:8000 --name portainer2 \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /app/portainer2/data:/data \
-d portainer/portainer-ce:2.5.1
```



#### rabbit

-   缓存
-   开发环境
-   端口：23340(面板)，23342
-   账户：sellapi / e5SJkZb2vdYg6eb


```shell
docker  run -d --hostname rabbit -p 23340:15672 -p 23342:5672 --name rabbit -e RABBITMQ_DEFAULT_USER=rbUser -e RABBITMQ_DEFAULT_PASS=AaTGB  rabbitmq:3.8.14-management

# 打开mqtt
rabbitmq-plugins enable rabbitmq_mqtt


# 加载延时消息队列
docker exec -it rabbit /bin/bash

cd /plugins
apt-get update
apt-get install wget 
wget https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases/download/3.8.9/rabbitmq_delayed_message_exchange-3.8.9-0199d11c.ez
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```



####  grafana

admin/

```
docker run -d --name=grafana -p 23370:3000 grafana/grafana:7.5.7
```

####  loki



```shell
docker pull grafana/loki:2.3.0

docker pull grafana/promtail:2.3.0




```

```shell
# 可略
mkdir -p /app/loki

vi /app/loki/loki-config.yaml
```

```shell
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 3110
  grpc_server_max_recv_msg_size: 1073741824  #grpc最大接收消息值，默认4m
  grpc_server_max_send_msg_size: 1073741824  #grpc最大发送消息值，默认4m

ingester:
  lifecycler:
    address: 127.0.0.1
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1
    final_sleep: 0s
  chunk_idle_period: 5m
  chunk_retain_period: 30s
  max_transfer_retries: 0
  max_chunk_age: 20m  #一个timeseries块在内存中的最大持续时间。如果timeseries运行的时间超过此时间，则当前块将刷新到存储并创建一个新块

schema_config:
  configs:
    - from: 2021-01-01
      store: boltdb
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 168h

storage_config:
  boltdb:
    directory: /opt/loki/index #存储索引地址
  filesystem:
    directory: /opt/loki/chunks

limits_config:
  enforce_metric_name: false
  reject_old_samples: true
  reject_old_samples_max_age: 168h
  ingestion_rate_mb: 30  #修改每用户摄入速率限制，即每秒样本量，默认值为4M
  ingestion_burst_size_mb: 15  #修改每用户摄入速率限制，即每秒样本量，默认值为6M

chunk_store_config:
        #max_look_back_period: 168h   #回看日志行的最大时间，只适用于即时日志
  max_look_back_period: 0s

table_manager:
  retention_deletes_enabled: false #日志保留周期开关，默认为false
  retention_period: 0s  #日志保留周期
```

```shell
docker run -d \
--name loki \
--privileged=true \
-v /app/loki:/mnt/config \
-v /app/loki/index:/opt/loki/index \
-v /app/loki/chunks:/opt/loki/chunks \
-p 3100:3100 \
grafana/loki:2.3.0
```


#### promtail

```
mkdir -p /app/promtail

vi /app/promtail/config/promtail-config.yaml
```

```shell
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

#把loki当客户端连接
clients:
  - url: http://172.22.144.58:3100/loki/api/v1/push

scrape_configs:
 - job_name: system
   pipeline_stages:
   static_configs:
   - targets:
      - localhost
     labels:
      #标签，用于后面的查询
      job: joyoh
      __path__: /var/log/api/*.log
 - job_name: system
   pipeline_stages:
   static_configs:
   - targets:
      - localhost
     labels:
     #标签，用于后面的查询
      job: test
      __path__: /var/log/oms/*.log
```



```
docker run -d \
--name promtail \
--privileged=true \
-v /app/promtail/config:/mnt/config \
-v /app/deploy/logtest/:/var/log \
grafana/promtail:2.3.0 -config.file=/mnt/config/promtail-config.yaml
```

#### nginx-qa



#### 禅道

```shell
sudo docker pull easysoft/zentao:15.5

sudo docker run --name zentao -p 23371:80  -v /app/zentao/zentaopms:/www/zentaopms -v /app/zentao/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=HpTD765cFJKdb8cpAtsH -d easysoft/zentao:15.5

```






 **为所有组件新增自启动**
>
 ```shell
 [root@heysee-test conf.d]# docker container update --restart=always nginx-transfer
 nginx-transfer
 [root@heysee-test conf.d]# ^C
 [root@heysee-test conf.d]# docker container update --restart=always hs-oms-web 
 hs-oms-web
 [root@heysee-test conf.d]# docker container update --restart=always mysql-qa
 mysql-qa
 [root@heysee-test conf.d]# docker container update --restart=always mysql-dev
 mysql-dev
 [root@heysee-test conf.d]# docker container update --restart=always emqx 
 emqx
 [root@heysee-test conf.d]# docker container update --restart=always redis-qa
 redis-qa
 [root@heysee-test conf.d]# docker container update --restart=always redis-dev
 redis-dev
 [root@heysee-test conf.d]# docker container update --restart=always wiki 
 wiki
 [root@heysee-test conf.d]# docker container update --restart=always some-postgres 
 some-postgres

 ```





**启动所有容器**

```shell
 docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)
```

