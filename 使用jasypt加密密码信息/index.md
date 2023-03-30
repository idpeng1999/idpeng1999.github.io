# 使用jasypt加密密码信息


## jasypt加密

[使用参考教程](https://www.cnblogs.com/zhi-leaf/p/16628354.html)

### pom.xml引入依赖

```xml

<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>3.0.4</version>
</dependency>
```

### 写一个main函数将我们的密码进行加密

```java
public class JasyptTest {
    public static void main(String[] args) {
        StandardPBEStringEncryptor encryptor = new StandardPBEStringEncryptor();
        encryptor.setAlgorithm("PBEWITHHMACSHA512ANDAES_256");
        encryptor.setPassword("zhi");
        encryptor.setIvGenerator(new RandomIvGenerator());

        // 加密
        String encryptText = encryptor.encrypt("abc123");
        System.out.println("加密后的信息：" + encryptText);

        // 解密
        String decryptText = encryptor.decrypt(encryptText);
        System.out.println("解密后的信息：" + decryptText);
    }
}
```

### 可在配置文件写

```yaml
jasypt:
  encryptor:
    # 加密的盐值
    password: fa7bd111xx
    # 加密所采用的算法
    algorithm: PBEWITHHMACSHA512ANDAES_256
```

### 使用

* ENC(密文)

![使用](/img/jasypt加密密码信息/1.png)

