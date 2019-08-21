### CommonsIO的相关使用

   该工具类API众多，具体使用可在实际中自己根据需要选择

1. 针对文件进行的相关操作

   ```java
   public static void main(String[] args) {
           //文件大小
           long len = FileUtils.sizeOf(new File("zzf.jpg"));
           //目录大小
           long len2 = FileUtils.sizeOf(new File("F:/JetBrains project/javaBaseTest"));
           System.out.println(len+" "+len2);
           //子孙集,共三个参数，目录，文件过滤器，目录过滤器,其返回值是一个文件集合Collection
           //事例1：
           Collection<File> files = FileUtils.listFiles(new File("F:/JetBrains project/javaBaseTest"), EmptyFileFilter.NOT_EMPTY, null);//列出目录下所有非空文件，只列出第一层
           for (File file:files){
               System.out.println(file.getAbsolutePath());
           }
   
           //事例2：
           Collection<File> files2 = FileUtils.listFiles(new File("F:/JetBrains project/javaBaseTest"), EmptyFileFilter.NOT_EMPTY, DirectoryFileFilter.INSTANCE);//列出目录下所有非空文件，列出所有子孙集
           for (File file:files2){
               System.out.println(file.getAbsolutePath());
           }
   
           //事例3：
           Collection<File> files3 = FileUtils.listFiles(new File("F:/JetBrains project/javaBaseTest"), new SuffixFileFilter("java"), DirectoryFileFilter.INSTANCE);//列出目录下所有.java文件
           for (File file:files3){
               System.out.println(file.getAbsolutePath());
           }
   
           //事例4：文件过滤器的or方法使用
           Collection<File> files4 = FileUtils.listFiles(new File("F:/JetBrains project/javaBaseTest"), FileFilterUtils.or(new SuffixFileFilter("java"),new SuffixFileFilter("class")), DirectoryFileFilter.INSTANCE);//列出目录下.java与.class文件，
           for (File file:files4){
               System.out.println(file.getAbsolutePath());
           }
   
           //事例5：文件过滤器的and方法使用
           Collection<File> files5 = FileUtils.listFiles(new File("F:/JetBrains project/javaBaseTest"), FileFilterUtils.and(new SuffixFileFilter("java"), EmptyFileFilter.NOT_EMPTY), DirectoryFileFilter.INSTANCE);//列出目录下.java且不为空的文件
           for (File file:files5){
               System.out.println(file.getAbsolutePath());
           }
       }
   ```

   

2. 文件的读取

   ```java
   public static void main(String[] args) throws IOException {
           //读取文件
           String msg = FileUtils.readFileToString(new File("copy.txt"),"utf-8");
           System.out.println(msg);
           System.out.println("======================================");
           byte[] datas = FileUtils.readFileToByteArray(new File("copy.txt"));
           System.out.println(datas.length);
   
           System.out.println("======================================");
           //逐行读取
           List<String> msg2 = FileUtils.readLines(new File("copy.txt"), "utf-8");
           for (String str:msg2){
               System.out.println(str);
           }
           System.out.println("======================================");
           //逐行读取，迭代器方法LineIterator
           LineIterator lineIterator = FileUtils.lineIterator(new File("copy.txt"), "utf-8");
           while (lineIterator.hasNext()){
               System.out.println(lineIterator.next());
           }
       }
   ```

   

3. 写入到文件中

   ```java
   public static void main(String[] args) throws IOException {
           //写出文件,在字符串下，两函数功能相同
           FileUtils.write(new File("commonsIOtest.txt"),"学习真的很不容易","utf-8",false);
           FileUtils.writeStringToFile(new File("commonsIOtest.txt"),"我很迷茫","utf-8",true);//最后的布尔型变量表示是否追加
   
           //写入到文件，字节数组方式
           FileUtils.writeByteArrayToFile(new File("commonsIOtest.txt"),"我该怎么面对他们".getBytes("utf-8"),true);
   
           //写出列表,第三个参数代表分割符
           List<String> list = new ArrayList<>();
           list.add("马云");
           list.add("马化腾");
           list.add("弼马温");
           FileUtils.writeLines(new File("commonsIOtest.txt"),list,"--",true);
   
       }
   ```

   

4. 文件的拷贝

   ```java
   public static void main(String[] args) throws IOException {
           //文件的复制
           FileUtils.copyFile(new File("zzf.jpg"),new File("zzf5.jpg"));
           //复制文件到目录
           FileUtils.copyFileToDirectory(new File("zzf.jpg"),new File("dest"));
   
           //复制目录,将dest目录下的内容复制到dest2中
           FileUtils.copyDirectory(new File("dest"),new File("dest2"));
           //复制目录到目录,将dest作为dest3的子目录
           FileUtils.copyDirectoryToDirectory(new File("dest"),new File("dest3"));
   
   
           //拷贝URL内容
           String Url = "https://www.baidu.com";
           FileUtils.copyURLToFile(new URL(Url),new File("copybaidu.txt"));*/
   
           //将url内容拷贝到字符串中
           String string = IOUtils.toString(new URL(Url), "UTF-8");
           System.out.println(string);
       }
   ```

   