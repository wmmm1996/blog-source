---
title: 解决spring security自定义filter重复执行问题
date: 2019-01-14 19:29:51
tags:
  - spring-security
---

今天做项目的时候，发现每次拦截器日志都会打两遍，很纳闷，怀疑是Filter被执行了两遍。结果debug之后发现还真是！记录一下这个神奇的BUG！<!-- more -->

# 问题描述

项目中使用的是Spring-security作为权限框架，然后做了一个`JwtAuthenticationTokenFilter`作为拦截器拦截请求，校验Token，但是每次请求都会打两遍日志。下面是精简的源代码:

自定义的Filter类

```java
@Slf4j
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain chain) throws ServletException, IOException {
        //...省略
        //打出两遍日志的地方
        log.info("User:{} request path:{}, method:{}, param:{}", username, request.getServletPath(),
                request.getMethod(), request.getParameterMap() == null ? null : OBJECT_MAPPER.writeValueAsString(request.getParameterMap()));
        //...省略
        chain.doFilter(request, response);
    }
}
```

WebSecurityConfig配置类

```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    //...省略
    @Bean
    public JwtAuthenticationTokenFilter authenticationTokenFilterBean() throws Exception {
        return new JwtAuthenticationTokenFilter();
    }
    
        @Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {
        //...省略
        //把JwtAuthenticationTokenFilter加入到RememberMeAuthenticationFilter之前
        httpSecurity.addFilterBefore(authenticationTokenFilterBean(), RememberMeAuthenticationFilter.class);
    }
    //...省略
}
```

请求日志如下:

![](https://i.loli.net/2019/01/14/5c3c75bd6e40b.png)

# 问题解决

把自定义Filter`JwtAuthenticationTokenFilter`的`@Component`取消掉就可以了，不让它被Spring容器管理。

# 原因

在spring容器托管的OncePerRequestFilter的bean，都会自动加入到servlet的filter chain，而上面的定义，还额外把filter加入到了spring security的
ememberMeAuthenticationFilter之前。而spring security也是一系列的filter，在mvc的filter之前执行。因此在鉴权通过的情况下，就会先后各执行一次。

# 参考资料

[解决spring security自定义filter重复执行问题](https://segmentfault.com/a/1190000012173419)
