[SpringBoot2核心技术与响应式编程 · 语雀 ](https://www.yuque.com/atguigu/springboot)

Documentation Overview：文档的概述

getting started：springboot入门

upgrading spring boot applications：从1升级。x，升级到一个新特性，并升级h命令行接口

Using spring boot：使用springboot，一些基本的配置原理



spring boot features：多环境配置，日志，安全，缓存等

spring boot actuator：与生产对接的监控模块

Depioying spring boot application：springboot平台部署

spring boot cli：用命令行初始化项目（基本没用）

Build tool plugins：运行期间构建maven插件

“How-to”Guldes：spring boot的小技巧

## Spring Boot中重要的类与方法

1. ApplicationContext：表示ioc容器类，表示一个上下文对象
2. UrlPathHelper：这个类是路径的帮助器，比如解析矩阵变量

## spring boot中的注释

### 测试Jubit5注解

http://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations

@Test:表示方法是测试方法，但是与JUnit4不同@Test，他的职责非常单一不能生命任何的属性，拓展的测试将会由Jupiter提供额外的测试

@RepeatedTest :表示方法可重复执行，下方会有详细介绍

@DisplayName :为测试类或者测试方法设置展示名称

@BeforeEach :表示在每个单元测试之前执行

@AfterEach :表示在每个单元测试之后执行

@BeforeAll :表示在所有单元测试之前执行（是静态方法）

@AfterAll :表示在所有单元测试之后执行

@Tag :表示单元测试类别，类似于JUnit4中的@Categories

@Disabled :表示测试类或测试方法不执行，类似于JUnit4中的@Ignore

@Timeout :表示测试方法运行如果超过了指定时间将会返回错误

@ExtendWith :为测试类或测试方法提供扩展类引用、

@RepeatedTest：重发测试几次

@SpringbootTest：表示是一个springboot的测试类

### 注册JavaBean

@Bean：表方法是配置的组件
@Component：表示该类是一个组件
@Controller：配置controller组件
@Service：配置service组件
@Repository：配置dao层组件
@ComponentScan：配置组件的扫描路径，默认配置在主程序类中
@Import：导入相应的组件@Import({User.class, DBHelper.class})。可以写在配置类中。也可已是一个继承importSelector的类，重写方法返回的全类名都能注册成组件。也可已是一个继承importBeanDefinitionRegistrar的类，重写方法把自己定义的bean注册，注册前可以判段是否有某个组件，再注册
@Conditional：条件装配，满足Conditional指定的条件（比如判断有某个组件就注入组件），则进行组件注入
@ImportResource("classpath:beans.xml")：表示导入一个配置文件的资源
### 配置绑定
@configurationProperties(prefix = "mycar")：表示把bean与配置文件的中的字段一一绑定，prefix表示配置前缀
@EnableConfigurationProperties(Car.class)：1、开启Car配置绑定功能、把这个Car这个组件自动注册到容器中public class MyConfig {}


### 请求映射的
@RequestMapping：表示映射请求。
@GetMapping：映射get请求。
@PostMapping：映射post请求
@PutMapping：映射put请求

#### 请求参数的处理
@PathVariable：获取rest风格的路径参数
@RequestHeader：获取请求头信息
@RequestParam：获取请求参数
@CookieValue：获取cookie信息

@RequestPart("file")MultipartFile headerImg：用于文件上传的方法

@RequestBody：获取请求体的数据

![](G:\java\java知识\6SpringBoot2\图片\Snipaste_2022-06-07_20-54-41.png)

@MatrixVariable("low")：取出矩阵变量的值，springboot默认禁用了矩阵变量。需要再配置类中开启。有如下两种配置方法

```java
@Configuration(proxyBeanMethods = false)
public class WebConfig /*implements WebMvcConfigurer */{
//    @Override
//    public void configurePathMatch(PathMatchConfigurer configurer) {
//        UrlPathHelper urlPathHelper = new UrlPathHelper();
////        不移除;号后面的内容，据政府变量功能就能实现
//        urlPathHelper.setRemoveSemicolonContent(false);
//        configurer.setUrlPathHelper(urlPathHelper);
//    }


@Bean
public WebMvcConfigurer webMvcConfigurer(){
    return new WebMvcConfigurer() {
        @Override
        public void configurePathMatch(PathMatchConfigurer configurer) {
            UrlPathHelper urlPathHelper = new UrlPathHelper();

////        不移除;号后面的内容，据政府变量功能就能实现
                urlPathHelper.setRemoveSemicolonContent(false);
                configurer.setUrlPathHelper(urlPathHelper);
            }
        };
    }
}
```

进行解析

```java
@RequestMapping("/{path}")
public Map carsSell(
    //pathVar = "path"表示那个路径的值
        @MatrixVariable(value = "low", pathVar = "path") Integer low, @MatrixVariable("brand")
        List<String> brand
){
    HashMap<String, Object> map = new HashMap<>();
    map.put("low", low);
    map.put("brand", brand);
    return map;
}
```


#### 获取request域中的参数
@RequestAttribute("msg")：获取request参数

@Configuration：表示对象是一个配置类
	proxyBeanMethods默认是true，当通过配置类调用方法时，调用的时代里对象的方法，会检查是否有相应的组件，如果有返回组件
    proxyBeanMethods是false时，不会调用代理对象，直接返回新的实例


## 编写一个hello word

[Spring Initializr](https://start.spring.io/)：创建项目的地址

https://start.springboot.io：一般使用这个连接初始化项目，比较稳定

## 配置文件

springboot也支持yaml配置文件，详细请看语雀

## web开发

### 参数解析器解析参数

request参数：是由servletRequestMethodArgumentResolver参数解析器解析的

把请求参数封装成对象：是可以自动封装的

## 指标监控

引入依赖以后只要访问http://localhost:8080/actuator/**。都是统计的数据



