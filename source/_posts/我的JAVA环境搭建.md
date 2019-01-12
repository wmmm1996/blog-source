---
title: 我的JAVA环境搭建
date: 2019-01-13 01:15:13
tags: 
  - java
  - 工具
---

每次重装系统后的开发环境搭建，总是会花费大量的时间精力，软件下载安装啦，配置修改啦等等，索性把这些流程记录一下，毕竟时间就是金钱。<!-- more -->

# 软件列表

- JDK1.8
- IntelliJ IDEA
- Navicat数据库管理工具
- Postman
- Git
- SourceTree
- XShell5
- DevCenter(cassandra数据库管理工具)
- RedisDesktopManager(redis管理工具)

这些工具已经可以满足我的日常工作了，什么印象笔记，markdownPad2等等不包含于此。
这些都可以在我的博客下载到 -> [常用软件集合](https://gcdd1993.github.io/2019/01/08/%E5%B8%B8%E7%94%A8%E8%BD%AF%E4%BB%B6%E9%9B%86%E5%90%88/#more)

# 软件配置

## IntelliJ IDEA配置

### 主题

当然是选黑色主题了，毕竟提倡保护眼睛。

![](https://i.imgur.com/wyJ25mS.png)

### 字体

我一般使用默认字体+14号大小。

![](https://i.imgur.com/4gl570N.png)

### 设置编辑器的快捷键，也就是keymap

由于以前用惯了Eclipse,所以还得改为Eclipse的快捷键

![](https://i.imgur.com/GokNm9o.png)

### 代码自动提示不区分大小写

这个比较重要，毕竟谁也不可能无时无刻注意大小写，到时候不快捷提示就浪费太多时间了，也影响开发体验。

![](https://i.loli.net/2019/01/13/5c3a2d475bb3b.png)

### 自动导入包和导入包优化的设置

![](https://i.loli.net/2019/01/13/5c3a2d473806d.png)

### Java代码默认注释

一般创建一个java类的时候，需要指定创建者以及创建时间

![](https://i.loli.net/2019/01/13/5c3a2d473b94e.png)

注释代码可以自己决定，这里举个例子:

```java
/**
 * @author: gaochen
 * Date: ${DATE}
 */
 ```
 
 然后创建的类是这样的:
 
 ```java
 /**
 * @author: gaochen
 * Date: 2019/1/12
 */
 ```
 
 ### IntelliJ IDEA常用插件
 
 - .ignore:自动生成.ignore文件，并支持一键添加文件到.ignore列表
 - Grep Console:在控制台支持筛选，类似shell命令的cat 1.txt | grep '11'
 - Lombok plugin:使用lombok必须要装的一个插件
 - CodeGlance:代码编辑区迷你缩放图插件，非常好用
 - HighlightBracketPair:自动化高亮显示光标所在代码块对应的括号，可以定制颜色和形状，再也不怕看代码看到眼花了
 - Rainbow Brackets:彩色显示所有括号,有点类似上一个
 - Alibaba Java Coding Guidelines:阿里巴巴Java开发手册配套插件，一键扫描帮你优化代码。

# 参考资料

- [Intellij IDEA插件推荐](https://segmentfault.com/a/1190000013504412)