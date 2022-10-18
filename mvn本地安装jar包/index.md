# Mvn本地安装jar包


## win Mvn本地安装jar包

```shell
mvn install:install-file -Dfile=F://wrongOrder//lib//ojdbc14-10.2.0.5.0.jar  -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.5.0 -Dpackaging=jar
```

1. `-Dfile=F://wrongOrder//lib//ojdbc14-10.2.0.5.0.jar` 本地jar路径
2. `-DgroupId=com.oracle` 如图
3. `-DartifactId=ojdbc14` 如图
4. `-Dversion=10.2.0.5.0` 如图

![pom图](/img/Mvn本地安装jar包/pom.png)


