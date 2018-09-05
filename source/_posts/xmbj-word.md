---
title: 项目笔记 —— Word模板开发
date: 2018-09-05 10:52:00
categories: "Java"
tags:
	- Java
	- Word
	- 项目笔记

---

## 前言 
在项目中遇到，需要使用数据库里面的数据填入word模板中，最后找到了FreeMarker，可以说是非常好用了。

> Apache FreeMarker™是一个模板引擎：一个Java库，用于根据模板和更改数据生成文本输出（HTML网页，电子邮件，配置文件，源代码等）。模板是用FreeMarker模板语言（FTL）编写的，这是一种简单的专用语言（不像PHP这样的完整编程语言）。通常，使用通用编程语言（如Java）来准备数据（发布数据库查询，进行业务计算）。然后，Apache FreeMarker使用模板显示准备好的数据。在模板中，您将关注如何呈现数据，而在模板之外，您将关注于要呈现的数据。

<!-- more -->

[FreeMarker 官方文档](https://freemarker.apache.org/docs/index.html)

创建配置实例
首先，您必须创建一个 freemarker.template.Configuration实例并调整其设置。一个Configuration实例是存储FreeMarker的应用水平设置的中心位置。此外，它还处理预解析模板（即 对象）的创建和 缓存Template。
通常，您只在应用程序（可能是servlet）生命周期开始时执行此操作一次：
```java
//创建您的Configuration实例，并指定是否属于FreeMarker
//版本（此处为2.3.27）是否要应用非100％的修补程序
// 向下兼容。有关详细信息，请参阅配置JavaDoc。
配置cfg = new配置（Configuration.VERSION_2_3_27）;

//指定模板文件的来源。我在这里设置了一个
//它的普通目录，但也可以使用非文件系统源：
cfg.setDirectoryForTemplateLoading（new File（“ / where / you / store / templates ”））;

//设置存储的首选charset模板文件.UTF-8是
//在大多数应用程序中都是一个很好
cfg.setDefaultEncoding（ “UTF-8”）;

//设置错误的显示方式。
//在网页*开发期间* TemplateExceptionHandler.HTML_DEBUG_HANDLER更好。
cfg.setTemplateExceptionHandler（TemplateExceptionHandler.RETHROW_HANDLER）;

//不要在FreeMarker中记录它会抛出的异常：
cfg.setLogTemplateExceptions（假）;

//将模板处理期间抛出的未经检查的异常包装到TemplateException-s中。
cfg.setWrapUncheckedExceptions（真）;
```
从现在开始，您应该使用这个单一 配置实例（即它的单例）。但请注意，如果系统有多个使用FreeMarker的独立组件，那么他们当然会使用自己的私有 Configuration实例。

**不要不必要地重新创建Configuration 实例; 它很昂贵，其中包括你丢失了模板缓存。Configuration实例意味着应用程序级单例。**

**我的使用**

```xml
<dependency>
   <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.28</version>
</dependency>
```

1. 首先建立一个Word文档  在需要填充的地方写上几个字母或者数字方便等会定位别用空格或者特殊符号
2. 另存为XML文件 查找`<w:t> 之前填充地方填的字母 </w:t>` 然后把字母改成需要映射的方法或者属性` ${getName()} 或者 ${Name}`
3. 改完后把后缀名改为 ` .ftl 或者 .ftlh`
4. 然后就可以开始写代码了
```java
//根据上面官网所说 我们需要它是一个单例
import java.io.File;
import java.io.IOException;

import freemarker.template.Configuration;
import freemarker.template.TemplateExceptionHandler;

public class Freemark {
    private Freemark() {
    }

    private static class CfgSingleton {
        private static final Configuration CFG = new Configuration(Configuration.VERSION_2_3_28);

        static {
            try {
                CFG.setDirectoryForTemplateLoading(new File("templates"));
            } catch (IOException e) {
                e.printStackTrace();
            }
            CFG.setDefaultEncoding("UTF-8");

            //设置错误的显示方式。
            //在网页开发期间 TemplateExceptionHandler.HTML_DEBUG_HANDLER更好。
            CFG.setTemplateExceptionHandler(TemplateExceptionHandler.RETHROW_HANDLER);

            //不要在FreeMarker中记录它会抛出的异常：
            CFG.setLogTemplateExceptions(false);

            //将模板处理期间抛出的未经检查的异常包装到TemplateException-s中。
            CFG.setWrapUncheckedExceptions(true);
        }
    }

    public static Configuration getCfg() {
        return CfgSingleton.CFG;
    }
}
```

```java

import com.doc.Freemark;
import com.entity.Data;
import freemarker.template.Configuration;
import freemarker.template.Template;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.swing.JOptionPane;
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @author ： @gf
 * Create on 2018/8/20 14:05
 */
public final class DocMapperUtil {
    private static final Logger LOGGER = LoggerFactory.getLogger(DocMapperUtil.class);
    // Data是实体类记录文档信息 Object是需要映射进去的数据是另一个实体类对应ftlh中的方法或者变量
    public static String mapper(Object obj, Data data) {
        Configuration cfg = Freemark.getCfg();
        Writer out;
        String path = "";
        try {
            Template template = cfg.getTemplate(data.getName()+".ftlh");
//            Template template = cfg.getTemplate("Demo.ftlh");

            File file = new File("fillInWord\\" + data.getName() + new SimpleDateFormat("yyyy-MM-dd_hh-mm-ss").format(new Date()) + ".doc");
            out = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(file), "UTF-8"));
            path = file.getAbsolutePath();
            data.setToDocPath(path);

            //把obj中数据映射到文档中
            template.process(obj, out);
            out.close();
        } catch (Exception e) {
            LOGGER.info("映射失败",e);
            JOptionPane.showConfirmDialog(null, "信息映射失败，该文件没有您的信息", "错误", JOptionPane.YES_NO_OPTION);
            return "";
        }
        return path;
    }
}
```

**为了简化第2和第3步的操作**
**我的规则是文档中每个需要的地方写上get{Num} 对应 Class 中的getFunc() 然后替换掉，需要注意的是顺序一定要是一样的，当然也可以在Xml中写上对应的方法然后做判断再替换 **
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;

/**
 * @author ： @gf
 * Create on 2018/9/4 11:38
 */
public class DocToXmlUtil {
    private static final Logger LOGGER = LoggerFactory.getLogger(DocToXmlUtil.class);
    public static void toDo(Class<?> c,String Path) {
        if (!Path.endsWith("xml")) {
            try {
                throw new Exception("文件路径不是一个xml文件");
            } catch (Exception e) {
                LOGGER.error("!Path.endsWith(\"xml\"):",e);
            }
        }

        List<String> funName = new ArrayList<>();
        for(Method method : c.getDeclaredMethods()) {
            if (method.getName().startsWith("get")) {
                funName.add(method.getName());
            }
        }

        FileInputStream fis = null;
        InputStreamReader isr = null;
        BufferedReader br = null;
        StringBuilder stringBuilder = null;
        FileOutputStream fos = null;
        OutputStreamWriter osw = null;

        try {
            fis = new FileInputStream(Path);
            isr = new InputStreamReader(fis, "UTF-8");
            br = new BufferedReader(isr);
            stringBuilder = new StringBuilder();
            String line;
            while((line = br.readLine()) != null){
                stringBuilder.append(line);
            }
        } catch (Exception e) {
            LOGGER.error("文件读取异常",e);
        }finally {
            try {
                if (br != null){
                    br.close();
                }
                if (isr != null){
                    isr.close();
                }
                if (fis != null){
                    fis.close();
                }
            } catch (IOException e) {
                LOGGER.error("文件读取关闭异常",e);
            }
        }
        String xmlStr = null;
        if (stringBuilder != null) {
            xmlStr = stringBuilder.toString();
            for (int i = 1; i <= funName.size(); i++) {
                xmlStr = xmlStr.replace("get"+i, "${"+funName.get(i-1)+"()"+"}");
            }
        }

        try {
            int s = 0;
            if (Path.contains("\\")) {
                s = Path.lastIndexOf("\\")+1;
            }else if (Path.contains("/")) {
                s = Path.lastIndexOf("/")+1;
            }
            String resource = Path.substring(s, Path.lastIndexOf("."));

            fos = new FileOutputStream(new File("templates\\"+resource+".ftlh"));
            osw = new OutputStreamWriter(fos, "UTF-8");
            if (xmlStr!= null) {
                osw.write(xmlStr);
            }
            osw.flush();
        } catch (Exception e) {
            LOGGER.error("文件写入异常",e);
        }finally {
            try {
                if (osw != null){
                    osw.close();
                }
                if (fos != null){
                    fos.close();
                }
            } catch (IOException e) {
                LOGGER.error("文件写入关闭异常",e);
            }

        }
    }
}
```