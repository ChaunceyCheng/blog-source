---
title: Java8获取参数名及Idea/Eclipse/Maven配置
date: 2018-01-19 17:39:21
tags: [java]
categories: language
---

> 在Java8之前，代码编译为class文件后，方法参数的类型固定，但是方法名称会丢失，方法名称会变成arg0、arg1....。而现在，在Java8开始可以在class文件中保留参数名，这就给反射带来了极大的遍历。像mybatis等需要使用反射机制获取方法参数的时候就可以不用像以前一样需要使用类似于`@Para`之类的注解。


# 功能测试
[代码参考自,点击进入](https://www.liaoxuefeng.com/article/00141999088629621039ee8c4614579bfedb78a5030bce3000)

1. 编写测试类
    ```java
    import java.lang.reflect.Method;
    import java.lang.reflect.Parameter;

    public class GetRuntimeParameterName {
    	public void createUser(String name, int age, int version) {

    	}

    	public static void main(String[] args) throws Exception {
    		for (Method m : GetRuntimeParameterName.class.getMethods()) {
    			System.out.println("--------------------");
    			System.out.println(" method: " + m.getName());
    			System.out.println(" return: " + m.getReturnType().getName());
    			for (Parameter p : m.getParameters()) {
    				System.out.println("parameter:" + p.getType().getName() + ", " + p.getName());
    			}
    		}
    	}
    }

    ```
2. 测试  
由于为了避免.class文件因为保留参数名而导致.class文件过大或者占用更多的内存，另外也避免有些参数（secrect/password)泄露安全信息，JVM即使时1.8默认是不会保留参数名称的。  
所以我们这里正常可以测试编译保留参数名和不保留参数名的情况。
    * 不保留参数名称  
    编译命令：
        ```
        javac GetRuntimeParameterName.java
        ```
    输出结果：
        ```
        --------------------
        method: createUser
        return: void
        parameter:java.lang.String, arg0
        parameter:int, arg1
        parameter:int, arg2
        ```

    * 保留参数名  
      编译命令：
        ```
        javac -parameters GetRuntimeParameterName.java
        ```
      输出结果：
        ```
        --------------------
        method: createUser
        return: void
        parameter:java.lang.String, name
        parameter:int, age
        parameter:int, version
        ```

# IDE和Maven开启-parameters的办法
## Eclipse中开启的办法
`Preferences->java->Compiler`下勾选`Store information about method parameters`选项。
这样在使用eclipse编译java文件的时候就会将参数名称编译到class文件中。
## Idea中开启的方法
`File->Settings->Build,Execution,Deployment->Java Compiler`下的`Additional command line parameters`选项中添加`-parameters`。
![idea修改配置](/images/idea_modify.png)
## Maven中开启的办法
在pom.xml的编译插件中增加参数配置`<arg>-parameters</arg>`。
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.3</version>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <compilerArgs>
            <arg>-parameters</arg>
        </compilerArgs>
    </configuration>
</plugin>
```

> 另外附上测试使用Maven install命令编译class文件的情况：
> 使用maven install编译生成jar包时：
> 1. 如果target/classes下class文件对于源码文件都没有修改，不会重新编译，直接打包classes下的class文件；
> 2. 如果target/classes下class文件对于源码文件都有修改，会重新编译所有class文件，然后打包classes下的class文件；
