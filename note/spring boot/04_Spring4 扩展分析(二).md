# Spring4 扩展分析(二)

## 1. BeanFactoryPostProcessor

### 1.1 MyBeanFactoryPostProcessor

```java
package com.spring;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.stereotype.Component;

@Component
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

	public void postProcessBeanFactory(ConfigurableListableBeanFactory 
										beanFactory) throws BeansException {
		System.out.println("==="+beanFactory.getBeanDefinitionCount()+"个bean对象===");
		
	}
}
```

### 1.2 实体类bean

```java
package com.spring.entity;

public class User {
	public void init() {
		System.out.println("User 的 init 方法");
	}
}
```

### 1.3 配置类

```java
package com.spring.resources;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import com.spring.entity.User;

@Configuration
public class MyConfig {
	
	@Bean(initMethod="init")
	public User createUser1() {
		return new User();
	}
	
	@Bean
	public User createUser2() {
		return new User();
	}
}
```

### 1.4 MyBeanPostProcessor

```java
package com.spring;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.stereotype.Component;

/**
 * BeanPostProcessor在spring容器初始化之后触发,而且仅触发一次
 * 触发的时机比BeanPostProcessor早
 * @author AllenWork
 */
@Component
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
	public void postProcessBeanFactory(ConfigurableListableBeanFactory 
										beanFactory) throws BeansException {
		System.out.println("==="+beanFactory.getBeanDefinitionCount()+"个bean对象===");
	}
}
```

### 1.5 测试及结果

```java
package com.spring;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class App {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext("com.spring");
		context.close();
	}
}


十月 27, 2019 6:16:11 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 18:16:11 CST 2019]; root of context hierarchy
===13个bean对象===
===postProcessBeforeInitialization===class org.springframework.context.event.EventListenerMethodProcessor
===postProcessAfterInitialization===class org.springframework.context.event.EventListenerMethodProcessor
===postProcessBeforeInitialization===class org.springframework.context.event.DefaultEventListenerFactory
===postProcessAfterInitialization===class org.springframework.context.event.DefaultEventListenerFactory
===postProcessBeforeInitialization===class com.spring.resources.MyConfig$$EnhancerBySpringCGLIB$$5882fc06
===postProcessAfterInitialization===class com.spring.resources.MyConfig$$EnhancerBySpringCGLIB$$5882fc06
===postProcessBeforeInitialization===class com.spring.entity.User
User 的 init 方法
===postProcessAfterInitialization===class com.spring.entity.User
===postProcessBeforeInitialization===class com.spring.entity.User
===postProcessAfterInitialization===class com.spring.entity.User
十月 27, 2019 6:16:12 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext doClose
信息: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 18:16:11 CST 2019]; root of context hierarchy
```



## 2. BeanDefinitionRegistryPostProcessor

### 2.1 实体类 Person

```java
package com.spring.entity;

public class Person {
	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "Person [name=" + name + "]";
	}
}
```

### 2.2 MyBeanDefinitionRegistryPostProcessor

```java
package com.spring;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.BeanDefinitionRegistryPostProcessor;
import org.springframework.stereotype.Component;

import com.spring.entity.Person;

@Component
public class MyBeanDefinitionRegistryPostProcessor implements 
								BeanDefinitionRegistryPostProcessor{

	public void postProcessBeanFactory(ConfigurableListableBeanFactory 
											beanFactory) throws BeansException {
		
	}

	public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry
											registry) throws BeansException {
		for(int i=0;i<=10;i++) {
			// rootBeanDefinition()方法: 表示基于什么类型的bean对象
			BeanDefinitionBuilder bdb = 		 			                                   BeanDefinitionBuilder.rootBeanDefinition(Person.class);
			// 对bean对象的属性进行赋值
			bdb.addPropertyValue("name", "admin"+i);
			// 给此bean对象进行取名
			registry.registerBeanDefinition("person"+i, bdb.getBeanDefinition());
		}
	}
}
```

### 2.3 测试及结果

```java
package com.spring;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.spring.entity.Person;

public class App {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext("com.spring");
		
		// System.out::println 中间的“::”是 jdk8版本的写法
		context.getBeansOfType(Person.class).values().forEach(System.out::println);
		
		context.close();
	}
}


十月 27, 2019 8:00:02 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 20:00:02 CST 2019]; root of context hierarchy
===24个bean对象===
User 的 init 方法
Person [name=admin0]
Person [name=admin1]
Person [name=admin2]
Person [name=admin3]
Person [name=admin4]
Person [name=admin5]
Person [name=admin6]
Person [name=admin7]
Person [name=admin8]
Person [name=admin9]
Person [name=admin10]
十月 27, 2019 8:00:03 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext doClose
信息: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Sun Oct 27 20:00:02 CST 2019]; root of context hierarchy
```

