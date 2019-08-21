### Java设计模式

设计模式是一种高内聚低耦合的方法论，使得复杂代码的维护变得简单。一般装饰器有四大组成部分：

抽象组件：需要装饰的抽象对象（接口或抽象父类）

具体组件：要装饰的对象

抽象装饰类：包含了对抽象组件的引用以及装饰者共有的方法

具体装饰类：被装饰的对象

##### 装饰器模式

1. 简单的装饰设计模式，模拟放大器		

   ```java
   public class DecorateTest01 {
   
       public static void main(String[] args) {
           Person p = new Person();
           p.say();
   
           Amplifier am = new Amplifier(p);
           am.say();
       }
   }
   
   interface Say{
       void say();
   }
   class Person implements Say{
       private int voice=10;
       @Override
       public void say() {
           System.out.println("人的声音为："+this.getVoice());
       }
   
       public int getVoice() {
           return voice;
       }
   
       public void setVoice(int voice) {
           this.voice = voice;
       }
   }
   
   class Amplifier implements Say{
   
       private Person p;
   
       Amplifier(Person p){
           this.p=p;
       }
   
       @Override
       public void say() {
           System.out.println("人的声音："+p.getVoice()*100);
           System.out.println("噪音");
       }
   }
   ```

   个人分析：

   > 两个继承自统一接口的类，一个类在另一个类（装饰器）中进行了修饰

2. 功能即模块完善的装饰设计模式，咖啡

   ```java
   public class DecorateTest02 {
   
       public static void main(String[] args) {
               Drink coffee = new Coffee();
               Drink sugar = new Sugar(coffee);
               System.out.println(sugar.info()+"---"+sugar.cost()+"元");
               Drink milk = new Milk(coffee);
               System.out.println(milk.info()+"---"+milk.cost()+"元");
               Drink milk = new Milk(sugar);
               System.out.println(milk.info()+"--->"+milk.cost()+"元");
       }
   }
   //抽象组件
   interface Drink{
       double cost();
       String info();
   }
   //具体组件
   class Coffee implements Drink{
   
       private String name="卡布奇诺";
       @Override
       public double cost() {
           return 25;
       }
   
       @Override
       public String info() {
           return name;
       }
   }
   
   //抽象的装饰类
   abstract class Decorate implements Drink{
       //对抽象组件的引用
       private Drink drink;
   
       Decorate(Drink drink){
           this.drink = drink;
       }
       @Override
       public double cost() {
           return this.drink.cost();
       }
   
       @Override
       public String info() {
           return this.drink.info();
       }
   }
   
   //具体装饰类
   class Milk extends Decorate{
   
   
       Milk(Drink drink) {
           super(drink);
       }
   
       @Override
       public double cost() {
           return super.cost()*4;
       }
   
       @Override
       public String info() {
           return super.info()+"加奶";
       }
   }
   
   class Sugar extends Decorate{
   
       Sugar(Drink drink) {
           super(drink);
       }
   
       @Override
       public double cost() {
           return super.cost()+2;
       }
   
       @Override
       public String info() {
           return super.info()+"加糖";
       }
   }
   /*执行结果：
   卡布奇诺加糖---27.0元
   卡布奇诺加奶---100.0元
   卡布奇诺加糖加奶--->108.0元
   */
   ```

   





##### 代理模式

分为：静态代理和动态代理，通常用于记录日志，增强服务

1. 静态代理的实现

   ```java
   /* Description:静态代理
    * 公共接口：
    * 1.真实角色
    * 2.代理角色
    */
   public class StaticProxy {
   
       public static void main(String[] args) {
           new WeddingCompany(new You()).happyMarry();
           // new Thread(线程对象).start();
       }
   
   }
   
   interface Marry{
       void happyMarry();
   }
   //真是角色
   class You implements Marry{
       @Override
       public void happyMarry() {
           System.out.println("你和嫦娥终于奔月了");
       }
   }
   
   //代理角色
   class WeddingCompany implements Marry{
   
       private Marry target;
       public WeddingCompany(Marry target) {
           this.target = target;
       }
   
       @Override
       public void happyMarry() {
           before();
           target.happyMarry();
           after();
       }
   
       private void before(){
           System.out.println("布置环境");
       }
       private void after(){
           System.out.println("撤出环境");
       }
   }
   ```

   

##### 单例模式

1. 写法多样

   > 懒汉式、饿汉式、double-chcking、dcl等等，其主要变现为对外只有一个对象，对内则不一定

2. 懒汉式基础上在多线程环境下保证对外存在一个对象，其中使用double-checking保证效率

   ```java
   public class DoubleChecking {
       //2.提供私有静态属性
       private static volatile DoubleChecking instace;//此处如果直接new一个对象，则称为饿汉式，如果想当前这样空着，则称为懒汉式
       //若不加volatile，则可能导致其他进程访问一个未初始化的空对象
       //1.构造器私有化
       private DoubleChecking(){
   
       }
   
       public static DoubleChecking getInstance(){
           //此处为Double-Checking
           if (null!=instace){
               return instace;
           }
           synchronized (DoubleChecking.class){
               if (null==instace){
                   instace = new DoubleChecking();
                   //new一个对象的步骤
                   //开辟空间、初始化对象信息、返回对象地址给引用
                   //可能存在的指令重排情况：初始化对象耗时，将一个空对象返回给引用，避免的方法，给instance加volatile,保证对象修改后同步
               }
               return instace;
           }
       }
   
       public static void main(String[] args) {
               Thread t =new Thread(()->{
                   System.out.println(DoubleChecking.getInstance());
               });
               t.start();
               Thread t2 =new Thread(()->{
                   System.out.println(DoubleChecking.getInstance());
               });
               t2.start();
       }
   
   
   }
   /*结果：
   com.yuan.ThreadCommunication.DoubleChecking@4eba943c
   com.yuan.ThreadCommunication.DoubleChecking@4eba943c
   加上延时后效果也相同
   */
   ```

   补充：
   
   ```java
   public class Monitor{
       public static String CLASS_INFO = "A班";
       private static Monitor mointor = new Monitor();
       private Monitor(){}
       public static Monitor getMonitor(){
           return monitor;
       }
   }
   /*一致班级信息是静态的，现在想要过去班级信息而不要执行构造方法将monitor对象初始化，则需要做如下更改：*/
   public class Monitor{
       public static String CLASS_INFO = "A班";
       private static class MonitorCreator{
           private static Monitor mointor = new Monitor();
       }
       private Monitor(){}
       public static Monitor getInstance(){
           return MOnitorCreater.monitor;
       }
   }
   /*静态内部类不会随着外部类的加载而加载，只有静态内部类的静态成员变量被调用时才会加载，这样既保证了惰性初始化，又由JVM保证了多线程并发访问的安全性（JVM内部机制保证当一个类被加载的时候，这个类的加载过长是线程互斥的）*/
   ```
   
   **枚举单例：**
   
   ```java
   public class enumTest {
       public static void main(String[] args) {
           DBConnection conn1 = DataSourceEnum.DATASOURCE.getConnection();
           DBConnection conn2 = DataSourceEnum.DATASOURCE.getConnection();
           System.out.println(conn1 == conn2);
   
       }
   
   }
   
   enum  DataSourceEnum {
       DATASOURCE;
       private DBConnection connection = null;
       private DataSourceEnum(){
           connection = new DBConnection();
       }
       public DBConnection getConnection(){
           return connection;
       }
   }
   
   class DBConnection {
   
   }
   /*单例，输出结果为true*/
   ```
   
   关于Enum
   
   ```java
   public class enumTest {
       public static void main(String[] args) {
           DBConnection conn1 = DataSourceEnum.DATASOURCE.getConnection();
           DBConnection conn2 = DataSourceEnum.DATASOURCE.getConnection();
           DBConnection conn3 = DataSourceEnum.MYENUM.getConnection();
           DBConnection conn4 = DataSourceEnum.MYENUM.getConnection();
   
           System.out.println(conn1 == conn2);
           System.out.println(conn2 == conn3);
           System.out.println(conn4 == conn3);
   
       }
   
   
   }
   
   class DBConnection {
   
   }
   
   enum  DataSourceEnum {
       DATASOURCE,MYENUM;
       private DBConnection connection = null;
       private DataSourceEnum(){
           connection = new DBConnection();
           System.out.println("枚举初始化");
       }
       public DBConnection getConnection(){
           return connection;
       }
   
   }
   /*运行结果：
   枚举初始化
   枚举初始化
   true
   false
   true
   */
   /*结果产生原因分析：DATASOURCE,MYENUM均属于DataSourceEnum的静态成员常量，初始化时会通过构造函数依次对静态常量进行初始化，所以两个成员常量输出了两次构造体；
   但是根据每个成员常量获得的Connection比较可以发现，通过每个成员常量获得的类实例是相同的，为单例，但是不同成员常量之间获得的类实例不相同*/
   ```
   
   