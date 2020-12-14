---
translate_title: ''
---
## 一、基础

## 二、配置文件

### 1.YAML语法

#### a、字面量



#### b、对象，Map（键值对）

#### c、数组（List，Set)



### 2.配置文件值注入

#### a、properties配置文件在idea中默认utf-8可能导致乱码

#### b、@Value和@ConfigurationProperties获取值比较

#### c、配置文件注入值校验

#### d、@PropertySource&@Import





### 3.配置文件占位符

#### a、随机数

#### b、指定配置文件中的值



### 4.Profile



### 5.配置文件加载位置

Spring boot启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

-file:/config/

-file:/

**以上两个项目路径不符合maven目录结构，打包时这两个路径下的配置文件没有打包在jar包内。**

-classpath:/config/

-classpath:/

优先级由高到低，高优先级的配置会覆盖低优先级的配置；

Spring boot会从四个位置全部加载主配置文件；

**互补配置**

一般高优先级配置部分内容，低优先级配置所有内容

通过**spring.config.location**改变配置文件位置

**项目打包以后，可以使用命令行参数的形式启动项目，指定配置文件的新位置。指定配置文件和默认加载位置的配置文件共同起作用。**

```java -jar XXX.jar --spring.config.location=xxx```

### 6.外部加载位置

Spring boot也可以从以下位置加载配置；以下顺序优先级由高到低。

[]: https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-external-config.html

Command line arguments.

JNDI attributes from `java:comp/env`.

Java System properties (`System.getProperties()`).

OS environment variables.

A `RandomValuePropertySource` that has properties only in `random.*`.

[Profile-specific application properties](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties) outside of your packaged jar (`application-{profile}.properties` and YAML variants).

[Profile-specific application properties](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties) packaged inside your jar (`application-{profile}.properties` and YAML variants).

Application properties outside of your packaged jar (`application.properties` and YAML variants).

Application properties packaged inside your jar (`application.properties` and YAML variants).

[`@PropertySource`](https://docs.spring.io/spring/docs/5.1.9.RELEASE/javadoc-api/org/springframework/context/annotation/PropertySource.html) annotations on your `@Configuration` classes.

Default properties (specified by setting `SpringApplication.setDefaultProperties`).

### 7.自动配置原理

配置文件中书写的内容

[配置文件书写内容]: https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/common-application-properties.html

Spring boot 启动架子啊主配置类，开启自动配置功能@EnableAutoConfiguration

@EnableAutoConfiguration作用

![1568254571014](C:\Users\v8\AppData\Roaming\Typora\typora-user-images\1568254571014.png)

![1568254608513](C:\Users\v8\AppData\Roaming\Typora\typora-user-images\1568254608513.png)

![1568254712466](C:\Users\v8\AppData\Roaming\Typora\typora-user-images\1568254712466.png)

扫描所有jar包类路径下 META-INF/spring.factorices 扫描到的内容包装成properties对象

从properties中获取对应的值放在容器中。

![1568255029601](C:\Users\v8\AppData\Roaming\Typora\typora-user-images\1568255029601.png)

### 三、Spring boot与日志

#### 1.日志框架

市面上的日志：

JUL、JCL、Jboos-loggin、logback、log4j、log4j2、slf4j……

| 日志门面（日志的抽象层）                                     | 日志实现                                        |
| ------------------------------------------------------------ | ----------------------------------------------- |
| JCL(Jakarta Commons Logging) `slf4j（Simple Logging Facade for Java` jboss-logging | Log4j  JUL(java.util.logging) Log4j2  `Logback` |

左边选一个门面，右边选一个实现<u>slf4j</u> <u>logback</u>

标注明显为选择的门面与实现，==Spring boot==选用也是以上两种。Spring框架默认使用JCL

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

![concrete-bindings.png (1152Ã636)](https://www.slf4j.org/images/concrete-bindings.png)

#### 2.遗留问题

（**slf4j+logback**)Spring boot默认使用这两个，项目中引入spring、Hibernate、Mybatis等会引入其他的日志实现日志门面。

统一日志记录

![legacy.png (1587Ã1123)](https://www.slf4j.org/images/legacy.png)

![1568426118473](D:\面试\springbootimages\1568426118473.png)

Spring boot也考虑到其他框架的影响使用替换jar包。

#### 3.日志使用

日志的级别由低到高

```java
logger.trace("");
logger.debug("");
logger.info("");
logger.warn("");
logger.error("");
```

通过调整日志级别控制输出的日志信息。默认使用的是info级别，输出info、warn、error级别日志。

```properties
logging.level.包名=trace
```

```properties
logging.path=/springboot/log
#在当前项目的所在磁盘的根目录下的指定文件夹下生成log文件spring.log默认文件名
logging.file=springboot.log
#当前项目下生成log文件
logging.file=G:/springboot.log
#在指定盘生成log文件
logging.pattern.console=
#控制台输出日志的格式
logging.pattern.file=
#指定文件日志输出格式
```

| Logging System          | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |

logback.xml:日志框架直接别识别

logback-spring.xml：日志框架不直接加载日志的配置项，有SpringBoot识别

```xml
<springProfile name="dev">
	<!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>
<springProfile name="!dev">
	<!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>
```

可以指定特殊运行环境的日志配置。

logback.xml不能识别`<springProfile>`

### 四、web开发

#### 1.开发步骤

创建项目选中需要的模块

进行少量的配置就可以完成配置

编写业务代码

#### 2.Spring boot项目静态资源

##### webjars

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
                CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
                if (!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

                String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if (!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

            }
        }
```



[webjars官方网站]: https://www.webjars.org/

![1568466801864](D:\面试\springbootimages\1568466801864.png)

##### /**



```java
"classpath:/META-INF/resources/", 
"classpath:/resources/", 
"classpath:/static/", 
"classpath:/public/",
"/"当前项目类路径
```

以上静态资源的文件夹

##### 欢迎页映射

```java
@Bean
        public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext) {
            WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(new TemplateAvailabilityProviders(applicationContext), applicationContext, this.getWelcomePage(), this.mvcProperties.getStaticPathPattern());
            welcomePageHandlerMapping.setInterceptors(this.getInterceptors());
            return welcomePageHandlerMapping;
        }
```

静态资源包下的所有index.html ;被/**映射

localhost:8080/

#### 配置图标

```java
@Configuration
        @ConditionalOnProperty(
            value = {"spring.mvc.favicon.enabled"},
            matchIfMissing = true
        )
public static class FaviconConfiguration implements ResourceLoaderAware {
            private final ResourceProperties resourceProperties;
            private ResourceLoader resourceLoader;

            public FaviconConfiguration(ResourceProperties resourceProperties) {
                this.resourceProperties = resourceProperties;
            }

            public void setResourceLoader(ResourceLoader resourceLoader) {
                this.resourceLoader = resourceLoader;
            }

            @Bean
            public SimpleUrlHandlerMapping faviconHandlerMapping() {
                SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
                mapping.setOrder(-2147483647);
                mapping.setUrlMap(Collections.singletonMap("**/favicon.ico", this.faviconRequestHandler()));
                return mapping;
            }

            @Bean
            public ResourceHttpRequestHandler faviconRequestHandler() {
                ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
                requestHandler.setLocations(this.resolveFaviconLocations());
                return requestHandler;
            }

            private List<Resource> resolveFaviconLocations() {
                String[] staticLocations = WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations());
                List<Resource> locations = new ArrayList(staticLocations.length + 1);
                Stream var10000 = Arrays.stream(staticLocations);
                ResourceLoader var10001 = this.resourceLoader;
                var10001.getClass();
                var10000.map(var10001::getResource).forEach(locations::add);
                locations.add(new ClassPathResource("/"));
                return Collections.unmodifiableList(locations);
            }
        }
```

所有的图标在静态资源文件下

#### 模板引擎

thymeleaf、freemark

#### 修改页面不重启项目的解决方案

在pom.xml添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <version>2.1.8.RELEASE</version>
</dependency>
```

在项目设置中勾选Build project automatically.

![1569751708741](D:\面试\springbootimages\1569751708741.png)

ctrl+shift+alt+/ 选择registry 

![1569751869548](C:\Users\v8\AppData\Roaming\Typora\typora-user-images\1569751869548.png)

