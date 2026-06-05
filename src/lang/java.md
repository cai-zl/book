# Java

## 官方资源

- 官网: [oracle.com/java](https://www.oracle.com/java/)
- OpenJDK: [openjdk.org](https://openjdk.org)
- 官方教程: [dev.java/learn](https://dev.java/learn/)
- API 文档: [docs.oracle.com/en/java/javase/21/docs/api](https://docs.oracle.com/en/java/javase/21/docs/api/)
- Java Language Specification: [docs.oracle.com/javase/specs](https://docs.oracle.com/javase/specs/)

## 环境管理

```bash
# 推荐用 SDKMAN 管理 JDK 版本
curl -s "https://get.sdkman.io" | bash
sdk install java 21.0.2-tem
sdk default java 21.0.2-tem

# 查看已安装版本
sdk list java
sdk current java
```

## 构建工具

### Maven

```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart
mvn compile             # 编译
mvn test                # 测试
mvn package             # 打包
mvn clean install       # 清理并安装到本地仓库
mvn dependency:tree     # 查看依赖树
```

### Gradle

```bash
gradle init             # 初始化项目
gradle build            # 构建
gradle test             # 测试
gradle bootJar          # Spring Boot 打包
gradle dependencies     # 查看依赖
```

## 推荐学习资源

- [Baeldung](https://www.baeldung.com) — 高质量 Java 教程
- [Java Point](https://www.javatpoint.com/java-tutorial) — 入门教程
- [Effective Java (Joshua Bloch)](https://www.oreilly.com/library/view/effective-java/9780134686097/) — 进阶必读
- [Java Concurrency in Practice](https://jcip.net) — 并发编程

## 常用框架/库

| 类别 | 推荐 |
|------|------|
| Web 框架 | [Spring Boot](https://spring.io/projects/spring-boot)、[Quarkus](https://quarkus.io)、[Micronaut](https://micronaut.io) |
| ORM | [Spring Data JPA](https://spring.io/projects/spring-data-jpa)、[MyBatis](https://mybatis.org/mybatis-3/)、[JOOQ](https://www.jooq.org) |
| HTTP 客户端 | [OkHttp](https://square.github.io/okhttp/)、[Spring WebClient](https://docs.spring.io/spring-framework/reference/web/webflux-webclient.html) |
| JSON | [Jackson](https://github.com/FasterXML/jackson)、[Gson](https://github.com/google/gson) |
| 日志 | [SLF4J + Logback](https://www.slf4j.org)、[Log4j2](https://logging.apache.org/log4j/2.x/) |
| 测试 | [JUnit 5](https://junit.org/junit5/)、[Mockito](https://site.mockito.org)、[Testcontainers](https://testcontainers.com) |
| 工具库 | [Guava](https://github.com/google/guava)、[Apache Commons](https://commons.apache.org) |
