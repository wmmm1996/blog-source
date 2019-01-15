---
title: spring-cloud微服务踩坑记录
date: 2019-01-14 00:08:58
tags:
  - spring-cloud
---
记录在开发微服务过程中遇到的问题以及解决方案。<!-- more -->

# No Feign Client for loadBalancing defined. Did you forget to include spring-cloud-starter-netflix-ribbon?

# @feignclient和@requestmapping混用的时候出错

重写springmvc扫描controller时不带有@feignclient才实例化

```java
@Configuration
@ConditionalOnClass({Feign.class})
public class FeignConfiguration {
    @Bean
    public WebMvcRegistrations feignWebRegistrations() {
        return new WebMvcRegistrationsAdapter() {
            @Override
            public RequestMappingHandlerMapping getRequestMappingHandlerMapping() {
                return new FeignRequestMappingHandlerMapping();
            }
        };
    }

    private static class FeignRequestMappingHandlerMapping extends RequestMappingHandlerMapping {
        @Override
        protected boolean isHandler(Class<?> beanType) {
            return super.isHandler(beanType) &&
                    !AnnotatedElementUtils.hasAnnotation(beanType, FeignClient.class);
        }
    }
}
```

# SpringCloud使用Zuul出现“Forwarding error”错误解决方法

在application.yml中添加ribbon的超时时间设置：

```yaml
ribbon:  
  ReadTimeout: 3000
  ConnectTimeout: 3000
zuul:
    host:
        connect-timeout-millis: 3000
        socket-timeout-millis: 3000
hystrix:
    command:
        default:
            execution:
                isolation:
                    thread:
                        timeout-in-milliseconds: 3000
```

