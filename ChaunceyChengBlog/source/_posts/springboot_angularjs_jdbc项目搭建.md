---
title: sprintboot-web项目搭建+angularjs+jdbc
date: 2018-01-22 11:35:15
tags: [java, sprintboot]
categories: java
---
![springboot_angularjs_jdbc](/images/springboot_angularjs_jdbc.png)

> 这里介绍一个springboot项目的搭建过程，并结合springboot-jdbc获取数据，angularjs展示数据。

<!-- more -->

# 环境搭建
1. 使用Idea新建springboot项目  
项目建立的时候选择springboot模板；  
maven配置文件pom.xml中需要引入：`spring-boot-starter-web`,参见：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    	<modelVersion>4.0.0</modelVersion>

    	<groupId>com.yihengliu</groupId>
    	<artifactId>angularjs_demo</artifactId>
    	<version>0.0.1-SNAPSHOT</version>
    	<packaging>jar</packaging>

    	<name>angularjs_demo</name>
    	<description>Demo project for Spring Boot</description>

    	<parent>
    		<groupId>org.springframework.boot</groupId>
    		<artifactId>spring-boot-starter-parent</artifactId>
    		<version>1.5.9.RELEASE</version>
    		<relativePath/> <!-- lookup parent from repository -->
    	</parent>

    	<properties>
    		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    		<java.version>1.8</java.version>
    	</properties>

    	<dependencies>
    		<dependency>
    			<groupId>org.springframework.boot</groupId>
    			<artifactId>spring-boot-starter-web</artifactId>
    		</dependency>

    		<dependency>
    			<groupId>org.springframework.boot</groupId>
    			<artifactId>spring-boot-starter-test</artifactId>
    			<scope>test</scope>
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

2. 新建项目结构
```
angularjs_demo/
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── yihengliu
    │   │           └── angularjs_demo
    │   │               ├── AngularjsDemoApplication.java
    │   │               ├── controller
    │   │               ├── dao
    │   │               ├── model
    │   │               └── service
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       |    ├── css
    │       |    └── js
    |       └── templates
    └── test
        ├── java
        │   └── com
        │       └── yihengliu
        │           └── angularjs_demo
        │               └── AngularjsDemoApplicationTests.java
        └── resources
```

3. 编写controller代码  

    * RestController  
    返回值字符串被直接传送到浏览器并显示给用户。

        ```java
        package com.yihengliu.angularjsdemo.controller;

        import org.springframework.web.bind.annotation.PathVariable;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.RequestMethod;
        import org.springframework.web.bind.annotation.RestController;

        /**
         * 访问controller
         *
         * @author liucheng
         * @version 0.1
         * @since 0.1 2018-01-22 上午11:49
         **/
        @RestController
        @RequestMapping("/server")
        public class RestRequestController {
            @RequestMapping("/index")
            public String method() {
                return "Index Page";
            }

            @RequestMapping("/users/{username}")
            public String userProfile(@PathVariable("username") String username) {
                return String.format("user %s", username);
            }

            @RequestMapping("/posts/{id}")
            public String post(@PathVariable("id") int id) {
                return String.format("post %d", id);
            }

            @RequestMapping(value = "/login", method = RequestMethod.GET)
            public String loginGet() {
                return "Login Page";
            }

            @RequestMapping(value = "/login", method = RequestMethod.POST)
            public String loginPost() {
                return "Login Post Request";
            }
        }
        ```

    * Controller  
    使用模板返回使用Thymeleaf模板引擎渲染后的HTML代码。
    引入依赖：

        ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        ```
    controller编写：

        ```java
        package com.yihengliu.angularjsdemo.controller;

        import org.springframework.stereotype.Controller;
        import org.springframework.ui.Model;
        import org.springframework.web.bind.annotation.PathVariable;
        import org.springframework.web.bind.annotation.RequestMapping;

        /**
         * 普通controller
         *
         * @author liucheng
         * @version 0.1
         * @since 0.1 2018-01-22 下午1:42
         **/
        @Controller
        public class HelloController {
            @RequestMapping("/hello/{name}")
            public String hello(@PathVariable("name") String name, Model model) {
                model.addAttribute("name", name);
                return "hello";
            }
        }
        ```
    html页面：

        ```html
        <!DOCTYPE html>
        <html xmlns:th="http://www.thymeleaf.org">
        <head>
            <meta charset="UTF-8" />
            <title>Getting started: Serving Web Content</title>
        </head>
        <body>
            <p th:text="'Hello, ' + ${name} + '!'" />
        </body>
        </html>
        ```

# 引入JDBC + angularjs
## 准备
1. 创建表

    ```sql
    CREATE TABLE `t_user` (
        id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
        name VARCHAR(20) NOT NULL,
        password VARCHAR(20)
    );
    ```

2. 修改pom.xml

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>commons-dbcp</groupId>
        <artifactId>commons-dbcp</artifactId>
    </dependency>
    ```

## 代码编写
`User.java` model
```java
package com.yihengliu.angularjsdemo.model;

/**
 * 用户类
 *
 * @author liucheng
 * @version 0.1
 * @since 0.1 2018-01-22 下午3:28
 **/
public class User {
    private Integer Id;
    private String name;
    private String password;

    public Integer getId() {
        return Id;
    }

    public void setId(Integer id) {
        Id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```
`UserDao.java`
```java
package com.yihengliu.angularjsdemo.dao;

import com.yihengliu.angularjsdemo.model.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.support.rowset.SqlRowSet;
import org.springframework.stereotype.Repository;

import java.math.BigInteger;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

/**
 * 用户Dao类
 *
 * @author liucheng
 * @version 0.1
 * @since 0.1 2018-01-22 下午3:29
 **/
@Repository
public class UserDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    public List<User> findAll() {
        List<User> list = new ArrayList<>();
        String sql = "SELECT * FROM t_user";
        SqlRowSet rows = jdbcTemplate.queryForRowSet(sql, new Object[]{});
        while (rows.next()) {
            User user = new User();
            user.setId(rows.getInt("id"));
            user.setName(rows.getString("name"));
            user.setPassword(rows.getString("password"));
            list.add(user);
        }
        return list;
    }

    public User get(String id) {
        User user = null;
        String sql = "SELECT * FROM t_user WHERE ID = ?";
        SqlRowSet rows = jdbcTemplate.queryForRowSet(sql, new Object[]{id});
        while (rows.next()) {
            user = new User();
            user.setId(rows.getInt("id"));
            user.setName(rows.getString("name"));
            user.setPassword(rows.getString("password"));
        }
        return user;
    }

    public Integer insert(User user) {
        String sql = "INSERT INTO t_user (name, password) VALUE (?, ?)";
        Map<String, Object> tableStatus = jdbcTemplate.queryForMap("SHOW TABLE STATUS WHERE NAME='T_USER';");
        jdbcTemplate.update(sql, new Object[]{user.getName(), user.getPassword()});
        BigInteger id = (BigInteger) tableStatus.get("Auto_increment");
        return id.intValue();
    }

    public void update(User user) {
        String sql = "UPDATE t_user SET NAME = ?, PASSWORD = ? WHERE ID = ?";
        jdbcTemplate.update(sql, new Object[]{user.getName(), user.getPassword(), user.getId()});
    }

    public void delete(String id) {
        String sql = "DELETE FROM t_user WHERE ID = ?";
        jdbcTemplate.update(sql, new Object[]{id});
    }
}
```
`UserController.java`
```java
package com.yihengliu.angularjsdemo.controller;

import com.yihengliu.angularjsdemo.dao.UserDao;
import com.yihengliu.angularjsdemo.model.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * 用户controller类
 *
 * @author liucheng
 * @version 0.1
 * @since 0.1 2018-01-22 下午3:34
 **/
@Controller
public class UserController {
    @Autowired
    private UserDao userDao;

    @GetMapping("/")
    public String index() {
        return "index";
    }

    @PostMapping("/save")
    @ResponseBody
    public Map<String, Object> save(@RequestBody User user) {
        Map<String, Object> result = new HashMap<>(5);
        if (user.getId() == null) {
            user.setId(userDao.insert(user));
        } else {
            userDao.update(user);
        }
        result.put("id", user.getId());
        return result;
    }

    @PostMapping("/get")
    @ResponseBody
    public User get(String id) {
       return userDao.get(id);
    }

    @ResponseBody
    @RequestMapping("/findAll")
    public List<User> findAll() {
        return userDao.findAll();
    }

    @PostMapping("/delete")
    @ResponseBody
    public Map<String, Object> delete(String id) {
        Map<String, Object> result = new HashMap<>(2);
        userDao.delete(id);
        result.put("id", id);
        return result;
    }
}
```
## 数据库配置
```
server.port=8088

# database settings
spring.datasource.initialize=false
spring.datasource.url=jdbc:mysql://192.168.6.209:3306/test
spring.datasource.username=system
spring.datasource.password=system
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

## 页面编写
使用tymeleaf模板+angularjs请求数据并绑定数据。  
`index.html`
```java
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8"/>
    <title>sprint boot web</title>
    <script src="//cdn.bootcss.com/angular.js/1.5.6/angular.min.js"></script>
    <script>
        var app = angular.module('app', []);
        app.controller("UserController", function ($rootScope, $scope, $http) {
            $scope.data = {};
            $scope.rows = [];

            // 添加数据
            $scope.add = function () {
                $scope.data = {
                    id: 1,
                    name: 'liucheng',
                    password: 'passowrd'
                };
            }

            // 编辑数据
            $scope.edit = function (id) {
                for (var i in $scope.rows) {
                    var row = $scope.rows[i];
                    if (id == row.id) {
                        $scope.data = row;
                        return;
                    }
                }
            }

            // 移除数据
            $scope.remove = function (id) {
                for (var i in $scope.rows) {
                    var row = $scope.rows[i];
                    if (id == row.id) {
                        $scope.rows.splice(i, 1);
                        return;
                    }
                }
            }

            // 保存数据
            $scope.save = function () {
                $http({
                    url: '/save',
                    method: 'POST',
                    data: $scope.data
                }).success(function (r) {
                    // 保存成功后更新数据
                    $scope.get(r.id)
                });
            }

            // 删除数据
            $scope.del = function (id) {
                $http({
                    url: '/delete?id=' + id,
                    method: 'POST',
                }).success(function (r) {
                    // 删除成功后移除数据
                    $scope.remove(r.id);
                })
            }


            // 获取数据
            $scope.get = function (id) {
                $http({
                    url: '/get?id=' + id,
                    method: 'POST',
                }).success(function (data) {
                    for (var i in $scope.rows) {
                        var row = $scope.rows[i];
                        if (data.id == row.id) {
                            row.name = data.name;
                            row.password = data.password;
                        }
                    }
                    $scope.rows.push(data);
                });
            }

            // 初始化时载入数据
            $http({
                url: '/findAll',
                method: 'POST'
            }).success(function (rows) {
                for (var i in rows) {
                    var row = rows[i];
                    $scope.rows.push(row);
                }
            })
        })
    </script>
</head>
<body ng-app="app" ng-controller="UserController">
<h1>sprint boot web</h1>
<h3>用户信息:</h3>
<table cellspacing="1" style="background-color: #a0c6e5">
    <tr>
        <td>用户Id:</td>
        <td><input id="id" ng-model="data.id"/></td>
        <td>用户名：</td>
        <td><input id="name" ng-model="data.name"/></td>
        <td>密码：</td>
        <td><input id="password" ng-model="data.password"/></td>
    </tr>
</table>
<input type="button" value="add" ng-click="add()"/>
<input type="button" value="save" ng-click="save()"/>

<h3>用户列表：</h3>
<table cellspacing="1" style="background-color: #a0c6e5">
    <tr style="text-align: center; color: #0076c8; background-color: #F4FAFF; font-weight: bold;">
        <td>操作</td>
        <td>ID</td>
        <td>用户名</td>
        <td>密码</td>
    </tr>
    <tr ng-repeat="row in rows" bgcolor='#F4FAFF'>
        <td>
            <input ng-click="edit(row.id)" value="编辑" type="button"/>
            <input ng-click="del(row.id)" value="删除" type="button"/>
        </td>
        <td>{{row.id}}</td>
        <td>{{row.name}}</td>
        <td>{{row.password}}</td>
    </tr>
</table>
<br/>
<br/>
<a href="http://yihengliu.com">访文章博客</a>
</body>
</html>
```

## 测试效果
![sprintboot_angularjs_jdbc](/images/springboot_angularjs_jdbc_display.png)

## 代码下载
[springboot_angularjs_jdbc](/resources/springboot_angularjs_jdbc.zip)
