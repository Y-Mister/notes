### springboot

##### 关于第一次搭建

1. 搭建的重点

   首先，项目pom依赖必须集成父项目springframework.boot的spring-boot-starter-parent启动器，其包含的内容见4

   ```xml
   <parent>
   		<groupId>org.springframework.boot</groupId>
   		<artifactId>spring-boot-starter-parent</artifactId>
   		<version>2.1.6.RELEASE</version>
   		<relativePath/> <!-- lookup parent from repository -->
   	</parent>
   ```

   web项目必须依赖web启动器包，这个包内内置了springWebMVC与tomcat等web开发需要的jar包,提供全栈式开发

   ```xml
   <dependency>
   			<groupId>org.springframework.boot</groupId>
   			<artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   ```

   其他启动器包的作用（更多官方文档13.5 starters）

   ```xml
   <artifactId>spring-boot-starter</artifactId>
   springboot核心启动器，包含自动配置、日志和Yaml，dao开发相关的jar包
   <artifactId>spring-boot-starter-aop</artifactId>
   提供了面向切面编程AOP,内含spring-aop和Aspectj
   <artifactId>spring-boot-starter-jdbc</artifactId>
   支持JDBC数据库
   <artifactId>spring-boot-starter-redis</artifactId>
   支持Redis键值存储数据库，包括spring-redis
   <artifactId>spring-boot-starter-log4j</artifactId>
   支持Log4J日志框架
   <artifactId>spring-boot-starter-tomcat</artifactId>
   引入了Spring Boot默认的HTTP引擎Tomcat
   个人理解：启动器的作用就是帮助我们简化项目配置，依赖jar包的引入；
   待弄清楚的项目：如何配置web试图解析器，如何完成
   ```

2. 各注解的作用

   类注解（class level annotation）

   | 注解                     | 作用                                                         |
   | ------------------------ | ------------------------------------------------------------ |
   | @RestController          | 是@Controller与@Responsebody（指明返回json）的组合注解，在springboot中，@Controller需要配合模板引擎使用 |
   | @EnableAutoConfiguration | springboot通过当前依赖包自动配置spring，可通过exclude过滤不需要的配置类 |
   | @SpringbootApplication   | 通常放置在整个应用程序的主类上，该注解通常放置于根目录，springboot会从该类向下扫描该包。可用@EnableAutoConfiguration与@ComponentScan（相当于配置文件中的context:component-scan，过滤、指定包等可选参数也相同）与Configuration代替 |
   | @ComponentScan           | 扫包，将符合条件的bean统一至spring管理                       |
   | @Configuration           | 用于定义配置类，其可以被ComponentScan扫描到（具体使用效果未知，官方文档15） |
   | @Import                  | 导入其他@configuration类                                     |

   注意：

   > 1.@SpringbootApplication注解只会扫描该注解标注的主类所在包以及其子包，如果一个包于主类所在的包平行，那么将无法被扫描到，这是需要在主类上通过@ComponentScan("包名")扩大扫描范围从而将平行包纳入扫描范围内进而被spring管理;
   >
   > 2.如果Application类的注解@SpringbootApplication上方加了一个扫包注解ComponentScan(package)【只加注解不填指定包注解无效】后，@SpringbootApplication自带的注解将失效，无法扫描其下子包内的类进而无法自动装配bean【目前的情况】

   其他注解

   | 注解                          | 作用                                                         |
   | ----------------------------- | ------------------------------------------------------------ |
   | @RequestMapping               | 与springMVC作用一致，设置path                                |
   | @value("${poperties's name}") | 通过properties文件或者configuration文件中设置的property注入  |
   | @PropertySource               | 通过文件路径手动加载配置文件                                 |
   | @ConfigurationProperties      | 允许通过前缀获取配置文件中的相关数据源信息（前缀在YAML文件中可以理解为父节点） |
   | @profile                      | 通过properties文件中配置的不同prifile名启用不同的配置        |

   

3. springboot项目的入口点

   通过main方法启动

   ```java
   public static void main(String[] args) {
           SpringApplication.run(Example.class,args);
       //SpringApplication.run()方法会启动spring，配置tomcat web服务器，并通过第一个参数告知spring某个类是其主要组成
       }
   ```

4. 关于spring-boot-starter-parent

   其提供如下内容（注意，继承该启动器时必须指定版本，否则将无法实现依赖管理部分内容）

   >默认jdk1.8为默认版本；
   >
   >默认字符集utf-8;
   >
   >依赖管理部分内容，包括其后的依赖包无需再指定版本，版本将从该启动器pom中继承；
   >
   >**还有资源过滤、插件管理等暂时没有使用过的东西**（见官方文档13.2）

   当然，尽管该启动器可以为我们指定version，但还是可以通过properties来覆盖更新想要的version

   ```xml
   <properties>
   	<spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
   </properties>
   ```

   关于如何不使用该父继承，官方文档13.2.2



5. Spring beas 和依赖注入

   > 如果将Application类放置在根包内，并且使用@SpringbootApplication注解或者@ComponentScan注解，那么springboot将会自动扫描Application类所在包下及其子包下的所有组成部分（@Component、@Service、@Repository、@Controller等注解）将自动注册为spring ；bean；

   如果待注入bean含有构造函数,那么在通过构造函数注入的时候可以省略Autowried

   ```java
   @Component
   public class BeanTest3 {
   	
       public BeanTest3(){  //此处bean有构造函数
           System.out.println("构造函数自动装配");
   
       }
   
       public void run(){
           System.out.println("构造函数自动装配");
       }
   }
   /*BeanTest4通过构造器注入BeanTest3，无需再加autowried*/
   @Component
   public class BeanTest4 {
   
       BeanTest3 beanTest3;
   
       public BeanTest4(BeanTest3 bean){
           this.beanTest3 = bean;
       }
   
       public void run(){
           beanTest3.run();
       }
   
   }
   ```

   

6. spring-boot-devtools（包含在继承的父类spring-boot-starter中）下关于是否需要重启springboot应用的问题

   > 默认情况下，只要classPath上的文件发生了修改，应用就会自动重启；
   >
   > 但是需要注意：静态资源与视图模板的修改不会触发自动重启，也不需要重启应用

   > 关于重启，springboot提供了两个类加载器，不会更改的类譬如第三方jar包等都会被加载到基础类加载器，而个人开发的类将会加载到重启类加载器中。应用重启的时候，重启类加载器将会被废弃并且重新生成一个重启类加载器。通过此方法，只重新加载重启加载器会提高应用的速度。

   禁用报告日志，配置文件中设置：

   ```xml
   spring.devtools.restart.log-condition-evaluation-delta=false
   ```

   默认情况下，/META-INF/maven,/META-INF/resources, /resources, /static, /public, or /templates目录下的资源发生改变时不会触发重启，但会触发热加载，如果想自定义重启目录的排除项，例如排除static和public目录下资源更改不引发重启：（此处理解不清楚，官方文档20.2.2）

   ```java
   spring.devtools.restart.exclude=static/**,public/**
   ```

   如果想保留这些默认项但需要排除其他，可以配置如下

   ```xml
   spring.devtools.restart.additional-exclude=***
   ```

   如果想要对非classpath路径下的资源更改引发重启或重新加载，需要添加如下配置语句：

   ```xml
   spring.devtools.restart.additional-paths
   //设置完成后可以在通过
   spring.devtools.restart.exclude=***
   来详细其他路径下的目录文件更改触发的是重启还是热加载
   ```

   禁用重启

   ```xml
   spring.devtools.restart.enabled=false  /*配置在application。properties中，这样做仍然会初始化重启类加载器，但不会再监视文件的更改*/
   
   /*完全禁用重启，需要在程序application类中调用SpringApplication.run(*.class,args)前启用spring.devtools.restart.enabled=false*/
   
   public static void main(String[] args) {
   System.setProperty("spring.devtools.restart.enabled", "false");
   SpringApplication.run(MyApp.class, args);
   }
   
   ```

   关于自定义重启类加载器（restart classLoader），跳过，官方文档20.2.6

   关于开发人员工具的其他部分，暂时读不懂，跳过20.3—20.5

##### Springboot特性

1. SpringApplication

   > springApplication类提供了静态方法run来引导spring应用程序的启动

   ```java
   public static void main(String[] args) {
   SpringApplication.run(MySpringConfiguration.class, args);
   }
   ```

2. 自定义启动加载横幅banner(官方文档23.2)

   > 做法：只要在resources目录下自定义需要的图形，springboot会自动调用

3. 自定义SpringbootApplication类

   > 其和默认SpringApplication的区别在于，默认调用的是静态方法，而自定义先new一个SpringApplication对象，设置自定义参数后再调用run方法

   ```java
   public static void main(String[] args) {
   		//SpringApplication.run(DemoApplication.class, args);
   		//beanTest.run();
           SpringApplication APP = new SpringApplication(DemoApplication.class);
           APP.setBannerMode(Banner.Mode.OFF);
           APP.run(args);
       //自定义SpringApplication类，设置不打印banner
   	}
   ```

4. 链式启动器API,其可以通过父子节点生成层次结构

   ```java
   new SpringApplicationBuilder()
   .sources(Parent.class)
   .child(Application.class)
   .bannerMode(Banner.Mode.OFF)
   .run(args);
   ```

   > 通过该链式调用，在demo例子中能够成功启动：我的测试理解是，在不同的包结构下注册两个Aplication，分别是DemoApplication与example，子入口不调用run方法，但是其是作为一个web页面应用；同时移除DemoApplication中的扫包，通过此链式调用，虽然不存在扫包，但是两个application类所在根包及其子包都被成功初始化为bean，且example类定义的web应用能够成功打开

   前后对比

   ```java
   //情形1：不适应父子链式调用的情况下，要打开localhost:8080必须扫包
   //example位于web包下，DemoApplication位于demo包下
   @ComponentScan({"com.example.web","com.example.demo"})
   @SpringBootApplication
   
   public class DemoApplication {
       
   
   	public static void main(String[] args) {
   		SpringApplication.run(DemoApplication.class, args);
   		//beanTest.run();
           /*SpringApplication APP = new SpringApplication(DemoApplication.class);
           APP.setBannerMode(Banner.Mode.OFF);
           APP.run(args);*/
   
   
           /*能够成功启动*/
           new SpringApplicationBuilder()
                   .sources(DemoApplication.class)
                   .child(Example.class)
                   .bannerMode(Banner.Mode.OFF)
                   .run(args);
   	}
   
   }
   ```

   ```java
   //情形二：采用父子链式调用
   //@ComponentScan({"com.example.web","com.example.demo"})
   
   @SpringBootApplication
   
   public class DemoApplication {
   
   	public static void main(String[] args) {
           /*能够成功启动*/
           new SpringApplicationBuilder()
                   .sources(DemoApplication.class)
                   .child(Example.class)
                   .bannerMode(Banner.Mode.OFF)
                   .run(args);
   	}
   
   }
   ```

   其中Example类结构如下

   ```java
   @RestController
   /*如下两个，情形1可注释，但情形2必须存在一个，缺少时报错无法启动web服务器，原因未知*/
   @EnableAutoConfiguration
   @SpringBootApplication
   public class Example {
   
       @RequestMapping("/")
       String home(){
           return "Hello World with springboot!";
       }
   
       public static void main(String[] args) {
           //SpringApplication.run(Example.class,args);
       }
   }
   ```

5. 想要在SpringApplication.run()方法运行前执行自定义的方法，可以实现ComandLineRunner或ApplicationRunner接口并实现run方法，其run方法会在SpringApplication.run()执行前先执行

   ```java
   @Component
   public class MyBean implements CommandLineRunner {
       @Override
       public void run(String... args) throws Exception {
           System.out.println("这在SpringApplication.run 之前运行！");
       }
   }
   /*并不需要显示调用*/
   ```

6. 配置任意值

   properties文件内使用${random.***}来获取随机值

   ```java
   local.server.port=8080
   my.secret=${random.int}
   my.float=${random.uuid}
   
   @Component
   public class MyBean implements CommandLineRunner {
   
       @Value("${local.server.port}")
       public String port;
       @Value("${my.secret}")
       public int intNum;
       @Value("${my.float}")
       public String uuid;
       @Override
       public void run(String... args) throws Exception {
           System.out.println("这在SpringApplication.run 之前运行！");
           System.out.println(port);
           System.out.println(intNum);
           System.out.println(uuid);
       }
   }
   /*输出结果：
   这在SpringApplication.run 之前运行！
   8080
   1797524238
   0e3e8d63-6d80-444e-987d-c2a9af320d71
   */
   ```

7. 修改Springboot默认配置文件名与默认配置文件路径

   > 项目打包后，通过命令行参数可以设置默认配置文件名和文件路径

   ```
   //修改默认配置文件名
   $ java -jar myproject.jar --spring.config.name=myproject
   //修改默认配置文件路径
   $ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties
   注意，对应自定义配置文件路径，springboot搜索时是按照从后往前的顺序搜索的，且通过spring.config.location定义的路径将会覆盖默认路径，如果不需要覆盖自定义路径，则需要使用
   spring.config.additional-location,多个配置文件中的同一参数采用优先级最后获胜原则，即最后一个值为参数最终值
   ```

8. YAML文件做配置文件

   > YAML采用层级式对应于点结构（与包的两种结构格式相同）

   例如：

   ```yaml
   enviroment: 
   		dev: 
   			url: https://localhost:8080
   			name: myApp
   		prop: 
   			url: https://localhost:8081
   			name: myApp2
   ```

   对应于

   ```properties
   environment.dev.url=https://localhost:8080
   environment.dev.name=myApp
   environment.prop.url=https://localhost:8081
   environment.prop.name=myApp2
   ```

   YAML列表语法对应于properties数组

   ```yaml
   my: 
    service: 
    	- my
    	- app
   ```

   对应于

   ```properties
   my.service[0]=my
   my.service[1]=app
   ```

9. 关于自定义配置文件的一些事项

   > 可以自定义配置文件，如果不想打包后通过命令行指定配置文件名，则需要通过@PropertySource("classpath:文件名.properties")来手动加载自定义配置文件，注意，springboot无法手动加载yml配置文件，

   通过@ConfigurationProperties获取yml配置文件属性

   ```yml
   #application.yml
   my:
     serverLocation: 8080
   test:
       time: 20:37
       date: 2019/8/13
       heart: impatient
   ```

   ```java
   //测试类YMLTest 属性名要相同，前缀要写对，属性get/set方法要有
   @Component
   @ConfigurationProperties(prefix = "test")
   public class YMLTest {
   
       private String time;
       private String date;
       private String heart;
   
       public String getTime() {
           return time;
       }
   
       public void setTime(String time) {
           this.time = time;
       }
   
       public String getDate() {
           return date;
       }
   
       public void setDate(String date) {
           this.date = date;
       }
   
       public String getHeart() {
           return heart;
       }
   
       public void setHeart(String heart) {
           this.heart = heart;
       }
   }
   //测试方法中输出 先通过autowried自动注入YMLtest类
   System.out.println(ymlTest.getDate()+" "+ymlTest.getTime()+" "+ymlTest.getHeart());
   ```

10. 多profile YAML文件

    > 可以通过在YAML配置文件中通过**spring.profile:value**的方式，指定多个特定profile的指定YAMML文档

    ```YAML
    server:
      address: 192.168.1.100
    ---
    spring:
      profiles: development
    server:
      address: 127.0.0.1
    ---
    spring:
      profiles: production & eu-central
    server:
      address: 192.168.1.120
    ```

    > 如上图，如果develement处于激活状态，则此时应用server.address为127.0.0.1；如果production和eu-central同时处于激活态，则应用server.addredd为192.168.1.120；
    >
    > **注意：**spring.profile中可以包含简单的profile名称，也可以包含一个profile表达式，其允许复杂的profile逻辑（什么是profile，见第25节，参考他人博客显示profile就是springboot对不同的环境或指令读取的不同的配置文件）；
    >
    > 在java代码中，可通过@profile("profile-name")来启用不同profile下配置的不同的配置文件（@profile注解只能组合@Configuration和@compinent注解使用）;
    >
    > spring.profile:支持通过！取反否定
    >
    > **YAML的缺点：**无法使用@PropertySource加载YAML文件，*.properties文件可以

11. 类型安全的属性配置

    > 即通过@Configuration(prefix="")来指定需要配置的属性在YAML文件中的属性前缀，此种方式注入属性无需在属性名上使用@Value注解，只需要将变量名于配置文件中相同即可;
    >
    > **注意：**get/set方法通常是必须的（map只要它们被初始化，就可以只要getter不需要setter，因为它们可以被binder修改）

    ```java
    @Component
    @ConfigurationProperties(prefix = "test")
    public class YMLTest {
    
        private String time;
        private String date;
        private String heart;
    
        public String getTime() {
            return time;
        }
    
        public void setTime(String time) {
            this.time = time;
        }
    
        public String getDate() {
            return date;
        }
    
        public void setDate(String date) {
            this.date = date;
        }
    
        public String getHeart() {
            return heart;
        }
    
        public void setHeart(String heart) {
            this.heart = heart;
        }
    }
    ```

    ```YAML
    #application.yml配置文件
    test:
        time: 20:37
        date: 2019/8/13
        heart: impatient
    ```

    > 关于@EnableConfigurationProperties，暂时未能理解，位置：24.8类型的安全配置

    **应用第三方配置**

    @ConfigurationProperties还可以在公共的@Bean方法上使用，以便于将属性绑定到第三方组件上

    ##### **宽松绑定**

    即通过@ConfigurationProperties绑定属性时，bean中的属性名与配置文件中的属性名不需要精确匹配，例如可将context-path绑定到contextPath、将PORT绑定到port

    关于宽松绑定，针对属性名firstName,在配置文件中可用如下写法

    | 属性       | 描述                                                   |
    | ---------- | ------------------------------------------------------ |
    | first-name | Kebab短横线命名风格，建议在.properties和.yml文件中使用 |
    | firstName  | 驼峰式                                                 |
    | first_name | 下划线表示法，.properties和.yml文件中使用              |
    | FIRSTNAME  | 大写风格，使用系统环境变量时推荐使用                   |

    注意：直接的prefix值必须是Kebab风格

    各属性源的的宽松绑定规则

    | 属性源          | 简单类型                                               | 列表集合类型                                                 |
    | --------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
    | properties 文件 | 驼峰式、短横线式或下划线式                             | 标准列表语法使用 `[]` 或逗号分隔值                           |
    | YAML 文件       | 驼峰式、短横线式或者下划线式                           | 标准 YAML 列表语法或者逗号分隔值                             |
    | 环境变量        | 大写并且以下划线作为定界符，`_` 不能放在属性名之间使用 | 数字值两边使用下划线连接，例如 `MY_ACME_1_OTHER = my.acme[1].other` |
    | 系统属性        | 驼峰式、短横线式或者下划线式                           | 标准列表语法使用 `[]` 或逗号分隔值                           |

    当将属性绑定到Map中时，如果key包含出字符数字以及‘-’以外的其他字符时，需要通过[ ]来保留特殊字符，否则其他字符将被删除

    ```YAML
    test:
        time: 20:37
        date: 2019/8/13
        heart: impatient
        map:
            "[/key1]": value1
            "[key-2]": value2
            /key3: value3
            key-4: value4
            \key\5: value5
    ```

    ```java
    @Component
    @ConfigurationProperties(prefix = "test")
    public class YMLTest {
    
        private String time;
        private String date;
        private String heart;
        private Map map;
     	·····省略get/set方法   
    }
    /*测试方法中输出为：
    /key1=value1
    key-2=value2
    key3=value3
    key-4=value4
    key5=value5
    */
    ```

    **合并复杂类型**

    即如果在配置文件中针对同一属性均做了配置（通过spring.profile指定配置的应用环境），注入时默认不会合并条目（即激活某一配置环境时，默认条目与指定环境下的配置条目不会发生合并）

    ```yaml
    #配置文件如下 application.yml
    acme:
        list:
          - name: my name
            description: my Description
    ---
    spring:
      profiles: dev
      acme:
        list:
          - name: my another name
    ```

    在使用默认配置的情况下注入list

    ```java
    public class MyPojo {
    
        private String name;
        private String description;
     	//此处······省略get/set
    }
    /*实现类*/
    @Component
    @ConfigurationProperties(prefix = "acme")
    public class YMLTest01 {
        private List<MyPojo> list;
    
        public List<MyPojo> getList() {
            return list;
        }
    
        public void setList(List<MyPojo> list) {
            this.list = list;
        }
    
    }
    /*测试类*/
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class DemoApplicationTests{
        
        @Autowired
    	private YMLTest01 ymlTest01;
        
        for (MyPojo temp:ymlTest01.getList()){
    			System.out.println(temp.getName()+" "+temp.getDescription());
    		}
    }
    /*测试类中输出该list：
    my name my Description
    */
    ```

    若配置了环境dev，并激活dev

    ```YAML
    acme:
        list:  #这同样也是yaml中list的写法
          - name: my name
            description: my Description
    ---
    spring:
      profiles:
        active: dev
    ---
    spring:
      profiles: dev
    acme:
        list:
          - name: my another name
    ```

    ```java
    /*bean类与测试类同上，结果输出为：
    my another name null
    */
    ```

    即默认的配置与新激活的dev环境配置中配置的list并不会合并注入

    综上：多个配置文件中配置同一个list，优先级最高的将被使用

    **但是**，对于map，则可以从多个数据源中取得数据并绑定，而对于相同Key的元素，则优先级最高的将被使用：

    ```yaml
    acme:
        list:
          - name: my name
            description: my Description
        map:
          key1:
            name: myname1
            description: my description1
    ---
    spring:
      profiles:
        active: #dev    激活配置项
    ---
    spring:
      profiles: dev
    acme:
        list:
          - name: my another name
        map:
          key1:
            name: my name1
          key2:
            name: my name2
            description: my description2
    ```

    ```java
    /*测试类*/
    @Component
    @ConfigurationProperties(prefix = "acme")
    public class YMLTest02 {
    
        private Map<String,MyPojo> map;
    
        public Map<String, MyPojo> getMap() {
            return map;
        }
    
        public void setMap(Map<String, MyPojo> map) {
            this.map = map;
        }
    }
    
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class DemoApplicationTests{
        
        @Autowired
    	private YMLTest02 ymlTest02;
        
        for (String temp:ymlTest02.getMap().keySet()){
    			MyPojo myPojo = ymlTest02.getMap().get(temp);
    			System.out.println(temp+":"+myPojo.getName()+" "+myPojo.getDescription());
    		}
    }
    /*当dev未激活时，其输出内容为：
    key1 myname1 my description1
    当dev激活后，输出为：
    key1:my name1 my description1
    key2:my name2 my description2  
    可见虽然dev被激活，但key1对于的value值仍为默认配置文件中的key1，dev下的key1被覆盖了
    */
    ```

    关于属性转换，暂时未能理解，章节24.8.4

    **@configurationProperties验证**

    > 通过将@validated直接在配置类上使用，并在需要验证的属性上设置约束注解（验证用,例如@NotNull，@NotEmpty，@Valid）

12. Profile

    > spring profile提供了一种配置部分隔离，并在指定环境中可用的方法。可以使用@Profile来注解任何@Component或者@Configuration来指定何时加载它（即将@Profile使用在@Component和@Configuration是用来指定这类配置文件可使用的环境，可见@Component和@Configuration属于配置类！！！），如果将@Profile使用在实现类上，则可以实现获取该实现类时的根据当前环境动态注入相应的实现类;

    环境在配置文件中的表现就是：

    > application.yml：全局配置
    >
    > application-dev.yml：生产环境配置
    >
    > application-prod.yml：开发环境配置
    >
    > application-test.yml：测试环境配置

    Profile的激活

    > 可以使用spring.profiles.active=dev····通过application.properties/application.yml来指定激活的配置文件，并通过命令行来替换修改；
    >
    > 此外，也可以通过Spring.prifiles.include属性无条件的激活配置文件（在指定配置文件中配置该属性，例如在prod中配置Spring.prifiles.include：prodbd，则只有prod被激活，他们均会被激活）；
    >
    > 还可以在SpringApplication入口处通过SpringApplication.setAddtionalProfiles(···)来设置激活profile

    关于特定的profile文件

    > 特定是profile文件，即除application.properties文件外，通过application-{profile}.properties命名规范命名的特定profile属性文件，若没有显示的激活特定profile，将加载application-default.properties配置文件
    >
    > **注意：**特定的profile和通过@ConfigurationProperties引用的文件被当做文件并加载