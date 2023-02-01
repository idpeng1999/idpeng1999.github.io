# IDEA乱码Tomcat控制台乱码

## 

### 修改IDEA文件编码格式

![乱码1](/img/IDEA乱码Tomcat控制台乱码/1.png)

### IDEA中配置Tomcat

* 配置Tomcat时，VM options填入`-Dfile.encoding=UTF-8` (选填)

![乱码2](/img/IDEA乱码Tomcat控制台乱码/2.png)

### 修改IDEA的配置文件

* IDEA的配置文件，找到idea64.exe.vmoptions或idea.exe.vmoptions，打开在最后添加`-Dfile.encoding=UTF-8`

![乱码3](/img/IDEA乱码Tomcat控制台乱码/3.png)
![乱码4](/img/IDEA乱码Tomcat控制台乱码/4.png)

### Tomcat升级后打印log乱码

如果是你升级后乱码了，不用第五步第三步了。看Tomcat的日志配置文件，`tomcat/conf/logging.properties`
这个文件就是tomcat的日志配置文件，只需要修改这个文件。
tomcat在新版的日志配置文件中加了指定编码为UTF-8的配置。这就是乱码的根源了。

* 将配置UTF-8那一行配置删除（这样应该就是采用操作系统默认编码，Windows下即为GBK） 
* 或不删除，将UTF-8改为GBK

![乱码5](/img/IDEA乱码Tomcat控制台乱码/5.png)

