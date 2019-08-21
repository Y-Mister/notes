# Java动态性

### 一、反射机制:reflection

1. ##### 概念

   > 指的是可以于运行时加载、探知、使用编译期间完全未知的类；
   >
   > 程序**在运行状态下**，可以**动态加载**一个只有名称的类，对于任意一个已加载的类，都可以指导这个类的所有属性和方法，对于任意一个对象，都可以调用它的任意一个方法和属性：
   >
   > Class c = Class.forName("com.···.···.classname");
   >
   > 加载完类之后，在堆内存中，就产生了一个Class类型的对象（一个类只有一个class对象），这个对象包含了完整的类的结构信息。我们可以通过这个对象看到类的结构，这个对象就像一面镜子，透过这个镜子看到类的结构，我们称之为反射。

2. ##### 关于Class类的官方介绍

   > Instances of the class {@code Class} represent classes and interfaces in a running Java application.  An enum is a kind of class and an annotation is a kind of interface.  Every array also belongs to a class that is reflected as a {@code Class} object that is shared by all arrays with the same element type and number of dimensions.  The primitive Java types ({@code boolean}, {@code byte}, {@code char}, {@code short}, {@code int}, {@code long}, {@code float}, and {@code double}), and the keyword {@code void} are also represented as {@code Class} objects.

3. ##### 获取类对象的方法

   ```java
   /*待测试被获取类*/
   //注意，java bean必须要有无参构造函数
   public class testBean {
       private String name;
       private int id;
       private int age;
   
       public String getName() {
           return name;
       }
   
       public testBean(String name, int id, int age) {
           this.name = name;
           this.id = id;
           this.age = age;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public int getAge() {
           return age;
       }
   
       public void setAge(int age) {
           this.age = age;
       }
   }
   ```

   ```java
   /*获取class对象的方式*/
   public class demo01 {
   
       public static void main(String[] args) {
   
           String path = "com.yuan.reflection.bean.testBean";
   
   
           try {
               /*通过Class.forName获取类对象*/
               Class c = Class.forName(path);
               System.out.println(c);
   
               /*通过.class获取类对象*/
               Class c2 = String.class;
               /*通过对象的.getClass也可以获取*/
               Class c3 = path.getClass();   //path是一个String对象
               System.out.println(c2==c3);
   
               //基本数据类型也可以获得相应的类对象
               Class intclazz = int.class;
               System.out.println("-----------------------------");
               //同样元素类型，同样维度的数组共享同一个类对象
               int[] arr01 = new int[10];
               int[] arr02 = new int[20];
               int[][] arr03 = new int[10][5];
               System.out.println(arr01.getClass().hashCode());
               System.out.println(arr02.getClass().hashCode());
               System.out.println(arr03.getClass().hashCode());
   
           } catch (ClassNotFoundException e) {
               e.printStackTrace();
           }
       }
       
   }
   /*输出：
   class com.yuan.reflection.bean.testBean
   true
   -----------------------------
   1846274136
   1846274136
   1639705018
   */
   ```

4. ##### 根据获取到的类的对象通过API获取类的名称、方法、属性、构造器等信息

   ```java
   /*依旧以testBean为测试类*/
   public class demo02 {
       public static void main(String[] args) {
           String path = "com.yuan.reflection.bean.testBean";
   
           try {
               /*通过Class.forName获取类对象*/
               Class c = Class.forName(path);
   
               //获取类名
               System.out.println(c.getName());//获得全名
               System.out.println(c.getSimpleName());//获得类名
               System.out.println("--------------------------");
               //获得属性
               Field[] field = c.getFields();//只能返回public属性
               Field[]  field2 = c.getDeclaredFields();//返回所有属性
               System.out.println(field.length+field2.length);
               for (Field temp:field2){
                   System.out.println("属性:"+temp);
               }
               Field name = c.getDeclaredField("name");//通过名称获取指定属性
               System.out.println(name);
               System.out.println("----------------------------");
               //获得方法
               // c.getMethod(""); c.getMethods(); 只能获得public方法
               //获得全部方法则用以下方法
               Method[] methods = c.getDeclaredMethods();
               for (Method temp:methods){
                   System.out.println("方法："+temp);
               }
               //通过方法名第二个参数表示要取得的方法的传入参数类型,如果有参，必须传入对于参数类型的类对象，这是因为java有重载，所以通过参数进行区分
               Method nameSetter = c.getDeclaredMethod("setName",String.class);
               Method nameGetter = c.getDeclaredMethod("getName",null);
   
               System.out.println("---------------------------");
               //获得构造器
               Constructor[] constructors = c.getConstructors();//获得public构造器
               Constructor[] constructors1 = c.getDeclaredConstructors();//获得所有构造器
               for (Constructor temp : constructors1){
                   System.out.println("构造器："+temp);
               }
               //获得指定构造器
               Constructor constructor = c.getDeclaredConstructor(null);//获得空构造器
               Constructor constructor1 = c.getDeclaredConstructor(String.class,int.class,int.class);//通过参数类型获得指定构造器
               System.out.println(constructor);
               System.out.println(constructor1);
   
   
           } catch (ClassNotFoundException e) {
               e.printStackTrace();
           } catch (NoSuchFieldException e) {
               e.printStackTrace();
           } catch (NoSuchMethodException e) {
               e.printStackTrace();
           }
   
   
       }
   }
   /*输出结果为：
   com.yuan.reflection.bean.testBean
   testBean
   --------------------------
   3
   属性:private java.lang.String com.yuan.reflection.bean.testBean.name
   属性:private int com.yuan.reflection.bean.testBean.id
   属性:private int com.yuan.reflection.bean.testBean.age
   private java.lang.String com.yuan.reflection.bean.testBean.name
   ----------------------------
   方法：public java.lang.String com.yuan.reflection.bean.testBean.getName()
   方法：public int com.yuan.reflection.bean.testBean.getId()
   方法：public void com.yuan.reflection.bean.testBean.setName(java.lang.String)
   方法：public void com.yuan.reflection.bean.testBean.setAge(int)
   方法：public void com.yuan.reflection.bean.testBean.setId(int)
   方法：public int com.yuan.reflection.bean.testBean.getAge()
   ---------------------------
   构造器：private com.yuan.reflection.bean.testBean(java.lang.String)
   构造器：public com.yuan.reflection.bean.testBean(java.lang.String,int,int)
   构造器：public com.yuan.reflection.bean.testBean()
   public com.yuan.reflection.bean.testBean()
   public com.yuan.reflection.bean.testBean(java.lang.String,int,int)
   
   */
   ```

5. ##### 使用通过反射获得的构造器，参数与方法

   ```java
   /*依旧使用testBean为测试类*/
   public class demo03 {
   
       public static void main(String[] args) {
           //动态操作构造器
           String path = "com.yuan.reflection.bean.testBean";
   
            /*通过Class.forName获取类对象*/
           try {
               Class c = Class.forName(path);//此处可以使用泛型，那么下面将不需要转型Class<testBean> c =(Class<testBean>) Class.forName(path);
               //通过动态调用构造方法，构造对象
               testBean bean = (testBean) c.newInstance();//通过反射使用无参构造器，必须有无参构造器，否则将报错
               System.out.println(bean);
   
               /*获得有参构造器并使用*/
               Constructor<testBean> constructor = c.getDeclaredConstructor(String.class,int.class,int.class);
               testBean bean1 = constructor.newInstance("老王", 1001, 21);//新建对象实例
               System.out.println(bean1.getName());
   
               System.out.println("----------------------");
               /*通过反射API调用普通方法*/
               testBean bean2 = (testBean) c.newInstance();
               //通过反射获得方法
               Method method = c.getDeclaredMethod("setName",String.class);
               method.invoke(bean2,"老李");//invoke激活，对应于bean2.setName("老李")
               System.out.println(bean2.getName());
   
               System.out.println("--------------------------");
               /*通过反射操作属性*/
               testBean bean3 = (testBean) c.newInstance();
              //通过反射获得指定属性
               Field field = c.getDeclaredField("name");
               field.setAccessible(true);
               field.set(bean3,"老刘");//此处若直接设置，是无法对private属性进行设置的,必须设置accessible，private方法也一样需要设置
               System.out.println(bean3.getName());//此处直接通过对象方法获得属性值
               System.out.println(field.get(bean3));//通过反射获取属性值
   
   
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   }
   /*输出为：
   com.yuan.reflection.bean.testBean@6e0be858
   老王
   ----------------------
   老李
   ----------------------
   老刘
   老刘
   */
   ```

6. ##### 反射机制的性能问题，反射会影响效率

   > 关于setAccessible:启用和禁用安全检查的开关，值为true时则指示反射的对象在使用时应该取消java语言访问检查；值为false则指示反射的对象应该实施java语言访问检查，并不是为true就可以访问，false就不可以访问。通过反射使用对象私有型属性方法构造器时，需要取消访问检查；
   >
   > 禁用安全检查，可以提高反射的运行速度
   >
   > 还可以考虑使用：cglib/javaassist字节码操作

   ```java
   /*性能测试*/
   public class demo04 {
       public static void test01(){
           testBean bean1 = new testBean();
           long startTime = System.currentTimeMillis();
           for (int i=0; i < 1000000000L;i++ ){
               bean1.getName();
           }
           long endTime = System.currentTimeMillis();
           System.out.println("普通方法执行，执行时间："+(endTime-startTime)+" ms");
       }
   
       public static void test02(){
           try {
               testBean bean1 = new testBean();
               Class c = Class.forName("com.yuan.reflection.bean.testBean");
               //testBean bean2 = (testBean) c.newInstance();
               Method getname = c.getDeclaredMethod("getName",null);
               long startTime = System.currentTimeMillis();
               for (int i=0; i < 1000000000L;i++ ){
                   getname.invoke(bean1,null);
               }
               long endTime = System.currentTimeMillis();
               System.out.println("普通反射方法执行，执行时间："+(endTime-startTime)+" ms");
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   
       public static void test03(){
           try {
               testBean bean1 = new testBean();
               Class c = Class.forName("com.yuan.reflection.bean.testBean");
               //testBean bean2 = (testBean) c.newInstance();
               Method getname = c.getDeclaredMethod("getName",null);
               getname.setAccessible(true);
               long startTime = System.currentTimeMillis();
               for (int i=0; i < 1000000000L;i++ ){
                   getname.invoke(bean1,null);
               }
               long endTime = System.currentTimeMillis();
               System.out.println("关闭语法检查，反射方法执行，执行时间："+(endTime-startTime)+" ms");
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   
       public static void main(String[] args) {
           test01();
           test02();
           test03();
       }
   }
   /*输出结果：
   普通方法执行，执行时间：807 ms
   普通反射方法执行，执行时间：2201 ms
   关闭语法检查，反射方法执行，执行时间：1605 ms
   */
   ```

7. 反射如何操作泛型

   > java采用泛型擦除的机制来引入泛型，java中的泛型仅仅是给编译器使用的，确保数据的安全性和免去强制类型转换的麻烦。但是，一旦编译完成，所有和泛型有关的类型全部擦除。
   >
   > 为了通过反射操作这些泛型，java新增加了ParameterizedType、GenericArrayType、TypeVariable和WildCardType几种类型来代表不能被归一到Class类中的类型但是又和原始类型齐名的类型。
   >
   > ParameterizedType：表示一种参数化的类型，比如Collection< String >中的String,通常<>中存放的就是参数化类型
   >
   > GenericArrayType:表示一种元素类型是参数化类型或者类型变量的数组类型
   >
   > TypeVariable：代表各种类型变量的公共父接口
   >
   > WildCardType：表示一种通配符类型选择表达式，比如？，？extends Number，？ super integer 【WildCard原意就是通配符】

   ```java
   /*demo*/
   public class demo05 {
   
       public void test01(Map<String,testBean> map,List<testBean> list){
           System.out.println("demo05 test01");
       }
   
       public Map<Integer,testBean> test02(){
           System.out.println("demo05 test02");
           return null;
       }
   
       public static void main(String[] args) {
   
           try {
               //获得指定方法参数泛型信息
               Method m = demo05.class.getDeclaredMethod("test01",Map.class,List.class);
               Type[] types = m.getGenericParameterTypes(); //获得带泛型参数类型
   
               for (Type paramType:types){
                   System.out.println("#"+paramType);//输出参数类型
                   if (paramType instanceof ParameterizedType){//是否是参数化类型
                       Type[] gengricTypes = ((ParameterizedType) paramType).getActualTypeArguments();//获得参数中的泛型
                       for (Type geneicType:gengricTypes){
                           System.out.println("泛型类型："+geneicType);
                       }
                   }
               }
   
               Method m2 = demo05.class.getDeclaredMethod("test02",null);
               Type returnType = m2.getGenericReturnType();//得到一个方法的正式返回类型
               if (returnType instanceof ParameterizedType){  //是否是参数化类型
                   Type[] types1 = ((ParameterizedType) returnType).getActualTypeArguments();//获得参数化类型
                   for (Type generictype:types1){
                       System.out.println("返回值泛型类型："+generictype);
                   }
               }
   
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   }
   /*输出结果：
   #java.util.Map<java.lang.String, com.yuan.reflection.bean.testBean>
   泛型类型：class java.lang.String
   泛型类型：class com.yuan.reflection.bean.testBean
   #java.util.List<com.yuan.reflection.bean.testBean>
   泛型类型：class com.yuan.reflection.bean.testBean
   
   返回值泛型类型：class java.lang.Integer
   返回值泛型类型：class com.yuan.reflection.bean.testBean
   */
   ```

8. 反射操作注解

   可参考java注解.md



### 二、动态编译 java compiler

```java
public class demo01 {

    public static void main(String[] args) throws IOException {
        JavaCompiler compiler = ToolProvider.getSystemJavaCompiler();
        /**
         * 参数一：通常是一个输入流
         * 参数二：通常是一个输出流
         * 参数三：通常是一个错误输出流
         * 参数四：待编译类的classPath
         */
        int result = compiler.run(null,null,null,"D:/yuanHang/myJava/helloWorld.java");
        System.out.println(result==0?"编译成功":"编译失败");

        //通过Runtime启动新线程动态运行编译好的类
        /*Runtime runtime = Runtime.getRuntime();
        Process process = runtime.exec("java -cp D:/yuanHang/myJava/ helloWorld");
        BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()));
        String str;
        while ((str = reader.readLine())!=null){
            System.out.println(str);
        }*/

        //通过反射和类加载器动态加载类
        URL[] urls = new URL[]{new URL("file:/"+"D:/yuanHang/myJava/")};
        URLClassLoader loader = new URLClassLoader(urls);
        try {
            Class c = loader.loadClass("helloWorld");
            c.getMethod("main",String[].class).invoke(null, (Object) new String[]{"aa","bb"});//此处若不加Object强转,则传入参数将变成"aa","bb"而不是一个字符串数组
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }


        /*如果想对该字符串进行动态编译，可以需要将其存储成一个临时文件，然后调用动态编译*/
        /*String str = "public class Hi{public static void main(String[] args){System.out.println(\"Hi\");}}}";
        File file = new File("testDynamicCompile.java");
        byte[] bytes = str.getBytes();
        OutputStream outputStream = new FileOutputStream(file);
        outputStream.write(bytes,0,bytes.length-1);
        outputStream.flush();
        outputStream.close();
        int result2 = compiler.run(null,null,null,"testDynamicCompile.java");
        System.out.println(result2==0?"编译成功":"编译失败");
        此处编译失败，原因未知*/
    }
}
/*输出结果：
编译成功
Hello world!
*/
```

