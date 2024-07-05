## SpringMVC介绍

---

springMVC的九大组件

![](.\图片\20181123095802126.jpg)

SSM项目是：SpringMVC，Spring，Mybatis

1、什么是MVC

![](.\图片\Snipaste_2022-01-09_22-10-42.png)

2、什么是SpringMVC

![](.\图片\Snipaste_2022-01-09_22-16-57.png)

3、SpringMVC的特点

![](.\图片\Snipaste_2022-01-09_22-25-05.png)

##  HelloWord

---

### 配置web.xml

![](.\图片\Snipaste_2022-01-09_23-00-45.png)

![](.\图片\Snipaste_2022-01-10_10-22-10.png)

### 创建请求控制器

![](.\图片\Snipaste_2022-01-10_10-46-41.png)

### SpringMVC配置文件

```xml
<!--扫描组件-->
<context:component-scan base-package="com.kjq.MVC.controller"/>
<!--配置视图解析器-->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <!--配置优先级-->
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!--视图前缀-->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!--试图后缀-->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8"/>
                </bean>
            </property>
        </bean>
    </property>
</bean>
```

### 测试HelloWorld

1、实现对首页的访问

2、通过超链接跳转到指定页面

![](.\图片\Snipaste_2022-01-10_10-54-18.png)

流程：浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。前端控制器会读取SpringMVC核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，处理请求的方法需要返回一个字符串类型的视图名称，视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到试图所对应页面

## @RequestMapping注解

### @RequestMapping注解的功能

![](.\图片\Snipaste_2022-01-10_12-49-48.png)

不能有两个相同的请求地址

### @RequestMapping注解的位置

可以写在类上和方法上

![](.\图片\Snipaste_2022-01-10_13-59-51.png)

### @RequestMapping注解的value属性

value可以是一个数组，表示只要请求满足数组中的任何一个。就能匹配到当前控制器

![](.\图片\Snipaste_2022-01-10_14-09-13.png)

### @RequestMapping注解的method属性

GET, HEAD, POST, PUT, PATCH, DELETE, OPTIONS, TRACE;

![](.\图片\Snipaste_2022-01-10_14-18-46.png)

如果不设置method那么，所有的请求类型都能请求得到

method是一个数组可以设置多个，只要有一个请求满足就能请求得到

![](.\图片\Snipaste_2022-01-10_14-30-19.png)

### @RequestMapping注解的params属性（了解）

根据请求参数去匹配请求，需要同时满足请求参数才能找到对应的请求

![](.\图片\Snipaste_2022-01-10_14-52-59.png)

![](.\图片\Snipaste_2022-01-10_15-03-08.png)

### @RequestMapping注解的headers属性（了解）

![](.\图片\Snipaste_2022-01-10_15-04-39.png)

### SpringMVC支持ant风格的路径

模糊匹配

![](.\图片\Snipaste_2022-01-10_15-13-33.png)

#RequestMapping("/a?*a/test")。注意两个星前后是不能添加东西的

### SpringMVC支持路径中的点位符（重点）

![](.\图片\Snipaste_2022-01-10_15-22-42.png)

```java
@RequestMapping("/testPath/{id}")
public String testPath(@PathVariable("id")Integer id) {
    System.out.println(id);
    return "success";
}
```

## SpringMVC获取请求参数

----

### 通过servletAPI获取

![](.\图片\Snipaste_2022-01-10_16-25-22.png)

```java
@RequestMapping(value = "/param/test")
public String param(HttpServletRequest request) {
    String id = request.getParameter("id");
    String username = request.getParameter("username");
    System.out.println(id + username);
    return "test_param";
}
```

### 通过控制器方法的形参获取请求参数

![](.\图片\Snipaste_2022-01-10_16-52-48.png)

```java
@RequestMapping(value = "/testParam")
//当传入的参数id值有多个的时候，可以用数组进行获取
public String param2(Integer[] id, String username) {
    System.out.println(id + username);
    return "test_param";
}
```

这种方式传入参数必须，形参参数名与传入的请求参数的参数名保持一致

当传入空的参数也可以

### @RequestParam

当我们想不让形参参数与请求参数相同，可以使用以上注解

```java
RequestParam//注解的内部
@AliasFor("name")
String value() default "";//表示以名称查找

@AliasFor("value")
String name() default "";//表示以值查找

boolean required() default true;//当此值为true表示必须有没有就报错

String defaultValue() default "\n\t\t\n\t\t\n\ue000\ue001\ue002\n\t\t\t\t\n";//表示不穿参数和传入一个空的字符串就给一个默认值
```

```java
@RequestMapping(value = "/testParam")
//默认情况@RequestParam("username")表示username你必须传输，如果不传就报错
public String param2(Integer id, @RequestParam("username")String name) {
    System.out.println(id + name);
    return "test_param";
}
```

![](.\图片\Snipaste_2022-01-10_17-10-17.png)

### @RequestHeader

获取请求头信息。想要获取请求头信息就必须使用

这个注解和上面的注解参数一样

```java
@RequestMapping(value = "/testParam1")
public String param3(@RequestHeader("Host")String username) {
    System.out.println("===============================");
    System.out.println(username);
    return "test_param";
}
```

### @CookieValue

这个注解和上面的注解参数一样

```java
@RequestMapping(value = "/testParam2")
public String param3(@CookieValue("JSESSIONID")String name) {
    System.out.println("===============================");
    System.out.println(name);
    return "test_param";
}
```

### 通过POJO获取请求参数

使用实体类接收请求参数

![](.\图片\Snipaste_2022-01-10_17-33-32.png)

### 解决获取请求参数的乱码问题

只有在请求参数之前设置编码才有效

因为DispatcherServlet已经获取到了请求参数，所以不能用request修改编码。只能用filter过滤器来设置编码。

```xml
<!--配置filter过滤器。然后配置字符编码过滤器-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <!--设置请求的编码-->
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <!--设置相应的编码-->
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## 域对象共享数据

---

### 使用ServletAPI向request域对象共享数据

最原始的方法

![](.\图片\Snipaste_2022-01-10_18-59-59.png)

在thymeleaf中只有（th:属性）才能被解析到

```html
<p th:text="${testRequest}"></p>
```

### 使用ModelAndView向request与对象共享数据(建议使用)

![](.\图片\Snipaste_2022-01-10_19-23-37.png)

必须作为方法的返回值返回

### 使用Model向request域对象通向数据

![](.\图片\Snipaste_2022-01-10_19-34-00.png)

### map向request域对象共享数据

```java
@RequestMapping("/testMap")
public String testMap(Map<String, Object> map){
    map.put("testRequest", "kdf");
    return "success";
}
```

### 使用ModelMap向request域对象共享数据

![](.\图片\Snipaste_2022-01-10_19-40-24.png)

```java
@RequestMapping("/testModelMap")
public String testModelMap(ModelMap modelMap) {
    modelMap.addAttribute("testRequest", "lc");
    return "success";
}
```

### Midel、ModelMap、Map的关系

![](.\图片\Snipaste_2022-01-10_19-50-36.png)

### 向session域共享数据

建议使用

![](.\图片\Snipaste_2022-01-10_20-07-26.png)

```xml
<p th:text="${session.testSession}"></p>
```

### 向application域共享数据(serlvetContext)

![](.\图片\Snipaste_2022-01-10_20-13-39.png)

```html
<p th:text="${application.testApplication}"></p>
```

## SpringMVC视图

---

![](.\图片\Snipaste_2022-01-10_20-50-11.png)

### ThymeleafView

![](.\图片\Snipaste_2022-01-10_20-56-46.png)

### 转发视图

![](.\图片\Snipaste_2022-01-10_20-57-32.png)

转发视图叫InternalResourceView

```java
//使用thymeleaf视图解析器解析的叫做ThymeleafView
//普通的jsp，转发如下创建的是internalResourceView
@RequestMapping("/testApplication")
public String testApplication(HttpSession session) {
    ServletContext application = session.getServletContext();
    application.setAttribute("testApplication", "234");
    return "success";
}

@RequestMapping("/test02")
///domain/testApplication这个路径是工程名后面的内容
public String test02() {
    return "forward:/domain/testApplication";
}
```

### 重定向视图

重定向的视图叫做RedlrectView

![](.\图片\Snipaste_2022-01-10_21-12-24.png)

```java
@RequestMapping("/testApplication")
public String testApplication(HttpSession session) {
    ServletContext application = session.getServletContext();
    application.setAttribute("testApplication", "234");
    return "success";
}

@RequestMapping("/test02")
public String test02() {
    return "redirect:/domain/testApplication";
}
```

### 视图控制器view-controller

在springMVC配置文件中配置如下内容

​	只有当请求页面中没有任何的页面跳转才这样使用

```xml
<mvc:view-controller path="/" view-name="index2"></mvc:view-controller>
```

http://localhost:8080/springMVC_demo2/就能访问得到

如果我们配置view-controller标签以后，那么控制器中所有的请求映射将全部失效。只有添加如下注解驱动，才会生效。

```xml
<!--开启mvc的注解驱动-->
<mvc:annotation-driven/>
```

![](.\图片\Snipaste_2022-01-10_21-52-57.png)

### jsp视图springMVC

jsp的解析也是用InternalResourceView的

![](.\图片\Snipaste_2022-01-10_22-10-15.png)

## RESTFul

---

是一种软件架构的风格

### RESTFul介绍

![](.\图片\Snipaste_2022-01-10_22-17-31.png)

### RESTFul的实现

![](.\图片\Snipaste_2022-01-11_10-10-24.png)

在web.xml中配置获取隐藏域参数过滤器，进行判断，如果是post请求而且传入_method与PUT的参数。可以被过滤器先劫获，然后判断，再进行把put请求传入request中，发送给控制器

```xml
<!--配置HiddenHttpMethodFilter-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

下面是html代码

```html
<form th:action="@{/user}" method="post">
    <input type="hidden" name="_method" value="put">
    用户名：<input name="username" type="text"/><br/>
    密码：<input name="password" type="password"/><br/>
    <input type="submit" value="修改"><br/>
</form>
```

### HiddenHttpMethodFilter

当有多个过滤器时，默认先执行靠前的过滤器。**所以应该把请求配置乱码的过滤器配置前面**，应为HiddenHttpMethodFilter已经获取了请求参数。获取编码之前不能获取任何的请求参数



在springMVC中配置这样的一个文件，表示对静态资源的访问

​	因为静态资源不能被springMVC处理。先被springMVC前端控制器处理，找不到相关的请求映射，就会交给DefaultServlet处理。

![](.\图片\Snipaste_2022-01-11_12-37-07.png)

上面的要与如下一起使用

![](.\图片\Snipaste_2022-01-11_13-57-42.png)

## HttpMessageConverter

---

![](.\图片\Snipaste_2022-01-11_14-06-29.png)

### @RequestBody

![](.\图片\Snipaste_2022-01-11_14-14-34.png)

### RequestEntity

![](.\图片\Snipaste_2022-01-11_14-22-21.png)

### @RespondeBody

![](.\图片\Snipaste_2022-01-11_14-25-57.png)

return后面的表示响应体，没有RespondeBody注解，会被视图解析器解析

### SpringMVC处理json

浏览器不能接收对象数据。只能转换为json再传入浏览器。需要加入jar包来处理json

![](.\图片\Snipaste_2022-01-11_16-08-33.png)

如果是{}就是json对象，如果是[]就是json数组

![](.\图片\Snipaste_2022-01-11_16-17-30.png)

```java
@RequestMapping(value = "/json1")
@ResponseBody
public User json1() {
    return new User("njig");
}
```

### SpringMVC处理ajax

处理ajax只能用响应流数据，不能用重定向或转发

![](.\图片\Snipaste_2022-01-11_16-46-15.png)

![](.\图片\Snipaste_2022-01-11_16-46-51.png)

### @RestController注解

![](.\图片\Snipaste_2022-01-11_16-47-25.png)

### ResponseEntity

![](.\图片\Snipaste_2022-01-11_16-49-07.png)

自定义的一个响应报文

## 文件上传和下载

---

### 文件下载  

![](.\图片\Snipaste_2022-01-11_17-43-11.png)

### 文件上传

加入依赖：

```xml
<!--上传文件的jar包-->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

为了能让Spring上传的文件转换成MultipartFile，需要在SpringMVC配置文件中配置一个文件上传解析器

```xml
<!--配置文件上传解析器，将上传的文件封装成MultipartFile-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    
</bean>
```

```html
<form th:action="@{/testUp}" method="post" enctype="multipart/form-data">
    头像：<input type="file" name="photo"><br/>
    <input type="submit" value="上传"/>
</form>
```

```java
@RequestMapping(value = "/testUp")
    //对上传的文件进行了封装
    public String testUp(MultipartFile photo, HttpSession session) throws IOException {
        //获取上传文件的名称
        String filename = photo.getOriginalFilename();
        
        //获取文件名的后缀
        String suffixName = filename.substring(filename.lastIndexOf("."));
        //将UUID作为文件名
        String s = UUID.randomUUID().toString();
        //将UUID和后缀名拼接后作为最终的结果
        filename = s + suffixName;
        
        ServletContext servletContext = session.getServletContext();
        //获取将要上传文件的路径
        String photoPath = servletContext.getRealPath("photo");
        File file = new File(photoPath);
        //判断路径不存在，就创建
        if (!file.exists()){
            file.mkdir();
        }
        //File.separator表示分割符
        String finalPath = photoPath + File.separator + filename;
        photo.transferTo(new File(finalPath));
        return "success";
    }
```

上述代码只会把代码覆盖掉。最好把文件名修改了

## 拦截器

---

springmvc的拦截器主要作用也是在服务端真正处理请求前后进行一些相关的操作。 例如初始化资源，权限监控，会话设置，菜单获取，资源清理等

### 拦截器的配置

![](.\图片\Snipaste_2022-01-11_18-58-23.png)

需要在springMVC配置文件中进行配置，才能让springMVC认识拦截器

```xml
<!--配置拦截器-->
<mvc:interceptors>
    
    <!--可以拦截所有请求-->
    <bean class="拦截器的类"></bean>
    
    <ref bean="拦截器对象"></ref>
    
    <mvc:interceptor>
        <!--指定拦截的路径-->
        <!--/*表示拦截上下文的一层。/**表示拦截所有请求-->
            <mvc:mapping path="/*"/>
        <!--指定不拦截的路径-->
            <mvc:exclude-mapping path="/"/>
            <bean class="拦截器的类"></bean>
        </mvc:interceptor>
    
</mvc:interceptors>
```

![](.\图片\Snipaste_2022-01-11_20-06-24.png)

### 拦截器的三个抽象方法

![](.\图片\Snipaste_2022-01-11_20-34-01.png)

### 多个拦截器的执行顺序

![](.\图片\Snipaste_2022-01-11_20-41-39.png)

因为preHandle在退出之前调用了afterComplation

## 异常处理

![](.\图片\Snipaste_2022-01-12_09-58-59.png)

HandlerExceptionResolver最顶层的异常接口

SimpleMappingExceptionResolver让我们自定义异常的实现类

DefaultHandlerExceptionResolver是speingMVC默认处理异常的类

---

### 基于配置的异常处理

![](.\图片\Snipaste_2022-01-12_10-07-13.png)

![](.\图片\Snipaste_2022-01-12_10-28-48.png)

```xml
<!--配置异常处理-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <prop key="java.lang.ArithmeticException">error</prop>
        </props>
    </property>
    <!--设置将异常异常信息放在request中-->
    <property name="exceptionAttribute" value="ex"></property>
</bean>
```

在html中打印异常信息

```html
<p th:text="${ex}"></p>
```

### 基于注解的异常处理

![](.\图片\Snipaste_2022-01-12_10-30-17.png)

```java
//此注解包含了@Controller
@ControllerAdvice
public class ExceptionController {
    @ExceptionHandler(value = ArithmeticException.class)
    public ModelAndView handleArithmeticException(Exception ex) {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("ex", ex);
        modelAndView.setViewName("error");
        return modelAndView;
    }
}
```

## 注解配置SpringMVC

---

使用配置类和注解的方式代替web.xml和SpringMVC配置文件的功能

### 创建初始化类，代替web.xml

![](.\图片\Snipaste_2022-01-12_11-08-27.png)

![](.\图片\Snipaste_2022-01-12_11-17-33.png)

```java
package com.kjq.mvc.config;

import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.filter.HiddenHttpMethodFilter;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

import javax.servlet.Filter;

//web工程的一个初始化类用来代替web.xml
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
    /**
     * 指定一个spring配置类
     * @return
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    /**
     * 指定springMVC的一个配置类
     * @return
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    /**
     * 指定DispatcherServlet的映射路径, url-pattern
     * @return
     */
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    @Override
    protected Filter[] getServletFilters() {
        //配置请求和响应的编码过滤器
        CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("UTF-8");
        characterEncodingFilter.setForceResponseEncoding(true);
        //配置HiddenHttpMethodFilter来解析Put，delete请求
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{characterEncodingFilter, hiddenHttpMethodFilter};
    }
}
```

### 创建SpringConfig配置类，代替spring的配置文件

```java
package com.kjq.mvc.config;

import org.springframework.context.annotation.Configuration;

//spring的一个配置类
@Configuration
public class SpringConfig {
}
```

### 创建WebConfig配置类，代替SpringMVC的配置文件

```java
package com.kjq.mvc.config;

import com.kjq.mvc.interceptor.TestInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.context.ContextLoader;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.multipart.MultipartResolver;
import org.springframework.web.multipart.commons.CommonsMultipartResolver;
import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.*;
import org.springframework.web.servlet.handler.SimpleMappingExceptionResolver;
import org.thymeleaf.spring5.SpringTemplateEngine;
import org.thymeleaf.spring5.view.ThymeleafViewResolver;
import org.thymeleaf.templatemode.TemplateMode;
import org.thymeleaf.templateresolver.ITemplateResolver;
import org.thymeleaf.templateresolver.ServletContextTemplateResolver;

import java.util.List;
import java.util.Properties;

/**
 * 扫描组件
 * 配置视图解析器
 * 视图控制器
 * 开启默认的servlet静态文件加载
 * 开启mvc的注解驱动
 * 配置文件上传解析器，将上传的文件封装成MultipartFile
 * 配置拦截器
 * 配置异常处理
 */
//将当前类标识一个配置类
@Configuration

//扫描组件
@ComponentScan("com.kjq.mvc")

//开启mvc的注解驱动
@EnableWebMvc

//SpringMVC的配置类
public class WebConfig implements WebMvcConfigurer {

    //配置文件上传解析器，将上传的文件封装成MultipartFile
    @Bean
    public MultipartResolver multipartResolver() {
        CommonsMultipartResolver commonsMultipartResolver = new CommonsMultipartResolver();
        return commonsMultipartResolver;
    }

    //配置异常处理
    @Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
        Properties properties = new Properties();
        properties.setProperty("java.lang.ArithmeticException", "error");
        exceptionResolver.setExceptionMappings(properties);
        resolvers.add(exceptionResolver);
    }

    //视图控制器
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/hello").setViewName("hello");
    }

    //配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        TestInterceptor testInterceptor = new TestInterceptor();
        registry.addInterceptor(testInterceptor).addPathPatterns("/**");
    }

    //开启默认的servlet静态文件加载
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //视图控制器
    //配置生成模板解析
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造器参数，可通过webApplicationContext的方法获取
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }
    //生成模板引擎并为模板引擎注入模板解析器。这里的参数是通过自动装配来进行赋值
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }
    //生成视图解析器并为解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }
}
```

### 测试功能

## SpringMVC执行流程

---

### SpringMVC常用组件

![](.\图片\Snipaste_2022-01-12_12-48-31.png)

### DidpatcherServlet初始化过程

![](.\图片\Snipaste_2022-01-12_13-34-34.png)

![](.\图片\Snipaste_2022-01-12_13-35-37.png)
