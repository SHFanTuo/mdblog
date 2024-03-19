



# 常用命令

- 文件压缩

  ```
  tar -czvf  /app/deploy/log/hs-sell-api2/app.log.tar.gz /app/deploy/log/hs-sell-api2/app.log
  ```

  

# I、prod-api

###  nginx-transfer

```shell
docker run --name nginx-transfer --restart=always -v /app/nginx/transfer/conf.d:/etc/nginx/conf.d -v /app/nginx/transfer/html:/usr/share/nginx/html:ro -p 80:80 -p 443:443 -d nginx:1.23.2


docker run --name nginx-transfer --restart=always -v /app/nginx/transfer/conf.d:/etc/nginx/conf.d -v /app/nginx/transfer/html:/usr/share/nginx/html:ro -p 80:80 -p 443:443 -d nginx:1.23.2

# nginx配置文件拷贝至宿主机
docker cp d3bb2a3a831a:/etc/nginx/etc/nginx/conf.d/default.con
app/nginx/default.conf

docker exec -i nginx-transfer nginx -s reload
```

## hs-static

```shell
docker run --name nginx-static --restart=always -v /app/nginx/static/conf.d:/etc/nginx/conf.d -v /app/nginx/static/html:/usr/share/nginx/html:ro -p 23384:80  -d nginx:1.23.2
```





# II、prod-mid

## jenkins

- 域名：jenkins.heysee.cn
- 

```


```

