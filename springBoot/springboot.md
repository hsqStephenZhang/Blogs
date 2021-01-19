# springboot master



## 1.依赖管理



### 版本仲裁

**使用了一个parent父项目来做版本仲裁**



所有的springboot的项目都会有一个spring-boot-parent父项目，该父项目还有一个parent是spring-boot-dependencies，这个就是用来声明对应版本的springboot的依赖关系，这里

```xml
普通springboot项目的依赖

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>

---------------
parent 项目的依赖

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>
```



如果要自定义包的版本，可以在properties标签里面定义所需的版本号，这个会覆盖dependencies里面的依赖版本

(首先需要查看spring-boot-dependencies里面的版本号)

```xml
<properties>
    <mysql.version>5.6.0</mysql.version>
</properties>
```



也就是 **自定义版本>spring-boot-parent的版本>spring-boot-dependencies的版本**



### starter



官方/第三方提供的starter成为场景启动器，包含了一个类型的应用所依赖的所有的版本号



而这些starter都会依赖一个自动配置的依赖，就是spring-boot-starter依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.3.4.RELEASE</version>
  <scope>compile</scope>
</dependency>
```







**springboot导入的依赖，不需要修改，或者声明其版本即可，但是自己使用的依赖，仍然需要自己导包**



## 2.自动配置



```java
package com.stephen;

import net.minidev.json.JSONUtil;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

import java.util.Arrays;

@SpringBootApplication
public class MainApplication {
  public static void main(String[] args) {
    ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
    String[] names = run.getBeanDefinitionNames();
    Arrays.asList(names).forEach(System.out::println);
  }
}
 
其中的 @SpringBootApplication 相当于下面的三个注解

@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan  // 可以通过这个注解修改包扫描的路径，默认是在和MainApplication的同级的目录下的标注了Spring注解的组件才会被扫描
    
```





springBoot所有的自动配置功能都会在spring-boot-autoconfigure包里面







## 3.容器配置



### 1.组件添加



1. @Configuration
2. @Bean @Component @Controller @Service @Repository
3. @ComponentScan @Import
4. @Conditional



1.@Configuration



配置类里面使用@Bean注解给容器添加组件，默认为单例

配置类的配置方法，必须是public，而且返回的Bean的名字，默认为方法名，也可以使用Bean的注解的属性来指定Bean名称

配置类也是组件



@Configuration的很重要的属性，就是proxyMethods，如果是true的话，无论在外部对于这个组件注册的方法调用多少次，都是相同的结果，也就是Spring会去判断这个Bean是否已经注册了(代理的使用)，如果proxyMethods=false的话，在外部调用组件的注册方法，每一次都会创建新的对象

proxyMethods=true -------- FUll 模式

proxyMethods=false -------- Lite 模式  (跳过判断是否已经注册，所以更快)



2.@Component @Service @Repository 



这几个和Spring的注解是一样的意思





3.@Import



这个注解需要标注在Spring的组件中，才会导入声明的Bean

```java
@Import({User.class,Person.class})
@Configuration 
```





4.@Conditional



按需导入组件

比如 @ConditionalOnBean ，只有在容器中有某一个Bean的时候才会进行组件的注入 

@ConditionalOnProperty，只有在配置文件中有某个属性的时候才会进行组件注入





### 2.原生xml文件导入



@ImportResource 导入xml文件 



```java
@ImportResource("classpath:beans.xml")
@Component
public class User{
    ...
}

```



### 3.属性值绑定



1. @Component+ @ConfigurationProperties(prefix= "xxx")



2. @EnableConfigurationProperties(value={A.class,B.class}) +  @ConfigurationProperties(prefix= "xxx") 



前者是标注在配置类上，表明要为这个组件启用自动配置功能，后者是标注在类的定义上，表明使用容器创建组件的时候，可以使用什么前缀的属性





## 4.自动配置



```xml
0 = "org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration"
1 = "org.springframework.boot.autoconfigure.aop.AopAutoConfiguration"
2 = "org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration"
3 = "org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration"
4 = "org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration"
5 = "org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration"
6 = "org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration"
7 = "org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration"
8 = "org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration"
9 = "org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration"
10 = "org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration"
11 = "org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration"
12 = "org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration"
13 = "org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration"
14 = "org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration"
15 = "org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration"
16 = "org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration"
17 = "org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration"
18 = "org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration"
19 = "org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration"
20 = "org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration"
21 = "org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration"
22 = "org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRepositoriesAutoConfiguration"
23 = "org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRestClientAutoConfiguration"
24 = "org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration"
25 = "org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration"
26 = "org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration"
27 = "org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration"
28 = "org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration"
29 = "org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration"
30 = "org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration"
31 = "org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration"
32 = "org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration"
33 = "org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration"
34 = "org.springframework.boot.autoconfigure.data.r2dbc.R2dbcDataAutoConfiguration"
35 = "org.springframework.boot.autoconfigure.data.r2dbc.R2dbcRepositoriesAutoConfiguration"
36 = "org.springframework.boot.autoconfigure.data.r2dbc.R2dbcTransactionManagerAutoConfiguration"
37 = "org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration"
38 = "org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration"
39 = "org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration"
40 = "org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration"
41 = "org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration"
42 = "org.springframework.boot.autoconfigure.elasticsearch.ElasticsearchRestClientAutoConfiguration"
43 = "org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration"
44 = "org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration"
45 = "org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration"
46 = "org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration"
47 = "org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration"
48 = "org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration"
49 = "org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration"
50 = "org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration"
51 = "org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration"
52 = "org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration"
53 = "org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration"
54 = "org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration"
55 = "org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration"
56 = "org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration"
57 = "org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration"
58 = "org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration"
59 = "org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration"
60 = "org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration"
61 = "org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration"
62 = "org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration"
63 = "org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration"
64 = "org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration"
65 = "org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration"
66 = "org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration"
67 = "org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration"
68 = "org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration"
69 = "org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration"
70 = "org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration"
71 = "org.springframework.boot.autoconfigure.availability.ApplicationAvailabilityAutoConfiguration"
72 = "org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration"
73 = "org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration"
74 = "org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration"
75 = "org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration"
76 = "org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration"
77 = "org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration"
78 = "org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration"
79 = "org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration"
80 = "org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration"
81 = "org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration"
82 = "org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration"
83 = "org.springframework.boot.autoconfigure.r2dbc.R2dbcAutoConfiguration"
84 = "org.springframework.boot.autoconfigure.rsocket.RSocketMessagingAutoConfiguration"
85 = "org.springframework.boot.autoconfigure.rsocket.RSocketRequesterAutoConfiguration"
86 = "org.springframework.boot.autoconfigure.rsocket.RSocketServerAutoConfiguration"
87 = "org.springframework.boot.autoconfigure.rsocket.RSocketStrategiesAutoConfiguration"
88 = "org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration"
89 = "org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration"
90 = "org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration"
91 = "org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration"
92 = "org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration"
93 = "org.springframework.boot.autoconfigure.security.rsocket.RSocketSecurityAutoConfiguration"
94 = "org.springframework.boot.autoconfigure.security.saml2.Saml2RelyingPartyAutoConfiguration"
95 = "org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration"
96 = "org.springframework.boot.autoconfigure.session.SessionAutoConfiguration"
97 = "org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration"
98 = "org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration"
99 = "org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration"
100 = "org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration"
101 = "org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration"
102 = "org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration"
103 = "org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration"
104 = "org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration"
105 = "org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration"
106 = "org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration"
107 = "org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration"
108 = "org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration"
109 = "org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration"
110 = "org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration"
111 = "org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration"
112 = "org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration"
113 = "org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration"
114 = "org.springframework.boot.autoconfigure.web.reactive.function.client.ClientHttpConnectorAutoConfiguration"
115 = "org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration"
116 = "org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration"
117 = "org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration"
118 = "org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration"
119 = "org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration"
120 = "org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration"
121 = "org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration"
122 = "org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration"
123 = "org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration"
124 = "org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration"
125 = "org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration"
126 = "org.springframework.boot.autoconfigure.webservices.client.WebServiceTemplateAutoConfiguration"
```



场景依赖



（配置文件debug=true开启自动配置报告）

生效： positive match

不生效：negative match



导入依赖的方法

**1.参照文档修改配置文件**

**2.自定义Bean，注入到容器中**





## 5.自定义starter



需要有两个包，一个是custom-spring-boot-starter,用来导入依赖关系，还有一个是真正的服务类，是custom-spring-boot-autoconfiguration，xxx-autoconfiguration都是真正实现功能的包，而starter就是一个场景启动器

starter的类路径下需要有一个META-INF/spring.factories文件，用来声明所有的需要导入的包，比如自动配置类，监听器类。而autoconfiguration是通过@Configuration注解，结合各种条件装配来实现所需要功能的组件的自动装配





