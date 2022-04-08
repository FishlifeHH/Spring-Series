# SpringCloud

![image-20220320195344089](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320195344089.png)

![image-20220313212748487](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220313212748487.png)

![image-20220320175607400](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320175607400.png)

## 微服务远程调用

- 通过微服务模块之间发送http请求实现
- 利用RestTemplate实现模块发送http请求
- 提供者与消费者
  - 提供者：被调用服务的服务
  - 消费者：调用服务的服务

## Nacos

- 负责服务注册与发现，职责同Eureka

- 服务地址默认8848
- 服务分级存储模型：服务--->集群--->实例
- 配置集群：配置文件中cluster-name，优先选择同级群服务

```yaml
spring:
	cloud:
		nacos:
			discovery:
				cluster-name:xxx
```

- 负载均衡
  - 按权重负载均衡：直接在nacos客户端调节，权重越高访问越频繁。

![image-20220320160309033](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320160309033.png)

- 环境隔离 - namespace命名空间

  - 控制台新建命名空间
  - 配置文件处修改命名空间

  ![image-20220320161245951](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320161245951.png)

### Nacos配置管理

- 实现配置统一管理

  - dataId格式`${prefix}-${spring.profile.active}.${file-extension}`
    - `prefix`默认为`spring.application.name`的值，也可通过配置项`spring.cloud.nacos.config.prefix`来配置。
    - `spring.profile.active`即为当前环境对应的profile。当`spring.profile.active`不存在时，对应连接符将消失，dataId格式变为`${prefix}.${file-extension}`
    - `file-extension`为配置内容的数据格式，可通过配置项`spring.cloud.nacos.config.file-extension`来配置。目前只支持`properties`和`yaml`。

  - 在控制端填写配置，将需要热更新的配置写上nacos

  ![image-20220320161932140](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320161932140.png)

  - 优先级bootstrap.yml > application.yml，nacos地址放在前者

  - 引入依赖
  - 编写bootstrap.yml

  ![image-20220320162203475](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320162203475.png)

- 实现配置热更新

  - **@RefreshScope**：加在有**@value**注解的类上，实现热更新
  - **@ConfigurationProperties**：用其注入配置，可直接实现自动更新

- 多环境配置共享
  - 控制端写共享配置文件，不需要后缀
  - 代码改写配置类
  - 多配置文件冲突时，优先级共享带环境>共享不带环境>本地
  
- NameSpace，Group和dataId
  
  - 类似Java的package层次关系
  - NameSpace区分环境，NameSpace>group>dataId
  
- 集群搭建
  - 配置nacos
  - 分别启动
  - nginx配置
  - 更改配置文件nacos地址

![image-20220320164846841](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320164846841.png)

## Feign（替代RestTemplate）

- 声明式http客户端，实现http请求发送

- 定义与使用

  - 引入依赖

  - 编写Feign客户端

    - **@FeignClient**：标注在接口上声明feign客户端，后面参数为服务名称

    - 接口内方法格式同SpringMVC（Mapper语法）

      ![image-20220320170026203](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320170026203.png)

    - 顺带集成Ribbon，实现负载均衡

- 自定义Feign配置

  ![image-20220320170145917](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320170145917.png)

  - 配置方式：配置文件

    - 全局生效

      ```yaml
      feign:
       client:
        config:
         default:
          loggerLevel: xxx
      ```

    - 局部生效

      ```yaml
      feign:
       client:
        config:
         userservice:
          loggerLevel: xxx
      ```

  - 配置方式：配置类

    ![image-20220320170659861](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320170659861.png)

    

- 性能优化

  - 底层实现：

    - URLConnection：默认，不支持实现
    - Apache HttpClient/OKHttp：支持连接池

  - 优化实现

    - 引入依赖

    - 配置连接池

      ![image-20220320171001446](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320171001446.png)

      

- 最佳实践

  - 继承：给消费者的FeignClient和提供者的Controller提供一个统一接口（API）（不推荐，导致紧耦合）

  - 抽取：将FeignClient抽取为独立模块，把接口相关pojo，默认Feign配置放入该模块，提供给所有消费者使用

    ![image-20220320172158292](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320172158292.png)

    - 当SpringBootApplication扫不到Feign所在的包时

      ![image-20220320172732155](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320172732155.png)

## Zuul（网关）

### 网关

- 功能
  - 身份认证与权限校验
  - 服务路由（发请求获取数据库信息）
  - 负载均衡
  - 请求限流

- 技术实现

  - zuul
  - gateway

- 代码实现

  - 编写配置文件

    ![image-20220320180149519](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320180149519.png)

  - **@EnableZuulProxy**：开启Zuul代理，加在启动类上

  - 配置路由：隐藏原url内的项目信息

    ![image-20220320180932411](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320180932411.png)

    

## Elastic search（开源搜索引擎）

### 倒排索引

- 形成词条-文档表
  - 文档：一条数据就是一个文档
  - 词条：文档按照语义分成的词语
- 过程：根据词条找id，根据id找数据

### 概念

- 面向文档存储：以json格式存储

- 索引：相同类型文档的集合

- 映射：索引中文档的字段约束

  ![image-20220320174510668](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220320174510668.png)

- mysql负责数据存储，es负责数据搜索，二者需要做数据同步

### 索引库操作

- mapping映射属性

  - type：字段数据类型
    - 字符串：text（可分词文本），keyword（精确值）
    - 数值：long，integer，short，byte，double，float
    - 布尔：boolean
    - 日期：date
    - 对象：Object
  - index：是否创建（倒排）索引，默认true
  - analyzer：分词器
  - properties：子字段

- 创建索引库（通过Restful请求）

  ![image-20220405175903893](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220405175903893.png)

  

## Docker（部署用）

### 基本概念

- 镜像：Docker将应用程序及所需依赖，函数库，环境，配置等文件打包在一起，称为镜像，一定只读。
- 容器：镜像中应用程序运行后的形成的进程
- Docker Registry：镜像托管平台

### 架构

- 服务端server：Docker守护进程，处理docker指令，管理镜像，容器
- 客户端client：通过命令或RestAPI向Docker服务端发送指令。

![image-20220405203431805](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220405203431805.png)