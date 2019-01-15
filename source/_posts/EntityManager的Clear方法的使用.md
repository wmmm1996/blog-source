---
title: EntityManager的Clear方法的使用
date: 2019-01-11 22:37:28
tags:
  - spring-jpa
---

在日常开发中，如果使用hibernate的话，常常会被hibernate的事务搞得焦头烂额。今天解决了之前项目中一直存在的问题，记录一下。<!-- more -->

# 问题描述

有一张表TemplateCopy,如下

```java
public class TemplateCopy {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    private String name;

    private String description;

    @OneToMany(mappedBy = "template")
    private Set<SubDomainWeightsCopy> subDomainWeights;

    @OneToMany(mappedBy = "template")
    private Set<QuestionWeightsCopy> questionWeights;

}
```

关联了两张表:
```java
public class SubDomainWeightsCopy {
    @JsonIgnore
    @Id
    @ManyToOne
    @JoinColumn(name = "template_id")
    private TemplateCopy template;

    @Id
    @ManyToOne
    @JoinColumn(name = "sub_domain_id")
    private SubDomainCopy subDomain;

    private BigDecimal weights; //权重

    private BigDecimal score;

    @Data
    public static class RelationId implements Serializable {
        private Integer template;
        private Integer subDomain;
    }

}
```

```java
public class QuestionWeightsCopy implements IWeightsValue {

    @JsonIgnore
    @Id
    @ManyToOne
    @JoinColumn(name = "template_id")
    private TemplateCopy template;

    @Id
    @ManyToOne
    @JoinColumn(name = "question_id")
    private QuestionCopy question;

    private BigDecimal weights;

    private BigDecimal score;

    @Data
    public static class RelationId implements Serializable {
        private Integer template;
        private Integer question;
    }
}
```

简单的看一下，TemplateCopy中有一堆SubDomainWeightsCopy，和一堆QuestionWeightsCopy，我们在保存TemplateCopy的时候，通常按照如下来保存

```
1. templateCopy = save(TemplateCopy)
2. QuestionWeightsCopy.setTemplateCopy(templateCopy)
3. save(QuestionWeightsCopy)
4. SubDomainWeightsCopy.setTemplateCopy(templateCopy)
5. save(SubDomainWeightsCopy)
```

到这就好了，数据库已经保存了关联关系。但是，这时候如果返回save好的templateCopy，subDomainWeights和questionWeights将会是null。

# 问题解决

## 使用EntityManager的clear方法

1. 保存完毕后，执行entityManager.clear();
2. 然后再次查询该对象，即可完整返回该对象。

### EntityManager clear的作用？

EntityManager clear方法会清空其关联的缓存，从而强制在事务中稍后执行新的数据库查询。

### 什么时候使用EntityManager clear

1. 在进行批处理时，为了避免巨大的缓存占用内存并因长时间的脏检查而增加刷新的时间
2. 在进行DML或SQL查询时，它将完全绕过实体管理器缓存。在这种情况下，由于缓存，将不会实际去数据库查，会直接将缓存返回。所以造成了数据库已经保存了，但是查出来还是未保存的状态。这时候需要清除缓存以避免这种不一致。(本案例就是这种情况的实际例子)

# 参考

[StackOverFlow大神回答](https://stackoverflow.com/questions/13886608/when-to-use-entitymanager-clear "StackOverFlow大神回答")


