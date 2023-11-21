# Word导出


[//]: # (![集合]&#40;/img/集合/img.png&#41;)

## Freemarker导出word

* [参考链接1](https://blog.51cto.com/u_13410146/3069160)
* [参考链接2](https://blog.csdn.net/zhaosongbin/article/details/87918339)
* [参考链接3](https://blog.csdn.net/justry_deng/article/details/84144023)

### 1. 说明

* 如何使用Freemarker导出word(.doc)(.docx未实现)
* word格式正确,wps格式会出错

### 2. 依赖

```xml
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.32</version>
</dependency>
```

### 3. 制作模板(.ftl)

1. ftl文件就是word的源代码，类似html一样是拥有标签和样式的代码
2. 创建一个word模板，里面的对应位置使用FreeMarker的占位符表示
3. 把doc文件另存为xx.xml文件(会出现一些不符合规范的标签，需要手动调整)
4. 把xx.xml文件重命名为xx.ftl文件
5. 调整xx.ftl文件的内容，使其符合语法规范

### 4. 导出代码

* 前端下载

```vue
this.download("后端路径",
              "参数,
              "导出名称" + new Date().getTime() + ".doc"
        );
```

* 后端代码

```java
    @PostMapping("/exportCutoverWord")
    public void exportCutoverWord(@Validated TcCutoverForecastBo bo, HttpServletResponse response) {
        Map<String, Object> dataMap = iTcCutoverForecastService.exportCutoverWord(bo);
        String templatePath = path;
        String templateName = "opticalTemplate.ftl";
        WordUtil.exportWord(response, dataMap, templatePath, templateName);
    }
```

* WordUtil工具类

```java

import freemarker.template.Configuration;
import freemarker.template.Template;
import freemarker.template.TemplateException;
import freemarker.template.TemplateExceptionHandler;
import sun.misc.BASE64Encoder;

import javax.servlet.http.HttpServletResponse;
import java.io.*;
import java.util.Map;

/**
 * @className: WordUtil
 * @Description: Word工具类
 * @version: v1.8.0
 * @author: hahaen
 * @date: 2023/8/8 14:10
 */
public class WordUtil {

    /**
     * 生成word文档
     *
     * @param response
     * @param map
     * @param templatePath
     * @param templateName
     */
    public static void exportWord(HttpServletResponse response, Map<String, Object> map, String templatePath, String templateName) {
        Configuration configuration = new Configuration();
        // 设置Freemarker的编码格式
        configuration.setDefaultEncoding("UTF-8");
        try {
            //模板文件配置路径
            configuration.setDirectoryForTemplateLoading(new File(templatePath));
            configuration.setTemplateExceptionHandler(TemplateExceptionHandler.IGNORE_HANDLER);
            // 获取模板
            Template template = configuration.getTemplate(templateName, "UTF-8");
            // 输出文件
            OutputStream outputStream = response.getOutputStream();
            Writer out = new BufferedWriter(new OutputStreamWriter(outputStream, "UTF-8"));
            // 生成文件
            template.process(map, out);
            // 关闭流
            out.flush();
            out.close();
            outputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TemplateException e) {
            e.printStackTrace();
        }
    }

    /**
     * 将图片转换成base64字符串
     *
     * @param imgFile
     * @return
     * @throws IOException
     */
    public static String getImageBase64String(File imgFile) throws IOException {
        InputStream inputStream = new FileInputStream(imgFile);
        byte[] data = new byte[inputStream.available()];
        int totalNumberBytes = inputStream.read(data);
        BASE64Encoder encoder = new BASE64Encoder();
        return encoder.encode(data);
    }
}
```

### 5. 模板语法

```text
${data.name} ：字符串
${data.name!"-"}：代表当data.name是空的时候用“-”代替这个字符展示
${data.name?html}：转化为html格式
```

* if判断

```text
？？代表的是？前方的字符不是空的时候，?size常用语list数据，判断？前方数据的size是不是小于0
<#if data.list?? && (data.list?size > 0) >
 
和if是功效是一样的
<# else if (data.list?size < 10) >
</#if>
```

* list循环

```text
<#list data.list as list>
　　${list_index} :表示集合的位置或者下标，初始为0
</#list>
```

* ftl标签
```text
<w:vMerge w:val="restart"/>：表示要合并单元格
<w:vMerge w:val="continue"/>：表示被合并的单元格
 
<w:t xml:space="preserve">：表示需要格式化的特殊字符
```










