# Project

## 1 Spring Boot

### 1.1 Spring

Spring boot是为了进一步简化Spring而创造的, 但是Spring才是整个项目的基石.
Spring MVC则是为了处理浏览器的请求, 是最流行的技术. 同时服务端也要处理与数据库的交互, MyBatis是一般使用的框架. Spring, Spring MVC, MyBatis合称**SSM**  
Redis是数据库, Kafka是现在最流行的消息队列的管理框架, ElasticSearch是当下最流行的搜索引擎框架. 这些都能提高服务器的性能.  

Spring Security负责提高服务的安全性, Spring Actuator负责监控服务器的运行状态.

### 1.2 搭建开发环境

构建工具: Apache Maven
集成开发工具: IntelliJ IDEA
数据库: MySQL, Redis
应用服务器: Apache Tomcat
版本控制工具: Git

#### Apache Maven

- 可以帮助我们构建项目, 管理项目中的jar包
- 分为本地仓库和远程残酷, 远程仓库分为中央仓库, 是Maven组织自己的服务器, 还有镜像服务器, 以及私有服务器.

#### Spring Boot

Spring Boot 的核心功能是提供依赖(避免手动输入), 自动配置, 端点监控(对服务的监控).

#### Mybatis

常用网站[mybatis-3](http://www.mybatis.org/mybatis-3)和[mybatis-spring](http://www.mybatis.org/spring)
MyBatis是管理和数据库链接的框架.

##### 核心组件

- SqlSession
- SqlSessionFactory: 用于创建SqlSession的工厂类.
- SqlSeesion: MyBatis的核心嘴贱, 用于向数据库执行SQL
- 主配置文件: XML配置文件, 可以对MyBatis的底层行为做出详细的配置.
- Mapper接口: DAO接口, 在Mybatis中习惯称为Mapper
- Mapper映射器: 用于编写SQL, 并将SQL和实体类映射的组件, 采用XML, 注解均可实现.

### 1.3 Spring 入门

Spring全家桶

- Spring Framework: 基石与核心
- Spring Boot: 开发项目
- Spring Cloud: 微服务
- Spring Cloud Data Flow: 终端数据集成

Nowcoder的项目只需要用到Spring Framework和Spring Boot, 其他两个因为项目还不够复杂, 所以不需要用到.

#### Spring IOC

- Inversion of Control
  - 控制反转, 采用面向对象编程的设计思想
- Dependency Injection
  - IoC思想的实现方式
- IoC Container
  - IoC容器, 实现依赖注入的关键, 本质上是一个工厂

> [工厂方法模式](https://refactoringguru.cn/design-patterns/factory-method)是一种创建型设计模式, 其在父类中提供一个创建对象的方法, 允许子类决定实例化对象的类型.

什么是配置类?

TODO: 什么是Spring容器? 如何理解bean?