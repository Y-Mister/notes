## springMVC注解

#### 一、关于注解

| 注解            | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| @controller     | 将类标记为控制器(用于control层)                              |
| @RequestMapping | 用于标记处理请求的路径(control层中使用)                      |
| @Service        | 用于服务层，将类标记为服务                                   |
| @Repository     | 用于dao层，用于标记dao接口                                   |
| @Autowired      | 自动注入，即成员变量、方法、构造函数如果是已被spring管理，可通过该标记自动注入，不需要手动创建 |
| @Override       | 标记该方法将覆盖超类方法                                     |
| @Component      | mapper层使用（mybatis不这么用）                              |
| @ResponseBody   | 消息转换机制注解，当controller方法返回值需要转换成json等其他类型时使用 |
| @ModelAttribute | 放置于controller的方法上，则只需该controller的任意方法前都将先执行该方法 |
|                 |                                                              |

> @Service,@Repository注解可以指定该类被spring管理时使用的别名，如若不指定，则默认为类名首字母小写，在自动导入时对应的对象名必须是对应类在spring中被管理时用的别名，否则将无法注入【@Service("别名")  @Repository(value="别名")】
>
> 需要注意的是，注解的使用需要在初始化时加载一个注解驱动器：<mvc:annotation-driven/>；
>
> 所有使用了注解的包必须在xml文件中<context:component-scan base-package="packageName"/>来指明该被spring管理，否则注解将不生效

> mybatis的mapper需要在xml文件中生成对于的mapper代理，以进行使用

