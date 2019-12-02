# Spring4 快速入门 （上）


## 一、bean创建
1. 使用配置文件(xml文件)的方式
2. 使用注解配置类(class文件)的方式



### 1.使用配置文件方式:



### 2.使用配置类方式:
#### 	1. 使用 @Configuration 注解类 MyConfig.class
#### 	2. 配置bean对象注入到spring容器中:
##### 	 2.1 直接配置
```java
@Bean(name = "myBean")
@Scope("prototype")
public MyBean createMyBean() {
	return new MyBean();
}
```
##### 	2.2 使用定义工厂配置
```java
@Bean
public RunnableFactoryBean createRunnableFactoryBean() {
    return new RunnableFactoryBean();
}
```



#### 项目代码:

1. ##### 需要创建、管理的bean的class
```java
    package com.spring;
    /**
     * @description 要创建的bean
     */
    public class MyBean {

    }
```
```java
    package com.spring;
	/**
     * @description 要创建的bean
     */
    public class Jeep {

    }

```
```java
    package com.spring;
	/**
     * @description 要创建的bean
     */
    public class Car {

    }

```

2. ##### 定义工厂:

  1)  RunnableFactoryBean类
  ```java
  package com.spring;
  
  import org.springframework.beans.factory.FactoryBean;
  
  /**
   * @description 特殊的类,专门用来创建其余的bean对象的factroyBean
   */
  public class RunnableFactoryBean implements FactoryBean<Runnable> {
  
  	// 获取factoryBean创建的实例对象
  	public Runnable getObject() throws Exception {
  		return () -> {};
  	}
  
  	// 获取创建对象的类型
  	public Class<?> getObjectType() {
  		return Runnable.class;
  	}
  
  	// 判断是否为单例
  	public boolean isSingleton() {
  		return true;
  	}
  }
  ```

  2) RunnableFactoryBean2类
  ```java
  package com.spring;
  
  import org.springframework.beans.factory.FactoryBean;
  
  public class RunnableFactoryBean2 implements FactoryBean<Jeep> {
  
  	// 获取factoryBean创建的实例对象
  	public Jeep getObject() throws Exception {
  		return new Jeep();
  	}
  
  	// 获取创建对象的类型
  	public Class<?> getObjectType() {
  		return Jeep.class;
  	}
  
  	// 判断是否为单例
  	public boolean isSingleton() {
  		return true;
  	}
  }
  ```

  3)  CarFactory类
  ```java
  package com.spring;
  /**
   * @description 第二种定义工厂
   * @author AllenWork
   *
   */
  public class CarFactory {
  	
  	public Car create() {
  		return new Car();
  	}
  }
  ```

3. ##### 写配置类 MyConfig 类
 ```java
    package com.spring;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.Scope;

    /**
     * 配置类
     * @author AllenWork
     */
    @Configuration
    public class MyConfig {
        /*
         * 配置一个bean: 创建一个bean对象返回 
         * 
         * 此bean对象的默认名字就是方法的名字“createMyBean”,
         * 如果想要指定名字,使用 @Bean(name = "")
         * 
         * 默认情况下,bean是单例的,通过 @Scope("prototype")来修改设置设置
         */
        @Bean(name = "myBean")
        @Scope("prototype")
        public MyBean createMyBean() {
            return new MyBean();
        }

        // 第一种定义工厂创建的bean
        @Bean
        public RunnableFactoryBean createRunnableFactoryBean() {
            return new RunnableFactoryBean();
        }

        @Bean
        public RunnableFactoryBean2 createRunnableFactoryBean2() {
            return new RunnableFactoryBean2();
        }

        // 第二种定义工厂创建的bean
        @Bean
        public CarFactory createJeepFactory() {
            return new CarFactory();
        }

        /*
         * 默认从spring容器中获取参数 : CarFactory对象
         */
        @Bean
        public Car createCar(CarFactory factory) {
            return factory.create();
        }
    }
 ```



## 二、对bean对象进行初始化和销毁

### 1. 通过实现spring框架的接口
 ```java
    package com.spring;

    import org.springframework.beans.factory.DisposableBean;
    import org.springframework.beans.factory.InitializingBean;

    /**
     * @description 实现spring提供的接口,来实现对bean对象的初始化和销毁操作(方法一)
     * @author AllenWork
     */
    public class Cat implements InitializingBean,DisposableBean{

        @Override
        /**
         * 需要先实现InitializingBean接口,重写此方法
         * 在创建bean对象时，调用的方法进行初始化
         */
        public void afterPropertiesSet() throws Exception {
            System.out.println("========afterPropertiesSet=========");
        }

        @Override
        /**
         * 需要先实现DisposableBean接口,重写此方法
         * 此方式销毁bean对象时,调用的方法
         */
        public void destroy() throws Exception {
            System.out.println("==============destroy===============");
        }

    }
 ```
### 2. 通过自定义方法

 ```java
    package com.spring;

    /**
     * @description 自己写方法来实现对bean对象的初始化和销毁操作(方法二)
     * @author AllenWork
     */
    public class Dog {

        public void init() {
            System.out.println("=========init==========");
        }

        public void destroy() {
            System.out.println("========destroy========");
        }
    }
 ```
### 3. 通过java自带的注解

 ```java
    package com.spring;

    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;

    /**
     * @description 通过javax自带的注解来实现对bean对象的初始化和销毁操作(方法三)
     * @author AllenWork
     *
     */
    public class Animal {

        @PostConstruct
        public void init() {
            System.out.println("==========initial===========");
        }

        @PreDestroy
        public void close() {
            System.out.println("===========close============");
        }
    }
 ```

### 4. 配置类:

 ```java
     package com.spring;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    /**
     * @description spring容器创建bean对象进行初始化的配置类
     * @author AllenWork
     */
    @Configuration
    public class MyConfig2 {

        // 方法一
        @Bean
        public Cat createCat() {
            return new Cat();
        }

        // 方法二
        @Bean(name="dog",initMethod="init",destroyMethod="destroy")
        public Dog createDog() {
            return new Dog();
        }

        // 方法三
        @Bean
        public Animal createAnimal() {
            return new Animal();
        }
    }

 ```
### 5. 启动类:

 ```java
    package com.spring;

    import org.springframework.context.annotation.AnnotationConfigApplicationContext;

    public class App2 {
        public static void main(String[] args) {
            AnnotationConfigApplicationContext context = 
                    new AnnotationConfigApplicationContext(MyConfig2.class);
            // 初始化和销毁bean对象的方法一
            System.out.println(context.getBean(Cat.class));

            // 初始化和销毁bean对象的方法二
            System.out.println(context.getBean("dog"));

            // 初始化和销毁bean对象的方法三
            System.out.println(context.getBean(Animal.class));
            context.close();
        }
    }

 ```

### 6. 结果:

 ```java
     十月 14, 2019 11:17:00 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext prepareRefresh
    信息: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Mon Oct 14 23:17:00 CST 2019]; root of context hierarchy
    ========afterPropertiesSet=========
    =========init==========
    ==========initial===========
    com.spring.Cat@d6da883
    com.spring.Dog@45afc369
    com.spring.Animal@799d4f69
    十月 14, 2019 11:17:01 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext doClose
    信息: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@7e6cbb7a: startup date [Mon Oct 14 23:17:00 CST 2019]; root of context hierarchy
    ===========close============
    ========destroy========
    ==============destroy===============
 ```

## 三、 bean对象依赖注入

## 1. 可以通过几个特殊的注解将对象纳入spring容器管理

### 1.1 @Component

```java
package com.spring;

import org.springframework.stereotype.Component;

/**
 * 使用 @Component 将类放入spring容器中进行管理
 * @Component 并没有特殊的含义,表示就是一个spring容器管理的组件
 */
@Component("myUser")
public class User {
	
}
```

### 1.2 @Controller

```java
package com.spring;

import org.springframework.stereotype.Controller;

/**
 * 一般用于展现层
 */
@Controller
public class UserController {
	
}

```

### 1.3 @Service

```java
package com.spring;

import org.springframework.stereotype.Service;

/**
 * 业务逻辑层
 */
@Service
public class UserService {
	
}

```

### 1.4 @Repository

```java
package com.spring;

import org.springframework.stereotype.Repository;

/**
 * 通常用于数据访问管理
 */
@Repository
public class UserDao {
	
}

```

## 2. 注入到bean对象中

### 2.1 通过 @Autowired注入

```java
package com.spring;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

/**
 * 业务逻辑层
 */
@Service
public class UserService {
	
	/*
	 * 1. 使用 @Autowired 将UserDao注入时,不需要get/set方法
	 * 2. 当spring容器中有多个UserDao的bean对象时,无法直接通过 @Autowired注入
	 * 	  得使用 @Qualifier("bean对象的名字") 来注入
	 */ 
	@Autowired
	@Qualifier("createUserDao")
	private UserDao userDao;

	@Override
	public String toString() {
		return "UserService [userDao=" + userDao + "]";
	}
		
}

```

### 2.2 通过 @Resource注入

### 2.3 通过 @Inject注入

```java
package com.spring;

import javax.annotation.Resource;
import javax.inject.Inject;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

/**
 * 一般用于展现层
 */
@Controller
public class UserController {
	
	@Autowired
	private UserService userService;
	
	// JSR-250的注解（jdk自带）
	@Resource
	private UserService2 userService2;
	
	// JSR-330的注解（需要手动添加依赖）
	@Inject
	private UserService3 userService3;

	@Override
	public String toString() {
		return "UserController [userService=" + userService + ", userService2=" + userService2 + ", userService3="
				+ userService3 + "]";
	}
}

```

## 3. 配置类

```java
package com.spring;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.context.annotation.Scope;

@Configuration
public class MyConfig3 {
	
	@Bean
	public User createUser() {
		return new User();
	}
	
	@Bean
	public UserService2 createUserService() {
		return new UserService2();
	}
	
	/*
	 * 在这里写这个,那么注入spring容器的bean对象就有两个
	 * spring容器中管理2两个UserDao的bead对象
	 * 在 Service层中直接 @Autowired 注入时,就会报错,不知道注入哪个
	 * 解决方法:
	 * 1.当时用 @Primary后,Service层中 @Autowired 注入时优先注入 有此注解的bean对象
	 * 2.在 @Autowired时,也使用 @Qualifier("bean对象的名字") 来指定要注入的对象
	 */
	@Bean
//	@Primary
	public UserDao createUserDao() {
		return new UserDao();
	}
	
	@Bean 
	public UserDao createUserDao2() {
		return new UserDao();
	}
}
```

## 4.要注入的类

```java
package com.spring;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

/**
 * 业务逻辑层
 */
@Service
public class UserService {
	
	/*
	 * 1. 使用 @Autowired 将UserDao注入时,不需要get/set方法
	 * 2. 当spring容器中有多个UserDao的bean对象时,无法直接通过 @Autowired注入
	 * 	  得使用 @Qualifier("bean对象的名字") 来注入
	 */ 
	@Autowired
	@Qualifier("userDao")
	private UserDao userDao;

	@Override
	public String toString() {
		return "UserService [userDao=" + userDao + "]";
	}
		
}
```

```java
package com.spring;

/*
 * 此处没有写 @Service 将UserService2纳入spring容器管理,
 * 但是后面将此类的实例注入时没有发生错误,
 * 是因为在配置类中(MyConfig.class)使用了 @Bean,
 * 将此类纳入了spring容易管理,所以没有报错
 */ 
public class UserService2 {
	
}

```

```java
package com.spring;

import org.springframework.stereotype.Service;
/*
 * 如果没有将此类纳入spring容器管理,再将其注入其他类时,会报错:
 * Caused by: org.springframework.beans.factory.NoSuchBeanDefinitionException:
 * No qualifying bean of type [com.spring.UserService3]
 * found for dependency [com.spring.UserService3]: 
 * expected at least 1 bean which qualifies as autowire candidate 
 * for this dependency. Dependency annotations: {@javax.inject.Inject()}
 */
@Service
public class UserService3 {
	
}

```

## 5. 启动类

```java
package com.spring;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class App3 {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext(MyConfig3.class,User.class,UserDao.class,UserService.class,UserController.class,UserService3.class);
		/*
		 * 通过 @Component也可以直接将对象纳入spring容器,
		 * 1. 默认的名称就是 类名首字母小写 “user”,
		 * 2. 如果想修改名字,使用 @Component("修改后的名字") ,
		 * 3. 可以通过 getBeansofType(Class)方法获取Map对象,
		 * 	  从中可以看出 spring 容器中bean对象的名字
		 */
		System.out.println(context.getBean("myUser"));
		/*
		 * System.out.println(context.getBean(User.class));
		 * 报以下异常:
		 * Exception in thread "main" org.springframework.beans.
		 * factory.NoUniqueBeanDefinitionException:
		 * No qualifying bean of type [com.spring.User] is defined: 
		 * expected single matching bean but found 2: myUser,user
		 */
		System.out.println(context.getBeansOfType(User.class));
		System.out.println("createUserDao:" + context.getBean("createUserDao"));
		System.out.println("userDao:" + context.getBean("userDao"));
		System.out.println(context.getBean(UserService.class));
		System.out.println(context.getBean(UserController.class));
		context.close();
	}
}
```

## 6. 输出结果:

```java
十月 16, 2019 10:13:50 下午 org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor <init>
信息: JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
com.spring.User@6166e06f
{myUser=com.spring.User@6166e06f, createUser=com.spring.User@1c72da34}
createUserDao:com.spring.UserDao@6b0c2d26
userDao:com.spring.UserDao@3d3fcdb0
UserService [userDao=com.spring.UserDao@3d3fcdb0]
UserController [userService=UserService [userDao=com.spring.UserDao@3d3fcdb0], userService2=com.spring.UserService2@641147d0, userService3=com.spring.UserService3@6e38921c]
十月 16, 2019 10:13:50 下午 org.springframework.context.annotation.AnnotationConfigApplicationContext doClose
```

## 四、简化

### 1. 

```java
package com.spring;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class AnnotationClient {
	
	public static void main(String[] args) {
		/*
		 * 除了通过 配置类 为参数的构造方法,还可以通过 扫描的包 
		 * 将带有 注解的类都放入spring容器中管理
		 */
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext("com.spring");
		context.close();
	}
}

```

### 2. 写一个配置类,添加注解 @AnnotationScan("扫描的包")

#### step1：spring容器将要注入的类

```java
package com.spring;

public class Cat {

}
```

```java
package com.spring;

import org.springframework.stereotype.Component;

@Component
public class Dog {

}
```

```java
package com.spring;

import org.springframework.stereotype.Component;

@Component
public class Bird {

}
```

#### step2: 添加要创建bean对象的配置类（可写可不写）

```java
package com.spring;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyConfig {
	
	@Bean
	public Cat createCat() {
		return new Cat();
	}
	
	@Bean
	public Dog createDog() {
		return new Dog();
	}
	
	@Bean
	public Bird createBird() {
		return new Bird();
	}
	
}

```

#### step3:  记录要扫描类的配置类

```java
package com.spring;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

/*
 * 通过 excludeFilters来设置扫描时要排除的类,
 * ASSIGNABLE_TYPE : 表示自定义的类类型
 * classes : 表示具体的类
 * 此处是将 Dog 排除了
 */
@ComponentScan(basePackages="com.spring",
               excludeFilters=@Filter(type=FilterType.ASSIGNABLE_TYPE,
                                      classes= {ExcludeConfig.class,Dog.class}))
@Configuration
public class AnnotationScan {
	
}
```

#### step4: 记录不扫描对象的类

```java
package com.spring;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ExcludeConfig {

	@Bean
	public Bird createBird() {
		return new Bird();
	}
	
}
```

#### step5: 测试

```java
package com.spring;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;

public class ClientApp2 {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext(AnnnotationScan.class);
		
		System.out.println(context.getBean("createCat"));
		System.out.println(context.getBean(Dog.class));
        System.out.println(context.getBeansOfType(Dog.class));
		System.out.println(context.getBeansOfType(Bird.class));
		
		context.close();
	}
}

```

#### step6：结果

```java
com.spring.Cat@4d49af10
com.spring.Dog@58134517
{createDog=com.spring.Dog@58134517}
{bird=com.spring.Bird@4450d156, createBird=com.spring.Bird@4461c7e3}
```

