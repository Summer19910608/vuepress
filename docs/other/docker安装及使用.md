---

title: Docker安装与使用

meta:

  - name: description

    content: 测试文章

  - name: keywords

    content: 测试，文章

date: 2019-12-09

update_date: 2019-12-09
 
tags: 

  - Dokcer

  - Yur

  - Blog
  - Mysql
  - Laravel
---



### 删除docker

```
yum remove docker docker-common docker-selinux docker-engine -y
/etc/systemd -name '*docker*' -exec rm -f {} ;
find /etc/systemd -name '*docker*' -exec rm -f {} \;
find /lib/systemd -name '*docker*' -exec rm -f {} \;
```

### 换国内源

```
yum-config-manager --add-repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
```


### docker yum安装


```
yum install docker-ce
```


### 启动 docker 并加入开机自启

```
systemctl enable docker
systemctl start docker
```



## docker下载mysql镜像并运行

```
docker pull mysql:5.7
```


```
docker run -p 3306:3306 --name zui_mysql \
-v /usr/local/etc/mysql/conf/my.conf:/etc/mysql/conf.d/mysql.conf \
-v /usr/local/etc/mysql/logs:/logs \
-v /usr/local/etc/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 --privileged=true -d mysql:5.7
```

## docker下载php镜像并运行



```
docker pull php:7.3-fpm
```


```
docker run -d -v /home/www:/home/www -p 9000:9000 --link sd_mysql:mysql --name sd_phpfpm php:7.3-fpm
```

```
docker run --name zui_phpfpm -p 9000:9000 \
-v /home/www:/home/www \
-v /usr/local/etc/php/php.ini:/usr/local/etc/php/conf.d/php.ini \
-v /usr/local/etc/php/www.conf:/usr/local/etc/php-fpm.d/www.conf \
-v /usr/local/etc/php/log:/var/log \
--privileged=true -d php:7.1-fpm

```





## docker下载nignx镜像并运行


```
docker pull nginx
```

```
docker run -d -p 80:80 --name sd_nginx -v /home/www:/home/www --link sd_phpfpm:phpfpm --name sd_nginx nginx:latest
```

```
docker run \
-d -p 80:80 \
--name c_nginx \
--link zui789_phpfpm:phpfpm \
-v /home/www:/home/www \
-v /usr/local/etc/nginx/logs:/var/log/nginx \
-v /usr/local/etc/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /usr/local/etc/nginx/conf/conf.d:/etc/nginx/conf.d \
nginx:latest
```


## docker 运行redis

```
docker run -p 6900:6379 \
--name zui_redis \
-v /usr/local/etc/redis/redis.conf:/etc/redis/redis.conf \
-v /usr/local/etc/redis/data:/data \
-d redis:4.0 redis-server /etc/redis/redis.conf --appendonly yes

```

```
--name 给容器起一个名
-p 端口映射 宿主机:容器
-v 挂载自定义配置 自定义配置:容器内部配置
-d 后台运行 
redis-server 使用指定的配置文件启动
```


#### 容器访问redis

```
 docker exec -it 89e12af9c304 redis-cli
```




## docker查看redis运行日志

```
docker logs zui_redis
```







### docker 查看所有运行的镜像


```
docker ps
```


## 进入指定镜像


```
docker exec -it 2a8e910b40db /bin/bash
```


### 删除指定镜像


```
docker rmi e143ed325782
```


### 删除所有已经停止的容器

```
docker rm $(docker ps -a -q)
```


### 停止指定镜像


```
docker stop e143ed325782
```




### nginx 配置文件



```
docker cp e143ed325782:/etc/nginx/conf.d/default.conf default.conf
```


```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /home/www;
        index  index.php;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
	root           /home/www;
        fastcgi_pass   phpfpm:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /home/www/$fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```



```
docker cp default.conf e143ed325782:/etc/nginx/conf.d/default.conf
```



### 重新加载 Nginx
```
nginx -s reload
```
