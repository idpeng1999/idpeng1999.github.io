# Mysql Docker版教程

例：

```
docker run --name mymysql -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -d mysql:5.7.27
```

```
docker run --name mysql-5.5 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql --lower_case_table_names=1
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

--lower_case_table_names=1
```

1. 上面命令中的 \ 是换行 
2. -d 是指定镜像，本地没有的话会从docker服务器下载 
3. -p 映射容器的3306到本地3306，前面是本地端口，-p 3306:3306 
4. 这里已经设置了一组管理数据库的用户名:wikiuser 密码:mysecret   (管理员root密码zhang123)
5. 通常使用-e MYSQL_RANDOM_ROOT_PASSWORD=1 把root设置为随机，只使用wikiuser用户来管理
6. -v 是映射本地目录到容器，目录需要提前创建，或者sudo chmod 777 /var/mediawiki，启动容器会自己创建mysql目录
7. `--lower_case_table_names=1` 设置数据库不敏感，不区分大小写

<br/>
进入容器：

docker exec -it [容器名或容器ID] bash




