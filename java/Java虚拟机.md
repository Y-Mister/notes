### Java虚拟机

#### 运行时数据区域

1. ##### JVM运行时数据区域划分

   执行java程序过程中会将它所管理的内存划分为以下数据区域

   > 包括：
   >
   > 方法区 method area；
   >
   > 堆 heap；
   >
   > 虚拟机栈 VM Stack；
   >
   > 本地方法栈 Native Method Stack；
   >
   > 程序计数器 Program Couter Register;
   >
   > 注意：方法区和堆是线程共享的，而每个线程都有自己的程序计数器、本地方法栈和虚拟机栈



2. ##### 程序计数器

   > **线程私有**；
   >
   > 如果线程执行的是一个java方法，则其作用是当前线程所执行的字节码的行号指示器，它指明了当前线程所执行到的地方，使得线程切换后能够恢复到正确的执行位置；若线程执行的是Native方法，则计数器值为空；
   >
   > 程序计数器是java虚拟机中唯一没有规定内存溢出错误（OutOfMemoryError）的区域



3. ##### Java虚拟机栈（JVMS）

   > **线程私有；**
   >
   > 生命周期与线程相同；
   >
   > 用于描述java方法执行的内存模型：线程的每个方法被执行都会创建一个**栈帧**用于存储该方法的**局部变量表(本地变量表)**、**操作栈**、**动态链接**、**方法出口**等信息。一个方法从被调用到执行结束的过程，对应着一个栈帧在虚拟机栈中从入栈到出栈的过程；
   >
   > 栈帧中保存的局部变量表内存在编译期间完成分配；
   >
   > 区域异常：线程请求栈深度大于虚拟机所允许的深度，抛出StackOverflowError异常；虚拟机栈的动态扩展无法申请到足够的内存时抛出OutOfMemoryError;

   局部变量（local veriable）

4. ##### 本地方法栈（NMS）

   > 与VMS类似，区别在于VMS为java方法服务，而NMS为Native方法服务；
   >
   > 异常：与VMS相同的StackOverflowError与OutOfMemoryError异常；



5. ##### Java堆（heap）

   > **线程共享；**
   >
   > 虚拟机启动时创建；
   >
   > 用于存放创建好的对象实例（所有的对象实例与数组都会在堆上分配内存）；
   >
   > 堆的内存在物理上不一定连续，但逻辑上连续（可扩展，若无法继续扩展，会抛出OutOfMemoryError异常）
   >
   > 是GC管理的主要区域



6. ##### 方法区（method area）

   > **线程共享；**
   >
   > 存放已被虚拟机加载的类的代码、类信息、常量、静态变量等数据（即存放运行中不会变的东西）；
   >
   > GC较少处理此区域；
   >
   > 若方法区无法申请到足额的内存扩展空间，抛出OutOfMemoryError异常



7. ##### 运行时常量池

   > 属于方法区的一部分；
   >
   > 存放编译器生成的各种Java量与符号引用，此外，翻译出来的直接引用也存储在运行时常量池中；
   >
   > 运行时常量池具备动态性，java并不要求常量一定要在编译期间产生，运行期间也可能将新的常量放入池中（例如String 的 intern()方法）；
   >
   > 其受到方法区的限制，当常量池无法申请到足够的内存时排除OutOfMemoryError异常；



#### 对象访问

1. ##### 关于对象的访问

   > 涉及到java栈，java堆与方法区

   ```java
   Objec obj = new Object();
   /*以上述语句为例，Object obj将会反应到java虚拟机栈的本地变量表中，作为reference类型数据出现；而new Object()方法则反应为在java堆中形成一块存储Object类型所有实例数据的结构化内存；
   而reference类型数据值规定了指向对象的引用，并没有指明该引用通过何种方式去定位，主流的访问方式有：使用句柄和直接指针
   */
   ```



2. 使用句柄方式

   > java堆中会划分一块内存为句柄池，reference中存储的对象就是对象的句柄地址，而句柄中包含了对象实例数据和类型数据各自的具体地址信息

   ![1564736747436](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1564736747436.png)



3. 使用直接指针访问方式

   > reference中直接存储的就是对象地址

   ![1564736977307](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1564736977307.png)



4. 句柄与直接指针方式的区别

   > 直接指正方式较为稳定，当对象地址更改是只需要更改句柄保存的对象地址，而不需要改变reference数据的内容；
   >
   > 而直接指针相比句柄少了一次句柄到对象数据的定位过程，速度快；



#### 垃圾收集器与内存分配策略

1. ##### 垃圾分类算法

   引用计数算法

   > 给对象添加一个引用计数器，每当一个地方引用它时，计数器值就加1；当引用失效时，计数器值就减1,任何时刻计数器都为0的对象就是不可能再被使用的；
   >
   > 问题：文法解决相互循环引用的问题

    ```java
   public class ReferenceCounting {
   
       public Object instace = null;
       private static final int _1MB = 1024*1024;
   
       private byte[] bigSize = new byte[2*_1MB];
   
       public static void testGC(){
           ReferenceCounting objA = new ReferenceCounting();
           ReferenceCounting objB = new ReferenceCounting();
           objA.instace = objB;
           objB.instace = objA;
   
           objA = null;
           objB = null;
           System.gc();
       }
   
       public static void main(String[] args) {
           ReferenceCounting.testGC();
       }
   
   }
   /*此处虽然objA、objB都置空，但是因为它们互相引用着对方，使得GC引用计数法无法进行垃圾回收；但是，此处java的GC仍然能够回收，因为java不再使用引用计数法*/
    ```

   可达性分析算法（根搜索算法）

   > 通过一系列的GC ROOTS的对象作为起始点，从这些节点向下搜索，搜索多经过的路径称为引用链，当一个对象到GC ROOTS没有任何引用链相连时（即从GC ROOTS到该对象不可达），则判定该对象不可用可回收；
   >
   > 可作为GC ROOTS的对象：虚拟机栈（栈帧中的本地变量表）中引用的对象、方法区中的类静态属性引用的对象、方法区中的常量引用的对象、本地方法栈中Native方法的引用的对象

   引用的概念扩充

   > 无论是引用计数还是可达性分析，都关系到引用，而引用可分为强引用（Strong reference/）、软引用（soft reference）、弱引用（Weak reference）、虚引用（Phantom reference）

   > **强引用**就是代码中普遍存在的，类似Objec obj = new Object()此类，只有强引用还存在，垃圾回收期永远不会回收掉被引用的对象；
   >
   > **软引用**描述一些还有用，但非必须的对象，对于软引用关联着的对象，在系统将要发生内存泄露之前，将会把这些对象列进到回收范围之中进行第二次回收，如果此次回收任没有足够的内存，才会抛出内存溢出异常，jdk提供SoftReference类来实现软引用；
   >
   > **弱引用**也描述非必须的对象，比弱引用强度更若，被弱引用关联的对象只能存活到下一次垃圾回收发生之前。当垃圾回收器工作时，无论当前内存是否充足，都会回收掉弱引用对象。jdk提供WeakReference类实现弱引用；
   >
   > **虚引用**是最弱的引用关系，一个对象是否有虚引用的存在完全不影响其生存时间，也无法通过虚引用来取得一个对象实例。为对象设置一个虚引用关联的唯一目的就是希望该对象被垃圾回收器回收之前收到一个系统通知。jdk提供PhantomReference类实现虚引用。

2. ##### 方法区的垃圾回收

   > 废弃常量的定义：若没有任何对象/没有任何对象对某个常量进行引用，则该常量可能会被清除常量池；
   >
   > 无用的类的定义：该类所有实例均被回收，java堆中不存在该类的任何实例；加载该类的ClassLoader以及被回收；该类对应的java。lang.Class对象没有在任何地方引用，无法在任何地方通过反射访问该类的方法。虚拟机**可以**对满足上述三个条件的类进行回收

3. ##### 垃圾收集算法

   > 标记清除算法、复制算法、标记整理算法、分代收集算法

   标记—清除算法

   > 首先标记处所有需要回收的对象，标记完成后统一回收；
   >
   > 缺点：标记与清除的效率不高；空间问题，标记清除会产生大量的内存碎片

   复制算法

   > 将内存等分两份，每次只使用其中一块。当此块内存用完，将仍然存活的对象复制到另一块内存上，然后将前一块内存统一清理；
   >
   > 解决了内存碎片化的问题，实现简单，运行高效，但内存使用率大大降低

   分代垃圾回收算法

   > 即年轻代、年老代，持久代的划分，jvm将内存分为Eden、survivor、old分区，survivor还划分为from和to；
   >
   > 垃圾回收的流程：
   >
   > 1. 新创建的对象，多数放入Eden区；
   > 2. 当Eden满了不能创建新的对象，触发minor GC，将无用对象清理掉，剩余对象放入survivor中的from区，同时清空Eden；
   > 3. 当Eden再次满，同时将Eden中的不能清除的对象复制到from，然后将from区的不能清空的对象存入到to区，最后清空Eden和from区（from区与to区不存在实际差别，他们两在每次只需minorGC是会交换职责，如第一次minorGC将Eden和from区存货对象赋值到to区，那么第二次minorGC将会将Eden与to区的存活对象赋值到from区，依次类推）
   > 4. 重复多次（默认最大15次），survivor区中任然没有被清理的对象，将会复制到old区中
   > 5. 当old区满了（或达到某个比例），将会触发major GC,触发stop-the-world，清空old区
   > 6. note:还有一个full GC,同时清理年轻代，年老代区域，成本较高，会对系统性能产生影响；导致full GC的原因：年老代写满；持久-代写满；System.gc()的显示调用；上一次GC之后Heap的各域分配策略动态变化

   标记—整理算法

   > 标记过程与标记—整理算法一致，但后续的清理则不同，标记—整理算法是让所有存活对象向一端移动，随后清除掉存活边界意外的内存

4. ##### 收集器

   > 深入理解java虚拟机3.4

5. ##### 内存分配与回收策略

   > 大多数情况下，对象在新生代Eden区中分配，**当Eden区没有足够的空间进行分配时，虚拟机将发起一次minorGC**；
   >
   > 而大对象则直接进入老年代，大对象指的是需要大量连续空间的java对象（例如长字符串与数组），大对象直接在老年代，避免了在Eden区的两个survivor区的大量内存拷贝
   >
   > 长期存活对象将进入老年代，对此，虚拟机给每个对象定义了一个对象年龄（Age）计数器，对象再Eden区出生并经历过第一次MinorGC后仍然存活，并且可被Survivor容纳的话，对象将被移动到survivor区，此后对象每在survivor中度过一次MinorGC并存活，则其年龄增加一岁，当期年龄增加到一定值（默认为15），则将会被晋升到老年代中，年龄阈值可通过参数-XX:MaxTenuringThreshold设置

6. 动态对象年龄判定

   > 针对长期存活对象，为了适应不同的内存状况，并不一定要求对象年龄一定要达到阈值才能晋升老年代，如果survivor中相同年龄的所有对象大小总和大于survivor空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代，无需等待阈值要求

7. #####  minorGC与fullGC的区别

   > **MinorGC**是新生代GC，指发生在新生代的垃圾回收动作，因此java对象大多具有朝生夕死的特性，所以minorGC非常频繁，一般回收速度也比较快;
   >
   > **MajorGC/FullGC**是老年代GC，指发生在老年代的GC,出现了majorGC，经常会伴随着至少一次的minorGC(非绝对，收集器不同可能存在区别)，majorGC一般会比minorGC慢10倍以上

8. ##### 空间分配担保

   > 在发生minorGC时，虚拟机会检测**之前**每次晋升到老年代的**平均大小**是否大于老年代的剩余空间，如果大于，则改为直接进行一次FullGC；如果小于，则查看HandlerPromotionFailure设置是否允许担保失败，如果允许，那只会进行MinorGC，如果不允许，则也要改为进行一次FullGC；
   >
   > 所谓担保，即虚拟机采用复制收集算法采用survivor中的一个作为轮换备份，当一次minorGC后仍然有大量对象存活，则需要老年代进行分配担保，让survivor区无法存放的对象直接进入老年代，前提是老年代空间足够，此算法采用平均值作为参考值，与老年代大小进行比较，决定是否采用fullGC来让老年代腾出空间给新生代对象（深入理解java虚拟机3.5.5）





#### 虚拟机性能监控与故障处理

深入理解java虚拟机第四章 以下均可通过  command -h查看命令格式

主要工具及调用方法参数信息

##### 命令行工具

1. jsp虚拟机进程状况工具

   > 命令格式：jsp [options] [hostid]
   >
   > 例如jsp  -l ：输出当前虚拟机执行的进程

   常用options

   | options | 作用                                                         |
   | ------- | ------------------------------------------------------------ |
   | -q      | 只输出**LVMID**,省略主类的名称                               |
   | -m      | 输出虚拟机进程启动时传递给主类main函数的参数                 |
   | -l      | 输出**LVMID**,主类的全名，如果进程执行的是jar包，输出jar包路径 |
   | -v      | 输出虚拟机进程启动时JVM参数                                  |

   

2. jstat虚拟机统计信息监视工具

   > 用于监视虚拟机各种运行状态信息的工具，可以显示本地或远程虚拟机进程中的类装载、内存、垃圾收集、JIT编译等运行数据
   >
   > 命令格式：
   >
   > jstat [option vmid   [   interval[s [ms]  [count] ]    ]     ]
   >
   > 如果是本地虚拟机进程VMID与LVMIDs 一致的，如果是远程虚拟机进程，则VMID格式为：[ protocal: ] [//] lvmid [@hostname [:port] /servername ]
   >
   > interval与count 参数代表查询间隔和次数，若省略则表示只查询一次
   
   可用option参数
   
   | 选项              | 作用                                                         |
   | ----------------- | ------------------------------------------------------------ |
   | -class            | 监视类装载、卸载数量、总空间及类装载所耗费的时间             |
   | -gc               | 监视java堆状况，包括Eden区、2个survivor区、老年代、永久代等的容量、医用空间、GC时间合计等信息 |
   | -gccapacity       | 监视内容与-gc基本相同，但输出主要关注Java堆各个区域使用到的最大和最小的空间 |
   | -gcutil           | 监视内容与-gc基本相同，但输出主要关注已使用空间占占总空间的百分比 |
   | -gccause          | 与-gcutil功能相同，但会额外输出导致上一次GC产生的原因        |
   | -gcnew            | 监视新生代GC的状况                                           |
   | -gcnewcapacity    | 监视内容与-gcnew基本相同，输出主要关注使用到的最大和最小空间 |
   | -gcold            | 监视老年代GC状况                                             |
   | -gcoldcapacity    | 监视内容与-gcold基本相同，输出主要关注使用到的最大和最小空间 |
   | -gcpermcapacity   | 输出永久代使用到的最小和最大空间                             |
   | -compailer        | 输出JIT编译器编译过的方法、耗时等信息                        |
   | -printcompilation | 输出已经被JIT编译的方法                                      |
   
   参数信息：部分可见深入理解java虚拟机p102



3. jinfo：Java配置信息工具

   > 实时地查看和调整虚拟机的各项参数（p103）
   >
   > 可以在运行期修改虚拟机部分参数，使用 -flag [+|-]name或者 -flag name=value方式改写；
   >
   > 命令格式：
   >
   > jinfo [option] pid 

   option参数提供：（根据JDK1.8查询到的可用option）

   | 选项                     | 作用                    |
   | ------------------------ | ----------------------- |
   | -flag  < name >          | 来查看未显示指定的参数  |
   | -flag  [+\|-]< name >    | 启用/禁用某一虚拟机参数 |
   | -flag  < name >=< value> | 给虚拟机参数设值        |
   | -flags                   | 打印虚拟机参数          |
   | -sysprops                | 打印java系统属性        |
   | < no option >            | 打印以上所有            |

   

4. jmap：Java内存印象工具

   > 生成堆存储快照（heapdump/dump文件）、查询finalize执行队列、java堆和永久代的详细信息（如空间利用率、当前是什么垃圾收集器等）
   >
   > 命令格式：
   >
   > jamp [option] < pid > :连接正在运行的进程
   >
   > jmap [option] < exxcutable < core > >:连接核心文件
   >
   > jmap [option] [server_id@] < remote server IP or hostname>:连接远程调试服务器

   可选参数：

   | 参数                  | 作用                                                         |
   | --------------------- | ------------------------------------------------------------ |
   | < none>               | 打印与Solaris pmap相同的内容                                 |
   | -heap                 | 打印java堆概要信息                                           |
   | -histo[:live]         | 打印java对象堆的直方图，如果指定live，则只打印活对象         |
   | -clstats              | 打印类加载器统计信息                                         |
   | -finalizerinfo        | 打印等待完成的对象信息                                       |
   | -dump:< dump-options> | 生成java堆转储快照，dump-options包括：live :是否只打印活的对象；format=b：二进制形式；file=< file> 生成堆到文件 |
   | -F                    | 强制生成堆快照，此状态下live参数不可用                       |
   | -J< flag>             | 将< flag >直接传递到运行时系统中                             |
   |                       |                                                              |



5. jhat：虚拟机堆转储快照分析工具

   > jmap与jhat搭配使用分析jmap生成的堆转储快照；
   >
   > jhat内置微型http/html服务器，生成dump文件的分析结果后可以在路蓝旗中查看；
   >
   > 命令：jhat [-stack < bool>]  [-refs < bool>]  [-port < port>]  [-baseline < file>]  [-debug < int>]  [-version ]  [-h|-help]  < file>

   | option            | 作用                                                         |
   | ----------------- | ------------------------------------------------------------ |
   | -J< flag>         | 将flag参数作用于运行时系统中                                 |
   | -stack false      | 关闭跟踪对象分配调用堆栈                                     |
   | -ref false        | 关闭对对象引用的跟踪                                         |
   | -port < port>     | 修改http server的端口信息                                    |
   | -exclude < file>  | 指定一个文件，该文件列出应从ReachableFrom查询中排除的数据成员 |
   | -baseline < file> | 指定基线对象转储。具有相同ID和相同类的两个堆转储中的对象将标记为不是“新的”。 |
   | -debug < int>     | 设置debug 等级 0：不输出debug内容；1：调试hprof文件分析；2：调试hprof文件分析，没有服务器 |
   | -version          | 版本信息                                                     |
   | < file>           | 要分析的文件地址                                             |



6. jstack：java堆栈跟踪工具

   > 用于生成当前虚拟机的当前时刻的线程快照，线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合，用于定位线程出现长时间停顿的原因（死锁、死循环、请求外部资源导致的长时间等待等），通过jstack可以看到线程调用堆栈，从而可以回到线程在做什么;
   >
   > 命令用法：
   >
   > ​		jstack [-l] < pid>：连接正在运行中的进程
   >
   > ​		jstack -F [-m] [-l] < pid>：连接挂起的进程
   >
   > ​		jstack [-m] [-l] < executable> < pid>：连接核心文件
   >
   > ​		jstack [-m] [-l] [server_id@] < remote server IP or hostname>：连接远程调试服务

   | options | 作用                                         |
   | ------- | -------------------------------------------- |
   | -F      | 强制线程转储，当jstack无反应，进程挂起时使用 |
   | -m      | 同时输出java和native框架                     |
   | -l      | 长清单，打印有关锁的其他信息                 |



##### 可视化工具

1. JConsole

   > 基于JMX的可视化监视和管理工具

该部分内容基于深入理解java虚拟机4.3，暂时跳过





#### 虚拟机调优案例分析与实战

暂时跳过





#### ***类文件结构

所谓平台无关性与语言无关性，其基础为虚拟机与字节码存储格式，即将不同语言通过各自的编译器编译为同种结构的字节码文件，从而实现在同一虚拟机平台上运行

1. class文件的结构

   > class文件是一组以8位字节为基础单位的二进制流，各个数据项目严格按照顺序紧凑地排列在class文件中，中间没有任何分割符。若需要存储8位字节以上的空间数据项时，按照高位在前的顺序拆分为若干8位字节进行存储；(8为字节值得应该是一个字节占8位吧！！！)
   >
   > class的存储格式为伪结构体，包含两种数据类型：**无符号数和表**；
   >
   > **无符号数**属于基本数据类型，以u1、u2、u4、u8分别代表1个字节、2个字节、4个字节和8个字节的无符号数，无符号数可以用来描述数字、索引应用、数量值、或者按照UTF-8编码构成的字符串值；
   >
   > **表**是由多个无符号数或其他表作为数据项的符合数据类型，所有的表习惯性以“_info”结尾。表用于描述有层次关系的复合结构的数据，整个class文件本质上就是一个表

   class文件由如下所示数据项组成：

   | 类型           | 名称                | 数量                |
   | -------------- | ------------------- | ------------------- |
   | u4             | magic               | 1                   |
   | u2             | minor_version       | 1                   |
   | u2             | major_version       | 1                   |
   | u2             | constant_pool_count | 1                   |
   | cp_inf         | constant_pool       | constant_pool_count |
   | u2             | access_flags        | 1                   |
   | u2             | this_class          | 1                   |
   | u2             | super_class         | 1                   |
   | u2             | interfaces_count    | 1                   |
   | u2             | interfaces          | interfaces_count    |
   | u2             | fields_count        | 1                   |
   | field_info     | fields              | fields_count        |
   | u2             | methods_count       | 1                   |
   | method_info    | methods             | methods_count       |
   | u2             | attributes_count    | 1                   |
   | attribute_info | attributes          | attributes_count    |

   > 配合F:\JetBrains project\javaBaseTest\target\classes\com\yuan\JVMTest\TestClass.class文件查看

   - magic

     >class文件的头4个文件成为魔数，它的唯一作用是确定该文件是否是一个可以被虚拟机接受的Class文件（java class文件魔数为0xCAFEBABE）。很多其他的文件存储标准中都是用了魔数进行身份识别，譬如图片格式，如gif或jpeg等。

   - minor version&major version  

     >第二个和第三参数共四个字节存储Class文件的版本信息：第二个参数是此版本号、第三个参数是主版本号

   - 常量池信息

     > 常量池入口是一个两个字节的常量池容量计数器constant_pool_count，存放常量池内容量计数值；
     >
     > cp_info为常量池，其内存放两大类常量：**字面量**（Literal）和符号引用(Symbolic References)。字面量接近java语言的常量概念，如字符串常量、被声明为final的常量值等；**符号引用**是编译原理方面的概念，包括：**类和接口的全限定名**（fully  qualified Name）、**字段的名称和描述**（descriptor）、**方法的名称和描述符**;（**关于全限定名、描述符等特殊字符串，将在后面介绍**）
     >
     > **注意：**java编译时没有“连接”这一步骤，而是虚拟机加载class文件是动态连接，所以class文件中不会保存各个方法和字段的最终布局信息，因此这些字段和方法的符号引用不经过转换无法直接被虚拟机使用。当虚拟机运行时，需要从常量池获得对应符号应用，再在类创建时或运行时解析并翻译到具体的内存地址之中

   - 常量池的项目结构

     > 常量池中的每一项常量都是一个表，共有11中结构各不相同的表结构数据，这些表的第一位都是一个u1类型的标志位（取值1，3-12），表示该常量属于那种常量类型。一下所有表各自均有自己的结构

     | 类型                             | 标志 | 描述                     |
     | -------------------------------- | ---- | ------------------------ |
     | CONSTANT_Utf8_info               | 1    | UTF-8编码字符串          |
     | CONSTANT_Integer_info            | 3    | 整型字面量               |
     | CONSTANT_Float_info              | 4    | 浮点型字面量             |
     | CONSTANT_Long_info               | 5    | 长整型字面量             |
     | CONSTANT_Double_info             | 6    | double型字面量           |
     | CONSTANT_Class_info              | 7    | 类或接口的符号引用       |
     | CONSTANT_String_info             | 8    | 字符串字面量             |
     | CONSTANT_Fieldref_info           | 9    | 字段符号引用             |
     | CONSTANT_Methodref_info          | 10   | 类中方法符号引用         |
     | CONSTANT_InterfaceMethodref_info | 11   | 接口中的方法符号引用     |
     | CONSTANT_NameAndType_info        | 12   | 字段或方法的部分符号引用 |
| CONSTANT_MethodHandle_info       | 15   | 表示方法句柄             |
     | CONSTANT_MethodType_info         | 16   | 标识方法类型             |
     | CONSTANT_InvokeDynamic_info      | 18   | 表示一个动态方法调用点   |
     
     关于表结构，以下为两个实例
     
     **CONSTANT_Class_info**型常量的结构：
     
     | 类型 | 名称       | 数量 |
     | ---- | ---------- | ---- |
     | u1   | tag        | 1    |
     | u2   | name_index | 1    |
     
     > tag为标志位，指明该常量类型；name_index为索引值，它指向常量池中的一个CONSTANT_Class_info类型的常量，此常量代表了这个类（或接口）的全限定名，该name_index值指代的是CONSTANT_Class_info在常量池中的位置，如name_index=20,则常量池中第20个常量的值即为该CONSTANT_Class_info的类（或接口全限定名）；
     
     **CONSTANT_Utf8_info**型常量的结构：
     
     | 类型 | 名称   | 数量   |
     | ---- | ------ | ------ |
     | u1   | tag    | 1      |
     | u2   | length | 1      |
     | u1   | bytes  | length |
     
     > length值说明了该utf-8编码的字符串的长度是多少个字节，bytes是length个字节的连续数据是一个采用UTF-8 缩略编码表示的字符串（缩略编码：从‘\u0001’ 到 '\u007f' 间的字符采用一个字节描述；从‘\u0080’ 到 '\u07ff' 间的字符采用两个字节描述；从‘\u0800’ 到 '\uffff' 间的字符采用普通编码规则用三个字节描述）；
     >
     > **注意：**Class文件中方法、字段都需要引用CONSTANT_Utf8_info型常量来描述名称，所以 CONSTANT_Utf8_info 型常量的最大长度就是 java 中方法和字段名的最大长度，而此最大长度就是length的最大值（两个字节65535，该值指代的是字节长度），所以 java 程序中不能定义超过64KB=65536个字节长度的英文字符变量或方法名，否则将无法编译
     
     **数值**类型常量的结构：
     
     > 其中Integer、Float、Long、Double结构一致，含义略有不同
     
     | 类型 | 名称  | 数量 |
     | ---- | ----- | ---- |
     | u1   | tag   | 1    |
     | u4   | bytes | 1    |
     
     > tag:标志位；
     >
     > bytes：
     >
     > ​	Integer：按照高位在前存储的int值
     >
     > ​	Float：按照高位在前存储的float值
     >
     > ​	Long：按照高位在前存储的long值
     >
     > ​	Double：按照高位在前存储的double值
     
     **CONSTANT_String_info**型常量结构：
     
     | 类型 | 名称  | 数量 |
     | ---- | ----- | ---- |
     | u1   | tag   | 1    |
     | u2   | index | 1    |
     
     > tag：标志位
     >
     > index：指向字符串字面量的索引，应该也是指向一个CONSTANT_Utf8_info型常量位置
     
     **CONSTANT_Fieldref_info**型常量结构：
     
     | 类型 | 名称   | 数量 |
     | ---- | ------ | ---- |
     | u1   | tag    | 1    |
     | u2   | index1 | 1    |
     | u2   | index2 | 1    |
     
     > tag：标志位
     >
     > index1：指向声明字段的类或接口描述符CONSTANT_Class_info的索引项
     >
     > index2：指向字段描述符CONSTANT_NameAndType_info的索引项
     
     **CONSTANT_Methodref_info**型常量结构	
     
     | 类型 | 名称   | 数量 |
     | ---- | ------ | ---- |
     | u1   | tag    | 1    |
     | u2   | index1 | 1    |
     | u2   | index2 | 1    |
     
     > tag：标志位
     >
     > index1：指向声明方法描述符CONSTANT_Class_info的索引项
     >
     > index2：指向名称及类型描述符CONSTANT_NameAndType_info的索引项
     
     **CONSTANT_InterfaceMethodref_info**型常量结构：
     
     | 类型 | 名称   | 数量 |
     | ---- | ------ | ---- |
     | u1   | tag    | 1    |
     | u2   | index1 | 1    |
     | u2   | index2 | 1    |
     
     >tag：标志位
     >
     >index1：指向声明方法的接口描述符CONSTANT_Class_info的索引项
     >
     >index2：指向名称及类型描述符CONSTANT_NameAndType_info的索引项
     
     **CONSTANT_NameAndType_info**型常量结构：
     
     | 类型 | 名称   | 数量 |
     | ---- | ------ | ---- |
     | u1   | tag    | 1    |
     | u2   | index1 | 1    |
     | u2   | index2 | 1    |
     
     > tag：标志位
     >
     > index1：指向该字段或方法名称常量项的索引
     >
     > index2：指向该字段或方法描述符常量项的索引
     
     ==注意==：以上index1指代name_index；index2指代descriptor_index
     
     JDK1.7补充内容：
     
     **CONSTANT_MethodHandle_info**型常量结构
     
     | 类型 | 名称            | 数量 |
     | ---- | --------------- | ---- |
     | u1   | tag             | 1    |
     | u1   | reference_kind  | 1    |
     | u2   | reference_index | 1    |
     
     > tag：标志位
     >
     > reference_kind：值必须在1~9之间，它决定了方法句柄的类型。方法句柄类型的值表示方法句柄的字节码行为；
     >
     > reference_index：对常量池的有效索引
     
     **CONSTANT_MethodType_info**型常量结构：
     
     | 类型 | 名称             | 数量 |
     | ---- | ---------------- | ---- |
     | u1   | tag              | 1    |
     | u2   | descriptor_index | 1    |
     
     > tag：标志位
     >
     > descriptor_index：值必须是对常量池的有效索引，常量池在该索引处的项必须是CONSTANT_Utf8_info结构，表示方法的描述符
     
     **CONSTANT_InvokeDynaamic_info**型常量结构：
     
     | 类型 | 名称                        | 数量 |
     | ---- | --------------------------- | ---- |
     | u1   | tag                         | 1    |
     | u2   | bootstrap_method_attr_index | 1    |
     | u2   | name_and_type_index         | 1    |
     
     > tag：标志位
     >
     > bootstrap_method_attr_index：值必须是对当前class文件中引导方法表示的bootstrap_method[]数组的有效索引；
     >
     > name_and_type_index：值必须是对当前常量池的有效索引，常量池在该索引处的索引项必须是CONSTANT_NameAndType_info结构，表示方法名和方法描述符