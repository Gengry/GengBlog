---
title: Mybatis返回Map带null值
date: 2018-01-31 09:58:53
tags:
categories:
- Mybatis
---

# 前言

为客户端提供api接口，返回json数据，规定好了返回的字段，但是当使用Mybatis时如果使用map作为返回值，如果数据库中的值为null，则不会在map中生成对应的key。
有的时候一些必要的字段，想要添加上这些key需要使用实体bean作为返回值，但是很多时候，表中的字段很多，很多业务务求只需要部分字段，用表生成bean不适用，单独定义一个bean又嫌麻烦，用map需要在查询之后再补key，很难受。
今天看mybatis官方文档，突然看到一个设置，用来解决这个问题。  
没事多看看官方文档，会有意想不到的收获。
<!--more-->

# 配置

[Mybatis官方文档](http://www.mybatis.org/mybatis-3/configuration.html#)
![Mybatis](https://raw.githubusercontent.com/Gengry/blogImage/master/20180131/QQ%E6%88%AA%E5%9B%BE20180131101846.jpg)

Mybatis官方文档的目录结构很清楚，我们本次需要关注的是 Configuration XML中的setting

> callSettersOnNulls  
> Specifies if setters or map's put method will be called when a retrieved value is null. It is useful when you rely on Map.keySet() or null value initialization. Note primitives such as (int,boolean,etc.) will not be set to null.  
> true | false  
> 默认 false  

## XML配置

```xml
<!-- mybatis configuration -->
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" 
 "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="callSettersOnNulls" value="true"/>  <!-- 配置属性值为true-->
    </settings>
<plugins>
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <property name="dialect" value="mysql" />
            <property name="rowBoundsWithCount" value="true"/>
            <property name="pageSizeZero" value="true" />
            <property name="reasonable" value="false" />
        </plugin>
</plugins>
</configuration>  
```

```XML
<!--spring configuration xml-->
	<bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation" value="classpath:spring-mybatis.xml" />
		<property name="mapperLocations" value="classpath*:com/***/***/mapping/*/*.xml" />
		<property name="typeAliasesPackage" value="com.***.bean" />
	</bean>
```

## java配置
mybatis配置文件中的set属性其实对应的是org.apache.ibatis.session.Configuration中的变量。
例如：
```java
  protected boolean safeRowBoundsEnabled;
  protected boolean safeResultHandlerEnabled = true;
  protected boolean mapUnderscoreToCamelCase;
  protected boolean aggressiveLazyLoading;
  protected boolean multipleResultSetsEnabled = true;
  protected boolean useGeneratedKeys;
  protected boolean useColumnLabel = true;
  protected boolean cacheEnabled = true;
  protected boolean callSettersOnNulls;
  protected boolean useActualParamName = true;
  protected boolean returnInstanceForEmptyRow;
```

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory =
  new JdbcTransactionFactory();
Environment environment =
  new Environment("development", transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
configuration.setCallSettersOnNulls = true;
SqlSessionFactory sqlSessionFactory =
  new SqlSessionFactoryBuilder().build(configuration);
```

## 业务代码

```java
    @Auth
    @RequestMapping(value = "/sdfs",method= RequestMethod.POST)
    @ResponseBody
    public Object sdfs (){
        List<Map<String,Object>> dd = awardBaseMapper.getUserSignStatuss();
        System.out.println(dd);
        return dd;
    }
```
```java
public interface AwardBaseMapper {
	@Select("select * from user_sign")
    List<Map<String,Object>> getUserSignStatuss();
}
```

```json
[
	{
		"sign_year_week":4,
		"sign_year_day":23,
		"sign_year_month":0,
		"user_id":"cfeb266707c94b8a92d00c8c05d8b1f7",
		"sign_week_day":3,
		"award_ticket_id":null,
		"id":7,
		"sign_award":"未获取到奖励。",
		"sign_type_index":1,
		"sign_year":2018,
		"award_type":2,
		"sign_time":"2018-01-23 16:56:19"
	},
	{
		"sign_year_week":5,
		"sign_year_day":30,
		"sign_year_month":0,
		"user_id":"1e11281446b84655813ad08fd9da32a1",
		"sign_week_day":3,
		"award_ticket_id":-1,
		"id":8,
		"sign_award":null,
		"sign_type_index":1,
		"sign_year":2018,
		"award_type":2,
		"sign_time":"2018-01-30 09:25:43"
	}
]
```

上面可见，返回的json串中已经携带了null值的key。不同版本中的属性差异还是挺大的，官网现在是3.4.6，我用的3.2.6中很多属性就没有，如果需要使用新特性，需要升级mybatis版本。

# 后记

* 最后在提醒下，对于map中null值引发的空指针问题，可以很好的用 apache commons包中MapUtils解决，可以减少很多不必要的bug。
* 给客户端返回的json数据中，数组，集合，字典，对象不要有null值，如果是null需要put一个空HashMap或ArrayList，否则客户端反序列化会异常。

<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>