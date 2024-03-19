

[toc]



# static
```shell
docker run --name nginx-static --restart=always -v /app/nginx/static/conf.d:/etc/nginx/conf.d -v /app/nginx/static/html:/usr/share/nginx/html:ro -p 23384:80  -d nginx:1.23.2

docker exec -i nginx-transfer nginx -s reload

```



# htpasswd 

用来给nginx加密，访问需要输入用户名和密码

```
touch /app/nginx/htpasswd/passwd

htpasswd -c /app/nginx/htpasswd/passwd tongqiang
htpasswd /app/nginx/htpasswd/passwd lixiang

```

- lixiang:  sWD64pep
- tongqiang:  UV28ht4i
- myadmin:   tuYiHAKp5ESP7yf62Yiw



# gitlab

-   git仓库
-   gitlab.heysee.cn
-   token
    -   jenkins：au-Q_VYSNHBmpxCU7jvi


```shell
sudo docker run --detach \
  --hostname gitlab.gy.com \
  --publish 23350:80 --publish 23353:443  --publish 23352:22 \
  --name gitlab \
  --restart always \
  --volume /app/gitlab/config:/etc/gitlab \
  --volume /app/gitlab/logs:/var/log/gitlab \
  --volume /app/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:13.9.1-ce.0
  
  15.9.3-ce.0
```


```shell
进入终端修改以下文件即可。

复制代码

vim /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
复制代码

## GitLab settings
  gitlab:
    ## Web server settings (note: host is the FQDN, do not include http://)
    host: localhost         >> 这里改为主机的IP即可
    port: 80
    https: false

    # Uncommment this line below if your ssh host is different from HTTP/HTTPS one
    # (you'd obviously need to replace ssh.host_example.com with your own host).
    # Otherwise, ssh host will be set to the `host:` value above

重启GitLab  
gitlab-ctl restart
```

# postgresSql 9.6.19

-   数据库
-   支撑wiki
-   端口：23311


```shell
docker run -d     --name some-postgres     -p 23311:5432     -e POSTGRES_PASSWORD=5641038     -e PGDATA=/var/lib/postgresql/data/pgdata     -v /app/pgsql9.16/custom/mount:/var/lib/postgresql/data postgres:9.6.19
```

# postgresSql 13.5

-   数据库
-   支撑odoo
-   端口：43311


```shell
docker run -d     --name odoo-postgres     -p 43311:5432     -e POSTGRES_PASSWORD=5641038     -e PGDATA=/var/lib/postgresql/data/pgdata     -v /app/pgsql3.5/custom/mount:/var/lib/postgresql/data postgres:13.5



docker run -d -e POSTGRES_USER=odoo -e POSTGRES_PASSWORD=odoo -e POSTGRES_DB=postgres  -e PGDATA=/var/lib/postgresql/data/pgdata     -v /app/pgsql3.5/custom/mount:/var/lib/postgresql/data --name odoo-postgres postgres:13.5
```

# odoo

```
docker run -v /app/odoo:/var/lib/odoo -d -p 8069:8069 --name odoo --link odoo-postgres:db -t odoo:15.0
```


# wiki

-   wiki站点
-   windinsky122@163.com / 2wsx@WSX


```shell
docker run -d -p 23355:3000 \
    --name wiki --restart unless-stopped \
    --link some-postgres:some-postgres \
    -e "DB_TYPE=postgres" -e "DB_HOST=some-postgres" -e "DB_PORT=5432" -e "DB_USER=postgres" -e "DB_PASS=5641038" -e "DB_NAME=wiki" \
requarks/wiki:2.5
```

# mysql-dev

-   数据库
-   开发环境
-   端口：23320


```shell
docker run --detach \
    --name mysql-dev \
    --publish 23320:3306 \
    --volume /app/database/mysql5.7-dev/:/var/lib/mysql \
    --env MYSQL_ROOT_PASSWORD=5641038 \
mysql:5.7.33
```

# mysql-qa

-   数据库
-   开发环境
-   端口：23322


```shell
docker run --detach \
    --name mysql-qa \
    --publish 23322:3306 \
    --volume /app/database/mysql5.7-qa/:/var/lib/mysql \
    --env MYSQL_ROOT_PASSWORD=5641038 \
mysql:5.7.33
```



# redis-dev

-   缓存
-   开发环境
-   端口：23326


```shell
docker run -d --name redis-dev -p 23326:6379 redis:6.0.8 --requirepass "3F2AD67"
```



# redis-qa

-   缓存
-   开发环境
-   端口：23328


```shell
docker run -d --name redis-qa -p 23328:6379 redis:6.0.8 --requirepass "3F2AD67"
```

# rabbit-local

-   缓存
-   开发环境
-   端口：23330，23332
-   账户：mqtt/mqtt123


```shell
docker  run -d --hostname rabbit -p 23330:15672 -p 23332:5672 --name rabbit -e RABBITMQ_DEFAULT_USER=rbUser -e RABBITMQ_DEFAULT_PASS=AaTGB  rabbitmq:3.8.15-management

# 打开mqtt
rabbitmq-plugins enable rabbitmq_mqtt

# 加载延时消息队列
docker exec -it rabbit /bin/bash

cd /plugins
apt-get update
apt-get install wget 
wget https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases/download/v3.8.0/rabbitmq_delayed_message_exchange-3.8.0.ez
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

# nginx-qa


```shell
docker run --name nginx-transfer --restart=always -v /app/nginx/transfer/conf.d:/etc/nginx/conf.d -v /app/nginx/transfer/html:/usr/share/nginx/html:ro -p 80:80 -p 443:443 -d nginx:1.23.2


docker run --name nginx-transfer --restart=always -v /app/nginx/transfer/conf.d:/etc/nginx/conf.d -v /app/nginx/transfer/html:/usr/share/nginx/html:ro -p 80:80 -p 443:443 -d nginx:1.23.2

# nginx配置文件拷贝至宿主机
docker cp d3bb2a3a831a:/etc/nginx/etc/nginx/conf.d/default.conf /app/nginx/default.conf
```

# jenkins

- http://47.116.74.114:23330

- windinsky122
    f5641038
    windinsky122@163.com

- dev
    9qRYGMG8D0PUPtZpbBaq
    
- admin

    f5641038


```shell
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum upgrade
sudo yum install jenkins java-1.8.0-openjdk-devel
sudo systemctl daemon-reload
```



```shell
docker run -d --name jenkins -p 23330:8080 -p 23331:50000 --restart=always  -v /etc/localtime:/etc/localtime:ro -v /app/jenkins:/var/jenkins_home -e JAVA_OPTS="-Dsun.jnu.encoding=UTF-8 -Dfile.encoding=UTF-8" jenkins/jenkins:2.277.1-lts-centos7
```

```shell
chmod /app/jenkins 777
```





```shell
[root@heysee-test down]# vi /etc/sysconfig/jenkins
# 修改端口
[root@heysee-test down]# systemctl restart jenkins


#maven配置
ll /var/lib/jenkins/hudson.tasks.Maven_MavenInstallation/
```



# xxl-job

- admin / vgJByEWFZ2ZWgLHHMkVt



```
docker pull xuxueli/xxl-job-admin:2.3.0

docker run -e PARAMS="--spring.datasource.url=jdbc:mysql://172.19.183.135:23320/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=root --spring.datasource.password=5641038 --xxl.job.accessToken=R8i8ehrW5ZuJgnACiaLF" -p 29101:8080 -v /app/xxl-job-admin:/data/applogs --name xxl-job-admin  -d xuxueli/xxl-job-admin:2.3.0
```





# EMQ





- qa: http://47.116.74.114:18083/
- admin/3edc#EDC
- emqProd/ahWnUHsUUeQMnc5tIMia
- emqMachine/hTRaTrjCt5jwhxRdFxu9


```shell
docker pull emqx/emqx:4.3.0
docker run -d --name emqx --restart=always -p 1883:1883 -p 8081:8081 -p 8083:8083 -p 8084:8084 -p 8883:8883 -p 18083:18083 emqx/emqx:4.3.0

# 更改默认用户可以订阅$SYS
docker exec -it emqx /bin/bash
vi /opt/emqx/etc/acl.conf

#更改
{allow, {user, "emqProd"}, subscribe, ["$SYS/#"]}.
```


# Portainer

admin / f5641038

```
docker run -p 23900:9000 -p 23800:8000 --name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /app/portainer/data:/data \
-d portainer/portainer
```


# ngrinder

```
docker run -d -v ~/ngrinder-controller:/opt/ngrinder-controller --name controller -p 8099:80 -p 16001:16001 -p 12000-12009:12000-12009 ngrinder/controller
```



# kong

```shell
docker run -d --name kong \
    -e "KONG_DATABASE=off" \
    -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
    -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
    -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
    -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
    -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
    -p 18000:8000 \
    -p 18443:8443 \
    -p 18001:8001 \
    -p 18444:8444 \
    kong:2.7.0
```


# PaddleOCR

```shell
docker run --name paddleocr -d -v /app/paddleocr:/mnt -p 8888:8888 --shm-size=8g paddlecloud/paddleocr:2.6-cpu-latest
```

# ELK

> https://www.docker.elastic.co/



| 更改宿主机内存可用数量                                       |
| ------------------------------------------------------------ |
| max_map_count文件包含限制一个进程可以拥有的VMA(虚拟内存区域)的数量。虚拟内存区域是一个连续的虚拟地址空间区域。在进程的生命周期中，每当程序尝试在内存中映射文件，链接到共享内存段，或者分配堆空间的时候，这些区域将被创建。调优这个值将限制进程可拥有VMA的数量。限制一个进程拥有VMA的总数可能导致应用程序出错，因为当进程达到了VMA上线但又只能释放少量的内存给其他的内核进程使用时，操作系统会抛出内存不足的错误。如果你的操作系统在NORMAL区域仅占用少量的内存，那么调低这个值可以帮助释放内存给内核用。 |

  

```shell
  在 /etc/sysctl.conf文件最后添加一行（重启变更）
  vm.max_map_count=262144
  
  执行
  sysctl -w vm.max_map_count=262144
```

## elasticsearch

```shell
  docker pull elasticsearch:7.16.2
  
  chmod -R 777 /app/elasticsearch/
```

```shell
  docker run --name elasticsearch -d -p 29200:9200 -p 29300:9300 -v /app/elasticsearch/data:/usr/share/elasticsearch/data -e ES_JAVA_OPTS="-Xms1g -Xmx4g" -e "discovery.type=single-node" elasticsearch:7.16.2
```

- 设置安全策略

```shell
  docker exec -it elasticsearch /bin/bash
  vi /usr/share/elasticsearch/config/elasticsearch.yml
```

```
  
  cluster.name: "docker-cluster"
  network.host: 0.0.0.0
  xpack.security.enabled: true
  
```

```
  bin/elasticsearch-setup-passwords interactive -u 'http://localhost:9200'
```



##  kibana


```shell
  docker run -d --name kibana  -p 25601:5601 kibana:7.16.2
  
  docker exec -it kibana /bin/bash
  
  vi /usr/share/kibana/config/kibana.yml
```

```yaml

#
# ** THIS IS AN AUTO-GENERATED FILE **
#

# Default Kibana configuration for docker target
server.publicBaseUrl: "https://elk.heysee.cn"
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://172.22.144.58:29200" ]
monitoring.ui.container.elasticsearch.enabled: true
elasticsearch.username: "kibana"
elasticsearch.password: "KdLup7YHtpWksdGPAoJ8"
  
```

## filebeats

```
docker run -d --name filebeats -v /app/filebeats/filebeats.yml:/usr/share/filebeat/filebeat.yml:ro  -v /app/deploy/log:/app/deploy/log --volume="/var/lib/docker/containers:/var/lib/docker/containers:ro"   --volume="/var/run/docker.sock:/var/run/docker.sock:ro" docker.elastic.co/beats/filebeat:7.16.2 
\
setup -E setup.kibana.host=172.22.144.58:25601 \
-E output.elasticsearch.hosts=["172.22.144.58:29200"] 
```

- filebeat.docker.yml

```yaml

filebeat.config:
  modules:
    path: ${path.config}/modules.d/*.yml
    reload.enabled: false

filebeat.autodiscover:
  providers:
    - type: docker
      hints.enabled: true

processors:
- add_cloud_metadata: ~

filebeat.inputs:
- type: log
  paths:
    - "/app/deploy/log/*"

output.elasticsearch:
  hosts: "172.22.144.58:29200"
  username: "elastic"
  password: "KdLup7YHtpWksdGPAoJ8"

setup.kibana:
  host: "172.22.144.58:25601"
  username: "fuhaishan"
  password: "f5641038"

```





# **为所有组件新增自启动**
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