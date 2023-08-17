[000-Spring高级49讲-导学_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1P44y1N7QG?p=1&vd_source=796ed40051b301bfa3a84ba357f4828c)的自学笔记

# Spring

## 1.快速入门- BeanFactory 快速入门

### 1.1使用BeanFactory完成了IoC思想的实现 步骤

>1. 导入Spring的jar包或Maven坐标；
>2. 定义UserService接口及其UserServiceImpl实现类；
>3. 创建beans.xml配置文件，将UserServiceImpl的信息配置到该xml中；
>4. 编写测试代码，创建BeanFactory，加载配置文件，获取UserService实例对象。

 1）导入Spring的jar包或Maven坐标

````xml
  <!--Spring核心容器-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.7</version>
    </dependency>
````

 2）定义UserService接口及其UserServiceImpl实现类

```java
public interface UserService {}
public class UserServiceImpl implements UserService {}
```

 3）创建beans.xml配置文件，将UserServiceImpl的信息配置到该xml中

```xml
<bean id="userService" class="com.itheima.service.impl.UserServiceImpl"></bean>
```

 4）编写测试代码，创建BeanFactory，加载配置文件，获取UserService实例对象

```java
//创建BeanFactory
DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
//创建读取器
XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);
//加载配置文件
reader.loadBeanDefinitions("beans.xml");
//获取Bean实例对象
UserDao userService = (UserService) beanFactory.getBean("userService");
```

### 1.2DI依赖注入步骤：

>1. 定义UserDao接口*及其*UserDaoImpl实现类；
>2. 修改UserServiceImpl代码，添加一个setUserDao(UserDao userDao)用于接收注入的对象；
>3. 修改beans.xml配置文件，在UserDaoImpl的`<bean>`中嵌入`<property>`配置注入；
>4. 修改测试代码，获得UserService时，setUserService方法执行了注入操作。

## 2.[快速入门-ApplicationContext](C:\Users\^\Documents\Tencent Files\1302344595\FileRecv\spring_ioc_test01)

### 简介

> ApplicationContext 称为Spring容器，内部封装了BeanFactory，比BeanFactory功能更丰富更强大，
> 使用 ApplicationContext 进行开发时，xml配置文件的名称习惯写成applicationContext.xml

### 步骤

```java
//创建ApplicationContext,加载配置文件，实例化容器
ApplicationContext applicationContext = new ClassPathxmlApplicationContext(“applicationContext.xml");
//根据beanName获得容器中的Bean实例
UserService userService = (UserService) applicationContext.getBean("userService");
System.out.println(userService)
```

##  BeanFactory与ApplicationContext的关系

> 1. BeanFactory是Spring的早期接口，称为Spring的Bean工厂，ApplicationContext是<u>后期更高级接口</u>，称之为 Spring 容器；
> 2. ApplicationContext在BeanFactory基础上<u>对功能进行了扩展</u>，例如：监听功能、国际化功能等。BeanFactory的 API更偏向底层，ApplicationContext的API大多数是对这些底层API的封装；
>
> 3. Bean创建的主要逻辑和功能都被封装在BeanFactory中，ApplicationContext不仅<u>继承了BeanFactory</u>，而且 ApplicationContext内部还<u>维护着BeanFactory的引用</u>，所以，ApplicationContext与BeanFactory既有继承关系，又 有融合关系。 
>
> 4. <font color='25, 94, 12'>Bean的初始化时机不同</font>，原始BeanFactory是在首次调用getBean时才进行Bean的创建，而<u>ApplicationContext</u>则 是配置文件加载，容器<u>一创建就将Bean都实例化并初始化好</u>

### ApplicationContext接口实现关系

> 在BeanFactory基础上<u>对功能进行了扩展</u>，例如：监听功能、国际化功能等。

<img src=".\MDImg\1.2.01.png" style="zoom:120%;"/>

### ApplicationContext类继承体系

<mark>只在Spring基础环境下</mark>

<img src=".\MDImg\1.2.02.png" style="zoom:120%;"/>

 <mark>只在Spring基础环境下</mark>，常用的三个ApplicationContext作用如下

| 实现类                             | 功能概述                                                 |
| :--------------------------------- | :------------------------------------------------------- |
| ClassPathXmlApplicationContext     | 加载<mark>类路径</mark>下的xml配置的ApplicationContext   |
| FileSystemXmlApplicationContext    | 加载<mark>磁盘路径</mark>下的xml配置的ApplicationContext |
| AnnotationConfigApplicationContext | 加载<mark>注解配置类</mark>的ApplicationContext          |

如果<mark>Spring基础环境中加入了其他组件</mark>解决方案，如web层解决方案，即导入spring-web坐标，**此时 ApplicationContext的继承体系**

导入spring-web之后

````xml
<!--  导入spring-web依赖 -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-web</artifactId>
	<version>5.3.7</version>
</dependency>
````

<img src=".\MDImg\1.2.03.png" style="zoom:120%;"/>

<mark>在Spring的web环境下</mark>，常用的两个ApplicationContext作用如下：

| 实现类                                | 功能概述                                                     |
| ------------------------------------- | ------------------------------------------------------------ |
| XmlWebApplicationContext              | web环境下，加载<mark>类路径下的xml配置</mark>的ApplicationContext |
| AnnotationConfigWebApplicationContext | web环境下，加载<mark>注解配置类</mark>的ApplicationContext   |

*PS*：web环境下的这两个ApplicationContext，在学习Spring集成web时在进行讲解