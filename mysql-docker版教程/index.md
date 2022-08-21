# Mysql Docker版教程

例：

```
docker run --name mymysql -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -d mysql:5.7.27
```

<br/>

```
docker run --name=mediawiki_mysql \
-e MYSQL_DATABASE=wikidb \
-e MYSQL_USER=wikiuser \
-e MYSQL_PASSWORD=mysecret \
-e MYSQL_ROOT_PASSWORD=zhang123 \
-v /var/mediawiki/mysql:/var/lib/mysql \
-d mysql:5.7.27
```

上面命令中的 \ 是换行

-d 是指定镜像，本地没有的话会从docker服务器下载

-p 映射容器的3306到本地3306，前面是本地端口，-p 3306:3306

这里已经设置了一组管理数据库的用户名:wikiuser 密码:mysecret

通常使用-e MYSQL_RANDOM_ROOT_PASSWORD=1 把root设置为随机，只使用wikiuser用户来管理

-v 是映射本地目录到容器，目录需要提前创建，或者sudo chmod 777 /var/mediawiki，启动容器会自己创建mysql目录

<br/>
进入容器：

docker exec -it [容器名或容器ID] bash

