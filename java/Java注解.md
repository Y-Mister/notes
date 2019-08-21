### Java注解

#### 什么是注解

注解不是程序本身，可以对程序作出解释。重点在于其可以被其他程序（编译器）读取，例如通过反射处理注解信息，如果没有注解信息处理流程，注解和注释将没有区别，即注解必须被解析才有意义。

#### 内置注解

| 注解             | 作用                                                         |
| ---------------- | ------------------------------------------------------------ |
| @Override        | 修饰方法，指明被注解方法打算重写父类方法                     |
| @Deprecated      | 修饰方法、属性、类，表明不鼓励程序员使用该元素，通常因为它很危险或存在更好的选择 |
| @SuppressWarning | 修饰方法、属性、类型等等，用来抑制编译时警告，可指明抑制的警告类型@SupressWarning(value={"",""}) |

#### 元注解（对注解的进一步解释）

| 注解       | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| @Target    | 修饰注解类，指明该注解的使用范围可以修饰的地方               |
| @Retention | 表示需要在什么级别保留该注解信息，用于描述注解的生命周期，取值有SOURCE、CLASS、RUNTIME，一般自定义注解都为RUNTIME; |
| @Document  | 被该注解标记的注解A在注解其他资源时，注解A的信息将会包含在被A注解标注的资源的javadoc文档中 |
| @Inherited | 被该注解标记的注解A，如果注解A用于注解类，那么该注解将会被其标注的类的子类所继承，只注解类时有效 |



#### 自定义注解

1. 要点

   > 使用@interface来自定义注解，自动继承java.lang.annotation.Annotation接口；
   >
   > 格式为：public @interface 注解名（定义注解体）
   >
   > 其中的每一个方法实际上就是申明了一个配置参数，即方法即是参数：
   >
   > 方法名称就是参数名称；
   >
   > 返回值类型就是参数的类型（返回值类型只能是基本类型、Class、String、enum）；
   >
   > 可以通过default来声明参数的默认值；
   >
   > 如果只有一个参数成员，一般参数名为value，且赋值时可以省略参数名；
   >
   > 需要注意的是，注解元素的参数成员必须要有值，使用default来指明默认值，经常使用空字符串和0或者-1等负数作为默认值，负数通常表示不存在，若不指明默认值，则使用该注解时必须为该参数赋值；

   ```java
   @Target(value = {ElementType.TYPE,ElementType.METHOD})
   @Retention(RetentionPolicy.RUNTIME)
   public @interface myAnnotation {
       String student() default "";//以下均为属性而非方法
       int age() default 0;
       int id();
       String [] schools() default "";
       
   }
   /*使用*/
   public class myAnnotationTest {
   
       @myAnnotation(id=5)//id没有默认值，必须指定
       public void test(){
   
       }
   }
   ```

   

2. 插曲，ORM(object relationship mapping)对象关系映射

   > 1.类和表结构对应；
   >
   > 2.属性和字段对应；
   >
   > 3.对象和记录对应，一条记录对应一个对象；

#### 反射读取注解

1. 注解的完整使用

   > 1.定义注解
   >
   > 2.在类中使用注解
   >
   > 3.通过解析程序读取注解的内容

2. demo

   此处对应一个orm，对应数据库中的tb_student表

   ```java
   /*注解的定义1*/
   /**
    * Description:此为表名注解
    */
   @Target(value = ElementType.TYPE)
   @Retention(RetentionPolicy.RUNTIME)
   public @interface StuTable {
       String value();
   }
   /*注解的定义2*/
   /**
    * Description:此为表内字段的注解
    */
   @Target(ElementType.FIELD)
   @Retention(RetentionPolicy.RUNTIME)
   public @interface stuField {
       String columnName();
       String type();
       int length();
   }
   ```

   ```java
   /*类的定义与注解的使用*/
   @StuTable(value = "tb_student") //表明对应的表
   public class RefStudent {
       @stuField(columnName = "id",type = "int",length = 10)  //指明该成员变量对应的字段
       private int id;
       @stuField(columnName = "sname",type = "varchar",length = 10)
       private String studentName;
       @stuField(columnName = "age",type = "int",length = 3)
       private int age;
   
       //省略get/set方法
   }
   ```

   ```java
   /*通过反射机制获取类与注解的信息*/
   public class demo03 {
   
       public static void main(String[] args) {
           try {
               //通过反射获取类的全部信息
               Class relStudent = Class.forName("com.yuan.Annotation.RefStudent");
               //获得该类的所有注解
               Annotation[] annotations = relStudent.getDeclaredAnnotations();
               for (Annotation annotation:annotations){
                   System.out.println(annotation);
               }
               //通过注解类获取类的指定注解
               StuTable table = (StuTable) relStudent.getAnnotation(StuTable.class);
               System.out.println(table);
   
               //获得类的属性注解,先获取属性，在通过属性获取属性的注解
               Field name = relStudent.getDeclaredField("studentName");
               stuField stuField = name.getAnnotation(stuField.class);
               System.out.println(stuField.columnName()+"-->"+stuField.type()+"--"+stuField.length());
   
               //根据获得的表明字段等信息，就可以拼接出sql语句
           } catch (ClassNotFoundException e) {
               e.printStackTrace();
           } catch (NoSuchFieldException e) {
               e.printStackTrace();
           }
       }
   }
   /*输出结果：
   @com.yuan.Annotation.StuTable(value=tb_student)
   @com.yuan.Annotation.StuTable(value=tb_student)
   sname-->varchar--10
   */
   ```

   