spring 的官方网站: spring.io

## 1、springboot简单使用

### 1.1 创建maven项目

### 1.2 配置pom.xml文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.spring</groupId>
	<artifactId>springboot</artifactId>
	<version>1.0.0</version>
	<packaging>jar</packaging>

	<name>springboot</name>
	<url>http://maven.apache.org</url>

	<!-- 使用 springboot 必须先继承父的模板: spring-boot-starter-parent -->
	<parent>
		<groupId>spring.framework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.4.0.RELEASE</version>
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<!-- 版本为1.8 -->
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

	<dependencies>
        <!-- 添加依赖 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
	</dependencies>
</project>

```

或者

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.spring</groupId>
	<artifactId>springboot</artifactId>
	<version>1.0.0</version>
	<packaging>jar</packaging>

	<name>springboot</name>
	<url>http://maven.apache.org</url>

	<!-- 不继承父module时,可以使用dependencyManagement -->
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-dependencies</artifactId>
				<version>1.4.0.RELEASE</version>
				<!-- scope是import -->
				<scope>import</scope>
				<!-- type是pom -->
				<type>pom</type>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<!-- 版本为1.8 -->
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

	<dependencies>	
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
	</dependencies>
</project>

```

### 1.3 创建启动类并运行

```java
package com.spring.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class App {
	
	@Bean
	public Runnable createRunnable() {
		return () -> {System.out.println("spring boot is running");};
	}
	
    public static void main( String[] args ) {
    	ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
    	context.getBean(Runnable.class).run();
    	context.close();
    }
}
```

### 1.4 测试结果

```java

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.4.0.RELEASE)

2019-10-28 21:26:58.035  INFO 2060 --- [           main] com.spring.springboot.App                : Starting App on AllenWork-PC with PID 2060 (E:\eclipse\workspace-springboot\springboot\target\classes started by AllenWork in E:\eclipse\workspace-springboot\springboot)
2019-10-28 21:26:58.040  INFO 2060 --- [           main] com.spring.springboot.App                : No active profile set, falling back to default profiles: default
2019-10-28 21:26:58.139  INFO 2060 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@481a15ff: startup date [Mon Oct 28 21:26:58 CST 2019]; root of context hierarchy
2019-10-28 21:26:59.118  INFO 2060 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-10-28 21:26:59.137  INFO 2060 --- [           main] com.spring.springboot.App                : Started App in 1.527 seconds (JVM running for 2.073)
spring boot is running
2019-10-28 21:26:59.138  INFO 2060 --- [           main] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@481a15ff: startup date [Mon Oct 28 21:26:58 CST 2019]; root of context hierarchy
2019-10-28 21:26:59.139  INFO 2060 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```



## 2、环境搭建

### 2.1 第一种方式 : 继承父模板 parent

#### 2.1.1首先要继承一个模板

```xml
<parent>
    <groupId>org.springframework.boot</group>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.0.RELEASE</version
<parent>
```

​	maven通过此继承一个模板，其对应的是 <font color=#FF0000>spring-boot-starter-parent-1.4.0.RELEASE.pom </font>文件，是一个pom文件

#### 2.1.2 配置依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

此时就不需要再添加版本号了，因为父模板中已经有版本号了

### 2.2 第二种方式 : dependencyManagement

#### 2.2.1添加dependencyManagement

```xml
<!-- 不继承父module时,可以使用dependencyManagement -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>1.4.0.RELEASE</version>
            <!-- scope是import -->
            <scope>import</scope>
            <!-- type是pom -->
            <type>pom</type>
        </dependency>
    </dependencies>
</dependencyManagement>
```

#### 2.1.2 配置依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

此时也需要添加版本号了



## 3、注解 @SpringBootApplication

#### 3.1 @SpringBootApplication是由多个注解构成的

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class))
public @interface SpringBootApplication {
    ......
}
```

- @SpringBootConfiguration 继承于@Configuration，表示是配置类，可以在其中添加@Bean的注释
- @EnableAutoConfiguration 启动自动的配置
- @ComponentScan 表示自动扫描包，默认是 注解了SpringBootApplication的类 的包下

#### 3.2 实体bean对象

```java
package com.spring.springboot;

import org.springframework.stereotype.Component;

@Component
public class User {

}
```

#### 3.3 测试

```java
package com.spring.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;

import cn.spring.springboot.User2;

@SpringBootApplication
public class App {
	
	@Bean
	public Runnable createRunnable() {
		return () -> {System.out.println("spring boot is running");};
	}
	
    public static void main( String[] args ) {
        /*
         * SpringApplication.run(App.class, args)方法:
         * 第一个参数是配置类
         * 第二个参数是main方法的参数
         */
    	ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
    	context.getBean(Runnable.class).run();
    	System.out.println(context.getBean(User.class));
    	context.close();
    }
}
```

#### 3.4 结果

```java

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.4.0.RELEASE)

2019-10-28 23:12:44.574  INFO 2368 --- [           main] com.spring.springboot.App                : Starting App on AllenWork-PC with PID 2368 (E:\eclipse\workspace-springboot\springboot\target\classes started by AllenWork in E:\eclipse\workspace-springboot\springboot)
2019-10-28 23:12:44.600  INFO 2368 --- [           main] com.spring.springboot.App                : No active profile set, falling back to default profiles: default
2019-10-28 23:12:44.684  INFO 2368 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@481a15ff: startup date [Mon Oct 28 23:12:44 CST 2019]; root of context hierarchy
2019-10-28 23:12:45.670  INFO 2368 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-10-28 23:12:45.702  INFO 2368 --- [           main] com.spring.springboot.App                : Started App in 1.499 seconds (JVM running for 1.968)
spring boot is running
com.spring.springboot.User@7068e664
2019-10-28 23:12:45.703  INFO 2368 --- [           main] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@481a15ff: startup date [Mon Oct 28 23:12:44 CST 2019]; root of context hierarchy
2019-10-28 23:12:45.706  INFO 2368 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown

```

## 4、Springboot的启动方法

### 4.1 使用 SpringApplication

实例如上

### 4.2 使用 SpringApplication的构造方法

实例代码

```java
package com.spring.springboot;

import java.util.HashSet;
import java.util.Set;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;

import cn.spring.springboot.User2;

@SpringBootApplication
public class App {
	
	@Bean
	public Runnable createRunnable() {
		return () -> {System.out.println("spring boot is running");};
	}
	
    public static void main( String[] args ) {

    	SpringApplication app = new SpringApplication();
        
    	Set<Object> sets = new HashSet<>();
    	sets.add(App.class);
    	app.setSources(sets);
        
    	ConfigurableApplicationContext context = app.run(args);
    	
    	context.getBean(Runnable.class).run();
    	System.out.println(context.getBean(User.class));
        
    	context.close();
    }
}
```

### 4.3 使用 SpringApplication有参构造

```java
package com.spring.springboot;

import java.util.HashSet;
import java.util.Set;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;

import cn.spring.springboot.User2;

public class App2 {
	
    public static void main( String[] args ) {
    	
    	SpringApplication app = new SpringApplication(App.class);

    	ConfigurableApplicationContext context = app.run(args);
    	
    	context.getBean(Runnable.class).run();
    	System.out.println(context.getBean(User.class));
    	
    	context.close();
    }
}

```

