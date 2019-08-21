### Lambda函数式编程

1. 基本概念

   > 避免内部类定义过多；
   >
   > 其实质是函数式编程；
   >
   > 使用语法：param->expression;
   >
   > ​					param->statment;
   >
   > ​					param->{statments};
   >
   > ​					(params)->expression/statment/{statments};

2. lambda基本推导

   ```java
   public class lambdaTest01 {
   
       static class Like2 implements ILike{
           @Override
           public void lambda() {
               System.out.println("i like lambda2");
           }
       }
   
   
       public static void main(String[] args) {
           /*1.外部类*/
           ILike like = new Like();
           like.lambda();
   
           /*2.静态内部类*/
           ILike like2 = new Like2();
           like2.lambda();
   
   
           /*3.方法内部类*/
           class Like3 implements ILike{
               @Override
               public void lambda() {
                   System.out.println("i like lambda3");
               }
           }
           ILike like3 = new Like3();
           like3.lambda();
   
   
   
           /*4.匿名内部类*/
           ILike like4 = new ILike(){
   
               @Override
               public void lambda() {
                   System.out.println("i like lambda4");
               }
           };
           like4.lambda();
   
           /*4.使用lambda简化*/
           ILike like5 = ()->{
                   System.out.println("i like lambda5");
           };
           like5.lambda();
   
           /**
            * lambda推导必须存在类型，他需要根据你所赋给的对象来进行推导,所以下面的写法是错误的
            * ()->{
                System.out.println("i like lambda5");
                };
                like5.lambda();
            */
       }
   }
   
   interface ILike{
       void lambda();
   
   }
   
   class Like implements ILike{
   
       @Override
       public void lambda() {
           System.out.println("i like lambda1");
       }
   }
   ```



3. 带参数的lambda写法以及持续简化

   ```java
   public class lambdaTest02 {
   
       public static void main(String[] args) {
           ILove love = (int a)->{
               System.out.println("I love lambda-->"+a);
           };
           love.lamba(100);
   
           //简化
           love = (a)->{
               System.out.println("I love lambda-->"+a);
           };
           love.lamba(50);
           //只有一个参数时，括号也可以省略
           love = a->{
               System.out.println("I love lambda-->"+a);
           };
           love.lamba(25);
           //如果程序体只有一行代码，还可以继续省略
           love = a-> System.out.println("I love lambda-->"+a);
           love.lamba(10);
       }
   }
   
   interface ILove{
       void lamba(int a);
   
   }
   /*外部类，作参考用*/
   class Love implements ILove{
   
   
       @Override
       public void lamba(int a) {
           System.out.println("I love lambda-->"+a);
       }
   }
   ```



4. 带返回值的lambda写法及持续简化

   ```java
   public class lambdaTest03 {
   
   
       public static void main(String[] args) {
           //简化形式1
           IInterset interest = (int a,int b) -> {
               System.out.println("i am interseted in-->"+a+"+"+b);
               return a+b;
           };
           int sum = interest.lambda(1,1);
           System.out.println("a+b="+sum);
   
           //简化形式2，省略类型，但括号不能省
           interest = (a,b) -> {
               System.out.println("i am interseted in-->"+a+"+"+b);
               return a+b;
           };
           sum = interest.lambda(1,10);
           System.out.println("a+b="+sum);
   
           //简化形式3，只有一行return时，可以省略花括号与return
           interest = (a,b)->a+b;
           sum = interest.lambda(5, 5);
           System.out.println("a+b="+sum);
       }
   
   
   }
   
   
   interface IInterset{
       int  lambda(int a,int b);
   }
   
   //外部类形式，作简化lambda的参考
   class Interest implements IInterset{
   
       @Override
       public int lambda(int a, int b) {
           System.out.println("i am interseted in-->"+a+"+"+b);
           return a+b;
       }
   }
   ```

   

5. lambda写法的使用总结

   > lambda表达式必须要进行赋值或作为参数，否则将无法完成lambda的推导；
   >
   > lambda一般只用于程序体较为简单的地方；