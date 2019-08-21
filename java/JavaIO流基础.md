### JavaIO流基础

一切以程序为中心，那么进入程序的，就称为输入流，从程序中出去的，就是输出流

##### 核心类

![1563794533064](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1563794533064.png)

##### 流分类

1. 按功能分为节点流和处理流

   > 节点流：可以直接从数据源或目的地读写数据,一般前缀是File,byte
   >
   > 处理流（包装流）：不是直接连接到数据源或目的地，是其他流进行封装，目的主要是简化操作和提高性能；
   >
   > 节点流和处理流的管理：
   >
   > ​	节点流处于IO最基本的位置，所有操作都必须通过他们进行；
   >
   > ​	处理流可以对其他流进行处理，提高效率和操作灵活性

2. 按方向分为输入和输出流

3. 按处理单元分为字节流和字符流

   > 字节流：以字节为单位获取数据，命名上以Stream结尾的流一般是字节流，如FileInputStream、FileOutputStream。
   >
   > 字符流：以字符为单位获取数据，命名上以Reader/Writer结尾的流一般是字符流，如FileReader、FileWriter。底层任然是基于字节流操作



##### File文件流常用API与编码

1. 查看一个自己不熟悉的类，应该怎么做呢？

   > 先查看继承体系；
   >
   > 查看构造器，有直接使用，没有则看其是不是静态工具类，或者静态方法返回实例对象；
   >
   > 查看方法，关注方法名，形参，返回值

2. 相对路径与绝对路径

   > 存在盘符：绝对路径
   >
   > 不存在盘符：相对路径，相对于用户工作空间，user.dir

3. File对象的实质，是一个路径表现形式

   > 用File对象，可以构建一个文件，可以构建一个不存在的路径，也可以构建一个文件夹

4. 文件的相关API操作

   ```java
           File file = new File("D:/magic2.txt");
           System.out.println(file.getName());//获取文件名称
           System.out.println(file.getPath());//获得路径
           System.out.println(file.getAbsolutePath());//获得绝对路径
           System.out.println(file.getParent());//取出传入路径最后有一个分割符前面的内容，有就打印，没有就是null
           System.out.println(file.getParentFile().getName());//获取父对象
           System.out.println(file.exists());//文件是否存在
           System.out.println(file.isFile());//是否是文件
           System.out.println(file.isDirectory());//是否是文件夹
           System.out.println(file.length());//如果是文件，返回文件长度，否则返回0
           File src = new File("IO.txt");
           System.out.println(src.createNewFile());//文件不存在，则创建文件，如果文件本来存在，将创建失败
           System.out.println(src.delete());//删除已经存在的文件
   
           //补充con com3...是操作系统设备名，无法进行创建
           src = new File("con");
           System.out.println(src.createNewFile());//无法创建
   /*结果
   magic2.txt
   D:\magic2.txt
   D:\magic2.txt
   D:\
   true
   true
   false
   76
   true
   true
   false
   */
   ```

5. 文件夹相关

   ```java
           File file =new File("D:/电影/华语/test");
           System.out.println(file.mkdir());//确保上级目录一定要存在，不存在则创建失败
           System.out.println(file.mkdirs());//上级目录可以不存在，不存在则一同创建，推荐使用
           File src =new File("D:/电影");
           String[] subNames = src.list();//返回下级目录，只有一层
           for (String name:subNames){
               System.out.println(name);
           }
   
           File[] subfiles = src.listFiles();//返回下一层文件对象
           for (File ff:subfiles){
               System.out.println(ff.getAbsolutePath());
           }
   
           File[] fff = src.listRoots();//返回所有盘符
           for (File r:fff){
               System.out.println("盘符"+r.getAbsolutePath());
           }
   /*结果
   false
   true
   华语
   D:\电影\华语
   盘符C:\
   盘符D:\
   盘符E:\
   盘符F:\
   
   */
   ```

6. File字符集与乱码

   > 字符->字节：编码
   >
   > 字节->字符：解码
   >
   > utf-8：一个汉字3个字节；
   >
   > GBK：一个汉字2个字节；

   ```java
   //编码与解码
   		String msg = "性命生命使命";
           //编码encode：字节数组
           byte[] datas = msg.getBytes();//默认使用工程的字符集
           System.out.println(datas.length);//GBK一个汉字2个字节
   
           datas = msg.getBytes("utf-8");
           System.out.println(datas.length);
           datas = msg.getBytes("utf-16LE");
           System.out.println(datas.length);
   
           //解码decode 字节->字符串
           msg = new String(datas,"utf-16LE");//一定要给出正确的编码，否则会乱码，此外重载的方法还有很多
           System.out.println(msg);
   /*输出
   12
   18
   12
   性命生命使命
   */
   ```

7. IO流 四大抽象类

   > ![1563805762844](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1563805762844.png)
   >
   > 字节流可以处理一切，但是字符流只能处理纯文本

##### 字节流相关

8. 文件字节输入流基本操作

   ```java
   //abc.txt内容为adf
   //1.创建源
    		File src = new File("abc.txt");
           FileInputStream fi=null;
           try {
               //2.选择流
               fi = new FileInputStream(src);
               //3.操作
               int data1 = fi.read();
               int data2 = fi.read();
               int data3 = fi.read();
               int data4 = fi.read();
               System.out.println((char)data1);
               System.out.println((char)data2);
               System.out.println((char)data3);
               System.out.println(data4);//文件末尾，返回-1
               
               //4.释放资源
   			fi.close()
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }
   /*结果
   a
   d
   f
   -1
   
   */
   ```

   标准操作步骤，通过循环实现

   ```java
           //创建源
           File src = new File("abc.txt");
           FileInputStream fi=null;
           try {
               //选择流
               fi = new FileInputStream(src);
               int temp;
               //操作
               while ((temp=fi.read())!=-1){
                   System.out.println((char)temp);
   
               }
               //释放资源
   
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }finally{
               try {
                   if (fi!=null)
                       fi.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   ```

   通过字节数组读取指定字节长度（分段读取）

   ```java
   		//创建源
   		File file = new File("D:/magic2.txt");
           InputStream is = null;
   
           try {
               is= new FileInputStream(file);
               //int temp;
               byte[] flush = new byte[1024];//字节数组长度取决于你想读取的字节长度
               int len=-1;
               while((len=is.read(flush))!=-1){
                   //字节数组到字符串
                   String str= new String(flush,0,len);//解码，要取实际大小，否则会将垃圾读入
                   System.out.print(str);
               }
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   if (is!=null){
                       is.close();
                   }
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   ```

9. 文件字节输出流基本操作

   ```java
   //创建源
           File file = new File("D:/magic3.txt");
           //选择流
           OutputStream os =null;
           InputStream is = null;
           try {
               os =  new FileOutputStream(file);//每次写入覆盖之前的文件内容,文件不存在将自动创建
               //os =  new FileOutputStream(file,true);//每次写入在之前的文件内容后追加
               String msg = "i'm studing io stream now";
               //编码准备写入
               byte[] datas = msg.getBytes();
               os.write(datas,0,datas.length);
               os.flush();//强制刷新，避免文件驻留内存中
   
               File file1 = new File("D:/magic3.txt");
               is = new FileInputStream(file1);
               byte[] flush = new byte[1024];
               int len=-1;
               while ((len=is.read(flush))!=-1){
                   //解码
                   String str = new String(flush,0,flush.length);
                   System.out.println(str);
               }
   
   
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   if (null!=os){
                       os.close();
                   }
                   if (is!=null){
                       is.close();
                   }
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   ```

10. 文件的拷贝：以程序为中转站，通过字节流将文件读入再写出

     ```java
    	public static void copy(String srcPath,String destPath){
            //创建源 输入与输出文件
            File src = new File(srcPath);
            File dest = new File(destPath);
            InputStream is = null;
            OutputStream os = null;
    
            try {
                is = new FileInputStream(src);
                os = new FileOutputStream(dest);
                byte[] flush = new byte[3];
                int len=-1;
                while ((len=is.read(flush))!=-1){
                    os.write(flush,0,len);
                }
    
    
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                try {
                    //关闭流，一个小知识先打开的后关闭
                    if (os!=null){
                        os.close();
                    }
    
                    if (is!=null){
                        is.close();
                    }
    
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
    
    
    
        }
     ```

11. 小作业：递归实现文件夹的层层拷贝

    ```java
    public static void main(String[] args) {
    
            File file = new File("D:/电影");
            File destFile = new File("E:/");
            listDir(file,destFile);
    
        }
    
    
        public static void listDir(File file,File destFile){
    
            if (file.isDirectory()){ //是目录，首先根据目标位置创建一个相同名称的文件夹
                File newFile = new File(destFile.getAbsolutePath(),file.getName());
                newFile.mkdirs();
                File[] files = file.listFiles();//获取该文件夹下的所有文件对象
                for (File file1:files){
                    listDir(file1,newFile);//递归调用
                }
    
            }else{//是文件，在目标位置创建一个文件对象，将原文件内容拷贝过去
                File newFile = new File(destFile.getAbsoluteFile(),file.getName()); 
                copy(file.getAbsolutePath(),newFile.getAbsolutePath());
                return;
            }
    
    
    
        }
    
    
        public static void copy(String srcPath,String destPath){
            //创建源 输入与输出文件
            File src = new File(srcPath);
            File dest = new File(destPath);
            InputStream is = null;
            OutputStream os = null;
    
            try {
                is = new FileInputStream(src);
                os = new FileOutputStream(dest);//如果不存在将主动创建
                byte[] flush = new byte[3];
                int len=-1;
                while ((len=is.read(flush))!=-1){
                    os.write(flush,0,len);
                }
    
    
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                try {
                    //关闭流，一个小知识先打开的后关闭
                    if (os!=null){
                        os.close();
                    }
    
                    if (is!=null){
                        is.close();
                    }
    
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
    
    
    
        }
    ```



##### 	字符流相关FileReader/FileWriter

1. 相关知识

   > FileReader:通过字符方式读取文件，仅适合纯文本文件
   >
   > FileWriter:通过字节的方式写入或追加数据到文件，仅适合文本文件

2. FileReader相关方法的使用与结果

   ```java
           //创建源
           File src = new File("abc.txt");
           //选择流
           FileReader fr = null;
   
           try {
               fr = new FileReader(src);
               char[] flush = new char[1024];//操作的是字符，所以要读入到字符数组中
               int len=-1;
               while ((len=fr.read(flush))!=-1){
                   String str = new String(flush,0,len);
                   System.out.println(str); //乱码，待解决
               }
   
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   if (fr!=null)
                       fr.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   /*结果为：
   IO is easy action!
   aaaa
   */
   ```

3. FileWriter相关方法的使用与结果

   ```java
           //创建源
           File src = new File("abc.txt");
           //选择流
           FileWriter fw = null;
   
           try {
               fw = new FileWriter(src);
               String str = "IO is easy action!\n这是";
               char[] array = str.toCharArray();//字符串转化为字符数组
               //写法一，可以进行部分写入
               // fw.write(array,0,array.length);
               //写法二：不需要部分写入，那么直接传入数据
               //fw.write(str);
   
               //写法三:适用于多次写入,是追加而不是覆盖
               fw.append("IO is easy action!\n");
               fw.append("aaaa");
               fw.flush();
   
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   if (fw!=null)
                       fw.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   /*结果：将str内容写入到文件中*/
   ```

##### 字节数组流

1. 注意

   > 即源头由文件换成字节数组，字节数组流不用关闭，由gc进行回收；
   >
   > 已知，任何数据都可以转化为字节数组，方便进行网络的传输；
   >
   > 将数据转化为字节数组时，尽量保证量小一点，避免内存饱和降低运行效率；
   >
   > 总是忘记的一点：读取或者写入一个文件它是连续的，不需要考虑其他诸如图片分段读取或写入会发生错误等问题

2. 字节数组输入流的操作

   ```java
   //创建源：字节数组 不要太大
           byte[] src = "talk is cheap show me the code".getBytes();
   
           InputStream fi=null;
           try {
               //选择流
               fi = new ByteArrayInputStream(src);
               //操作
               byte[] flush = new byte[5];
               int len=-1;
               while ((len=fi.read(flush))!=-1){
                   String str = new String(flush,0,len);
                   System.out.println(str);
   
               }
               //释放资源：可以不用处理
   
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }finally{
               try {
                   if (fi!=null)
                       fi.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   /*结果：
   talk 
   is ch
   eap s
   how m
   e the
    code
   */
   ```

3. 字节数组输出流（较为特殊，需要注意）

   ```java
   //创建源：原则上字节数组输出流是没有源头的，这里是为了保持和之前的风格统一
           byte[] dest = null;
   
           //选择流,此处要用到ByteArrayOutputStream特有的方法，所有不能用父类来定义即不能使用多态
           ByteArrayOutputStream os = null;
           try {
           os = new ByteArrayOutputStream();
           //操作
           String str = "Show me the code";
           byte[] msg = str.getBytes();
           os.write(msg,0,msg.length);
           os.flush();
           dest = os.toByteArray();
               System.out.println(dest.length);
           } catch (IOException e) {
               e.printStackTrace();
           }
   
   /*输出结果：
   16--->Show me the code
   */
   ```

4. 分析：

   > 将字节数组流与文件流相比较可以发现，文件流是将文件作为数据源，对文件进行操作，而字节数组流则是对内存中存在的字节数组进行操作；
   >
   > 此外，ByteArrayInputStream是将内存中存在的字节数组当做数据源，可以将其读入到程序中的其他字节数组中；而ByteArrayOutputStream则并不需要数据源，它的主要作用是将内存中的一个字节数组读入到流对象中，然后通过流对象进行处理；
   >
   > 可见，字节数组流可以充当文件到字节数组之后操作的中间件来使用；

5. 文件->字节数组->文件 应该怎么做呢

   ```java
   		File src = new File("e:/zzf1.jpg");
           File dest = new File("zzf.jpg");
           FileInputStream fi = null;
           OutputStream os = null;
           ByteArrayOutputStream bo = null;
   
           try {
   			
               fi = new FileInputStream(src);
               os = new FileOutputStream(dest);
               bo = new ByteArrayOutputStream();
               byte[] flush = new byte[1024*10];
               int len=-1;
               while ((len=fi.read(flush))!=-1){//首先通过分段读取将整个文件读入到字节数组中
                   bo.write(flush,0,len);//每进行一次读取就将字节数组写入到字节数组输出流中，方便进行输出到文件
               }
               bo.writeTo(os);//通过字节数组输出流将字节数组输出到文件输出流
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   os.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
               try {
                   fi.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   ```

6. 文件拷贝的封装utils

   ```java
   public class FileUtils {
   
       public static void main(String[] args) {
               //文件到文件
           /try {
               InputStream is = new FileInputStream("abc.txt");
               OutputStream os = new FileOutputStream("abc-copy.txt");
               copy(is,os);
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           }
   
           //文件到字节数组
           byte[] datas = null;
           try {
               InputStream is = new FileInputStream("zzf.jpg");
               ByteArrayOutputStream bo = new ByteArrayOutputStream();
               copy(is,bo);
               datas = bo.toByteArray();
               System.out.println(bo.size());
               System.out.println(datas.length);
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           }
   
           //字节数组到文件
           try {
               OutputStream os2 = new FileOutputStream("zzf2.jpg");
               ByteArrayInputStream bo = new ByteArrayInputStream(datas);
               copy(bo,os2);
   
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           }
   
       }
       /*
       * 对接输入输出流，进行拷贝
       * */
       public static void copy(InputStream is,OutputStream os){//利用顶层抽象类保证数据传入
   
           try {
               byte[] flush = new byte[1024];
               int len=-1;
               while ((len=is.read(flush))!=-1){
                   os.write(flush,0,len);
               }
               os.flush();
           } catch (IOException e) {
               e.printStackTrace();
           }
   
   
       }
   
       public static void close(InputStream is,OutputStream os){
   
           try {
               if (os!=null)
                   os.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
   
           try {
               if (is!=null)
                   is.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
   
       }
   
       public static void close(Closeable... ios){//可变参数
   
           for (Closeable io:ios){
               try {
                   if (io!=null)
                       io.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   
   
   
       }
   
   }
   ```

   > 值得注意的点：
   >
   > 在copy与close的封装中，利用顶层抽象类或者接口作为传入参数，利用多态实现可传入参数的多样性，从而实现代码的高可重用性能；
   >
   > **close的第二个封装里使用了可变参数；**

   jdk1.8以后可用try...with...resource来释放资源

   ```java
   try(is;os){//将流对象或者将流对象声明放入try后的括号内，出去finally
               byte[] flush = new byte[1024];
               int len=-1;
               while ((len=is.read(flush))!=-1){
                   os.write(flush,0,len);
               }
               os.flush();
           } catch (IOException e) {
               e.printStackTrace();
           }
   ```

   

##### 字节缓冲流

1. 概念

   > 属于处理流，在其他节点流或处理流的基础上加上缓冲区以提高效率；
   >
   > 能够提高性能，底层一定是节点流，释放时直接释放最外部的处理流，其内部会自动释放节点流，若要手动释放，需要从内到外释放；

2. 使用

   ```java
   /*和普通字节流的处理没有太大差别，只需要在原字节流基础上包装一层字节缓冲流即可*/
   
   InputStrean is=null;
   is = new BufferedInputStream(new FileInputStream(src));
   /*所以字节流的处理都可以加上缓冲流进行性能上的提升*/
   ```

   

##### 字符缓冲流

1. 概念

   > 其与字节缓冲流的差别在于其提供了新增方法，所以使用时最好不要发生多态
   >
   > 新增方法查阅API

2. 使用

   ```java
   File file = new File("abc.txt");
   File file2 = new File("abc-copy.txt");
   BufferReader reader = new BufferedReader(new FileReader(file));
   BufferWriter writer = new BufferWriter(new FileWriter(file2));
   String line = null;
   while((line=reader.readLine())!=null){//逐行读取
       writer.write(line);//逐行写入
       writer.newLine();//手动添加一个换行符
   }
   //接着关闭流就可以了
   /*所有字符流操作的地方也可以加上缓冲字符流进行处理*/
   ```
```
   
   

##### 转换流

1. 概念

   > 包括InputStreamReader/OutputStreamWriter
   >
   > InputStreamReader的作用在于可以将字节流转换为字符流，并为字节流指定字符集，可处理一个一个的字符
   >
   > OutputStreamWriter的作用是将字符流转换为字节流输出到一个字节流中

2. 使用

   实例使用

   ```java
   BufferedReader br = new BufferedReader(new InputStreamReader(System.in));//字节流转换为字符流
           
   BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));//字符流转换为字节流
           String msg ="";
           try {
               while (!"exit".equals(msg)){
                       msg = br.readLine();
                       bw.write(msg);
                       bw.newLine();
                       bw.flush();//使用缓冲流，一定要注意，当你的数据流未达到内部缓冲区大小时，缓冲流将不会做输出处理，需要强制刷新
               }
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   bw.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
   
               try {
                   br.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
```

   网络流下载时使用实例

   ```java
   InputStreamReader is = null;
           try {
               is =new InputStreamReader(
                       new URL("http://www.baidu.com").openStream(),"utf-8");//网络流是一个字节流
               int temp;
               while ((temp=is.read())!=-1){
                   System.out.print((char) temp);//此处出现乱码，可能是因为字符集不同导致字节数不够
               }
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   is.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   ```

   针对以上代码加入缓冲流进行包装处理

   ```java
   BufferedReader is = null;
           BufferedWriter bs = null;
           try {
               is =new BufferedReader(
                       new InputStreamReader(
                               new URL("http://www.baidu.com").openStream(),"utf-8"));//new URL("http://www.baidu.com").openStream()是一个网络流同时是一个字节流
               bs =new BufferedWriter(
                       new OutputStreamWriter(
                               new FileOutputStream("baidu.html"),"utf-8"));//若不指定字符集，则字符集将会与工程字符集相同
               String msg;
               while ((msg=is.readLine())!=null){
                   bs.write(msg);//这里出现乱码可能是因为字符集不统一
                   bs.newLine();
                   bs.flush();
               }
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   is.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
   
               try {
                   bs.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   ```

   

##### 数据流

1. 概念

   > 包括DataInputStream/DataOutputStream
   >
   > 其作用在于，写入数据和读取数据时可以依据数据类型进行操作，需要注意，读取的顺序一定要和写入的顺序相同，否则将会因为数据类型不同导致错误

2. 具体的使用

   ```java
    		//写出
           ByteArrayOutputStream bos = new ByteArrayOutputStream();
           DataOutputStream dos = new DataOutputStream(new BufferedOutputStream(bos));
           //操作
           dos.writeUTF("编码辛酸泪");
           dos.writeBoolean(true);
           dos.writeInt(155);
           dos.writeChar('C');
           dos.flush();
           byte[] bytes = bos.toByteArray();
           //读取
           DataInputStream dis = new DataInputStream(
                                   new BufferedInputStream(
                                       new ByteArrayInputStream(bytes)));
           String s = dis.readUTF();
           boolean b = dis.readBoolean();
           int i = dis.readInt();
           char c = dis.readChar();
           System.out.println(s+" "+b+" "+i+" "+c);
           //读取时的顺序一定要与写入是相一致，否则将报错
   
           dos.close();
           dis.close();
   /*操作结果
   编码辛酸泪 true 155 C
   所有涉及节点的操作都可以加上缓冲流进行包装以进行提升性能
   */
   ```

   

##### 对象流

1. 概念

   > ObjectInputStream/ObjectOutputStream是以数据类型和“对象”为数据源，但是必须将传输的对象进行序列化与反序列化操作。同时，只有实现了java.io.serializable接口的对象才能写入到对象流中

2. 操作及注意点

   ```java
   public class ObjectTest01 {
   
       public static void main(String[] args) throws IOException, ClassNotFoundException {
           //写出
           ByteArrayOutputStream bos = new ByteArrayOutputStream();
           ObjectOutputStream oos = new ObjectOutputStream(new BufferedOutputStream(bos));
           //操作基本数据类型+数据
           oos.writeUTF("编码辛酸泪");
           oos.writeBoolean(true);
           oos.writeInt(155);
           oos.writeChar('C');
           //加入对象 序列化
           oos.writeObject("编码辛酸泪，谁解其中味");
           oos.writeObject(new Date());
           Employee employee = new Employee(101,"张三");
           oos.writeObject(employee);
           oos.flush();
           byte[] bytes = bos.toByteArray();
           //读取 对象数据还原反序列化
           ObjectInputStream ois = new ObjectInputStream(
                                   new BufferedInputStream(
                                       new ByteArrayInputStream(bytes)));
           String s = ois.readUTF();
           boolean b = ois.readBoolean();
           int i = ois.readInt();
           char c = ois.readChar();
           Object str = ois.readObject();
           Object date = ois.readObject();
           Object emp = ois.readObject();
           System.out.println(s+" "+b+" "+i+" "+c);
           //读取时的顺序一定要与写入是相一致，否则将报错
           if (str instanceof String){
               System.out.println((String)str);
           }
           if (date instanceof Date){
               System.out.println((Date)date);
           }
           if (emp instanceof Employee){
               System.out.println((Employee)emp);
           }
           oos.close();
           ois.close();
       }
   }
   
   class Employee implements Serializable{
       private transient int id;//若数据不需要序列化，可加transient关键字
       private String name;
   
       public Employee(int id, String name) {
           this.id = id;
           this.name = name;
       }
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       @Override
       public String toString() {
           return "Employee{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   '}';
       }
   }
   /*运行结果
   编码辛酸泪 true 155 C
   编码辛酸泪，谁解其中味
   Sun Jul 28 15:58:24 CST 2019
   Employee{id=0, name='张三'}
   */
   ```

   

##### printStream打印流

1. 概念

   > 打印流，提供数据打印功能，System.out就是一个打印流；
   >
   > 使用打印流，可以将数据打印到文件中，从而更好的处理打印事务；
   >
   > 同时还可以重定向输出端，例如从控制台重定向到文件中；

2. 相关使用与API接口

   ```java
    //打印流
           PrintStream ps = System.out;
           ps.println("这是打印流");
   
           ps = new PrintStream(
                   new BufferedOutputStream(
                           new FileOutputStream("print.txt")),true,"utf-8");//true表示自动刷新
           ps.println("这是打印流");
           ps.close();
           //重定向输出端,输出端需要是一个打印流
           System.setOut(ps);
           System.out.println("change");//change被打印到文件中
           //重定向会控制台，控制台也是一个系统定义好的打印流
           System.setOut(
                   new PrintStream(
                           new BufferedOutputStream(
                                   new FileOutputStream(FileDescriptor.out)),true));//系统描述符，常量表示控制台
           System.out.println("重定向回控制台");//重新再控制台打印
   /*输出结果：
   这是打印流
   重定向回控制台
   */
   ```

   

##### PrintWriter打印流

1. 概念

   > 具体可参见PrintStream

2. 使用

   ```java
    //打印
           PrintWriter ps = new PrintWriter(
                   new BufferedOutputStream(
                           new FileOutputStream("print.txt")),true);//true表示自动刷新
           ps.println("这是打印流");
           ps.close();
   /*结果：打印到文件中*/
   /*使用不多，了解即可*/
   ```

   

##### IO之文件分割与合并

1. 说明

   > 使用RandomAccessFile类，该类支持读取和写入随机访问文件，具体实现是它在构造函数中有一个mode选项，RandomAccessFile(File file, String mode)/RandomAccessFile(String name, String mode);
   >
   > mode包括："r"，"rw"，"rws"，"rwd";
   >
   > 关键方法：seek(long pos); 定位文件指针到文件的指定位置

2. 分割，封装成工具类

   ```java
   public class SplitFileUtil {
       //源头
       private File src;
       //目的地
       private File dest;
       //所有文件分割后的存储路径
       private List<String> destPath;
       //每块大小
       private int blocKSize;
       //总文件块数
       private int size;
   
       public SplitFileUtil(String srcPath,String destDir ,int blocKSize ) {
           this.blocKSize = blocKSize;
           this.dest = new File(destDir);
           this.src = new File(srcPath);
           this.destPath = new ArrayList<>();
           init();
       }
       //初始化
       private void init(){
           //总长度
           long len = this.src.length();
           //块数
           this.size= (int) Math.ceil(len*1.0/blocKSize);
           //路径
           for (int i=0;i<size;i++){
               this.destPath.add(this.dest+"/"+i+this.src.getName());
               System.out.println(this.dest+"/"+i+this.src.getName());
           }
       }
       /*分割
       * 计算每一块的起始位置
       * 分割
       * */
       public void split() throws IOException {
           //总长度
           long len = this.src.length();
           int beginPos=0;
           int actualSize=0;//(int)(blocKSize>len?len:blocKSize)
           for (int i=0;i< size;i++){
               beginPos=i*blocKSize;
               if (i==size-1){
                   actualSize= (int) len;
               }else{
                   actualSize = blocKSize;
                   len-=actualSize;
               }
               splitDetail(i,beginPos,actualSize);
           }
       }
   
       //分块读取初级，读取指定大小的块，起始位置，实际大小
       public void splitDetail(int i,int beginPos,int actualSize) throws IOException {
           RandomAccessFile raf = new RandomAccessFile(this.src,"r");
           RandomAccessFile raf2 = new RandomAccessFile(this.destPath.get(i),"rw");
           raf.seek(beginPos);
           byte[] flush = new byte[100];
           int len;
           while ((len=raf.read(flush))!=-1){
               if (actualSize>len){//获取本次读取的全部内容
                   raf2.write(flush,0,len);
                   actualSize-=len;
               }else{
                   raf2.write(flush,0,actualSize);
                   break;
               }
           }
   
   
           raf2.close();
           raf.close();
       }
   
       public static void main(String[] args)  {
           SplitFileUtil sfu = new SplitFileUtil("zzf.jpg","dest",1024*10);
           try {
               sfu.split();
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
   
   ```

3. 合并1，多输入流合并到一个输出流

   ```java
   public void merge(String destDir) throws IOException {
           //输出流
           OutputStream os = new BufferedOutputStream(new FileOutputStream(destDir,true));//注意要使用append模式
   
           for (int i=0;i<size;i++){
               //输入流
               InputStream is = new BufferedInputStream(new FileInputStream(destPath.get(i)));
               byte[] flush = new byte[1024];
               int len=-1;
               while ((len=is.read(flush))!=-1){
                   os.write(flush,0,len);
               }
               os.flush();
               is.close();
           }
           os.close();
   
       }
   ```

4. 合并2，采用sequenceInputStream方法,将多个输入流合并到容器中统一合并

   ```java
    public void merge2(String destDir) throws IOException {
           //输出流
           OutputStream os = new BufferedOutputStream(new FileOutputStream(destDir,true));
           Vector<InputStream> Vis = new Vector<InputStream>();
           SequenceInputStream sis = null;
           for (int i=0;i<size;i++) {
               //输入流
               Vis.add(new BufferedInputStream(new FileInputStream(destPath.get(i))));
           }
           sis=new SequenceInputStream(Vis.elements());//当需要输入多个输入流时，需要通过vector来存储多个流，进而将流放入sequeneceInputStream
           byte[] flush = new byte[1024];
           int len=-1;
           while ((len=sis.read(flush))!=-1){
               os.write(flush,0,len);
           }
           os.flush();
           sis.close();
           os.close();
   
       }
   ```




##### 总结

对于IO流，首先把握三个大方向

1. 根据不同的角度：看待数据有了字节流、对字符敏感有了字符流、字节流与字符流之间还有转换流
2. 根据流向分为输入与输出流
3. 根据是否与节点直接相关：如果操作的是文件、字节数组，那么我们称之为节点流；在节点之上操作的称之为装饰流/操作流

需要重点掌握的部分

 1. 文件的拷贝

 2. 具体操作流程

    > 创建源  //文件或者字节数组
    >
    > 选择流  //根据操作对象选择合适的流，建议都包上一层缓冲流以提高效率
    >
    > 进行操作 固定套路
    >
    > ```java
    > byte datas[];
    > int len=-1；
    > while(read){
    > 	write
    > 	flush
    > }
    > /*对于字符流，有BufferedReader可以逐行读取等方法*/
    > ```
    >
    > 其他操作：DataInputStream/DataOutputStream  ObjectInputStream/ObjectOutputStream(称之为序列化与反序列化，对象需要实现serializable接口，不需要序列化的对象可以夹transient修饰符)

IO操作一般不需要写原生操作，一般都可以使用CommonsIO,需要对其相关API进行了解与掌握