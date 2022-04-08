# SpringBoot（狂神）

### 彩蛋

banner.txt更换springboot图标

server：port改端口号

### 自动装配原理

- pom.xml中核心依赖在父工程中
- 版本仓库使得我们不需要标注某些依赖的版本
- 启动器（spring-boot-starter-xx）
  - 自动导入xx场景所有依赖
  - 每个功能场景对应一个启动器

- 主程序
  - **@SpringBootApplication**：标注这是个springboot应用，是个综合注解：
    - @Target：
    - @Retention：
    - @Documented：
    - @Inherited：
    - **@SpringBootConfiguration**: springboot配置
      - @Configuration：spring配置类
      - @Component：spring组件
    - **@EnableAutoConfiguration**: 启用自动配置
      - @AutoConfigurationPackage：自动配置包
        - @Import({Registrar.class}) ：导入注册器
      - @Import({AutoConfigurationImportSelector.class})：自动配置导入选择
    - @ComponentScan:
    - @Filter:
    - META-INF/spring.factories：自动配置核心文件
  - **@ConditionalOnClass, @ConditionalOnProperty, @ConditionalOnWebApplication**: 条件成立后才生效（条件配置）
  - ![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy91SkRBVUtyR0M3SVBFWFp0VUFVQmhuU1p2VW1yUHpiREdjSlJ2ZEszUHRxSFBBV1lCQm1wZTFYQlZqUUplaWF0VTR2YXNFYXhja0hsT2dhMUJWOVJQYXcvNjQw?x-oss-process=image/format,png)
  - 能配置入yaml文件中配置的东西xxx一般存在于xxxAutoConfiguration xxxProperties.

### 配置文件 application.yaml

- 格式：

  - 普通键值对：key: value

  - 对象：object：

    ​				property1：

    ​				property2：

    ​				。。。

    object：{property: ..., ...}

  - 数组：array：

     			- element

​							- 。。。

- **可直接对实体类赋值**（代替@Value的作用）： **@ConfigurationProperties**(prefix = "[nameInYaml]")
- 多配置文件优先级：file./config > file./ > classpath./config > classpath./
- 多环境配置文件：
  - 选择：spring:profiles:active:[name]
  - 命名：spring:profiles:[name]

### 配置文件application.properties

- 格式：

  object.property(.key)=value

- 指定配置文件：**@PropertySource**("[properties文件路径]")

- 多配置文件优先级：file./config > file./ > classpath./config > classpath./

### JSR303数据校验

- 注解：**@Validated**
  - @Email(message:"[希望输出的错误信息]")：邮箱格式
  - @Pattern([格式的正则表达式])

# SpringBoot Web开发

### 静态资源

- 方式
  - webjars
  - ./resources/, ./static/, ./public/

- 优先级：./resources > ./static > ./public

### 首页定制

- index.html置于静态资源任一目录下
- template内页面只能通过controller访问

### 模板引擎：thymeleaf

- 需要导入
- 将html放入template，然后通过controller访问即可

- 所有html元素均可被thymeleaf接管：th:[elementName]
- 仅作了解，这已经涉及前端，前端可用vue解决

### 装配SpringMVC

- 自定义扩展：写对应组件并装入bean中，springboot会自动装配
- **@EnableWebMvc**：自动配置MVC，作用为导入一个类DelegatingWebMvcConfiguration，**自定义扩展时不能加该注解**

### Mybatis整合

1. 导包

2. 配置文件

   ```yml
   spring:
     datasource:
       username: root
       password: root
       url: jdbc:mysql://localhost:3306/db1?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
       hikari:
         driver-class-name: com.mysql.cj.jdbc.Driver
   # mybatis
   mybatis:
     type-aliases-package: com.fishlife.pojo
     mapper-locations: classpath:mybatis/mapper/*.xml
   ```

3. 配置mybatis：mapper接口——mapper.xml

4. 编写sql

5. 业务层调用DAO层

6. Controller层调用service

### 前端模板：bootstrap

# 实战

### 首页配置

- 所有页面静态资源交由thymeleaf保管（后续会由vue接手，可忽略）
- url：@{}

### 国际化

- 配置i18n
- 语言切换需自定义国际化组件
- 自己的组件记得**@Bean**
- 文档：#{}

### 登录拦截器

- 方法类似自定义视图解析器
- 静态资源不能被拦截

### CRUD

### 404

# SpringBoot（尚硅谷）

### 自动配置特性

- 扫包
  - **@ComponentScan**
  - **@SpringBootApplication（scanBasePackages=[path])**=
    - **@SpringBootConfiguration**：代表当前是个配置类
    - **@EnableAutoConfiguration**：
    - **@ComponentScan**：指定扫描包路径

### 底层注解

- **@Configuration**：配置类注解，等效于配置文件

  - **proxyBeanMethods**：是否为代理方法，为true时springboot总会保持组件单实例（Full模式），为false时每次注册组件总是创建一个新的实例（Lite模式）,用于处理组件依赖问题（e.g.人与宠物)
- **@Bean([name])**：标注在方法上，给容器中添加组件，id为方法名（默认）或自定义名，返回类型为组件类型，返回值为组件在容器中实例
- **@Import()**：导入指定组件，给容器中自动创建出对应类型组件
- **@ConditionalOnxxx**：条件装配

- **@ImportResource([path])**：导入Spring的配置文件xml
- **@ConfigurationProperties(prefix=[name])**：将组件的属性值与path下名为[name]的组件绑定

### 自动配置源码解析

- **@EnableConfigurationProperties([class].class)**：开启class的属性配置功能

- **@EnableAutoConfiguration**=
  - **@AutoConfigurationPackage**：利用registrer，批量注册组件，将指定包下所有组件导入（默认为main类所在包及其下的所有包）
  - **@Import(AutoConfigurationImportSelector.class)**：selector获取所有需要导入容器的组件

- **@ConditionalOnxxx**：条件注解实现按需启动

- springboot自动配置所有底层，如果某个底层用户有配置则按照用户的配置来。

- 每个配置类（config）伴随着一个配置文件（.properties，.yml）

### 开发步骤

- 根据需求引入场景依赖
- 查看已有的自动配置（debug = true，开启debug功能，查看自动配置报告）
- 是否需要修改配置
  - 参照文档修改配置项，分析配置文档绑定了哪些
  - 自定义加入或替换新组件
  - 自定义器xxxCustomizer

### Lombok

- **@Data**：自动生成get，set方法
- **@ToString**：自动重写toString方法
- **@NoArgsConstructor**：加入无参构造器
- **@AllArgsConstructor**：加入全参构造器
- **@EqualsAndHashCode**：自动重写equalsAndHashCode方法
- **@Slf4j**：生成日志（log.info)

### Dev-tools

热部署更新工具（build快捷键ctrl+F9）

### Spring Initializr

### yaml

- 基本语法：key:**[space]**value
- 以缩进表示层级关系
- #表示注释

- '' / ""表示字符串内容，会被转义/不转义

- 基本类型参数：直接写

- list,set类型参数

  - 行内写法：property: [xxx,xxx]

  - 多行写法：property: 

    ​						-

    ​                        -

- Map类型参数

  - 多行写法： property: 

       					 k: v

    ​						k: v

  - 单行写法：property: {k:v, k:v}

  - 

- 对象类型参数：嵌套一层

### 自定义类绑定的提示

- 导入依赖

![image-20220305180353809](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305180353809.png)



# SpringBoot Web开发

**官方文档很重要**

## 简单功能分析

### 静态资源访问

- 静态资源目录：
  - [path]/resources/static
  - [path]/resources/templates
  - [path]/resources/resources
  - [path]/resources/public
  - [path]/resources/META-INF/resources

- 静态资源映射为/**，获得请求时先去找controller，controller无法解决的请求给静态资源处理
- 静态资源访问前缀：默认无，可在yml/properties中配置，用于给拦截器识别放行

```yaml
spring:
	mvc: 
		static-path-pattern=[prefix]/**
```

- 欢迎页index.html
  - 在静态资源目录下放置index.html
  - 在控制类中写个controller处理/index请求
- 网页图标favicon
  - 将favicon.ico放在静态资源路径下

- 静态资源配置原理

  - WebMvcAutoConfiguration
  - 配置文件和相关属性进行绑定:WebMvcProperties\=\=spring.mvc, ResourceProperties\=\=spring.resources
    - 配置类只有一个有参构造器：有参构造器所有参数由容器决定
  - 静态资源配置的默认规则

  ```yaml
  spring:
  	resources: 
  		static-locations: //自定义静态资源路径
  		add-mapping: //为false时禁用所有静态规则
  		cache: 
  			period: //设置缓存时间请求参数处理
  ```

### 请求参数处理

- 请求映射

  - **@xxxMapping**

  - Rest风格支持

    - delete和put请求需要隐藏参数_method以及post请求

    ![image-20220305194600809](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305194600809.png)

    - 手动开启hiddenmethod.filter

    ![image-20220305194808702](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305194808702.png)

    ```yaml
    spring: 
    	mvc: 
    		hiddenmethod: 
    			filter: 
    				enabled: true
    ```

    - 兼容put，delete，patch

  - Rest在表单情况下只有get，post，需要开启filter，客户端工具的请求中包含上述Rest风格请求，故不需要开启filter

  - 改变默认的_method：自己加一个配置类，里面配置注册HiddenHttpMethodFilter

  - 请求映射原理：本质是个Servlet

    - HttpServlet的doGet方法在FrameWorkServlet中被重写，返回processRequest
    - processRequest调用doService,doService在DispatcherServlet实现
    - 每个请求都会调用doDispatch方法
    - 所有处理请求的方法都在同一个HandlerMapping中，同样一个请求只能由一个方法处理，请求来时遍历HandlerMapping寻找能处理该请求的方法
    - 需要自定义HandlerMapping

### 常用参数注解使用

- **@PathVariable**：路径变量：在Mapping注解后的参数中以{varname}形式展示
- **@RequestHeader**：获取请求头，参数为请求头参数名，无参时默认为取所有请求头参数
- **@RequestParam**：获取请求参数，参数为获取的参数名，无参时默认为获取所有请求参数
- **@CookieValue**：获取Cookie值，参数为Cookie名，可由一个Cookie对象接受所有的Cookie参数
- **@RequestAttribute**：获取request域属性，参数为属性名
- **@RequestBody**：获取请求体数据，用于接受表单提交
- **@MatrixVariable**：矩阵变量，应绑定在路径变量中，参数为矩阵变量名
  - url路径重写：把cookie值用矩阵变量形式传递
  - [path]/xxx;key=value1;key=value2;...
  - Springboot默认禁用矩阵变量，需手动开启
    - removeSemicolonContent在urlPathHelper下，默认为false，表示关闭矩阵变量
    - 需要自定义一个配置类实现webMvcConfigurer，改写removeSemicolonContent为true
    - 参数
      - value：矩阵变量名
      - pathVar：对应的路径变量名

### 参数处理原理

- HandlerMapping中找能处理请求的Handler（即Controller.method()）

- 为当前Handler找一个适配器Adapter，默认返回第一个：RequestMappingHandlerAdapter

- 参数解析器（HandlerMethodArgumentResolver）：决定之后将执行的目标方法能写多少参数类型

- 返回值处理器

  - 遍历返回值处理器，找到能处理该返回值的处理器
  - 使用该处理器处理

- 确定目标方法参数：
  - 遍历所有参数解析器，确定哪个参数解析器支持这个参数
  - 用该参数解析器获取参数名后解析参数

- Model，Map，HttpServletResponse均可以向request域传送数据

- Model，Map均把数据放至ModelAndViewContainer，包含要去的页面地址和数据

- 自定义参数绑定原理：
  - 参数处理器：ServletModelAttributeMethodProcessor
  
  - web数据绑定器：WebDataBinder，将请求数据绑定到指定JavaBean中，其含有大量数据类型转换器Converter
  
  - 遍历所有Converter寻找能够使用的Converter
  
  - 可自定义Conveter实现个性化需求
  
    - 在webMvcConfigurer配置
  
    ![image-20220305222434411](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305222434411.png)
  
    - 会在Converter中添加自己的Converter，然后被遍历到

### 数据响应

- 响应json数据
  - 引入jackson依赖
  - 标注注解**@ResponseBody**：其返回值处理器为RequestResponseBodyMethodProcessor
    - 利用MessageConverter（消息转换器）
      - 内容协商：浏览器默认以请求头方式告诉服务器他能接受什么样的内容类型
      - 遍历所有MessageConverter，判断哪个支持将该class对象转为MediaType类型数据
      - 最终MappingJackson2Messageconverter把对象转为json

### 内容协商

- 导入依赖：jackson支持xml模块

- 原理
  - 判断当前响应头中是否有确定的MediaType
  - **获取客户端支持的请求类型**
    - contentNegotiationManager内容协商管理器，默认使用基于请求头的策略
  - 遍历所有MessageConverter，找到支持当前对象的Converter
  - 统计Converter支持的MediaType
  - 将服务器能产生的MediaType和浏览器能支持的MediaType进行匹配

- 基于请求参数的内容协商(url后加/format=xxx)

  ```yaml
  spring: 
  	mvc: 
  		hiddenmethod: 
  			filter: 
  				enabled: true
  		contentnegotiation: 
  			favor-parameter: true
  ```

  - 开启后，contentNegotiationManager将增加参数方式解析策略并首先遍历

- 自定义MessageConverter
  - 在WebMvcConfigurer中配置extendMessageConverters
  - 自定义一个Converter类实现HttpMessageConverter接口，将其配置到extendMessageConverters中

- 浏览器与自定义类型内容协商适配：在WebMvcConfigurer的自定义contentNegotiationManager中自定义内容协商策略
  - 自定义后contentNegotiationManager中支持参数解析，不支持基于请求头，需要再次自定义基于请求头的策略

*ps：自定义功能可能导致除自定义部分外其他部分默认配置失效*

### 视图解析与模板引擎

### thymeleaf

![image-20220306111620520](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220306111620520.png)

![image-20220306111644109](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220306111644109.png)

- 防止表单重复提交：重定向

### 视图解析与视图原理

![image-20220306153850073](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220306153850073.png)

- 目标方法处理过程中，所有数据都被放在ModelAndView中，包括数据和视图地址

- 方法参数是一个自定义类对象，把他重新放在ModelAndViewContainer

- 任何目标方法执行完成后都会返回ModelAndView（数据和视图地址）

- processDispatchResult处理派发结果（页面该如何响应）

  - render进行页面渲染逻辑
    - RedirectView渲染页面方式：重定向到另一个页面

- 视图解析方式：

  - 返回值以forward开始：

  ```java
  new InternalResourceView(forwardUrl);
  ```

  - 返回值以redirect开始：

  ```java
  new RedirectView()
  ```

  - 返回值是普通字符串：

  ```java
  new ThymeleafView()
  ```

  

### 拦截器

- 底层接口：HandlerInterceptor
- 自定义拦截器需实现上述接口，返回true放行，返回false
- 要在WebMvcConfigurer的实现类WebConfig（即自定义配置类）里面配置拦截器
- preHandler：页面渲染之前
- postHandle：页面渲染之后
- afterCompletion：
- 原理：
  - 找出能处理当前表达式的handler及handler所有组件
  - 顺序执行所有拦截器的preHandle方法

### 文件上传

- MultipartFile：文件类，可自动封装上传的文件
- Spring.Servlet.Multipart：spring文件上传相关
- 配置文件上传大小

```yaml
spring: 
	servlet: 
		multipart: 
			max-file-size=xxxMB
			max-request-size=xxxMB
```

- 原理
  - 文件上传解析器（isMultipart）判断并封装（返回MultipartHttpServletRequest）文件上传请求
  - 参数解析器解析请求中文件内容，封装成MultipartFile

### 异常处理机制

- ![image-20220306164503484](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220306164503484.png)

- ErrorMvcAutoConiguration：配置了自动异常处理规则

  - ```java
    public class DefaultErrorAttributes implements ErrorAttributes, HandlerExceptionResolver
    ```

  - 错误处理路径配置

  ```yaml
  spring: 
  	error: 
  		path=...
  ```

- 异常处理流程

  - 执行目标方法，运行期间任意异常均被catch，并用dispatchException封装

  - 进入视图解析流程processDispatchResult

  - processHandlerException，处理Handler发生的异常，返回ModelAndView mv

  - 遍历所有handlerExceptionResolver，找出能处理该异常的异常解析器

  - DefaultErrorAttributes先处理异常：将异常信息保存至request域

    - 默认没人能处理异常，将异常抛出
    - 若异常未被处理，底层将发送/error请求

    - 遍历所有ErrorViewResolver找到能解析该错误视图的解析器
    - 默认的DefaultErrorViewResolver将响应状态码作为错误页地址，由模板引擎最终响应

- 定制错误处理逻辑
  - 自定义错误页：精确度越高越优先匹配
  - **@ControllerAdvice** + **@ExceptionHandler([Exception])**处理全局异常：自定义GlobalExceptionHandler类上标注前者，方法上标注后者，参数为异常种类 
  - **@ResponseStatus** + 自定义异常：自定义异常需继承某个异常类，注解参数为响应状态码value以及原因reason，解析器将注解信息封装为ModelAndView
  - **@Order**：异常处理器优先级，参数为优先级，数越小优先级越高，注解在自定义异常处理器上
  - ErrorViewResolver实现自定义

### 原生组件注入

- 使用Servlet API
  - **@WebServlet**：导入原生Servlet组件，参数为urlPatterns：指定路径
  - **@ServletComponentScan**：扫描包，将Servlet组件注入容器，参数为basepackages包路径
  - **@WebFilter**：导入原生Servlet Filter组件（过滤器），参数为urlPatterns拦截路径
  - **@WebListener**：导入原生监听器组件

- 使用RegistrationBean
  - 在一个配置类内写返回类型为xxxRegistrationBean的方法，返回一个容器，参数为Servlet组件及其作用路径

### 嵌入式Servlet容器

略

### 定制化原理

- 原理分析

  场景starter-xxxAutoConfiguration-导入xxx组件---绑定xxxproperties/yml---绑定配置文件项

- 定制化方式

  - 修改配置文件
  - xxxCustomizer
  - 编写自定义配置类xxxConfiguration + **@Bean**替换，增加容器中默认组件，视图解析器
  - WebMvcConfigurer定制化Web功能
  - **@EnableWebMvc** + **@Configuration**全面接管SpringMVC，原官方配置全部失效

## 数据访问

### SQL

- 数据源自动配置

  - 导入依赖

    - 修改版本
      - 在pom.xml里更改版本
      - 重新声明仲裁版本

  - 分析自动配置

    - DataSourceAutoConfiguration：数据源自动配置

    - DataSourceTransactionManagerAutoConfiguration：事务管理器自动配置

    - JdbcTemplateAutoConfiguration：JdbcTemplate自动配置，用于CRUD

      ```yaml
      spring:
      	jdbc:
      		template:
      			query-timeout: xx(设置超时时间)
      ```

      

    - JndiDataSourceAutoConfiguration：Jndi自动配置

    - XADataSourceAutoConfiguration：分布式事务相关

  - 修改相关配置：spring.datasource

  ```yaml
  spring: 
  	datasource:
  		url: xxx
  		username: xxx
  		password: xxx
  		type: (数据库类型)
  		driver-class-name: (数据库驱动)
  ```

  

  - 连接池配置是在没有配置DataSource时才自动配置

- 整合Druid数据源

  - 自定义一个配置类，将新创建的数据源注入容器
  - 也可以用配置文件形式配置

   

- **整合Mybatis（重点）**

  - 导入依赖

  - 配置模式

    - 全局配置文件

    *ps:可以不写全局配置文件，mybatis相关配置放到Configuration配置项中*

    - SqlSessionFactory：已自动配置
    - SqlSession
    - Mapper
    - 经典四步：
      - 实体类pojo
      - 接口Mapper
      - Mapper配置文件xml
      - controller/service调用

  - 注解模式

    - **@Select**，**@Update**，**@Delete**， **@Insert**：对应数据库四大操作，直接注解在Mapper接口的方法上，参数为对应要执行的sql语句
    - **@Options**：可配置原来在配置文件标签处的各项参数
    - **@MapperScan**：扫描包，将包内的所有接口视为Mapper，参数为包路径

  - 混合模式：简单语句用注解，复杂语句用配置文件

- 整合Mybatis-Plus

  *ps:MybatisX插件是他们的。。。*

  - 导入依赖
    - sqlSessionFactory已配置好，底层为默认数据源
    - mapperLocations已配置好，默认为任意路径下mapper文件夹下所有mapper的.xml均为mapper配置文件
    - **@Mapper**标注的接口也会被注册，**@MapperScan**依旧适用
    - **@TableField**：标注在属性上，可以标记表中元素或排除非表中元素，参数为exist（boolean）
  - 基类BaseMapper：具备基本的CRUD功能，Mapper直接继承即可用
  - CRUD功能
    - **@TableName**：标注在实体类上，标注其对应的表，参数为表名
    - 分页查询：page对象，需添加分页插件
    - 重定向带参数：redirectAttributes

### NoSQL

- Redis

  - 导入依赖

    - RedisAutoConfiguration自动配置类，RedisProperties属性类，spring.redis.xxx是对redis的配置
    - 连接工厂已准备好
    - 自动注入RedisTemplate，StringRedisTemplate

  - 环境搭建

    - 阿里云购买redis
    - 申请公网链接
    - 这里可以直接使用本地redis

  - 配置redis

    ```yaml
    spring:
    	redis: 
    		host:
    		port:
    		password:
    		url: xxx
    ```

  - 更换连接池

    - 导入对应连接池依赖

    - 更改配置文件

      ```yaml
      spring: 
      	redis:
      		client-type: 
      ```

  - Filter与interceptor

    - filter为Servlet原生组件
    - interceptor为Spring的组件

## 单元测试

### JUnit5

- 常用注解
  - **@Test**：表示这是一个测试方法
  - **@DisplayName**：为测试方法和测试类起名
  - **@ParameterizedTest**：表示方法为参数化测试
  - **@RepeatedTest**：表示方法可重复执行，参数为次数
  - **@BeforeEach**：表示在每个测试单元之前执行
  - **@AfterEach**：表示在每个单元之后进行
  - **@beforeAll**：表示在所有单元测试之前进行
  - **@AfterAll**：表示在所有单元测试之后进行
  - **@Tag**：表示单元测试类别，类似于Junit4**中的@Catagories**
  - **@Disabled**：表示测试类或测试方法不执行，类似Junit4中的**@Ignore**
  - **@Timeout**：表示测试方法运行超过指定时间将报错
  - **@EXtendWith**：为测试类或测试方法提供扩展类引用

- 断言assert

  - 简单断言

    ![image-20220307160659183](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220307160659183.png)

  - 断言失败，断言后的部分不会执行

  - 组合断言

    - assertAll：参数为断言名以及断言数组，断言数组用函数式编程传参() -> [assert]

  - 异常断言

    - assertThrows：参数为异常种类，断言以及信息，断言为函数式编程

  - 快速失败：fail

- 前置条件assumption
  - 前置条件满足的情况下，该测试方法才会运行，否则将跳过不报错

- 嵌套测试
  - **@Nested**：表示这是个嵌套测试
  - 嵌套测试情况下，外层测试无法驱动内层的**@beforeEach，@After Each，@BeforeAll，@AfterAll**
  - 但内层的测试可以驱动外层的**@beforeEach，@After Each，@BeforeAll，@AfterAll**
- 参数化测试
  - **@ValueSource**：为参数化测试指定入参来源，支持八大基础类型以及String，class
  - **@NullSource**：为参数化测试提供一个null的入参
  - **@EnumSource**：为参数化测试提供一个枚举入参
  - **@CsvFileSource**：读取指定csv文件作为参数化测试入参
  - **@MethodSource**：读取指定方法返回值（必须是个流）作为参数化测试入参
  - **@ParameterizedTest**：表示这是个参数化测试 

## 指标监控

### SpringBoot Actuator

- 使用

  - 导入依赖

  - 查看EndPoint（查官网）

  - 配置文件编写

    ```yaml
    management:
    	endpoints:
    		enabled-by-default: true #默认开启所有监控端点
    		web:
    			exposure: 
    				include: '*' #以Web方式暴露所有端点
    ```

  - 常用EndPoint：

    - Health：定时检查应用健康状况
    - Metrics：提供详细，层级，空间的=指标信息
      - 对接多种监控系统
    - Loggers

  - 对每个EndPoint进行详细配置：

    ```yaml
    management:
    	endpoint:
    		health:
    			show-details: always
    		[endpoint]:
    			....
    		
    ```

- 自定义EndPoint

  - 自定义HealthIndicator

    - 创建一个类继承AbstractHealthIndicator
    - 在里面重写检查方法

  - 改写info

    - 直接在配置文件内改写

      ```yaml
      info:
      	appName:
      	appVersion:
      	mavenProjectName:
      	mavenProjectVersion:
      ```

    - 创建一个类实现InfoContributor接口，在里面重写方法

  - 定制Metrics

  - 自定义EndPoint：

    - **@Component + @EndPoint**：标注在类上，表示这是个监控端点，后者参数为端点名
    - **@ReadOperation**：监控端点读方法
    - **@WriteOperation**：监控端点写方法

- Spring-Boot-Admin：可视化监控界面

  - 导入依赖

  - **@EnableAdminServer**：启动监控服务

  - 在需要监控的客户端引入另一对应依赖（文档）

  - 在客户端配置文件添加参数

    ```yaml
    spring:
    	boot:
    		admin:
    			client:
    				url: [adminUrl]
    				instance: 
    				prefer-ip: true #使用当前ip注册
    	application: 
    		name: xxx #为本服务取名
    ```

## 高级特性

### Profile功能（多环境适配）

- **@Value**：获取动态配置文件的值
- 对不同环境，SpringBoot使用不同的配置文件

- 环境配置文件格式：application-xxx.properties/yaml，xxx为环境缩写

  - prod：生产环境
  - test：测试环境
  - dev：开发环境
  - 其余名字可自取

- 也可在默认配置文件加载环境

  ```yaml
  spring:
  	profiles:
  		active: prod/test/dev
  ```

- 命令行激活：可修改配置文件任一值，命令行优先

- **@ConfigurationProperties**：将配置文件中的值配置到该类属性中并注入到容器，参数为配置文件中对象名

- **@Profile**：指定类或方法的生效环境，参数为环境名

- profile分组

  ```yaml
  spring:
  	profiles:
  		group:
  			[组名][下标]:[配置文件名]
  ```

  可一次加载一组（多个）配置文件

### 外部化配置

- 外部配置源
  - 命令行
  - yaml
  - properties
  - 环境变量

- 配置文件查找顺序

  - classpath根路径
  - classpath下config目录
  - jar包当前目录
  - jar包当前目录的config目录
  - jar包当前目录的config目录的子目录
  
  *ps：后者会覆盖前者相同配置*
  
- 配置文件加载顺序

  - 当前jar内application.properties/yaml
  - 当前jar内application-{profile}.properties/yaml
  - 引用jar的application.properties/yaml
  - 引用jar的application.{profiles}.properties/yaml

- 自定义starter
  - 编写starter，只引入依赖不写代码
  - 编写starter-autoconfigure，将真正的代码写在这里
  - 创建META-INF文件夹，新建spring.factories文件，将自定义配置加入

## SpringBoot原理

### SpringBoot启动过程

