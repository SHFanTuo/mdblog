

```shell
#!/bin/sh

#环境
export PROJECTENV='prod'

# 应用配置文件并maven打jar包
# 将配置文件替换为当前环境的配置文件，准备mvn打包
cp src/main/resources/application-${PROJECTENV}.yml src/main/resources/application.yml
cat src/main/resources/application-${PROJECTENV}.yml

echo '===== maven生成jar包 开始 ====='
mvn clean package
echo '===== maven生成jar包 结束 ====='
```


ssh hs-mid
```shell

#!/bin/sh
echo '===== 镜像打包生成 开始 ====='
## ====== 全局变量 ======


#环境
export PROJECTENV='pre'
#项目名称
export PROJECT_NAME='hs-sell-api'

#registry地址
export REGISTRYURL='registry-vpc.cn-shanghai.aliyuncs.com'
#仓库名
export REPOSITORIE_NAME='heysee'
#镜像名称
export IMAGE_NAME=${REGISTRYURL}'/'${REPOSITORIE_NAME}'/'${PROJECT_NAME}'_'${PROJECTENV}



# 生成镜像
echo '===== 生成本地镜像 开始 ====='
current=`date "+%Y%m%d%H%M%S"`
currentDataStr=$current$((RANDOM %10))

echo 'tag:'${currentDataStr}
echo '/app/jenkins/workspace/'${PROJECTENV}'_'${PROJECT_NAME}
echo ${IMAGE_NAME}':'${currentDataStr}
sudo docker build -t ${IMAGE_NAME}:${currentDataStr} -f /app/jenkins/workspace/${PROJECTENV}'_'${PROJECT_NAME}/Dockerfile .
echo $r
echo '===== 生成本地镜像 结束====='


echo '===== 生成latest 开始 ====='
sudo docker tag ${IMAGE_NAME}:${currentDataStr} ${IMAGE_NAME}:'latest'
echo '生成'${IMAGE_NAME}':latest镜像成功！'
echo '===== 生成latest 结束 ====='

echo ''
echo ''

echo '===== 登陆aliyun registry 开始 ====='
sudo docker login --username=巧盒智能科技 --password=2wsx@WSX ${REGISTRYURL}
echo '===== 登陆aliyun registry 结束 ====='

echo ''
echo ''

echo '===== 上传镜像到aliyun registry 开始 ====='
sudo docker push ${IMAGE_NAME}:${currentDataStr}
sudo docker push ${IMAGE_NAME}:'latest'
echo '===== 上传镜像到aliyun registry 结束 ====='

# 删除老的本地镜像（排除latest）
sudo docker rmi --force $(sudo docker images | grep ${PROJECT_NAME} | grep -v latest | grep -v ${currentDataStr} |awk '{print $3}')
echo '删除老的本地镜像（排除latest）'

echo '===== 镜像打包生成 结束 ====='
```

ssh hs-api1
```shell
#!/bin/sh
## ====== 全局变量 ======

#环境
export PROJECTENV='pre'
#项目名称
export PROJECT_NAME='hs-sell-api'

#registry地址
export REGISTRYURL='registry-vpc.cn-shanghai.aliyuncs.com'

#仓库名
export REPOSITORIE_NAME='heysee'
#镜像名称
export IMAGE_NAME=${REGISTRYURL}'/'${REPOSITORIE_NAME}'/'${PROJECT_NAME}'-'${PROJECTENV}

#容器运行的端口映射参数
export CONTAINER_PORT_PARAMS='--publish 21501:21201'
#宿主机文件卷挂载配置
export VOLUME_PARAMS='-v /app/deploy/log/'${PROJECT_NAME}'-'${PROJECTENV}':/log'


# 部署镜像
echo '===== 登陆aliyun registry 开始 ====='
sudo docker login ${REGISTRYURL} --username=巧盒智能科技 --password=2wsx@WSX
echo '===== 登陆aliyun registry 结束 ====='

echo '===== 拉取镜像 开始 ====='
echo ${IMAGE_NAME}:'latest'
sudo docker pull ${IMAGE_NAME}:'latest'
sudo docker images
echo '===== 拉取镜像 结束 ====='

#========= 停止和删除正在运行的镜像容器 BEGIN =========

run_container_id=`sudo docker ps -a|grep -i ${PROJECT_NAME}'-'${PROJECTENV}|sort -k 2 -r|sed -n '1p;1q'|awk '{print $1}'`
if [ "$run_container_id"x != ""x ]
then
	echo '已存在正在运行的'${PROJECT_NAME}'-'${PROJECTENV}'镜像容器，正在停止和移除...'
	sudo sudo docker stop ${run_container_id}
	sudo sudo docker rm ${run_container_id}
	echo '停止和删除正在运行的'${PROJECT_NAME}'-'${PROJECTENV}'项目镜像容器完毕'
else
	echo '当前项目'${PROJECT_NAME}'-'${PROJECTENV}'不存在正在运行的容器，无需停止和删除'
fi
#========= 停止和删除正在运行的镜像容器 END =========


#========= 容器机将最新的镜像run起来 BEGIN =========
echo '开始部署容器'
run_msg=`sudo docker run --detach \
  --restart always \
  --name ${PROJECT_NAME}'-'${PROJECTENV}  \
  ${VOLUME_PARAMS}  --volume /etc/localtime:/etc/localtime:ro \
  ${CONTAINER_PORT_PARAMS} \
${IMAGE_NAME}:"latest"`

echo ${run_msg}

if [ "$run_msg"x == ""x ]
then
	echo '发布容器出现错误！ '
	exit 1
else
	echo '已将最新的'${PROJECT_NAME}'-'${PROJECTENV}'项目镜像发布为容器，container id是'${run_msg}
fi
#========= 容器机将最新的镜像run起来 END =========



# 删除老的本地镜像（排除latest）
sudo docker rmi --force $(sudo docker images | grep ${PROJECT_NAME}'-'${PROJECTENV} | grep -v latest |awk '{print $3}')
echo '删除老的本地镜像（排除latest）'
```