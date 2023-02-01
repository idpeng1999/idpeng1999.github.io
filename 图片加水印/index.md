# 图片加水印


[//]: # (![集合]&#40;/img/集合/img.png&#41;)

## 图片水印

### Thumbnailator实现

```java
import net.coobird.thumbnailator.Thumbnails;
import org.junit.jupiter.api.Test;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
```

```java

    // 图片水印
    // 图片水印的话用 Thumbnailator 会方便一点
    // Thumbnailator实现
    public void addImgWaterMark(BufferedImage targetImg, BufferedImage waterImg, String outPath) {
        try {
            Thumbnails.of(targetImg)
                    .size(targetImg.getWidth(), targetImg.getHeight()) // 大小
                    .watermark(myPositions.ppp, waterImg, 0.5f)  //Positions.BOTTOM_RIGHT 图片位置 0.5f表示透明度，最大值为1
                    .outputQuality(1)   // 图片质量，最大值为1
                    .toFile(new File(outPath));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Test
    public void addImg2Test() throws IOException {
        BufferedImage targetImg = ImageIO.read(new File("D:\\Desktop\\test\\1.jpg"));
        BufferedImage waterImage = ImageIO.read(new File("D:\\Desktop\\test\\2.jpg"));
        addImgWaterMark(targetImg, waterImage, "D:\\Desktop\\test\\1+2.jpg");
    }

```

* `myPositions类`

```java

import net.coobird.thumbnailator.geometry.Position;
import java.awt.*;

/**
 * 自定义图片位置，Positions.BOTTOM_RIGHT已经定义好的位置
 */
public enum myPositions implements Position {
    ppp {
        public Point calculate(int enclosingWidth, int enclosingHeight, int width, int height, int insetLeft, int insetRight, int insetTop, int insetBottom) {
            int var9 = enclosingWidth - enclosingWidth / 3;
            int var10 = enclosingHeight - enclosingHeight / 3;

            return new Point(var9, var10);
        }
    }
}

```

### Graphics2D实现

```java
import org.junit.jupiter.api.Test;

import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
```

```java

// 图片水印
    // Graphics2D实现
    public void addImageWaterMark(BufferedImage targetImg, BufferedImage waterImg, String outPath) {
        try {
            int width = targetImg.getWidth(); //图片宽
            int height = targetImg.getHeight(); //图片高
            BufferedImage bufferedImage = new BufferedImage(width, height, BufferedImage.TYPE_INT_BGR);
            Graphics2D g = bufferedImage.createGraphics();
            g.drawImage(targetImg, 0, 0, width, height, null);
            // 水印内容放置在右下角
            int x = width - waterImg.getWidth();
            int y = height - waterImg.getHeight();
            g.drawImage(waterImg, x, y, waterImg.getWidth(), waterImg.getHeight(), null);
            FileOutputStream outImgStream = new FileOutputStream(outPath);
            ImageIO.write(bufferedImage, "jpg", outImgStream);
            outImgStream.flush();
            outImgStream.close();
            g.dispose();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Test
    public void addImg1Test() throws IOException {
        BufferedImage targetImg = ImageIO.read(new File("D:\\Desktop\\test\\1.jpg"));
        BufferedImage waterImage = ImageIO.read(new File("D:\\Desktop\\test\\2.jpg"));
        addImageWaterMark(targetImg, waterImage, "D:\\Desktop\\test\\1111.jpg");
    }

```

## 文字水印

```java
import org.junit.jupiter.api.Test;

import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
```

```java

    // 文字水印
    // Graphics2D实现
    public void addTextWaterMark(BufferedImage targetImg, Color textColor, int fontSize, String text, String outPath) {
        try {
            int width = targetImg.getWidth(); //图片宽
            int height = targetImg.getHeight(); //图片高
            BufferedImage bufferedImage = new BufferedImage(width, height, BufferedImage.TYPE_INT_BGR);
            Graphics2D g = bufferedImage.createGraphics();
            g.drawImage(targetImg, 0, 0, width, height, null);
            g.setColor(textColor); //水印颜色
            g.setFont(new Font("微软雅黑", Font.ITALIC, fontSize));
            
            // 水印内容放置在右下角
            int x = width - (text.length() + 1) * fontSize;
            int y = height - fontSize * 2;
            
            g.drawString(text, x, y);
            FileOutputStream outImgStream = new FileOutputStream(outPath);
            ImageIO.write(bufferedImage, "jpg", outImgStream);
            outImgStream.flush();
            outImgStream.close();
            g.dispose();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Test
    public void addTextTest() throws IOException {
        File file = new File("D:\\Desktop\\test\\2.jpg");
        BufferedImage image = ImageIO.read(file);
        addTextWaterMark(image, Color.RED, 8, "测试文本水印", "D:\\Desktop\\test\\444.jpg");// 80 or 8
    }

```






