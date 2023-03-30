# Linux安装MySQL教程


[安装教程参考1](https://blog.csdn.net/weixin_47617417/article/details/126009268)
[安装教程参考2](https://cloud.tencent.com/developer/article/1451186)
[赋权限教程](https://www.jianshu.com/p/6cc7c1887ad2)

## linux安装(压缩包方式)

### 官网下载安装

* [MySQl5.7](https://dev.mysql.com/downloads/mysql/5.7.html)
* [MySQl8](https://dev.mysql.com/downloads/mysql/)

注意事项
1. centos7对应下载 `Linux Generic`

![LinuxGeneric](/img/安装MySQL教程/1.png)

### 检查服务器是否有MySQL

1. `rpm -qa|grep mysql` 显示没有东西，便是没有安装过mysql
2. 如果安装过或者系统自带，便需要去查询所有的mysql对应的文件，进行卸载，全部删除
*  `whereis mysql`
*  `find / -name mysql`

### 卸载CentOS7自带的mariadb

1. 查看系统自带的 `rpm -qa|grep mariadb`
2. 卸载Mariadb `rpm -e --nodeps mariadb-libs-5.5.68-1.el7.x86_64`
3. 查看etc下面是否有的my.cnf,`cat /etc/my.cnf`  有则删掉 `rm /etc/my.cnf`

#### 为什么要卸载CentOS7自带的mariadb？

* 首先centos7已经不支持mysql，因为收费，所以内部集成了mariadb，
* 而安装mysql的话会和mariadb的文件冲突，所以需要先卸载掉mariadb。

### 创建mysql用户组

1. 检查有没有mysql用户组，没有便进行创建
```shell
  cat /etc/group | grep mysql
  cat /etc/passwd |grep mysql
```
2. 创建MySQL用户组和用户
```shell
groupadd mysql
useradd -r -g mysql mysql
```

### 上传服务器

* 解压到当前mysql5.7.41 `tar -zxvf mysql-5.7.41-linux-glibc2.12-x86_64.tar.gz -C .`
* 改名 `mv mysql-5.7.41-linux-glibc2.12-x86_64 mysql5.7.41`
* 移动到默认位置 `/usr/local`

### 安装MySQL

1. 给用户组添加权限 
```shell
chown -R mysql:mysql /usr/local/mysql5.7.41
chmod -R 755 /usr/local/mysql5.7.41
```

2. 进入bin目录，编译并初始化mysql，请务必记住这个密码
```shell
cd /usr/local/mysql5.7.41/bin
./mysqld --initialize --user=mysql --datadir=/usr/local/mysql5.7.41/data --basedir=/usr/local/mysql5.7.41
```

3. 编写etc目录下的my.cnf配置文件，并添加配置
```shell
//进入配置文件
vi /etc/my.cnf
```
```shell
//在插入模式下编写,完成后保存,当然这个可以自己添加
[mysqld]
datadir=/usr/local/mysql5.7.41/data
port = 3306
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
symbolic-links=0
max_connections=400
innodb_file_per_table=1
#表名大小写不明感1,敏感为0
lower_case_table_names=1
```

4. 授予my.cnf 775权限
```shell
chmod -R 775 /etc/my.cnf
```

5. 修改mysql5.7.41/support-files/目录下的mysql.server文件,如下图中5个位置的/usr/local/mysql全部修改成自己安装的目录

![修改](/img/安装MySQL教程/2.png)

6. 启动服务，并添加软链接
```shell
// 查询服务
ps -ef|grep mysql
ps -ef|grep mysqld

// 启动服务
/usr/local/mysql5.7.41/support-files/mysql.server start

//添加软连接,并重启mysql 服务
ln -s /usr/local/mysql5.7.41/support-files/mysql.server /etc/init.d/mysql
ln -s /usr/local/mysql5.7.41/bin/mysql /usr/bin/mysql
//重启mysql服务
service mysql restart
```

7. 登录mysql，密码是之前让记住的，并修改密码
```shell
 // 登录
 mysql -u root -p
 // 修改密码
set password for root@localhost = password('你的密码');
```

8. 开启远程连接
```shell
use mysql;
update user set user.Host='%' where user.User='root';
flush privileges;
```

9. 开放端口

10. 设置开启自启（按需选择）
```shell
//将服务文件拷贝到init.d下,并重命名为mysqld
cp /usr/local/mysql5.7.41/support-files/mysql.server /etc/init.d/mysqld
//赋予可执行权限
chmod +x /etc/init.d/mysqld
//添加服务
chkconfig --add mysqld
//显示服务列表
chkconfig --list
```

## 创建mysql用户，赋权限

[教程参考](https://www.jianshu.com/p/6cc7c1887ad2)

1. 登录数据库(root权限，需要有mysql.user查看权限的用户）)
2. 创建用户 ` create user 'hahaen'@'%' identified by '123456';`
* `hahaen`用户名
* `123456`密码
* `%`指定该用户在哪个主机上可以登陆,如果是本地用户可用localhost,如果想让该用户可以从任意远程主机登陆,可以使用通配符%

3. 修改用户密码 `set password for hahaen=password('456789');`
4. 删除用户 `drop user hahaen;`
5. 授权
```mysql
grant all on *.* to hahaen;
# all :所有数据库权限，包括 select ,update ,insert ,delete 

grant select,insert on leo.* to hahaen;
# leo： 数据库名，表示该用户只可对该数据库进行操作
# *： 表名，表示该用户只可对该表进行操作
```
5. 取消用户的权限 `revoke select on leo .* from hahaen;`
6. 查看用户权限 `show grants for hahaen ;`
7. 更新数据库 `flush privileges;` 所有操作必须执行命令立即生效


