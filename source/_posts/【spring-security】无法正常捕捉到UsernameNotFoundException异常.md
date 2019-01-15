---
title: 【spring-security】无法正常捕捉到UsernameNotFoundException异常
date: 2019-01-08 21:25:54
tags:
  - 实践经验
  - spring-security
---
# 前言

在Web应用开发中,安全一直是非常重要的一个方面。在庞大的spring生态圈中，权限校验框架也是非常完善的。其中，spring security是非常好用的。今天记录一下在开发中遇到的一个spring-security相关的问题。<!-- more -->

# 问题描述

使用spring security进行授权登录的时候，发现登录接口无法正常捕捉UsernameNotFoundException异常，捕捉到的一直是BadCredentialsException异常。我们的预期是：
- UsernameNotFoundException -> 用户名错误
- BadCredentialsException -> 密码错误

贴几个比较重要的代码：

## 1. 登录业务逻辑
```java
@Service
public class AuthServiceImpl implements AuthService {

    @Autowired
    private UserDetailsService userDetailsService;

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private JwtTokenUtil jwtTokenUtil;

    @Override
    public JwtAuthenticationResponse login(String username, String password) {
		//构造spring security需要的UsernamePasswordAuthenticationToken
        UsernamePasswordAuthenticationToken upToken = new UsernamePasswordAuthenticationToken(username, password);
		//调用authenticationManager.authenticate(upToken)方法验证
		//该方法将会执行UserDetailsService的loadUserByUsername验证用户名
		//以及PasswordEncoder的matches方法验证密码
        val authenticate = authenticationManager.authenticate(upToken);
        JwtUser userDetails = (JwtUser) authenticate.getPrincipal();
        val token = jwtTokenUtil.generateToken(userDetails);
        return new JwtAuthenticationResponse(token, userDetails.getId(), userDetails.getUsername());
    }
}
```

## 2. spring security 的UserDetailsService 实现类

```java
@Service
public class JwtUserDetailsServiceImpl implements UserDetailsService {
    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        AbstractUser abstractUser = userRepository.findByUsername(username);
		//如果通过用户名找不到用户，则抛出UsernameNotFoundException异常
        if (abstractUser == null) {
            throw new UsernameNotFoundException(String.format("No abstractUser found with username '%s'.", username));
        } else {
            return JwtUserFactory.create(abstractUser);
        }
    }
}
```

## 3. 登录接口

```java
try {
    final JwtAuthenticationResponse jsonResponse = authService.login(authenticationRequest.getUsername(), authenticationRequest.getPassword());
    //存入redis
    redisService.setToken(jsonResponse.getToken());
    return ok(jsonResponse);
} catch (BadCredentialsException e) {
	//捕捉到BadCredentialsException，密码不正确
    return forbidden(LOGIN_PASSWORD_ERROR, request);
} catch (UsernameNotFoundException e) {
	//捕捉到UsernameNotFoundException，用户名不正确
    return forbidden(LOGIN_USERNAME_ERROR, request);
}
```

### 在上述代码中，如果用户名错误，应该执行

```java
catch (UsernameNotFoundException e) {
    return forbidden(LOGIN_USERNAME_ERROR, request);
}
```

### 如果密码错误，应该执行

```java
catch (BadCredentialsException e) {
    return forbidden(LOGIN_PASSWORD_ERROR, request);
}
```

实际上，不管是抛出什么错，最后抓到的都是BadCredentialsException

# 问题定位

## debug大法

### 断点

![](https://i.imgur.com/eRNlVGd.png)

### 跟踪

经过步进法跟踪代码，发现问题所在，位于

```java
AbstractUserDetailsAuthenticationProvider
public Authentication authenticate(Authentication authentication)
```

![](https://i.imgur.com/7somN4i.png)

### 结论
1. loadUserByUsername方法确实抛出了UsernameNotFoundException
2. 走到AbstractUserDetailsAuthenticationProvider的authenticate方法的时候，如果hideUserNotFoundExceptions = true，直接就覆盖了UsernameNotFoundException异常并抛出BadCredentialsException异常，这也就解释了，为什么总是捕捉到BadCredentialsException异常

# 问题解决

既然已经找到了是因为`hideUserNotFoundExceptions = true`导致的问题，那把`hideUserNotFoundExceptions = false`不就完事了吗？

## 方案1

[参考stackoverflow大神回答](https://stackoverflow.com/questions/17439628/spring-security-custom-exception-message-from-userdetailsservice "参考stackoverflow大神回答")

### 修改WebSecurityConfig配置，添加AuthenticationProvider Bean

```java
@Bean
public AuthenticationProvider daoAuthenticationProvider() {
    DaoAuthenticationProvider daoAuthenticationProvider = new DaoAuthenticationProvider();
    daoAuthenticationProvider.setUserDetailsService(userDetailsService);
    daoAuthenticationProvider.setPasswordEncoder(passwordEncoder());
    daoAuthenticationProvider.setHideUserNotFoundExceptions(false);
    return daoAuthenticationProvider;
}
```

### 配置AuthenticationProvider Bean

```java
@Autowired
public void configureAuthentication(AuthenticationManagerBuilder authenticationManagerBuilder) throws Exception {
    authenticationManagerBuilder
            .authenticationProvider(daoAuthenticationProvider());
}
```

## 方案2

由于以前项目中也是一样的技术栈，而且代码也差不多，登录这段逻辑可以说是完全相同，不过之前就一直都没有这个问题。反复查看之后发现，在login的代码有些不同

在

```java
val authenticate = authenticationManager.authenticate(upToken);
```

前面还有一个

```java
//执行UserDetailsService的loadUserByUsername验证用户名
userDetailsService.loadUserByUsername(authenticationRequest.getUsername());
```

该方法会直接抛出UsernameNotFoundException，而不走spring security的AbstractUserDetailsAuthenticationProvider，也就不存在被转换为BadCredentialsException了。

但是这个方案有个缺点，

如果验证用户名通过以后，再次调用

```java
val authenticate = authenticationManager.authenticate(upToken);
```

还会再执行一遍

```java
userDetailsService.loadUserByUsername(authenticationRequest.getUsername());
```

该操作是冗余的，产生了不必要的数据库查询工作。

## 推荐使用方案1

