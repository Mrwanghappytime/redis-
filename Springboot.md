# Springboot

## 1.配置文件（位置可以多种）

### 1.1yml：格式如下（可用${Random.int}获取随机值）

```yaml
server:
  port: 8001

person:
  id: 1
  name: wanghaofeng
  password: 123456
  list:
    lichunhua
    panyuqing
  #maps: {love: lichunhua,friend: panyuqing}
  map:
    love: lichunhua
    friend: panyuqing
  strings:
    list
    map
    eee
spring:
  profiles:
    active: dev #可指定profile的环境，环境不同的profile可以为是application-dev.yml
    			#application-prod.yml 还可用yml文件块，还可以使用命名行模式
```

javabean

```java
@Component
@ConfigurationProperties(prefix = "person")默认从全局变量中获取值
public class Person {
    private int id;
    private String name;
    private String password;
    private List<String> list;
    private Map<String,String> map;
    private String[] strings;
    //getter setter
}

```

```
@ImportResource("")可用在main类上引入xml文件
@Configuration 指定当前类为一个配置类，可以在其中配置bean，用@bean注解，将方法的返回值加入到容器中
```

### 1.2自动配置原理

1.springboot主注解（@SpringBootApplication）

![image-20200423214654964](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200423214654964.png)

2.EnableAutoConfiguration的作用

![image-20200423214953585](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200423214953585.png)

所有在配置文件可以配置中配置的属性都是在xxxProperties类中的封装者

![image-20200423215948922](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200423215948922.png)

@Conditional注解可以判断类是否存在

![image-20200423220606867](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200423220606867.png)

自动配置在一定条件下才生效。如何看那些自动配置类是否生效，可以在配置文件配置debug=true，可以查看那些配置类生效Negative matches:Positive matches:

## 2.日志（log）

springboot使用SLF4J LOGBACK

![image-20200426204446620](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200426204446620.png)

### 2.1 SLF4J使用

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

![img](http://www.slf4j.org/images/concrete-bindings.png)

每一个日志的实现框架都有自己的配置文件，使用SLF4J,**配置文件还是日志实现的配置文件**

### 2.2 遗留问题

多个框架存在多种日志框架，可以同时日志框架

![click to enlarge](http://www.slf4j.org/images/legacy.png)

可以使用SLF4J的适配

1.先排除其他日志框架，引入中间包替换原来的日志框架

2.导入SLF4J其他的实现

### 2.3 SPRINGBOOT日志使用

底层依赖关系

![image-20200426210152486](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200426210152486.png)

![image-20200426210211320](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200426210211320.png)

总结：1.springboot底层使用logback和slf4j框架

​			2.也引用了其他替换包

​           3.springboot引入其他jar包，**一定要移除默认jar包**（spring5日志框架已经变成了SLF4J）

### 2.4日志使用

```java
@SpringBootApplication
public class DemoApplication {
   Person person;
   private final static Logger logger = LoggerFactory.getLogger(DemoApplication.class);
   public static void main(String[] args) {
      logger.trace("这是trace日志");
      logger.debug("这是debug日志");
      logger.info("这是infor日志");
      logger.warn("这是警告信息");
      logger.error("这是错误信息");
      SpringApplication.run(DemoApplication.class, args);
   }
}
```

日志级别从低到高为trace debug info warn error，可以调整级别，在打印信息时可以只打印级别高的内容，默认dubug级别

```yaml
logging:
  level:
    com: trace
  file: springboot.log #可以指定完成路径 path 可以指定目录
  pattern:
    console:  #指定日志格式在控制台 
    file:     #指定文件中日志格式
   
```

可用上述配置修改配置级别，输出方式

## 3.WEB开发

**1.选中模块**

**2.springboot大部分类自动配置完成，自己只需少部分改动即可**

**3.进行业务编写**



**自动配置原理？**

每个场景springboot帮我们做了什么，配置了什么，怎么修改

### 3.1 springboot静态资源配置规则(可定义)

1）可以引入webjars依赖 引入jquery，springboot默认在webjars下查找资源

```xml
<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>jquery</artifactId>
			<version>3.3.1</version>
		</dependency>
```

2）

```
classpath:/META-INF/RESOURCES
classpath:/resource
classpath:/static
classpath:/public
/
```

3.欢迎页下的index.html页面（静态资源目录下）

4.图标：静态文件夹中的favicon.ico

### 3.2 thymeleaf

1.因为不支持jsp格式，所以需要用模板引擎

2.springboot推荐thymeleaf

### 3.2.1 引入thymeleaf

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
```

```java
@Controller
public class MyController {

    @ResponseBody
    @GetMapping("/hello")
    public String helloword(){
        return "helloword";
    }

    @RequestMapping("/success")
    public String success(){
        return "success";
    }
}
```

**默认在classpath:/template查找模板文件，进行渲染，后缀为.html**

### 3.2.2 thymeleaf语法

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>成功！</h1>
    <div th:text="${hello}"></div>
</body>
</html>
```

### 3.2.3 thymeleaf语法规则

```
th:属性 可替换所有html所有属性的值


```

![image-20200426222326359](C:\Users\汪浩锋\AppData\Roaming\Typora\typora-user-images\image-20200426222326359.png)

### 3.2.4 thymeleaf表达式

```properties
见文档第4章
```

## 4.springmvc

### 4.1 springmvc自动配置

##### Spring MVC Auto-configuration

Spring Boot provides auto-configuration for Spring MVC that works well with most applications.

The auto-configuration adds the following features on top of Spring’s defaults:

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.
- Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.6.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-static-content))).
- Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.
  - ​	Converter： 转化器，类型转化
  - ​    Formatter:   格式化器，日期转化为日期类型
  - 自定添加格式化转化器，只需要放在容器中即可
- Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.6.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-message-converters)).
  - SpringMvc用来转化http请求和响应的；User --- JSON
- Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.6.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-spring-message-codes)).
- Static `index.html` support.
- Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.6.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-favicon)).
- Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.2.6.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-web-binding-initializer)).

#### 4.1.1 springmvc自动配置类和ContentNegotiatingViewResolver

```java
//自动配置类
@Configuration
class WebMvcAutoConfiguration{
	    @Bean
        @ConditionalOnBean({ViewResolver.class})
        @ConditionalOnMissingBean(
            name = {"viewResolver"},
            value = {ContentNegotiatingViewResolver.class}
        )
        public ContentNegotiatingViewResolver viewResolver(BeanFactory beanFactory) {
            ContentNegotiatingViewResolver resolver = new ContentNegotiatingViewResolver();
            resolver.setContentNegotiationManager((ContentNegotiationManager)beanFactory.getBean(ContentNegotiationManager.class));
            resolver.setOrder(-2147483648);
            return resolver;
        }
}


```

通过注入`ContentNegotiatingViewResolver` 视图解析器 ，这个视图解析器回去组合所有视图解析器

自己定制视图解析器：自己定义一个视图解析器，然后注册到容器中即可被`ContentNegotiatingViewResolver` 自定识别,识别原理如下

```java
public class ContentNegotiatingViewResolver extends WebApplicationObjectSupport implements ViewResolver, Ordered, InitializingBean {
 	private List<ViewResolver> viewResolvers;
    protected void initServletContext(ServletContext servletContext) {
        Collection<ViewResolver> matchingBeans = BeanFactoryUtils.beansOfTypeIncludingAncestors(this.obtainApplicationContext(), ViewResolver.class).values();
        ViewResolver viewResolver;
        if (this.viewResolvers == null) {
            this.viewResolvers = new ArrayList(matchingBeans.size());
            Iterator var3 = matchingBeans.iterator();

            while(var3.hasNext()) {
                viewResolver = (ViewResolver)var3.next();
                if (this != viewResolver) {
                    this.viewResolvers.add(viewResolver);
                }
            }
        } else {
            for(int i = 0; i < this.viewResolvers.size(); ++i) {
                viewResolver = (ViewResolver)this.viewResolvers.get(i);
                if (!matchingBeans.contains(viewResolver)) {
                    String name = viewResolver.getClass().getName() + i;
                    this.obtainApplicationContext().getAutowireCapableBeanFactory().initializeBean(viewResolver, name);
                }
            }
        }

        AnnotationAwareOrderComparator.sort(this.viewResolvers);
        this.cnmFactoryBean.setServletContext(servletContext);
    }
}
```

自定义视图解析器的实现

```java
public class MyViewResolver implement ViewResolver{
	



}
然后注册到容器中即可
```

### 4.2 扩展 springmvc

```xml
<mvc:view-controller/>
<mvc:interceptors>
	<mvc:interceptor>
    
    </mvc:interceptor>
</mvc:interceptors>
```

SPRINGBOOT中没有xml，如何进行

If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.

If you want to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, or `ExceptionHandlerExceptionResolver`, and still keep the Spring Boot MVC customizations, you can declare a bean of type `WebMvcRegistrations` and use it to provide custom instances of those components.

If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`, or alternatively add your own `@Configuration`-annotated `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`.

```java
@Configuration
public class MyWebMvcConfihurer implements WebMvcConfigurer {
	//添加功能只需重写对应的接口即可
	 @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("success");
    }
}
```

### 4.3国际化

1）配置国际化配置文件

2）使用Re'sourceBubdleMessageSource管理国际化资源文件

3）在页面使用fmt：message去除国际化内容

上述为以前的方法

步骤：

1. 编写国际化配置文件
2. 