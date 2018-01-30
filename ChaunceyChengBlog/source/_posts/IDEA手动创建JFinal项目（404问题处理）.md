---
title: IDEA手动创建JFinal项目（404问题处理）
date: 2018-01-30 13:42:49
tags: [java, jfinal]
categories: language
---
![jfinal](/images/jfinal.png)

> 公司项目使用jfinal有一段时间了，也有自己手动搭建过项目，但是没有使用demo中jetty方式启动过项目。这几天决定参考jfinal文档更好的学习下jfinal框架，其实挺简单，但是碰到了一个jetty报错404的问题，查询了论坛分享和网上一些创建方式也没也没解决，折腾了一段时间，这里做个记录希望同样碰到的人可以少折腾会儿。

<!-- more -->

## 创建项目
首先创建一个maven项目（使用模板）  
![create_project1](/images/create_project1.png)  
![create_project2](/images/create_project2.png)   
这里使用了我自己的maven配置文件 
![create_project3](/images/create_project3.png)   
由于使用idea创建项目，这里我一般项目目录（Project location）和模块目录（Module file location)会分开。这样的话再新建模块的时候所有模块的目录层级更清晰点。  
`而这也是为后面的出404问题埋下了伏笔`  
![create_project4](/images/create_project4.png)  
结束完成项目创建。

## 修改项目配置
1. 增加maven依赖  

    ```xml
    <dependency>
        <groupId>com.jfinal</groupId>
        <artifactId>jfinal</artifactId>
        <version>3.3</version>
        </dependency>

        <dependency>
        <groupId>com.jfinal</groupId>
        <artifactId>jetty-server</artifactId>
        <version>8.1.8</version>
        <!-- <scope>provided</scope>-->
    </dependency>
    ```
2. 修改maven项目结构配置  
![project_structure](/images/project_structure.png)  
![project_structure1](/images/project_structure1.png)  

## 简单代码编写
1. 创建控制类  
`TestController.java`

    ```java
    package com.yihengliu.test.controller;

    import com.jfinal.core.Controller;

    /**
    * 测试控制类
    *
    * @author liucheng
    * @version 0.1
    * @since 0.1 2018-01-30 下午2:31
    **/
    public class TestController extends Controller {
        public void index() {
        renderText("Hello, JFinal!");
        }
    }
    ```
1. 创建配置类  
`DemoConfig.java`

    ```java
    package com.yihengliu.test;

    import com.jfinal.config.*;
    import com.jfinal.template.Engine;
    import com.yihengliu.test.controller.TestController;

    /**
    * jfinal配置类
    *
    * @author liucheng
    * @version 0.1
    * @since 0.1 2018-01-30 下午2:30
    **/
    public class DemoConfig extends JFinalConfig{
        @Override
        public void configConstant(Constants me) {

        }

        @Override
        public void configRoute(Routes me) {
            me.add("/", TestController.class);
        }

        @Override
        public void configEngine(Engine me) {

        }

        @Override
        public void configPlugin(Plugins me) {

        }

        @Override
        public void configInterceptor(Interceptors me) {

        }

        @Override
        public void configHandler(Handlers me) {

        }
        
        public static void main(String[] args) {
            JFinal.start("src/main/webapp", 9091, "/");
        }
    }

    ```

3. `web.xml`文件配置  

    ```xml
    <filter>
        <filter-name>jfinal</filter-name>
        <filter-class>com.jfinal.core.JFinalFilter</filter-class>
        <init-param>
            <param-name>configClass</param-name>
            <param-value>com.yihengliu.test.DemoConfig</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>jfinal</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ```

## 测试问题处理
在`DemoConfig`类中运行main函数   
![run1](/images/run1.png)  

然后问题来了，页面怎么访问都是404，错误如图：  
![jfinal_error1](/images/jfinal_error1.png)
而不是jfinal报错(这种报错就肯定时jfinal路由配置的问题了)：  
![jfinal_error2](/images/jfinal_error2.png)  

其实很容易就能看出是因为没有读取web.xml配置，导致项目没有启动jfinal。论坛里面也有提到这种情况的，但是由于自己没有什么jetty使用经验，所以后面还是通过debug代码才发问题的原因。  
原因就是前面建立项目提到的，在建立项目的时候project目录和module目录不在同一个目录，所以启动的地方需要修改相对目录，加上module目录。  
`JFinal.start("jfinal_test/src/main/webapp", 9091, "/");`

最终，重启项目，顺利运行。  
![run2](/images/run2.png)

## 总结
不得不说下jfinal使用确实很简单、方便，而自己搭建项目碰到问题没有快速定位也说明自己确实还有很多的东西需要学习。
