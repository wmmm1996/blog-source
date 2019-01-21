---
title: 后端跨域的N种方法
date: 2019-01-21 19:11:31
tags:
  - 跨域
categories: 
  - 解决方案
---

> 简单来说，CORS是一种访问机制，英文全称是Cross-Origin Resource Sharing，即我们常说的跨域资源共享，通过在服务器端设置响应头，把发起跨域的原始域名添加到Access-Control-Allow-Origin 即可。

<!-- more -->

# 返回新的CorsFilter(全局跨域)
> 在任意配置类，返回一个新的CorsFilter Bean，并添加映射路径和具体的CORS配置信息。

```java
@Configuration
public class GlobalCorsConfig {
    @Bean
    public CorsFilter corsFilter() {
        //1.添加CORS配置信息
        CorsConfiguration config = new CorsConfiguration();
        //放行哪些原始域
        config.addAllowedOrigin("*");
        //是否发送Cookie信息
        config.setAllowCredentials(true);
        //放行哪些原始域(请求方式)
        config.addAllowedMethod("*");
        //放行哪些原始域(头部信息)
        config.addAllowedHeader("*");
        //暴露哪些头部信息(因为跨域访问默认不能获取全部头部信息)
        config.addExposedHeader("*");

        //2.添加映射路径
        UrlBasedCorsConfigurationSource configSource = new UrlBasedCorsConfigurationSource();
        configSource.registerCorsConfiguration("/**", config);

        //3.返回新的CorsFilter.
        return new CorsFilter(configSource);
    }
}
```

# 使用注解(局部跨域)

## 在方法上(@RequestMapping)使用注解 `@CrossOrigin`

```java
@RequestMapping("/hello")
@ResponseBody
@CrossOrigin("http://localhost:8080") 
public String index(){
    return "Hello World";
}
```

## 在控制器(@Controller)上使用注解 `@CrossOrigin`

```java
@Controller
@CrossOrigin(origins = "http://domain.com", maxAge = 3600)
public class AccountController {

    @RequestMapping("/hello")
    @ResponseBody
    public String index(){
        return "Hello World";
    }
}
```

## 手工设置响应头(局部跨域)
> 使用HttpServletResponse对象添加响应头（Access-Control-Allow-Origin）来授权原始域，这里Origin的值也可以设置为”*” ，表示全部放行。

```java
@RequestMapping("/hello")
@ResponseBody
public String index(HttpServletResponse response){
    response.addHeader("Access-Control-Allow-Origin", "*");
    return "Hello World";
}
```

## Nginx配置跨域

```
upstream server {
        server 127.0.0.1:8091;
}
server {
        listen 80;
        server_name domain.com;

        location ^~/api {
                //添加跨域请求头
                proxy_set_header Access-Control-Allow-Origin *;
                proxy_set_header Access-Control-Allow-Methods *;
                proxy_set_header Access-Control-Allow-Headers *;

                if ($request_method = 'OPTIONS') {
                        return 204;
                }
                rewrite ^/api/(.+?)$ /$1 break;
                proxy_pass http://server;
        }
}
```

