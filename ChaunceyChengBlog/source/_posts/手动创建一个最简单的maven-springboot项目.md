---
title: 手动创建一个最简单的maven springboot项目
date: 2018-01-23 09:54:12
tags: [java, maven]
categories: language
---
![maven_springboot](/images/maven_springboot.png)

> 我们可能经常需要建立一个简单的web项目测试，而手边又没有或时不想那么麻烦使用IDE工具。这是我们可以使用maven+springboot建立一个最简单的web项目，对外提供简单的REST之类的接口。

<!-- more -->

# 创建maven目录结构
1. 新建一个项目文件夹`springboot_test`;
2. 在项目根目录下新建代码目录`src\main\java\com\yihengliu\sprintboottest\controller`;
3. 在项目根目录下新建资源目录`src\main\resources`;

结构目录：
```
.
├── pom.xml
└── src
    └── main
        ├── java
        │   └── com
        │       └── yihengliu
        │           └── springboottest
        │               ├── controller
        └── resources
```

# 编写pom.xml文件
在项目根目录下新建`pom.xml`文件，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.yihengliu</groupId>
    <artifactId>springboot_test</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
   </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>          
```


# 编写运行类和Controller类
1. 目录`src/main/java/com/yihengliu/springboottest/`下编写运行类`SpringbootTestApplication.java`

    ```java
    package com.yihengliu.springboottest;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class SpringbootTestApplication {
    	public static void main(String[] args) {
    		SpringApplication.run(SpringbootTestApplication.class, args);
    	}
    }
    ```
2. 目录`src/main/java/com/yihengliu/springboottest/controller/`下编写Controller类

    ```java
    package com.yihengliu.springboottest.controller;

    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;

    /**
     * 测试controller
     *
     * @author liucheng
     **/
    @RestController
    public class TestController {
    	@RequestMapping("/index")
    	public String method() {
    		return "return request";
    	}
    }
    ```

# 启动测试
1. 启动项目，在目录根目录运行`mvn spring-boot:run`；

2. 运行访问  
![springboot_test_show](/images/maven_springboot_show.png)

# 参考代码
[点击下载](/resources/springboot_test.zip)
