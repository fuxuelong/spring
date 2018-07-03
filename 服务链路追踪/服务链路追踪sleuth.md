# 十一、Spring Cloud Sleuth
## （一） Spring Cloud Sleuth意义
 &emsp;一个微服务系统中会有众多微服务单元，业务也较为复杂，各个服务单元之间会相互消费，如果某一业务中的某一个服务节点出现问题，将会很难定位。所以在微服务系统中必须够进行链路追踪，对每个请求都能够清晰可见，记录展示请求都有哪些服务参与，这样可以在出现问题时能够快速定位。\
 &emsp;当前常见的链路追踪组件有Google的Dapper，Twitter的Zipkin，以及阿里的Eagleeye等。下面我们将讲解如何适用zipkin进行服务链路追踪。
## （二） 案例讲解
&emsp;再接下来的案例中使用Spring Boot的版本为2.0.3RELEASE，Spring Cloud的版本为Finchley.RELEASE,本案例采用Maven工程的多Module形式，工程中总共有三个module，eureka-server作为注册中心，trace-a、trace-b作为两个zipkin client，负责产生链路数据，并上传给链路追踪的服务中心。\
&emsp;需要特别注意的是在，Spring Boot 2.X以前，服务链路追踪的服务中心是需要自己创建的,但是在Spring Boot2.X版本之后，官方就不推荐自行定制服务端了，而是提供一个编译好的jar包供我们使用。

### 1. Eureka Server
    在编写服务注册中心eureka-server之前先建立一个Maven主工程sleuth-zipkin，在主Maven工程中引入其内部module所需的共同依赖：Spring Boot 2.0.3X和Spring Cloud Finchley.RELEASE,主Maven工程的POM文件如下：
```java
<?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchely.RELEASE</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```


