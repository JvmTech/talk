# Spring Projects 介绍

[Spring](https://spring.io/): **the source for modern java.**
> ![](./assets/spring.png)

Spring 是一个开源的项目, 社区开发活跃度非常高. 在 Java 技术栈中地位很高, Java Web 的项目基本都以 Spring Framework 为基础框架. 

Spring 有[很多项目](https://spring.io/projects), 如 `Spring Framework`, `Spring Boot`, `Spring Cloud`, `Spring Data` 等, 每个项目一般都有多个模块.

## 设计哲学 (Spring Framework)

> 当你去学习一个框架时, 除了要知道这个框架可以做什么, 了解这个框架所遵循的原则同样很重要. 后者可能对你更有意义.

以下为开发 Spring Framework 的指导原则:

- 在各个系统层面提供选择
> 比如: Spring 允许你在编码之前不用做出最终设计决策, 你可以通过修改配置文件改变你的持久层提供者而不需要修改代码
- 支持形形色色的应用场景
> Spring 拥抱灵活并不固执于事情应该怎么做
- 向后兼容
- 注重 API 的设计
- 高标准的代码质量

# Spring Framework

## 历史

> 第一版由 Rod Johnson 开发, 并在2002年10月发布在 Expert One-on-One J2EE Design and Development 一书中. 2003年6月, Spring Framework 第一次发布在 Apache 2.0 许可证下. 2004年3月, 发布了里程碑的版本1.0, 2004年9月以及2005年3月，又发布了新的里程碑版本. 2006年, Spring Framework 获得了 Jolt 生产力奖 和 JAX 创新奖.

> 2006年10月发布Spring 2.0, 2007年11月 Spring 2.5, 2009年12月 Spring 3.0,2011年 Spring 3.1, 2013年11月 Spring 3.2.5, 2013年12月发布了4.0版本. 值得注意的是, Spring 4.0 版本中增加了对 Java SE 8, Groovy 2, Java EE7 的一些方面以及 WebSocket 的支持. 2015年7月31号发布 4.2.0. 2016年6月10日 4.3. 4.3 版本将是 Spring 4 系列的最后一个版本, 4.3.x 的支持将到 2019 年.

> 新一代 Spring, 2017年9月28号 发布 5.0.0

[Source](https://github.com/spring-projects/spring-framework)

> ![](./assets/banner.png)

Spring Framework 可分为以下几部分

- Core
> 提供 Spring 框架的基本功能. IoC 容器, AOP, Validation, Data Binding, Type Conversion ...
- Dada Access
> 数据访问相关库, 数据库事务管理, JDBC, ORM 框架的集成
- Web Servlet
> Spring MVC, WebSocket, SockJS, STOMP messaging
- Web Reactive
> Spring WebFlux, WebClient, WebSocket
- Integration
> 用于集成其他外部技术, 如: JMS, Email, Tasks ...
- Languages
> 支持 Kotlin, Groovy, 动态语言
- Testing
> Spring 自身框架的测试工具

## Spring Core

### IoC 容器

Inversion of Control (IoC, 控制反转) 也被称作 dependency injection (DI, 依赖注入). 这是对象定义他们依赖的过程, 通过设置一个对象构造函数的参数或设置创建对象工厂的参数,或设置对象(被构造后或被工厂返回)的实例的属性, 当容器创建 Bean 的时候会把 Bean 的依赖注入. 这过程对比对象自己控制自己的初始化和通过直接调用对象的构造函数来创建依赖的过程是倒转的, 这也是IOC名字的由来.

为什么要这么做? 可以解决什么问题?

> 对象依赖松耦合. 不用修改代码核心逻辑和处理流程, 通过配置就可以定义对象依赖树, 满足不同场景的实现需求.

#### BeanFactory 和 ApplicationContext 接口

``` java
public interface BeanFactory {
    Object getBean(String name);
    <T> T getBean(String name, Class<T> requiredType);
    <T> T getBean(Class<T> requiredType);
    Object getBean(String name, Object... args);
    <T> T getBean(Class<T> requiredType, Object... args);
    boolean containsBean(String name);
    boolean isSingleton(String name);
    boolean isPrototype(String name);
    boolean isTypeMatch(String name, ResolvableType typeToMatch);
    boolean isTypeMatch(String name, Class<?> typeToMatch);
    Class<?> getType(String name);
    String[] getAliases(String name);
}

public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory, MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
    String getId();
    String getApplicationName();
    String getDisplayName();
    long getStartupDate();
    ApplicationContext getParent();
    AutowireCapableBeanFactory getAutowireCapableBeanFactory();
}
```

`BeanFactory` 提供了管理各种对象的配置机制和一些基本功能.

`ApplicationContext` 是 `BeanFactory` 的子接口是其超集, 添加了与 Spring AOP 特性的上集成, 国际化支持, 和 应用层面的特定上下文.

#### 容器的魔法

> ![](./assets/container-magic.png)

#### 配置元数据

提供一下三种方式, 注意三种方式可以混用.

##### XML-based
> 以 `XML` 格式的文件, 这是传统的方式. 如:
``` XML
<?xml version="1.0" encoding="UTF-8"?>
<beans>
    <bean id="..." class="..."></bean>
</beans>
```

##### Annotation-based
> Spring 2.5 引入 Java 的注解方式(Annotation). 如: `@Autowired`, `@Inject`, `@Resource`, `@Value`, `@RestController`, `@Component`, `@Service`, `@Repository`

##### Java-based
> Spring 3.0 开始, 可以通过 Java 代码.

#### 初始化容器
> `ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");`

#### 例子

通过构造函数:

``` Java
package x.y;
public class Foo {
    public Foo(Bar bar, Baz baz) {
        // ...
    }
}
```
``` XML
<beans>
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
    </bean>
    <bean id="bar" class="x.y.Bar"/>
    <bean id="baz" class="x.y.Baz"/>
</beans>
```

``` Java
package examples;
public class ExampleBean {
    private int years;
    private String ultimateAnswer;
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```
```XML
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

通过 `Setter` 方法:
``` Java
package examples;
public class SimpleMovieLister {
    private MovieFinder movieFinder;
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

### Aspect Oriented Programming


# Spring Boot

[Source](https://github.com/spring-projects/spring-boot)

> ![](./assets/spring-boot.png)

# Spring Cloud

[Source](https://github.com/spring-cloud)

> ![](./assets/spring-cloud.png)

# Spring 的缺点

### 参考

- [spring-framework-reference](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)
- [Wikipedia](https://en.wikipedia.org/wiki/Spring_Framework)
- [Spring 框架简介 .2005](https://www.ibm.com/developerworks/cn/java/wa-spring1/)