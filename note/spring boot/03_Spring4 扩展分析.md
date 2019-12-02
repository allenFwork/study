# Spring4 扩展分析（一）

## 1.将ApplicationContext对象注入到bean对象中

### 1.1 使用 spring的注解 @Autowired

```java
package com.spring.entity;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;
import javax.annotation.Resource;

/** 
 * spring容器中获取 ApplicationContext对象的第一种方法:
 * 通过 @Autowire 注入
 * @author AllenWork
 *
 */
@Component
public class User {
	
	@Autowired
	private ApplicationContext applicationContext;
	
	public void show() {
		System.out.println("User : " + applicationContext.getClass());
	}
}

```

### 1.2 使用 spring的接口 ApplicationContextAware

```java
package com.spring.entity;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

/**
 * spring容器中 第二种方法获取 ApplicationContext对象
 * 实现ApplicationContextAware接口,在重写其对应的方法,
 * 就可以在初始化时获取ApplicationContext对象
 * @author AllenWork
 *
 */
@Component
public class Book implements ApplicationContextAware{

	private ApplicationContext applicationContext;
	
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		this.applicationContext = applicationContext;
	}
	
	public void show() {
		System.out.println("Book : " + applicationContext.getClass());
	}
	
}
```

### 1.3 使用 spring 4.3 的新特性(注意点较多)

```java
package com.spring.entity;

import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

/**
 * spring容器中 第三种方法获取 ApplicationContext对象:
 * 利用spring 4.3 的新特性, 类中的构造函数如果有参数,
 * 那么spring容器在初始化时,就会将spring其中有的对象作为参数传进去
 * 
 * 注意:
 * 1. 构造函数只能有一个,如果有多个构造函数,就必须有一个无参的构造函数,
 *    此时，spring 会调用无参的构造函数进行初始化
 * 2. 构造函数的参数，必须都要在spring容器中存在
 * @author AllenWork
 *
 */
@Component
public class Bank {

	private ApplicationContext applicationContext;
	
	public Bank(ApplicationContext applicationContext) {
		this.applicationContext = applicationContext;
	}
	
	public void show() {
		System.out.println("Bank : " + applicationContext.getClass());
	}
    
}
```

### 1.4 测试及结果

```java
package com.spring;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.spring.entity.Bank;
import com.spring.entity.Book;
import com.spring.entity.User;

public class App {
	
    public static void main( String[] args ) {
    	
    	AnnotationConfigApplicationContext context = 
        		new AnnotationConfigApplicationContext("com.spring");
    	
    	System.out.println(context.getBeansOfType(User.class));
    	
    	context.getBean("user",User.class).show();
    	context.getBean("book",Book.class).show();
    	context.getBean(Bank.class).show();
    	
    	context.close();
    	
    }
}


十月 27, 2019 2:48:31 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 14:48:31 CST 2019]; root of context hierarchy
{user=com.spring.entity.User@f0f2775, myUser=com.spring.entity.User@5a4aa2f2}
User : class org.springframework.context.annotation.AnnotationConfigApplicationContext
Book : class org.springframework.context.annotation.AnnotationConfigApplicationContext
Bank : class org.springframework.context.annotation.AnnotationConfigApplicationContext
十月 27, 2019 2:48:31 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext doClose
信息: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 14:48:31 CST 2019]; root of context hierarchy

```



## 2.BeanPostProcessor接口

### 2.1 接口的功能

```java
package com.spring.entity;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.stereotype.Component;

/**
 * BeanPostProcessor 
 * 1. 会在每个bean初始化时调用一次,每次调用都会分别调用这两个方法
 * 2. 这两个方法不能返回为null,否则spring容器中就获取不到对象了
 */
@Component
public class EnableBeanPostPrecessor implements BeanPostProcessor{

	/**
	 * 在 bean对象完成依赖装配(属性设置)之后,才会触发执行
	 */
	public Object postProcessBeforeInitialization(Object bean, 
						String beanName) throws BeansException {
		System.out.println("=====postProcessBeforeInitialization====="+bean.getClass());
		return bean;
	}

	/**
	 * 在bean对象初始化方法(init()方法)执行之后触发执行的
	 */
	public Object postProcessAfterInitialization(Object bean, 
						String beanName) throws BeansException {
		System.out.println("=====postProcessAfterInitialization====="+bean.getClass());
		return bean;
	}

}
```

### 2.2 测试及结果

#### 2.2.1 测试的bean对象

```java
package com.spring.entity;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;
import javax.annotation.Resource;

/** 
 * spring容器中获取 ApplicationContext对象的第一种方法:
 * 通过 @Autowire 注入
 * @author AllenWork
 */
@Component
public class User {
	
	private ApplicationContext applicationContext;
	
	/**
	 * 初始化方法
	 */
	public void init() {
		System.out.println("User 的初始化方法 init 调用");
	}
	
	/**
	 * 通过 @Autowired 注释在set方法上来注入对象
	 * @param applicationContext
	 */
	@Autowired
	public void setApplicationContext(ApplicationContext applicationContext) {
		this.applicationContext = applicationContext;
		System.out.println("===========User的set方法调用注入对象============");
	}
	
	public void show() {
		System.out.println("User : " + applicationContext.getClass());
	}
}
```

#### 2.2.2 配置类

```java
package com.spring.resources;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import com.spring.entity.User;

@Configuration
public class MyConfig {

	@Bean(name="myUser",initMethod="init")
	public User createUser() {
		return new User();
	}
    
}
```

#### 2.2.3 启动测试

```java
package com.spring;

import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.spring.entity.Bank;
import com.spring.entity.Book;
import com.spring.entity.User;

public class App {
	
    public static void main( String[] args ) {
    	
    	AnnotationConfigApplicationContext context = 
        		new AnnotationConfigApplicationContext("com.spring");
    	
    	System.out.println(context.getBeansOfType(User.class));
    	
    	context.getBean("user",User.class).show();
    	context.getBean("book",Book.class).show();
    	context.getBean(Bank.class).show();
    	
    	context.close();
    	
    }
}


十月 27, 2019 4:29:55 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 16:29:55 CST 2019]; root of context hierarchy
=====postProcessBeforeInitialization=====class org.springframework.context.event.EventListenerMethodProcessor
=====postProcessAfterInitialization=====class org.springframework.context.event.EventListenerMethodProcessor
=====postProcessBeforeInitialization=====class org.springframework.context.event.DefaultEventListenerFactory
=====postProcessAfterInitialization=====class org.springframework.context.event.DefaultEventListenerFactory
=====postProcessBeforeInitialization=====class com.spring.entity.Bank
=====postProcessAfterInitialization=====class com.spring.entity.Bank
=====postProcessBeforeInitialization=====class com.spring.entity.Book
=====postProcessAfterInitialization=====class com.spring.entity.Book
===========User的set方法调用注入对象============
=====postProcessBeforeInitialization=====class com.spring.entity.User
=====postProcessAfterInitialization=====class com.spring.entity.User
=====postProcessBeforeInitialization=====class com.spring.resources.MyConfig$$EnhancerBySpringCGLIB$$1fffdfc5
=====postProcessAfterInitialization=====class com.spring.resources.MyConfig$$EnhancerBySpringCGLIB$$1fffdfc5
===========User的set方法调用注入对象============
=====postProcessBeforeInitialization=====class com.spring.entity.User
User 的初始化方法 init 调用
=====postProcessAfterInitialization=====class com.spring.entity.User
{user=com.spring.entity.User@351d0846, myUser=com.spring.entity.User@77e4c80f}
User : class org.springframework.context.annotation.AnnotationConfigApplicationContext
Book : class org.springframework.context.annotation.AnnotationConfigApplicationContext
Bank : class org.springframework.context.annotation.AnnotationConfigApplicationContext
十月 27, 2019 4:29:56 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext doClose
信息: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 16:29:55 CST 2019]; root of context hierarchy
```

### 2.3 作用实例(？搞不明白？)

#### 2.3.1 SpringContextAware

```java
package com.spring;

import org.springframework.context.ApplicationContext;

public interface SpringContextAware {
	
	public void setApplicationContext(ApplicationContext applicationContext);
	
}
```
#### 2.3.2 Dog
```java
package com.spring.entity;

import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

import com.spring.SpringContextAware;

@Component
public class Dog implements SpringContextAware{
	
	private ApplicationContext applicationContex;

	public void setApplicationContext(ApplicationContext applicationContext) {
		this.applicationContex = applicationContext;
	}
	
	public void show() {
		System.out.println("Dig : " + applicationContex.getClass());
	}

}
```
#### 2.3.3 SpringContextBeanPostPrecessor
```java
package com.spring.entity;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

import com.spring.SpringContextAware;

@Component
public class SpringContextBeanPostPrecessor implements BeanPostProcessor{
	
	@Autowired
	private ApplicationContext applicationContext;
	
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		if(bean instanceof SpringContextAware) {
			SpringContextAware sca = (SpringContextAware)bean;
			sca.setApplicationContext(applicationContext);
		}
		return bean;
	}

	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		
		return bean;
	}

}
```
#### 2.3.4
```java
package com.spring;

import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.spring.entity.Bank;
import com.spring.entity.Book;
import com.spring.entity.User;

public class App {
	
    public static void main( String[] args ) {
    	
    	AnnotationConfigApplicationContext context = 
        		new AnnotationConfigApplicationContext("com.spring");

    	context.close();
    	
    }
}

```

