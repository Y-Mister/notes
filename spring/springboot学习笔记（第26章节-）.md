## springboot学习笔记（第26章节-）

#### 26、日志记录(此部分较为粗略)

Spring Boot采用Commons Loggin 记录内部日志,其为java util Logging、Log4J2和Logback提供了默认配置，每种情况下，日志记录器都预先配置为使用控制台输出，并且还提供可选的文件输出。默认情况下使用Starter将采用Logback进行日志记录。

```java
//获取日志记录器
private Log logger = LogFactory.getLog(this.class);
```



##### 1.日志格式

https://docshome.gitbooks.io/springboot/content/pages/spring-boot-features.html#boot-features-logging-format

##### 2.控制台输出

默认配置会在写入时将消息回显到控制台，默认记录ERROR、WARN和INFO级别的日志，还可以通过命令行或者配置文件中启用DEBUG模式的输出

>$ java -jar myapp.jar  --debug    命令行形式
>
>application.properties：debug=true
>
>-------------------------------
>
>也可以通过 --trace（trace=true）标志启用跟踪模式

##### 3.日志着色输出

支持ANSI的终端在配置文件中设置spring.output.ansi.enabled以设置为受支持的值以覆盖自动检测；

具体更改日志颜色需要更改日志配置文件以最终实现颜色输出

##### 4.设置日志记录到文件

默认情况下Spring Boot仅记录到控制台，不会写入到日志文件，如果需要写入日志文件，需要在配置文件中设置logging.file或logging.path属性(**注意**：logging.file 与loggin.path属性似乎不能同时设置，同时设置后将不会输出日志到文件)

```yaml
#application.yml
logging:
  path: ./logs
#此后调用接口时将会打印日志到classpath：logs/spring.log中,demo中系统启动日志，如果调用example下的controller，则其下的日志都将被打印到上述文件中
```

##### 5.日志的等级

所有受支持的日志记录系统都可以使用logging.level.< logger_name>=< level>来设置spring environment中的记录器地址；

```yaml
logging.level.root=ERROR  #设置root记录器的等级
```



日志等级分为：TRACE、DEBUG、INFO、WARN、ERROR、FATAL和OFF