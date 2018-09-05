---
title: 项目笔记 —— Word To PDF To Image
date: 2018-09-05 10:52:00
categories: "Java"
tags:
	- Java
	- Word
	- PDF
	- Image
	- 桌面应用程序
	- 项目笔记

---
## 前言
在项目中需要使用桌面应用程序显示Word文档，并实现一个放大缩小的效果。但是我发现Word并不能直接转换成Image格式，所以就取了一个折中的方法。先把Word转换为PDF，再把PDF转换成Image。
<!--more-->

[jacob-1.18.dll版本下载](https://sourceforge.net/projects/jacob-project/files/jacob-project/)
```xml
 <!-- pdfToImage -->
<dependency>
  <groupId>org.apache.pdfbox</groupId>
  <artifactId>pdfbox</artifactId>
  <version>1.8.15</version>
</dependency>

<!-- docToPdf -->
<!-- 将jacob-1.18.dll 文件复制到对应位数的..java\jdk\bin目录下-->
<dependency>
  <groupId>com.hynnet</groupId>
  <artifactId>jacob</artifactId>
  <version>1.18</version>
</dependency>
```

```java
import com.jacob.activeX.ActiveXComponent;
import com.jacob.com.ComThread;
import com.jacob.com.Dispatch;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * @author ： @gf
 * Create on 2018/8/24 11:16
 */
public class DocToPdfUtil {
    private static final Logger LOGGER = LoggerFactory.getLogger(DocToPdfUtil.class);

    public static String formatConvert(String resourcePath) {
        String resource = resourcePath.substring(0, resourcePath.lastIndexOf("."));
        if (resourcePath.endsWith("doc")) {
            toPdf(resourcePath, resource + ".pdf");
        }
        return resource + ".pdf";
    }

    private static void toPdf(String docPath, String toPafName) {
        ActiveXComponent activeXComponent = null;
        try {
            ComThread.InitSTA();
            activeXComponent = new ActiveXComponent("Word.Application");

            //不显示转换界面
            activeXComponent.setProperty("Visible", false);
            LOGGER.info("toPdf() ...ing");
            Dispatch dispatch = activeXComponent.getProperty("Documents").toDispatch();
            dispatch = Dispatch.call(dispatch, "Open", docPath, false, true).toDispatch();
            //17 代表 doc 转 pdf的级别
            Dispatch.call(dispatch, "ExportAsFixedFormat", toPafName, 17);
            Dispatch.call(dispatch, "Close", false);
            LOGGER.info("toPdf() ...over");
        } catch (Exception e) {
            LOGGER.error("doc to pdf failure", e);
        } finally {
            if (activeXComponent != null) {
                activeXComponent.invoke("Quit", 0);
            }
            ComThread.Release();
        }
    }

//    public static void main(String[] args) {
//        formatConvert("E:\\workspace32\\Test\\fillInWord\\Demo.doc");
//    }
}

```

```java
import org.apache.pdfbox.pdmodel.PDDocument;
import org.apache.pdfbox.pdmodel.PDPage;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.swing.*;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;

/**
 * @author ： @gf
 * Create on 2018/8/24 14:10
 */
public class PdfToImageUtil {
    private static final Logger LOGGER = LoggerFactory.getLogger(PdfToImageUtil.class);

    public static Image toImage(String pdfFileName) {
        ImageIcon imageIcon = null;
        try {
            PDDocument pdDocument = PDDocument.load(new File(pdfFileName));
            int pageCount = pdDocument.getNumberOfPages();
            PDPage pdPage = (PDPage) pdDocument.getDocumentCatalog().getAllPages().get(0);
            BufferedImage bufferedImage = pdPage.convertToImage();
            if (bufferedImage != null) {
                imageIcon = new ImageIcon(Toolkit.getDefaultToolkit().createImage(bufferedImage.getSource()));
            }
            pdDocument.close();
            LOGGER.info("PDF To Image Success");
        } catch (IOException e) {
            LOGGER.error("To Image Failure:", e);
        }

        if (imageIcon != null) {
            return imageIcon.getImage();
        } else {
            return new ImageIcon().getImage();
        }
    }
}
```