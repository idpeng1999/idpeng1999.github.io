# Zip压缩解压base64


[//]: # (![集合]&#40;/img/集合/img.png&#41;)

## java代码

```java

package com.ruoyi.panel;

import java.io.BufferedOutputStream;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Base64;
import java.util.List;
import java.util.zip.ZipEntry;
import java.util.zip.ZipInputStream;
import java.util.zip.ZipOutputStream;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * @className: ZipToBase64
 * @Description: 文件压缩base64,压缩包base64解压
 * @version: v1.8.0
 * @author: hahaen
 * @date: 2023/1/16 19:19
 */
public class ZipToBase64 {
    private static final int BUFFER_SIZE = 2 * 1024;

    private static final Logger log = LoggerFactory.getLogger(ZipToBase64.class);

    /**
     * toBase64Zip
     * @param srcFiles
     * @return
     * @throws RuntimeException
     */
    public static String toBase64Zip(List<File> srcFiles) throws RuntimeException {
        log.info("开始压缩文件    [{}]", srcFiles);
        long start = System.currentTimeMillis();
        String base64toZip = "";
        ZipOutputStream zos = null;
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        try {
            zos = new ZipOutputStream(baos);
            for (File srcFile : srcFiles) {
                byte[] buf = new byte[BUFFER_SIZE];
                log.info("压缩的文件名称    [{}]   ", srcFile.getName() + "压缩的文件大小      [{}] ", srcFile.length());
                zos.putNextEntry(new ZipEntry(srcFile.getName()));
                int len;
                FileInputStream in = new FileInputStream(srcFile);
                while ((len = in.read(buf)) != -1) {
                    zos.write(buf, 0, len);
                }
                zos.closeEntry();
                in.close();
            }
            long end = System.currentTimeMillis();
            log.info("压缩完成，耗时：    [{}] ms", (end - start));
        } catch (Exception e) {
            throw new RuntimeException("zip error from ZipToBase64", e);
        } finally {
            if (zos != null) {
                try {
                    zos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

        byte[] refereeFileBase64Bytes = Base64.getEncoder().encode(baos.toByteArray());
        try {
            base64toZip = new String(refereeFileBase64Bytes, "UTF-8");
        } catch (Exception e) {
            throw new RuntimeException("压缩流出现异常", e);
        }
        return base64toZip;
    }

    /**
     * Base64ToFile
     * @param base64
     * @param path
     * @throws RuntimeException
     */
    public static void Base64ToFile(String base64, String path) throws RuntimeException {
        log.info("解压文件地址" + path);
        try {
            byte[] byteBase64 = Base64.getDecoder().decode(base64);
            ByteArrayInputStream byteArray = new ByteArrayInputStream(byteBase64);
            ZipInputStream zipInput = new ZipInputStream(byteArray);
            ZipEntry entry = zipInput.getNextEntry();
            File fout = null;
            File file = new File(path);
            String parent = file.getParent();
            while (entry != null && !entry.isDirectory()) {
                log.info("文件名称:    [{}]", entry.getName());
                fout = new File(parent, entry.getName());
                if (!fout.exists()) {
                    (new File(fout.getParent())).mkdirs();
                }
                BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(fout));
                int offo = -1;
                byte[] buffer = new byte[BUFFER_SIZE];
                while ((offo = zipInput.read(buffer)) != -1) {
                    bos.write(buffer, 0, offo);
                }
                bos.close();
                // 获取 下一个文件
                entry = zipInput.getNextEntry();
            }
            zipInput.close();
            byteArray.close();
        } catch (Exception e) {
            throw new RuntimeException("解压流出现异常", e);
        }

    }

    public static void main(String[] args) {
        // 文件压缩
        List<File> fileList = new ArrayList<File>();
        fileList.add(new File("D:\\Desktop\\test\\20230131\\hahaha112.png"));
        fileList.add(new File("D:\\Desktop\\test\\20230131\\hahaha333.jpg"));
        fileList.add(new File("D:\\Desktop\\test\\20230131\\hahaha1121.jpg"));
        String base64 = ZipToBase64.toBase64Zip(fileList);
        log.info("文件base 64 加密：" + base64);
        ZipToBase64.Base64ToFile(base64, "D:\\Desktop\\test\\20230131\\bbb\\a");

    }

}

```

![例子](/img/Zip压缩解压base64/1.png)





