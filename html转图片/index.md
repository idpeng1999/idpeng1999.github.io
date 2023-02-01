# Html转图片


## 方式一(png正常，jpg图片变红)

1. 引入依赖

```java

<dependency>
	<groupId>com.github.xuwei-k</groupId>
	<artifactId>html2image</artifactId>
	<version>0.1.0</version>
</dependency>

```

`import gui.ava.html.image.generator.HtmlImageGenerator;`

2. java代码

```java

public class TestGeneratePic {
    public static void main(String[] args) {
        generatePic();
    }

    public static void generatePic() {
        HtmlImageGenerator htmlImageGenerator = new HtmlImageGenerator();
        
        htmlImageGenerator.loadHtml(html);

        //图片名
        String fileName = "D:\\Desktop\\test\\20230131\\hahaha112.png";

        htmlImageGenerator.saveAsImage(fileName);

    }
}

```



## 方式二(推荐,都正常)

1. 引入依赖

```java

<dependency>
    <groupId>gui.ava</groupId>
    <artifactId>html2image</artifactId>
    <version>2.0.1</version>
</dependency>

```

`import gui.ava.html.parser.HtmlParser;`
`import gui.ava.html.parser.HtmlParserImpl;`
`import gui.ava.html.renderer.ImageRenderer;`

2. java代码

```java

    public static void main(String[] args) {
        HtmlParser htmlParser = new HtmlParserImpl();
        htmlParser.loadHtml(html);
        // jpg会变红
         ImageRenderer imageRenderer = new ImageRendererImpl(htmlParser);
        // 修复jpg变红问题
//        ImageRenderer imageRenderer = new ImageRendererSubImpl(htmlParser);
        String fileName = "D:\\Desktop\\test\\20230131\\hahaha333.jpg";
        imageRenderer.saveImage(fileName);
    }

```

3. ImageRendererSubImpl类(修复jpg变红问题)

```java

package com.ruoyi.htmlTop;

import gui.ava.html.exception.RenderException;
import gui.ava.html.parser.DocumentHolder;
import gui.ava.html.renderer.FormatNameUtil;
import gui.ava.html.renderer.ImageRendererImpl;
import org.xhtmlrenderer.util.FSImageWriter;

import java.awt.image.BufferedImage;
import java.io.*;

/**
 * @className: ImageRendererSubImpl
 * @Description: 修复jpg变红问题
 * @version: v1.8.0
 * @author: hahaen
 * @date: 2022/12/12 11:48
 */
public class ImageRendererSubImpl extends ImageRendererImpl {

    public ImageRendererSubImpl(DocumentHolder documentHolder) {
        super(documentHolder);
    }

    private String getImageFormat(String filename) {
        if (this.getImageFormat() != null) {
            return this.getImageFormat();
        } else {
            return filename != null ? FormatNameUtil.formatForFilename(filename) : FormatNameUtil.getDefaultFormat();
        }
    }

    private FSImageWriter getImageWriter(String imageFormat) {
        FSImageWriter imageWriter = new FSImageWriter(imageFormat);
        imageWriter.setWriteCompressionMode(this.getWriteCompressionMode());
        imageWriter.setWriteCompressionQuality(this.getWriteCompressionQuality());
        imageWriter.setWriteCompressionType(this.getWriteCompressionType());
        return imageWriter;
    }

    public void saveImage(File file) {
        try {
            BufferedOutputStream outputStream = new BufferedOutputStream(new FileOutputStream(file));
            this.save(outputStream, file.getName(), true);
        } catch (IOException var3) {
            throw new RenderException("IOException while rendering image to " + file.getAbsolutePath(), var3);
        }
    }

    public void saveImage(String filename) {
        this.saveImage(new File(filename));
    }

    private void save(OutputStream outputStream, String filename, boolean closeStream) {
        try {
            String imageFormat = this.getImageFormat(filename);
            FSImageWriter imageWriter = this.getImageWriter(imageFormat);
            BufferedImage bufferedImage = this.getBufferedImage(getImageType(imageFormat));
            imageWriter.write(bufferedImage, outputStream);
        } catch (IOException var15) {
            throw new RenderException("IOException while rendering image", var15);
        } finally {
            if (closeStream) {
                try {
                    outputStream.close();
                } catch (IOException var14) {
                }
            }

        }
    }

    /**
     * 获取图像类型
     * 根据图像的格式
     */
    public int getImageType(String imageFormat) {
        if ("jpg".equalsIgnoreCase(imageFormat)) {
            return BufferedImage.TYPE_3BYTE_BGR;
        }
        if ("bmp".equalsIgnoreCase(imageFormat)) {
            return BufferedImage.TYPE_INT_RGB;
        }
        return BufferedImage.BITMASK;
    }

}

```

## HTML例子

```java

String html = "<table border=\"1\">\n" +
        "   <tr>\n" +
        "     <th>名称</th>\n" +
        "     <th>官网</th>\n" +
        "     <th>性质</th>\n" +
        "     <th>性质</th>\n" +
        "     <th>性质</th>\n" +
        "     <th>性质</th>\n" +
        "     <th>性质性质性质性" +
        "质性质性质性质</th>\n" +

        "   </tr>\n" +
        "   <tr>\n" +
        "     <td>C语言中文网</td>\n" +
        "     <td>http://c.biancheng.net/</td>\n" +
        "     <td>教育</td>\n" +
        "   </tr>\n" +
        "    <tr>\n" +
        "     <td>百度</td>\n" +
        "     <td>http://www.baidu.com/</td>\n" +
        "     <td>搜索</td>\n" +
        "     </tr>\n" +
        "   <tr>\n" +
        "      <td>当当</td>\n" +
        "     <td>http://www.dangdang.com/</td>\n" +
        "      <td>图书</td>\n" +
        "    </tr>\n" +
        "</table>";
        
```


