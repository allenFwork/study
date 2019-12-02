# spring boot 配置分析

## 一、读取配置文件快速入门

### 1. springboot项目默认的配置文件 : application.properties

#### 1.1 src/main/resources下的application.properties文件

```properties
local.ip=192.168.1.100
```

#### 1.2 读取配置文件中的信息

```java
package com.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class App {
    public static void main( String[] args ) {
    	ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
    	
    	System.out.println(context.getEnvironment().getProperty("local.ip"));
    	
    	context.close();
    }
}
```

#### 1.3 结果

```java

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.4.0.RELEASE)

2019-10-29 21:21:07.775  INFO 6692 --- [           main] com.spring.App                           : Starting App on AllenWork-PC with PID 6692 (E:\eclipse\workspace-springboot\springboot02\target\classes started by AllenWork in E:\eclipse\workspace-springboot\springboot02)
2019-10-29 21:21:07.790  INFO 6692 --- [           main] com.spring.App                           : No active profile set, falling back to default profiles: default
2019-10-29 21:21:07.868  INFO 6692 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@78186a70: startup date [Tue Oct 29 21:21:07 CST 2019]; root of context hierarchy
2019-10-29 21:21:08.759  INFO 6692 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-10-29 21:21:08.774  INFO 6692 --- [           main] com.spring.App                           : Started App in 1.374 seconds (JVM running for 1.912)
192.168.1.100
2019-10-29 21:21:08.774  INFO 6692 --- [           main] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@78186a70: startup date [Tue Oct 29 21:21:07 CST 2019]; root of context hierarchy
2019-10-29 21:21:08.774  INFO 6692 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```



## 二、读取配置文件的两种方式

### 1.方式

2. **通过 org.springframework.core.env.Environment 对象**

3. **通过 @Value(“${}”)注解**

### 2.代码

**配置文件 application.properties** 

```properties
local.ip=192.168.1.100
local.port=8080

name=springboot
app.name=this is ${name}

```

**UserConfig.class**

```java
package com.spring;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;
/**
 * 两种获取配置信息的方法
 * 
 * 1.
 * 配置文件默认的文件名字为application.properties,
 * 默认的位置在classpath:/、classpath:/config、file:/、file:/config 目录下
 * 
 * 2.修改配置文件名字
 * 默认的配置文件名字可以使用 --spring.config.name 来指定,
 * 只需指定文件的名字,文件的扩展名可以省略
 * 
 * 3.修改配置文件路径
 * 默认的配置文件路径可以使用 --spring.config.location 来指定,
 * 配置文件需要指定全路径,包括文件名字,还可以指定多个,多个用逗号隔开,
 * 文件的指定方式有两种: 1)classpath: 2)file:
 * 
 * @author AllenWork
 *
 */
@Component
public class UserConfig {
	
	/*
	 * 读取配置文件(application.properties)信息(方法二):
	 * 使用 @Value()注解
	 * 如果直接写成  @Value("local.port") 就等于直接给localPort赋值为“local.port”的字符串
	 * 只有写成 @Value("${local.port}") 才是获取配置文件对应的 value 值
	 */
	@Value("${local.port}")
	private String localPort;
	@Value("${local.port}")
	private Integer localPort2;
	
	/*
	 * 读取配置文件(application.properties)信息(方法一):
	 * 使用 org.springframework.core.env.Environment 对象
	 * 通过此对象的 getPropery(String key)方法,读取value值
	 */
	@Autowired
	private Environment environment;
	
	public void show() {
		System.out.println("local.ip = " + environment.getProperty("local.ip"));
		// 获取value值将其转换为Integer类型
		System.out.println("local.port = " + environment.getProperty("local.port",Integer.class));
		
		System.out.println("local.port = " + localPort);
		System.out.println("local.port = " + localPort2);
		
		// 在配置文件中 引用 配置文件的信息
		System.out.println("name = " + environment.getProperty("name"));
		System.out.println("app.name = " + environment.getProperty("app.name"));
		
		// 指定多个配置文件
		System.out.println("local.tomcat.port = " + environment.getProperty("local.tomcat.port"));
	}

}
```

**启动类**

```java
package com.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class App {
    public static void main( String[] args ) {
    	ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
    	
    	System.out.println(context.getEnvironment().getProperty("local.ip"));
    	context.getBean(UserConfig.class).show();
        
    	context.close();
    }
}

```

### 3.运行结果

```java

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.4.0.RELEASE)

2019-10-30 22:35:17.302  INFO 8060 --- [           main] com.spring.App                           : Starting App on AllenWork-PC with PID 8060 (E:\eclipse\workspace-springboot\springboot02\target\classes started by AllenWork in E:\eclipse\workspace-springboot\springboot02)
2019-10-30 22:35:17.307  INFO 8060 --- [           main] com.spring.App                           : No active profile set, falling back to default profiles: default
2019-10-30 22:35:17.411  INFO 8060 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Wed Oct 30 22:35:17 CST 2019]; root of context hierarchy
2019-10-30 22:35:18.381  INFO 8060 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-10-30 22:35:18.400  INFO 8060 --- [           main] com.spring.App                           : Started App in 1.482 seconds (JVM running for 1.962)
192.168.1.100.3
local.ip = 192.168.1.100.3
local.port = 80803
local.port = 80803
local.port = 80803
name = springboot3
app.name = this is springboot3
local.tomcat.port = cat
2019-10-30 22:35:18.403  INFO 8060 --- [           main] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Wed Oct 30 22:35:17 CST 2019]; root of context hierarchy
2019-10-30 22:35:18.405  INFO 8060 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```



## 三、补充知识点

### 1. 在配置文件中可以<font color=#FF0000> 引用 </font>配置文件里的信息

```properties
local.ip=192.168.1.100
local.port=8080

name=springboot
app.name=this is ${name}
```

### 2. 设置默认值

```java
package com.spring;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

/**
 * 对配置文件中的内容设置默认值
 * @author AllenWork
 *
 */
@Component
public class DefaultConfig {
	
	// 设置默认值(方法一)
	/*
	 * 1.@Value("${配置项}") : 
	 *   默认配置文件中必须要有对应的配置项,配置项的value可以为空;
	 *   如果没有对应的配置项,就会出现异常:java.lang.IllegalArgumentException:
	 *    Could not resolve placeholder 'tomcat.port' in string value "${tomcat.port}"
	 * 
	 * 2.@Value("${配置项:默认值}") :
	 *   此时如果配置文件中没有对应的配置项,就不会出现异常,配置项的值就是给定的默认值
	 *   若果在配置文件中存在配置项,且那么配置项的值就以配置文件中的值为准
	 */
	@Value("${tomcat.port:9090}")
	private String tomcatPort;
	
	@Autowired
	private Environment environment;
	
	public void show() {
		
		// 设置默认值(方法二)
		System.out.println("tomcat.port2 = " + environment.getProperty("tomcat.port2", "2222"));
		
		System.out.println("tomcat.port = " + tomcatPort);
	}
	
}
```



## 四、配置文件（使用工具方式）

### 1. 特征

- 配置文件的默认名字是<font color=#FF0000>application.properties</font>

- 配置文件<font color=#FF0000>(application.properties)</font>默认是在<font color=#FF0000>classpath</font>的根目录下
- 配置文件<font color=#FF0000>(application.properties)</font>也可以默认在<font color=#FF0000>classpath</font>的config目录下
- 配置文件<font color=#FF0000>(application.properties)</font>默认是在<font color=#FF0000>file</font>的根目录下
- 配置文件<font color=#FF0000>(application.properties)</font>也可以默认在<font color=#FF0000>file</font>的config目录下

### 2. 修改配置文件的名字

#### 2.1 通过 <font color=#FF0000>--spring.config.name=</font> 来修改默认配置文件的名字，只需要指定文件的名字，文件扩展名可以省略

![](D:\Documents\spring boot\images\springboot修改配置文件名字.png)

#### 2.2 代码

配置文件 replaceApplication.properties

```properties
local.ip=192.168.1.100.3
local.port=80803

name=springboot3
app.name=this is ${name}

tomcat.port=3333
```

测试 app.class

```java
package com.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class App {
    public static void main( String[] args ) {
    	ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
    	
    	System.out.println(context.getEnvironment().getProperty("local.ip"));
    	context.getBean(UserConfig.class).show(); 	
    	context.getBean(DefaultConfig.class).show();
    	
    	context.close();
    }
}
```

#### 2.3 运行结果

```java

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.4.0.RELEASE)

2019-10-30 21:29:33.252  INFO 7620 --- [           main] com.spring.App                           : Starting App on AllenWork-PC with PID 7620 (E:\eclipse\workspace-springboot\springboot02\target\classes started by AllenWork in E:\eclipse\workspace-springboot\springboot02)
2019-10-30 21:29:33.268  INFO 7620 --- [           main] com.spring.App                           : No active profile set, falling back to default profiles: default
2019-10-30 21:29:33.346  INFO 7620 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Wed Oct 30 21:29:33 CST 2019]; root of context hierarchy
2019-10-30 21:29:34.299  INFO 7620 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-10-30 21:29:34.314  INFO 7620 --- [           main] com.spring.App                           : Started App in 1.452 seconds (JVM running for 1.989)
192.168.1.100.3
local.ip = 192.168.1.100.3
local.port = 80803
local.port = 80803
local.port = 80803
name = springboot3
app.name = this is springboot3
tomcat.port2 = 2222
tomcat.port = 3333
2019-10-30 21:29:34.314  INFO 7620 --- [           main] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Wed Oct 30 21:29:33 CST 2019]; root of context hierarchy
2019-10-30 21:29:34.314  INFO 7620 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```

### 3. 修改配置文件的位置

### 3.1 使用 <font color=#FF0000>--spring.config.location=</font> 修改配置文件目录

![](D:\Documents\spring boot\images\springboot修改配置文件目录位置.png)

#### 3.2 指定多个配置文件

使用<font color=#FF0000> --spring.config.location=</font>"第一个配置文件"<font color=#FF0000>,</font>"第二个配置文件" 中间用逗号隔开，来指定多个配置文件

![](D:\Documents\spring boot\images\springboot指定多个配置文件.png)



## 五、配置文件（使用编程的方式）

### 1. 通过配置类确定配置文件

```java
package com.spring;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.context.annotation.PropertySources;

/**
 * 加载外部的配置文件:(两种方法)
 * 1. @PropertySource 可以加在一个外部的配置文件(可以注解多次)
 * 2. @PropertySources 可以加在一个外部的配置文件(可以注解多次)
 * @author AllenWork
 *
 */
@Configuration
@PropertySource("classpath:jdbc.properties")
@PropertySource("file:E:/temp/jdbc.properties")
//@PropertySources({@PropertySource("classpath:jdbc.properties"),@PropertySource("file:E:/temp/jdbc.properties")})
public class FileConfig {

}
```

### 2. 配置文件

#### 2.1 classpath根目录下的 jdbc.properties

```properties
url=jdbc:mysql:///springboot
driverClassName=com.mysql.spring.boot
```

#### 2.2 在E盘temp文件下的jdbc.properties

```properties
username=shiwei
password=123456
```

### 3. 读取配置文件信息

```java
package com.spring;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class JdbcConfig {
	@Value("${url}")
	private String url;
	
	@Value("${driverClassName}")
	private String driverClassName;
	
	@Value("${username}")
	private String username;
	
	@Value("${password}")
	private String password;
	
	public void show() {
		System.out.println("===========JdbcConfig===========");
		System.out.println("url : " + url);
		System.out.println("driverClassName : " + driverClassName);
		System.out.println("username : " + username);
		System.out.println("password : " + password);
	}
}
```

### 4. 测试App2.class

```java
package com.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class App2 {
	
	public static void main(String[] args) {
		
		SpringApplication app = new SpringApplication(App2.class);
		ConfigurableApplicationContext context = app.run(args);
		
		context.getBean(JdbcConfig.class).show();
		
		context.close();
	}
}
```

### 5.运行结果

```java

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.4.0.RELEASE)

2019-10-30 23:55:42.023  INFO 8040 --- [           main] com.spring.App2                          : Starting App2 on AllenWork-PC with PID 8040 (E:\eclipse\workspace-springboot\springboot02\target\classes started by AllenWork in E:\eclipse\workspace-springboot\springboot02)
2019-10-30 23:55:42.026  INFO 8040 --- [           main] com.spring.App2                          : No active profile set, falling back to default profiles: default
2019-10-30 23:55:42.101  INFO 8040 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Wed Oct 30 23:55:42 CST 2019]; root of context hierarchy
2019-10-30 23:55:43.247  INFO 8040 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-10-30 23:55:43.264  INFO 8040 --- [           main] com.spring.App2                          : Started App2 in 1.659 seconds (JVM running for 2.115)
===========JdbcConfig===========
url : jdbc:mysql:///springboot
driverClassName : com.mysql.spring.boot
username : AllenWork
password : 123456
2019-10-30 23:55:43.265  INFO 8040 --- [           main] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Wed Oct 30 23:55:42 CST 2019]; root of context hierarchy
2019-10-30 23:55:43.266  INFO 8040 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```

## 六、补充

### 1. 配置文件

```properties
url=jdbc:mysql:///springboot
driverClassName=com.mysql.spring.boot

ds.url=jdbc:mysql://springboot
ds.driverClassName=com.mysql.spring.boot
ds.username=root
ds.password=123456
```

### 2. DataSourceProperties.class

```java
package com.spring;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
/**
 * 此类中要有get和set方法,否则读取配置文件信息后无法赋值
 */
@Component
@ConfigurationProperties(prefix="ds",locations="")
public class DataSourceProperties {

	private String url;
	
	private String driverClassName;
	
	private String username;
	
	private String password;
	
	public String getUrl() {
		return url;
	}

	public void setUrl(String url) {
		this.url = url;
	}

	public String getDriverClassName() {
		return driverClassName;
	}

	public void setDriverClassName(String driverClassName) {
		this.driverClassName = driverClassName;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public void show() {
		System.out.println("===========DataSourceProperties===========");
		System.out.println("ds.url : " + url);
		System.out.println("ds.driverClassName : " + driverClassName);
		System.out.println("ds.username : " + username);
		System.out.println("ds.password : " + password);
	}
}
```

### 3. 测试App2.class

```java
package com.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class App2 {
	
	public static void main(String[] args) {
		
		SpringApplication app = new SpringApplication(App2.class);
		ConfigurableApplicationContext context = app.run(args);
		
		context.getBean(JdbcConfig.class).show();
		context.getBean(DataSourceProperties.class).show();
		
		context.close();
	}
}
```

### 4.运行结果

```java

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.4.0.RELEASE)

2019-10-31 00:12:04.372  INFO 7524 --- [           main] com.spring.App2                          : Starting App2 on AllenWork-PC with PID 7524 (E:\eclipse\workspace-springboot\springboot02\target\classes started by AllenWork in E:\eclipse\workspace-springboot\springboot02)
2019-10-31 00:12:04.377  INFO 7524 --- [           main] com.spring.App2                          : No active profile set, falling back to default profiles: default
2019-10-31 00:12:04.492  INFO 7524 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Thu Oct 31 00:12:04 CST 2019]; root of context hierarchy
2019-10-31 00:12:05.612  INFO 7524 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-10-31 00:12:05.628  INFO 7524 --- [           main] com.spring.App2                          : Started App2 in 1.649 seconds (JVM running for 2.104)
===========JdbcConfig===========
url : jdbc:mysql:///springboot
driverClassName : com.mysql.spring.boot
username : AllenWork
password : 123456
===========DataSourceProperties===========
ds.url : jdbc:mysql://springboot
ds.driverClassName : com.mysql.spring.boot
ds.username : root
ds.password : 123456
2019-10-31 00:12:05.629  INFO 7524 --- [           main] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@4c762604: startup date [Thu Oct 31 00:12:04 CST 2019]; root of context hierarchy
2019-10-31 00:12:05.630  INFO 7524 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```

