# Springboot的搭配组件

# SpringSecurity（安全）

- 设计之初就要确定安全性

- 功能：认证，授权，代替原拦截器，过滤器的作用

  *ps.与学生，教师，管理员多角色可能有关*

- 权限：
  - 功能权限
  - 访问权限
  - 菜单权限

- 重点类

  ![image-20220303233733789](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303233733789.png)

- 操作（官方文档）：

  - 添加配置类，继承对应接口

  ![image-20220303235007436](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303235007436.png)

  - 撰写授权规则

  ![image-20220303235025907](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303235025907.png)

  - 开启登陆页面（自动生成/login页面）
  
  - ![image-20220303235049216](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303235049216.png)
  
  - 撰写认证规则
    - 密码需要加密
    
    ![image-20220303235309203](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303235309203.png)
    
  - 注销
  
  ![image-20220304090352919](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304090352919.png)
  
  - 记住我
  
  ![image-20220304091532565](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304091532565.png)

 

# Shiro（安全）

- 架构

![image-20220304110904669](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304110904669.png)

![image-20220304110913441](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304110913441.png)

- 三大对象
  - subject：用户
  - SecurityManager：管理所有用户
  - Realm：连接数据

- subject分析

  - 获取用户对象subject
  - 拿到当前用户session

  ![image-20220304112004118](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304112004118.png)

  - 测试当前用户是否被认证
  - 。。。在shiro quickstart源码中详细解说了

- SpringBoot整合

  - 导入依赖

  - 创建三大对象：逆序构造

    - Realm

    ![image-20220304113844148](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304113844148.png)

    - SecurityManager
    - Subject

  - 通过配置类将三大对象注入容器

  ![image-20220304114036317](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304114036317.png)

*ps:几个好用的模板网站用于练习：layui，bootstrap，模板之家*

- 登录拦截

  - 添加内置过滤器
  - 设置登录请求

  ![image-20220305083315369](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305083315369.png)

- 设置认证

  - 获取当前用户
  - 封装用户登录数据

  - 执行登录方法，捕获异常

  ![image-20220305084151013](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305084151013.png)

  - 改写realm中认证方法

  ![image-20220305084229327](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305084229327.png)

- 整合mybatis

  - 配置文件yml编写
  - 实体类构建pojo
  - 接口构建mapper
  - 编写mapper对应xml
  - 编写service接口
  - 编写service实现类
  - shiro连接真实数据库（调用service接口）

  ![image-20220305084926555](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305084926555.png)

- 设置授权

![image-20220305090013816](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305090013816.png)

![image-20220305090413010](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305090413010.png)

- 授权与认证部分数据传输

![image-20220305090335527](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220305090335527.png)

- 整合thymeleaf
  - 导入依赖
  - 改编realm

# Swagger（前后端分离重点之一）

### 前后端分离

- 后端时代：前端只用静态页面，html==> 后端。模板引擎
- 前后端分离时代
  - 后端：控制层，服务层，数据库访问层
  - 前端：前端控制层，视图层
    - 伪造后端数据——>无后端前端照样能跑
  - 前端后端交互：API
  - 问题：前后端沟通
  - 解决方案：实时更新API文档
    - 前端实时监测后端接口
    - 后端实时更新接口

### 作用

- API文档与API定义同时跟新，在线测试API接口，后端可以脱离前端进行接口测试
- 官网很重要

### SpringBoot集成Swagger2

- 导入依赖

  - swagger2
  - swaggerui

- 配置对应的config

  - 配置作者信息

  ![image-20220304100542423](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304100542423.png)

  - 配置扫描接口

    ![image-20220304101154796](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304101154796.png)

  - 配置是否启动Swagger

  ![image-20220304101311717](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304101311717.png)

  - 多环境配置Swagger（发布环境需关闭Swagger）

  ![image-20220304101739975](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304101739975.png)

  - 配置多个分组（多个Docket实例）

  ![image-20220304102112907](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304102112907.png)

  - 注解注释（API）
    - **@ApiModel**：给实体类添加注释
    - **@APiModelProperty**：给属性添加注释
    - **@ApiOperation**：给方法增加注释
    - **@ApiParam**：给方法参数加注释
    - **@Api(tags="[string]")**：添加内容为[string]的注释

*swaggerui.html即为API接口页面*

![image-20220304095946436](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304095946436.png)

# Redis

- 导入依赖
- 配置Redis

![image-20220304104213653](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304104213653.png)

- 测试
  - 所有对象需要序列化才能正常保存到Redis中

![image-20220304104653123](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304104653123.png)

- 自定义Redis Template

![image-20220304105651867](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304105651867.png)

![image-20220304105716053](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220304105716053.png)

- 编写工具类（开发常用）

