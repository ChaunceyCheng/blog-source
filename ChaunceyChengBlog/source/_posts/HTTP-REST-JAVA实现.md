---
title: HTTP/REST JAVA实现
date: 2018-01-05 13:20:49
tags: [java,REST]
categories: language
---

# 一、REST
REST(Representational State Transfer)表现层状态转化，它是一种web架构风格、设计风格，提供一些约束条件和原则。REST原则如下：  
1. 资源由URI来指定
2. 显示使用HTTP方式(GET/POST/PUT/DELETE，查/增/改/删)
3. 无状态

基于这个风格设计的软件可以更简介，更有层次，更易于`实现缓存等机制`。

# 二、JAVA访问
访问HTTP/REST服务目前主要就是使用不同的方法访问http服务。
这里主要提供三种访问示例：
1. 使用jdk自带的HttpURLConnection访问
    ```java
    package com.yihengliu.http;

    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.net.HttpURLConnection;
    import java.net.URL;

    /**
    * HttpURLConnection测试
    *
    * @author liucheng
    * @version 0.1
    * @since 0.1 2018-01-08 上午9:18
    **/
    public class HttpURLConnectionTest {
      public static void main(String[] args) throws Exception {
          URL url = new URL("http://IP:8080/info/runningStatus");
          HttpURLConnection conn = (HttpURLConnection) url.openConnection();

          // 设置提交模式，访问认证
          conn.setRequestMethod("GET");
          conn.setRequestProperty("Authorization", "YY");

          // 设置提交格式
          conn.setRequestProperty("Content-Type", "application/json");
          conn.setConnectTimeout(15000);
          conn.setReadTimeout(15000);

          // 第一种获取返回值的方式
    //        byte[] bytes = new byte[1024];
    //        InputStream inStream = conn.getInputStream();
    //        inStream.read(bytes, 0, inStream.available());
    //        System.out.println(new String(bytes, "utf-8"));

          // 第二种获取返回值的方式
          BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
          StringBuffer stringBuffer = new StringBuffer();
          String line;
          while ((line = br.readLine()) != null) {
              stringBuffer.append(line);
              stringBuffer.append("\r");
          }
          br.close();
          System.out.println(stringBuffer);
      }
    }
    ```
2. 使用apache提供的HttpClient访问  
    ```java
    package com.yihengliu.http;

    import org.apache.http.Header;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.NameValuePair;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.entity.UrlEncodedFormEntity;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.message.BasicNameValuePair;
    import org.apache.http.util.EntityUtils;

    import java.util.ArrayList;
    import java.util.List;

    /**
    * httpclient测试
    *
    * @author liucheng
    * @version 0.1
    * @since 0.1 2018-01-06 下午1:37
    **/
    public class HttpClientTest {
      /**
       * HttpClient 客户端访问
       */
      private static void httpClientVisit() {
          String clientResponse = "";
          String url = "http://IP:8080/info/runningStatus";
          try {
              HttpClient client = new DefaultHttpClient();
              HttpPost request = new HttpPost(url);

              StringEntity entity = new StringEntity("", "UTF-8");
              entity.setContentType("text/xml");
              request.setEntity(entity);

              HttpResponse response = client.execute(request);
              org.apache.http.HttpEntity httpEntity = response.getEntity();
              if (httpEntity != null) {
                  clientResponse = EntityUtils.toString(httpEntity, "UTF-8");
              }

              if (clientResponse == null || "".equals(clientResponse)) {
                  System.err.println("clientResponse is null or empty");
              }

              System.out.println(clientResponse);

          } catch (Exception e) {
              e.printStackTrace();
          }
      }

      /**
       * post 方式提交表单
       */
      public static void postForm() {
         String url ="http://IP:8080/userLoginController/userLogin";

          HttpClient client = new DefaultHttpClient();
          HttpPost httpPost = new HttpPost(url);

          List<NameValuePair> formParams = new ArrayList<NameValuePair>();
          formParams.add(new BasicNameValuePair("userAccount", "admin"));
          formParams.add(new BasicNameValuePair("userPassword", "123456"));
          UrlEncodedFormEntity uefEntity;
          try {
              uefEntity = new UrlEncodedFormEntity(formParams, "UTF-8");
              httpPost.setEntity(uefEntity);
              System.out.printf("executing request " + httpPost.getURI());
              HttpResponse response = client.execute(httpPost);
              Header[] headers = response.getAllHeaders();
              for (int i = 0; i < headers.length; i++) {
                  System.out.println(headers[i].getName());
              }

              try {
                  HttpEntity entity = response.getEntity();
                  if (entity != null) {
                      System.out.printf("Response content: " + EntityUtils.toString(entity, "UTF-8"));
                  }
              } catch (Exception e) {
                  e.printStackTrace();
              }
          } catch (Exception e) {
              e.printStackTrace();
          }
      }

      /**
       * get 请求
       */
      public static void getRequest() {
          try {
              HttpClient client = new DefaultHttpClient();

              HttpGet httpGet = new HttpGet("http://www.baidu.com");
              System.out.println("executing request " + httpGet.getURI());
              HttpResponse response = client.execute(httpGet);

              try {
                  HttpEntity entity = response.getEntity();
                  System.out.println(response.getStatusLine());
                  if (entity != null) {
                      System.out.println("Response content length: " + entity.getContentLength());
                      System.out.println("Response content: " + EntityUtils.toString(entity, "UTF-8"));
                  }
              } catch (Exception e) {

              }
          } catch (Exception e) {

          }
      }

      public static void main(String[] args) {
    //        httpClientVisit();
    //        postForm();
            getRequest();
      }
    }
    ```
3. 使用sprint-web提供的RestTemplate访问

    ```java
    package com.yihengliu.http;

    import org.springframework.http.HttpEntity;
    import org.springframework.http.HttpHeaders;
    import org.springframework.http.client.SimpleClientHttpRequestFactory;
    import org.springframework.web.client.RestTemplate;

    /**
    * RestTemplate 访问测试
    *
    * @author liucheng
    * @version 0.1
    * @since 0.1 2018-01-06 下午2:27
    **/
    public class RestTemplateTest {
      private static void restTemplateVisit() {
          String returnXml = "";

          try {
              SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
              requestFactory.setConnectTimeout(1000);
              requestFactory.setReadTimeout(1000);

              RestTemplate restTemplate = new RestTemplate(requestFactory);

              HttpHeaders requestHeaders = new HttpHeaders();
              requestHeaders.set("Accept-Charset", "utf-8");
              requestHeaders.set("Content-type", "text/xml; charset=utf-8");

              HttpEntity<String> entity = new HttpEntity<>("", requestHeaders);
              String url = "http://IP:8080/info/runningStatus";
              returnXml = restTemplate.postForObject(url, entity, String.class);

              System.out.println(returnXml);

          } catch (Exception e) {
             e.printStackTrace();
          }
      }

      public static void main(String[] args) {
         restTemplateVisit();
      }
    }
    ```

# 三、JAVA提供服务
主要需要根据REST的原则设计出符合要求的“RESTful API”。
可参考文章: [Principles of good RESTful API Design](https://codeplanet.io/principles-good-restful-api-design/)

早期的MVC设计并不能提供RESTful的服务，而只是支持GET和POST服务请求。
