# IOC控制反转

所有实现类均交由spring框架（容器）创建，管理

# xml文件开发



# 注解开发

### 1. @Autowired

- 自动装配，先按类型（byType）后按名字（byName）
- 若自动装配不能唯一识别，则需搭配**@Qualifier**（value = xx）

### 2. @Response

- 自动装配，先按名字（byName）后按类型（byType）

### 3. @Nullable

- 允许字段为空

### 4. @Component

- 放在类上，等价于xml中

  ```xml
  <bean id = "xx" class = "[path]"/>
  ```

  表示该类被spring管理

- 衍生注解：四者功能相同，将类放入容器中

  - @Repository: dao
  - @service：service
  - @Controller：controller

### 5. @Value

- 加在属性上，为属性注入值

### 6. @Scope

- 作用域，在后面加上（“作用范围”）

### 7. xml与注解

- xml管理bean
- 注解只负责属性注入

### 8. 脱离xml配置，仅使用java的JavaConfig来做

使用配置类来解决，使用AnnotationConfigApplicationContext类

- **@Configuration**
  -  本身自己是个配置类，等价于之前的xml
  - 注解下的类也会被Spring接管，注入到容器中
- **@Bean**
  - 等价于之前的一个Bean标签，对应方法名即为bean标签中id，方法返回值即为bean中class

# 代理模式（Spring AOP底层）

### 1.分类

- 静态代理
- 动态代理

![image-20220225155201940](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220225155201940.png) 

### 2.静态代理

- 抽象角色：抽象类或接口

- 真实角色：被代理的角色

- 代理角色：代理真实角色

- 客户：访问代理对象
- 好处：
  - 方便集中管理
  - 使真实角色操作更纯粹
  - 公共业务交给代理，实现业务分工
- 缺点：
  - 一个真实角色必须对应一个代理角色，代码量增大

![image-20220225162028837](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220225162028837.png)

### 3. 动态代理

- 角色与静态代理相同
- 代理类是动态生成的
- 分为两大类：基于接口，基于类
  - 基于接口--jdk动态代理
  - 基于类--cglib
  - 基于java字节码：JAVAssist

- Proxy（类）：代理
  -  提供动态代理的静态方法
- InvocationHandler（接口）
  - 仅有唯一方法invoke：处理代理实例并返回结果 

- 好处：
  - 一个动态代理类代理一个接口，且可代理多个实现同一接口的类
  - 静态代理的所有优点

# AOP面向切面编程

![image-20220225165147071](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220225165147071.png)

 

![image-20220225170226349](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220225170226349.png)

# Spring实现AOP

- 方式一：使用spring API接口，见上图。注重接口实现

  ```xml
  <aop:config>
    	<!--切入点：expression，execution(要执行的位置)-->  
  	<aop:pointcut id="", expression="execution()"
  	<aop:advisor> advice-ref="" pointcut-ref="">
  </aop:config>
  ```

- 方式二：使用自定义类，注重切面定义

```java
public class DiyPointCut {
    public void before {
        
    }
    
    public void after {
        
    }
    ...
}
```

-----------------------------

```xml
<aop:config>
    <aop:aspect ref:DiyPointCut>
        <!--切入点：expression，execution(要执行的位置)--!>  
        <aop:pointcut id="point", expression="execution()"/>
        <aop:before method="before" pointcut-ref="point"/>
    </aop:aspect>
</aop:config>
```

- 方式三：注解
- **@Aspect**：标注切面类（记得用bean注入容器）
  - **@Before(execution(...))**：标注前置日志
  - **@After(execution(...))**：标注后置日志
  - **@Around(execution(...))**：标注环绕日志
  - ...

# 整合Mybatis

- 步骤
  - 导入相关jar包
    - junit
    - mysql
    - mybatis
    - webmvc
    - jdbc
    - aspectj
    - mybatis-spring
  - 编写配置文件
  - 测试
- mybatis操作基本步骤
  - 编写实体类
  - 编写核心配置文件
  - 编写Mapper
  - 编写对应xml
  - 测试

##### 方式一：

- 配置数据源

  ```xml
  <bean id="dataSource" class=".../DriverManagerDataSource">
      <property name="driverClassName" value=""/>
      <property name="url" value=""/>
      <property name="username" value=""/>
      <property name="password" value=""/>
  </bean>
  ```

- sqlSessionFactory配置

  ```xml
  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  	<property name="dataSource" ref="dataSource"/>
      <property name="configLocation" value="classpath:mybatis-config.xml"/>
      <property name="mapperLocations" value="classpath:mapper的xml路径"/>
  </bean>
  ```

- sqlSessionTemplate配置: 对应之前的sqlSession,且由于无set方法只能使用构造器注入

  ```xml
  <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
  	<constructor-arg index="0" ref="sqlSessionFactory"/>
  </bean>
  ```

- 给接口加实现类

##### 方式二：使用SqlSessionDaoSupport

```java
public class userMapperImpl extends SqlSessionDaoSupport implements UserMapper
```

p.s:记得将类装配到Bean

# 声明式事务

### 事务（transaction）回顾

同时成功或同时失败。可以开启，提交，回滚

**ACID**：原子性（A），一致性（C），隔离性（I），持久性（D）

### Spring实现事务管理（声明式事务）

- 配置声明式事务

  ```xml
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  	<property name="dataSource" ref="dataSource"/>
  </bean>
  ```

- 配置事务通知及传播特性

  ```xml
  <tx:advice id="txAdvice" transaction-manager="transactionManager">
  	<tx:atrributes>
          <tx:method name="add" propagation="required"/><!--默认为required-->
          <tx:method name="delete"/>
          <tx:method name="update"/>
          <tx:method name="query" read-only=true/><!--只读为true不允许修改数据库-->
          <tx:method name="*"/><!--配置所有方法-->
      </tx:atrributes>
  </tx:advice>
  ```

- 配置事务切入

  ```xml
  <aop:config>
    	<!--切入点：expression，execution(要执行的位置)-->  
  	<aop:pointcut id="txpointcut", expression="execution()"
  	<aop:advisor> advice-ref="" pointcut-ref="txpointcut">
  </aop:config>
  ```

