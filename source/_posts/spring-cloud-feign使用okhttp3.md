---
title: spring cloud feign使用okhttp3
date: 2019-01-14 00:13:04
tags:
  - spring-cloud
---

spring cloud feign使用okhttp3<!-- more -->
# 指南

maven

```
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-okhttp</artifactId>
</dependency>
```

配置文件

```
feign.httpclient.enabled=false
feign.okhttp.enabled=true
```

配置

```java
@Configuration
@ConditionalOnClass(Feign.class)
@AutoConfigureBefore(FeignAutoConfiguration.class)
public class FeignOkHttpConfig {

    @Autowired
    OkHttpLoggingInterceptor okHttpLoggingInterceptor;

    @Bean
    public okhttp3.OkHttpClient okHttpClient(){
        return new okhttp3.OkHttpClient.Builder()
            .readTimeout(60, TimeUnit.SECONDS) 
            .connectTimeout(60, TimeUnit.SECONDS) 
            .writeTimeout(120, TimeUnit.SECONDS) 
            .connectionPool(new ConnectionPool())
            // .addInterceptor();
            .build();
    }
}
```

# 实践

不需要额外编写FeignOkHttpConfig，feign本身已经存在FeignOkHttpAutoConfiguration了，不需要额外配置。