# Linux漏洞修复


## yum 安装、卸载、升级软件

* 用YUM安装软件包命令: `yum install xxxx `
* 用YUM删除软件包命令: `yum remove xxxx`
* 使用YUM查找软件包: `yum search ~`
* 列出所有可更新的软件包: `yum list updates`

## 查看tomcat是否安装(使用管理员权限)

1. 检测是否有安装了Tomcat: `rpm -qa|grep tomcat`
2. 查看Tomcat的进程ID: `ps -ef|grep tomcat`
3. 查看Tomcat目录: `find / -name tomcat`
4. 卸载Tomcat: `yum remove tomcat`


## 升级当前sudo版本(使用管理员权限)

1. 查看版本: `sudo -V`
2. 下载升级sudo的版本
* [sudo下载网址](https://www.sudo.ws/dist/)
* /zip/Linux漏洞修复/sudo-1.9.5p2.tar.gz
3. 解压升级sudo的版本: `tar -zxvf sudo-1.9.5p2.tar.gz`
4. 编译升级sudo的版本: 
* `cd sudo-1.9.5p2` 
* `./configure`  
* `make`
5. 安装: `sudo make install`

## 升级polkit版本(使用管理员权限)

* 解决 Linux Polkit 存在权限提升的漏洞 (CVE-2021-4034)

1. 查看版本: `rpm -qa polkit`
2. 修复命令为: `sudo yum update -y polkit`

## 检测到目标URL存在http host头攻击漏洞

[教程链接](https://blog.csdn.net/doulicau/article/details/106685476)

### nginx修复方式

在 `http { ` 下的 `server {` 里添加  
```
	server_name  xxxx1 xxxx2;
	
	set $flag 0;
		if ($http_Host != 'xxxx1:端口') {
         set $flag "${flag}1";
        }
        if ($http_Host != 'xxxx2:端口') {
         set $flag "${flag}2";
        }
        if ($flag = "012") {
         return 403;
        }
```

解释

* `xxxx1:端口` 和 `xxxx2:端口`代表 nginx 放行地址，不为则返回403

验证

* 打开postman
* 调用随意的一个访问接口，能通
* 修改 Headers 下的 Host ，再次调用，返回403
* 则修复成功

![Linux漏洞修复](/img/Linux漏洞修复/1.png)





