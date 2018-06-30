## 博彩商城项目Spring Cloud微服务版

## 项目简介

| 微服务角色                     | 对应的技术选型                  |
| :----------------------------- | :------------------------------ |
| 注册中心(Register Server)      | Eureka                          |
| 服务提供者 (Service  Provider) | spring mvc、spring-data-jpa、h2 |
| 服务消费者(Service Consumer)   | Feign消费服务提供者的接口       |
| 网关路由(API Gateway)          | Zuul                            |



## 准备

### 环境准备:

| 工具    | 版本          |
| ------- | ------------- |
| JDK     | 1.8           |
| IDE     | IntelliJ IDEA |
| Gradle  | 4.8           |
| node.js | 8.11.1        |



### 主机规划:

| 项目名称          | 端口 | 描述         |
| ----------------- | ---- | ------------ |
| lottery-discovery | 8761 | 注册中心     |
| lottery-gateway   | 80   | API Gateway  |
| lottery-info      | 8081 | 彩票信息     |
| lottery-user      | 8082 | 用户管理     |
| lottery-order     | 8083 | 订单管理     |
| lottery-res       | 8084 | 静态资源服务 |



### 注册服务编写步骤:

- 1.添加依赖

```
compile('org.springframework.cloud:spring-cloud-starter-eureka')
```

- 2.启动类上添加注解

```java
//代表可被发现的客户端
@EnableDiscoveryClient
@SpringBootApplication
public class InfoApplication {

    public static void main(String[] args) {
        SpringApplication.run(InfoApplication.class, args);
    }
}
```

- 3.在bootstrap.yml中给服务命名

```yml
spring:
  application:
    name: info-service
```

- 4.设置端口和全文路径

```
server:
  port: 8081
  context-path: /info
```

- 5.客户端发现配置

```yml
#开发环境dev
eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
    
#生产环境prod
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:8761/eureka
```

- 6.不同环境使用不同的配置

  - application.yml

  ```
  spring:
    profiles:
      active: dev
  ```

  - application-dev.yml:	开发环境
  - application-prod.yml:  生产环境

- 7.配置开发环境中使用的H2数据库

```
spring:
  h2:
    console:
      enabled: true
      settings:
        web-allow-others: true
      path: /h2
  datasource:
    url: jdbc:h2:file:./info;DB_CLOSE_ON_EXIT=FALSE
    driverClassName: org.h2.Driver
    username: sa
    password: 123
    data: classpath:import.sql
  jpa:
      database-platform: org.hibernate.dialect.H2Dialect
      hibernate:
        ddl-auto: update
```

- 8.resources中添加sql脚本
  - import.sql

### 注册中心编写步骤:

- 1.添加依赖

```
compile('org.springframework.cloud:spring-cloud-starter-eureka-server')
```

- 2.添加注解

```
//声明此服务为注册中心
@EnableEurekaServer
@SpringBootApplication
public class MicroserviceDiscoveryApplication {

    public static void main(String[] args) {
        SpringApplication.run(MicroserviceDiscoveryApplication.class, args);
    }
}
```

- 3.服务命名bootstrap.yml

```java
spring:
  application:
    name: discovery-servcie
```

- 4.application.yml

```
server:
  port: 8761

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false

```



