# SpringMVC

### MVC架构模式

- M：模型（dao（mapper），service）

- V：视图（jsp）
- C：控制器（Servlet）

- 实体类：pojo
- 视图层实体类：vo

### Servlet

- 编写servlet类继承HttpServlet
- 重写doGet和doPost
  - 获取前端参数
  - 调用业务层
  - 视图转发或重定向
- xml配置servlet

### SpringMVC

![image-20220303150202333](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303150202333.png)

![image-20220303151100171](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303151100171.png)

### 注解

- **@Controller**：声明Spring实体类为控制器，解析用户请求并封装成一个**model**，返回字符串即为跳转路径，会被视图解析器解析

- **@RestController**：不会被视图解析器解析，返回的字符串就是字符串 = **@Controller + @ResponseBody**
- **@Service**：service
- **@Repository**：dao

- **@RequestMapping(value=[path],method=[method])**：接受来自[path]的[method]方法请求
- **@PathVariable**：注解在变量上，表示路径变量。在@RequestMapping后的路径内以/{varname}表示。
- **@[method]Mapping([url])**：接受来自[url]的[method]方法请求。
- **@RequestParam([name])**：标注在变量前，表示从前端接收在前端名为[name]的数据
- **@ResponseBody**：不走视图解析器，直接返回字符串

### RestFul风格

无论路径或参数，url均以斜线分隔

### 结果跳转方式

在controller的return内定义

- "/[url]"||"forward:[url]"：转发，有视图解析器时默认转发
- "redirect:[url]"：重定向

### 接收请求参数并回显

- controller内部步骤

  - 接收前端参数
    - 若名字在方法参数上，则直接由该参数接受
    - 若方法参数为对象，则前端参数名会与对象属性名作对应

  - 将返回的结果传递给前端
    - Model：精简版ModelMap
    - ModelMap：继承LinkedHashMap

### 解决乱码：过滤器

在web.xml中配置SpringmMVC的乱码过滤器

![image-20220303163013181](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303163013181.png)

还有可能是tomcat的问题

### json（前后端分离重点之一）

一种数据交换格式

- 键值对表示对象
- {}表示对象
- []表示数组

解析工具（需导入maven依赖）：

- jackson：下面代码将对象转换为json

```java
ObjectMapper mapper = new ObjectMapper()
    String str = mapper.writeValueAsString([object]);
```

- 乱码解决配置（固定代码）：

![image-20220303165102682](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303165102682.png)

- 可将其抽象为一个工具类

- Fastjson：阿里的json解析工具

### Ajax（异步无刷新请求，前后端分离关键之一）

不刷新网页的情况下实现局部更新，将主动权交给前端

ps：跟同一网页多教学资源并行需求可能有关

- html的iframe标签可制作伪ajax 

- 工具：jQuery
- 需要配置xml：静态资源过滤

![image-20220303173517125](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303173517125.png)

### 拦截器

对请求进行预处理，核心处理和后处理

- 自定义拦截器需继承HandlerInterceptor接口

- ```java
  return true//放行
  return false//拦截
  ```

  

- 需配置applicaotionContext.xml

![image-20220303175914496](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303175914496.png)

### 文件上传与下载

- 导入依赖

![image-20220303181849293](C:\Users\愚生\AppData\Roaming\Typora\typora-user-images\image-20220303181849293.png)

