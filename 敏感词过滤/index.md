# 敏感词过滤


## 教程地址

[github教程地址](https://github.com/houbb/sensitive-word)

## sensitive-word

![敏感词过滤](/img/敏感词过滤/1.png)

### Maven 引入

```java
<dependency>
    <groupId>com.github.houbb</groupId>
    <artifactId>sensitive-word</artifactId>
    <version>0.2.0</version>
</dependency>
```

### 创建敏感词表 sensitive_word

```sql
DROP TABLE IF EXISTS `sensitive_word`;
CREATE TABLE `sensitive_word`  (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `words` varchar(300) CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 31916 CHARACTER SET = utf8mb3 COLLATE = utf8mb3_general_ci COMMENT = '敏感词' ROW_FORMAT = DYNAMIC;
```

### 创建mapper目录，创建`MyWordMapper.java`

`MyWordMapper.java`

```java
import java.util.List;

/**
 * @className: MyWordDenyMapper
 * @Description: 黑名单Mapper
 * @version: v1.8.0
 * @author: hahaen
 * @date: 2023/2/13 10:03
 */
public interface MyWordMapper {
    public List<String> selectBlackWords();
}
```

### 创建service目录，创建`MyWordAllow.java`,`MyWordDeny.java`

`MyWordAllow.java`

```java
import com.github.houbb.sensitive.word.api.IWordAllow;

import java.util.Arrays;
import java.util.List;

/**
 * @className: MyWordAllow
 * @Description: 返回的列表，表示这个词不是一个敏感词(白名单)
 * @version: v1.8.0
 * @author: hahaen
 * @date: 2023/2/13 9:07
 */
public class MyWordAllow implements IWordAllow {
    @Override
    public List<String> allow() {
        return Arrays.asList("");
    }
}
```

`MyWordDeny.java`

```java
import com.github.houbb.sensitive.word.api.IWordDeny;
import com.ruoyi.business.sensitiveWord.mapper.MyWordMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @className: MyWordDeny
 * @Description: 返回的列表，表示这个词是一个敏感词(黑名单)
 * @version: v1.8.0
 * @author: hahaen
 * @date: 2023/2/13 9:09
 */
@Service
public class MyWordDeny implements IWordDeny {

    @Autowired
    private MyWordMapper myWordMapper;

    @Override
    public List<String> deny() {
        return myWordMapper.selectBlackWords();
    }

}
```

### 创建`SpringSensitiveWordConfig.java`

`SpringSensitiveWordConfig.java`

```java
import com.github.houbb.sensitive.word.bs.SensitiveWordBs;
import com.ruoyi.business.sensitiveWord.service.MyWordAllow;
import com.ruoyi.business.sensitiveWord.service.MyWordDeny;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @className: SpringSensitiveWordConfig
 * @Description: 敏感词
 * @version: v1.8.0
 * @author: hahaen
 * @date: 2023/2/13 10:00
 */
@Configuration
public class SpringSensitiveWordConfig {

    @Autowired
    private MyWordDeny myWordDeny;

    /**
     * 初始化引导类
     *
     * @return 初始化引导类
     */
    @Bean
    public SensitiveWordBs sensitiveWordBs() {
        SensitiveWordBs sensitiveWordBs = SensitiveWordBs.newInstance()
                .wordAllow(new MyWordAllow())
                .wordDeny(myWordDeny)
                // 各种其他配置
                .init();

        return sensitiveWordBs;
    }
}
```

### 创建`MyWordMapper.xml`

`MyWordMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="xxxxxxxxxxxxxxx.sensitiveWord.mapper.MyWordMapper">
    <select id="selectBlackWords" resultType="String">
        SELECT words
        FROM sensitive_word
    </select>
</mapper>
```


## 用法

### 引入

```java
@Autowired
 private SensitiveWordBs sensitiveWordBs;
```

### 方法使用

| 方法 | 参数 | 返回值| 说明 |
|:---|:---|:---|:---|
| contains(String) | 待验证的字符串 | 布尔值 | 验证字符串是否包含敏感词 |
| replace(String, ISensitiveWordReplace) | 使用指定的替换策略替换敏感词 | 字符串 | 返回脱敏后的字符串 |
| replace(String, char) | 使用指定的 char 替换敏感词 | 字符串 | 返回脱敏后的字符串 |
| replace(String) | 使用 `*` 替换敏感词 | 字符串 | 返回脱敏后的字符串 |
| findAll(String) | 待验证的字符串 | 字符串列表 | 返回字符串中所有敏感词 |
| findFirst(String) | 待验证的字符串 | 字符串 | 返回字符串中第一个敏感词 |
| findAll(String, IWordResultHandler) | IWordResultHandler 结果处理类 | 字符串列表 | 返回字符串中所有敏感词 |
| findFirst(String, IWordResultHandler) | IWordResultHandler 结果处理类 | 字符串 | 返回字符串中第一个敏感词 |

### 举例

```java
// 是否包含
sensitiveWordBs.contains(text)
```


## 更多特性

### 方法使用

用户可以使用 `SensitiveWordBs` 进行如下定义：

```java
SensitiveWordBs wordBs = SensitiveWordBs.newInstance()
        .ignoreCase(true)
        .ignoreWidth(true)
        .ignoreNumStyle(true)
        .ignoreChineseStyle(true)
        .ignoreEnglishStyle(true)
        .ignoreRepeat(true)
        .enableNumCheck(true)
        .enableEmailCheck(true)
        .enableUrlCheck(true)
        .init();

final String text = "五星红旗迎风飘扬，毛主席的画像屹立在天安门前。";

wordBs.contains(text);
```

其中各项配置的说明如下：

| 序号 | 方法 | 说明 |
|:---|:---|:---|
| 1 | ignoreCase | 忽略大小写 |
| 2 | ignoreWidth | 忽略半角圆角 |
| 3 | ignoreNumStyle | 忽略数字的写法 |
| 4 | ignoreChineseStyle | 忽略中文的书写格式 |
| 5 | ignoreEnglishStyle | 忽略英文的书写格式 |
| 6 | ignoreRepeat | 忽略重复词 |
| 7 | enableNumCheck | 是否启用数字检测。默认连续 8 位数字认为是敏感词 |
| 8 | enableEmailCheck | 是有启用邮箱检测 |
| 9 | enableUrlCheck | 是否启用链接检测 |



