# 一、用户与组

## 查看本机用户

```shell
cat /etc/passwd
cat /etc/group
```

## 创建用户并授权

创建一个用户名为：linuxidc

```shell
[root@localhost ~]# adduser linuxidc
```

为这个用户初始化密码，linux会判断密码复杂度，不过可以强行忽略：

```
[root@localhost ~]# passwd linuxidc
更改用户 zhangbiao 的密码 。
新的 密码：
无效的密码： 密码未通过字典检查 - 过于简单化/系统化
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
```

**授权**

个人用户的权限只可以在本home下有完整权限，其他目录要看别人授权。而经常需要root用户的权限，这时候sudo可以化身为root来操作。我记得我曾经sudo创建了文件，然后发现自己并没有读写权限，因为查看权限是root创建的。

新创建的用户并不能使用sudo命令，需要给他添加授权。

sudo命令的授权管理是在sudoers文件里的。可以看看sudoers：

```shell
[root@localhost ~]# sudoers
bash: sudoers: 未找到命令...
[root@localhost ~]# whereis sudoers
sudoers: /etc/sudoers /etc/sudoers.d /usr/libexec/sudoers.so /usr/share/man/man5/sudoers.5.gz
```

找到这个文件位置之后再查看权限：

```
[root@localhost ~]# ls -l /etc/sudoers
-r--r----- 1 root root 4251 9月  25 15:08 /etc/sudoers
```

是的，只有只读的权限，如果想要修改的话，需要先添加w权限：

```
[root@localhost ~]# chmod -v u+w /etc/sudoers
mode of "/etc/sudoers" changed from 0440 (r--r-----) to 0640 (rw-r-----)
```

然后就可以添加内容了，在下面的一行下追加新增的用户：

```shell
[root@localhost ~]# vim /etc/sudoers


## Allow root to run any commands anywher  
root    ALL=(ALL)       ALL  
linuxidc  ALL=(ALL)       ALL  #这个是新增的用户
```

wq保存退出，这时候要记得将写权限收回：

```shell
[root@localhost ~]# chmod -v u-w /etc/sudoers
mode of "/etc/sudoers" changed from 0640 (rw-r-----) to 0440 (r--r-----)
```

这时候使用新用户登录，使用sudo：

```shell
[linuxidc@localhost ~]$ sudo cat /etc/passwd
[sudo] password for linuxidc: 

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.
```

第一次使用会提示你，你已经化身超人，身负责任。而且需要输入密码才可以下一步。如果不想需要输入密码怎么办，将最后一个ALL修改成NOPASSWD: ALL。

sudo: a terminal is required to read the password; either use the -S option to read from standard in or configure an askpass helper
具有sudo权限的用户执行shell 脚本时，脚本中使用的sudo命令需要输入密码

原因：

具有sudo权限的用户执行shell 脚本时，脚本中使用的sudo命令需要输入密码

解决方法：

编辑文件/etc/sudoers, 配置此sudo权限用户(例如：sudoaccount）使用sudo命令时不需要输入密码

sudo visudo
sudoaccount ALL=(ALL:ALL) NOPASSWD: ALL

```
[root@VM-0-14-centos ~]# sudo visudo

## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
tx_qa-api-jenkins       ALL=(ALL)       NOPASSWD:ALL
```

## 赋予某目录某用户权限

```
chown -R  simon:simon  /srv/jenkins/jenkins_home/
```

## 将指定用户加入组

```
useradd -G {group-name} username
例如，一个新用户 cnzhx 并将其添加到用户组 developers 中
useradd -G developers cnzhx

useradd -G root centos
usermod -a -G root jenkins
```

# 二、文件与目录

## 删除目录

```
rm -rf /srv/jenkins/
```

## 创建目录

```
mkdir -p /etc/systemd/system/docker.service.d
```

## 创建文件

## 查找文件

```
find / -name 'settings.xml'
```

## 文件夹重命名

## 文件copy

```
cp -r bbs ../backup/bbs 

cp -R /data/web /data/bak/web


```

## 赋予权限

```
chmod -R 777 /app/temp
```

## 按文件夹查看大小

```
du -h --max-depth=1 /app
```

## 压缩与解压

压缩：

```
# touch a.c       
# tar -czvf test.tar.gz a.c   //压缩 a.c文件为test.tar.gz
# tar -czvf /app/jenkins/bak/FULL-2022-05-09_11-30.tar.gz /app/jenkins/bak/FULL-2022-05-09_11-30
a.c
```

解压：

```
# tar -xzvf FULL-2022-04-19_09-56.tar.gz
# tar -xzvf FULL-2022-05-09_11-30.tar.gz -C /app/jenkins/bak/FULL-2022-05-09_11-30
# tar -xzvf FULL-2022-05-09_11-30.tar.gz -C /
a.c

```

## cat写文件

```
cat > /opt/docker/etc/httpd/conf.d/11-server.conf <<-'EOF'
#
# Deny access to the entirety of your server's filesystem. You must
# explicitly permit access to web content directories in other 
# <Directory> blocks below.
#
<Directory />
	AllowOverride all
	allow from all
</Directory>
EOF
```

# 三、网络

### 安装最新版本curl

```shell
yum install wget gcc openssl-devel -y

cd /app/down
wget https://curl.se/download/curl-7.86.0.tar.gz

# 解压
tar -zxvf curl-7.86.0.tar.gz
cd curl-7.86.0
./configure --with-ssl
make
make install
```



## 查看端口占用

```
netstat -na|grep 8080
```

## 查看所有端口

```
netstat -tunlp 
```

## telnet查看远程端口

```
telnet 192.168.0.1 80
```

## ssh命令

```
ssh user@remote -p port
```

- user 是你在远程机器上的用户名，如果不指定的话默认为当前用户
- remote 是远程机器的地址，可以是 IP，域名，或者是后面会提到的别名
- port 是 SSH Server 监听的端口，如果不指定的话就为默认值 22

# 四、硬盘

## 挂载硬盘到目录

```
mount /dev/vdb /app
```



## 开机自动将硬盘挂载

```
[root@ott-devops-git app]# vim /etc/rc.local 
mount /dev/vdb /app
[root@ott-devops-git app]# cd /etc/rc.d/
[root@ott-devops-git rc.d]# chmod +x rc.local 

```

# 五、时区

## 更改时区

```
# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

Asia/Shanghai
```

## 显示所有可用的时区

```
timedatectl list-timezones           
```

## 设置当前时区

```
$ sudo timedatectl set-timezone Asia/Shanghai
$ sudo timedatectl set-time YYYY-MM-DD
$ sudo timedatectl set-time HH:MM:SS
```



# 其它

## 更改docker内得apt-get源为国内

```
#复制原文件备份
mv /etc/apt/sources.list /etc/apt/sources.list.bak

#修改sources.list
cat <<EOF >/etc/apt/sources.list
deb http://mirrors.ustc.edu.cn/debian stable main contrib non-free
deb http://mirrors.ustc.edu.cn/debian stable-updates main contrib non-free
EOF

#更新apt
apt update

#安装vim命令
apt install vim

#还原sources.list文件
mv /etc/apt/sources.list.bak /etc/apt/sources.list

```

# docker

## 删除某个容器的console日志

```
cat /dev/null > /var/lib/docker/containers/容器id/容器id-json.log


cat /dev/null > /app/docker/containers/1391ce65d3e822f6a457f4f1dbf6410e2a074ec5b6bbe87c46d9e8ea1e872d63/1391ce65d3e822f6a457f4f1dbf6410e2a074ec5b6bbe87c46d9e8ea1e872d63-json.log
```



