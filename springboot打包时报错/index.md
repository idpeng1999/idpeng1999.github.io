# Springboot打包时报错


[//]: # (![集合]&#40;/img/集合/img.png&#41;)

## 程序包com.sun.image.codec.jpeg不存在

* 出现原因: JPEGCodec类在JDK1.7之后移除，使用 JDK1.8 打包时会报错。

### 第一种解决办法:不使用JPEGCodec这个类

```java

// 构造一个类型为预定义图像类型之一的 BufferedImage
BufferedImage tag = new BufferedImage(widthdist, heightdist, BufferedImage.TYPE_INT_RGB);
tag.getGraphics().drawImage(src.getScaledInstance(widthdist, heightdist, Image.SCALE_SMOOTH), 0, 0, null);
//创建文件输出流
FileOutputStream out = new FileOutputStream(imgdist);

//以这个代替掉 JPEGCodec
ImageIO.write(tag,"jpeg",out);

```

### 第二种解决办法配置:maven-compiler-plugin


```java

<!--解决打包错 程序包com.sun.image.codec.jpeg不存在-->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <encoding>UTF-8</encoding>
        <compilerArguments>
            <verbose />
            <bootclasspath>${java.home}/lib/rt.jar;${java.home}/lib/jce.jar</bootclasspath>
        </compilerArguments>
    </configuration>
</plugin>
<!-- windows下用;，linux下用: -->

```

![配置](/img/Springboot打包时报错/1.png)







