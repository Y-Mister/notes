## Spring Framework 5

#### 控制反转/依赖注入

控制反转是spring框架的组件装配模式，马丁·福勒则将其称为“依赖注入”，其作用是将组件的配置与使用分离开，详情解释参见：http://insights.thoughtworkers.org/injection/

组件：一个软件单元，它将被作者无法控制的其他应用程序使用，但后者不能对组件进行修改。也就是说，使用一个组件的应用程序不能修改组件的源代码，但可以通过作者预留的某种途径对其进行扩展，以改变组件的行为

服务：与组件相似，都被外部的应用程序使用，但组件是在本地使用（例如JAR文件、程序集、DLL、或者源码导入），而服务是要通过同步或异步的远程接口来远程使用的（例如web service、消息系统、RPC，或者socket）

对马丁 · 福勒文章内容的个人注释：（属实难以理解）

- 一个简单的例子

  > 图1展现了这种情况下的依赖关系：MovieLister类既依赖于MovieFinder接口，也依赖于具体的实现类。我们当然希望MovieLister类只依赖于接口，但我们要如何获得一个MovieFinder子类的实例呢？

  为何想要只依赖与MovieFinder接口呢，显然，为了不同的使用者想要使用MovieLister时并不用关心具体的实现细节，并不需要被束缚存储方式，必然不能依赖于具体的MovieFinder子类，这将影响程序的横向扩展与通用性。

- 使用PicoContainer进行构造器注入

  > 尽管PicoContainer本身并不包含这项功能，但另一个与它关系紧密的项目NanoContainer提供了一些包装，允许开发者使用XML配置文件保存配置信息。NanoContainer能够解析XML文件，并对底下的PicoContainer进行配置。这个项目的哲学观念就是：将配置文件的格式与底下的配置机制分离开

  通过配置文件对应用程序进行配置，实现配置与程序的分离，所谓构造器注入，即通过构造器将何时的实例注入到指定的字段中，而配置文件，则可以实现指定注入对象的功能

- 使用spring 进行设值注入

  > 为了让MovieLister类接受注入，我需要为它定义一个设值方法，该方法接受类型为MovieFinder的参数

  那么Spring的设值注入，就是通过setter函数将指定的需要的对象赋值到需要该对象的地方，而赋值的过程就是一种抽象的注入。通用的，也可以通过配置文件来指定你需要的对象，从而实现配置配置文件来配置程序注入对象。

- 通过接口注入

  ```java
  public interface InjectFinder
  {
      void injectFinder(MovieFinder finder);
  }
  
  class MovieLister implements InjectFinder...
      public void injectFinder(MovieFinder finder)
      {
          this.finder = finder;
      }
  ```

  从示例代码来看，所谓通过接口注入只是将设值注入的设值函数通过接口定义并使实现类实现这个设值注入函数，那么为什么要这样封装一次呢？？？

- service locator

  > Service Locator模式背后的基本思想是：有一个对象（即服务定位器）知道如何获得一个应用程序所需的所有服务。也就是说，在我们的例子中，服务定位器应该有一个方法，用于获得一个MovieFinder实例。当然，这不过是把麻烦换了一个样子，我们仍然必须在MovieLister中获得服务定位器

  那么service locator与spring的依赖注入区别在哪呢？依赖注入通过装配器获得想要的子类从而丢弃对子类的直接依赖，而service locator通过服务定位器获得，其本质是相同的，摒弃对于子类的直接依赖，而通过多态让父类实现不同的子类功能。

- 动态服务定位器 dynamic service locator

  > 对于你所需要的每项服务，ServiceLocator类都有对应的方法。这并不是实现服务定位器的唯一方式，你也可以创建一个动态服务定位器，你可以在其中注册需要的任何服务，并在运行期决定获得哪一项服务;
  >
  > 在本例中，ServiceLocator使用一个map来保存服务信息，而不再是将这些信息保存在字段中。此外，ServiceLocator还提供了一个通用的方法，用于获取和加载服务对象

  ```java
  class ServiceLocator...
      private static ServiceLocator soleInstance;
      public static void load(ServiceLocator arg)
      {
          soleInstance = arg;
      }
      private Map services = new HashMap();
      public static Object getService(String key)
      {
          return soleInstance.services.get(key);
      }
      public void loadService (String key, Object service)
      {
          services.put(key, service);
      }
  ```

  代码中，将所有的service加载到map中，动态获取服务时从map中根据服务名获取相应的service对象，这就是动态的最基本实现吗？

  ```java
  private static ServiceLocator soleInstance;
  ```

  为什么要再类里放一个自身实例对象字段呢？单例模式？

  