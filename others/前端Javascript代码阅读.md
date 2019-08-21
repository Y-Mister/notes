前端Javascript代码

1. arguments对象

   > 该对象包含了传递给当前函数的所有参数，类似于array但不是一个array，其只有长度和索引元素；

2. Array.prototype属性

   > Array的构造函数的原型，通过该属性可以向Array添加新的属性和方法属性,其后所有的Array对象均可以使用

3. AMD异步模块加载机制define模块

   > define(模块名,[依赖数组]，JavaScript对象或者回调函数)
   >
   > 其中模块名与依赖数组均可省略

   前端框架结构
   
   ```javascript
   define(
   	[依赖的模块数组，包括html,css,和其它js依赖],
   	function(viewTpl,utils,css···){ //回调函数，此处可传入参数来历吧不清楚，已知有依赖js中定义的参数，utils为封装的功能js内定义的函数对象，下面将通过该对象调用函数完成功能
           var session = utils.GetSession();//获取session对象，可有可无，作用暂未知，除session外，还可自定义全局变量以便于下面使用
           return fish.View.extend({
               
               template : fish.compile(viewTpl);//编译页面源？
               
               events : {//页面内事件定义
               		//模板 '事件类型 #绑定的对象Id' : '事件名' 例如： 
           			'click #my-button' : 'queryBtn',
               		'·······' : '····'//此处定义的事件将在下面实现
           			}，
                                   
               initialize:function(){
               		//页面初始化时执行函数，可在内部执行一些初始化操作
           			}，
               
               //渲染页面函数模块
               _render : function(){  //貌似默认写法，若注释无法显示页面
                   this.$el.html(this.template(this.il8nData));
                   return this;
               },
               //初始化fish组件
               afterRender : function(){
                   //此处手动调用一些初始化功能函数，完成页面的最终显示
                   //也可以在此处完成上文中定义的全局变量的初始化操作
                   //调用模板：
                   //this.functionName(parameters);
                   //常见必写的有(可能是框架默认定义的函数)
                   this.init();
                   this.resize();
                   //还有一个常见的页面Grid加载函数，多个js函数名不一致，猜测函数名自定义
                   fish.onResize(this.resize);//该函数作用未知
               },
               init : function(){
                 	//可选择执行一些初始化操作，如做一次查询，将值放入表单元素内  
               },
               //窗口大小自适应，
           	resize : function(){
                   //调整窗口大小操作
                   //这个函数窗口没调整一次，其都会执行一次
               },
               //-----------------------------------------------------
               //目前以上为各个页面主加载js页面中全部都包含的函数，猜测为框架默认定义的函数，下面这是自定义函数，如上方events块定义的事件函数,例如：
               queryBtn : function(param){
                   //此处通常先定义一个js的Object对象，在通过页面对象Id获取操作对象或数据
                   var obj = new Object();
                   obj.field = $("#id").val();
                   //再通过自定义函数或依赖工具包js进行数据处理
               },
               functionName : function(){
                   //注意点，调用本页面下的同级函数必须通过this调用
                   this.queryBtn(param);
               },
               ······
               
           })//页面加载
           
           
       }
   )
   /*通过alert弹窗测试，从initialize到afterRender函数，执行顺序是之上而下的，其他顺序则是以调用顺序为准*/
   ```
   
   在页面内调用其他页面构成popupView组件：
   
   ```javascript
   fish.popupView({
      utl: '要使用的页面所在相对地址'，
      width : "指定popupView宽度",
      height : "指定popupView高度",
      viewOption : {
       	//作用未知
   	},
      close : $.proxy(function(){
       
   	},this);  
   	//$.proxy(fn,context);将函数绑定至上下文？在jquery中表示针对当前强制执行某一指定函数
   });
   ```
   
   框架封装的一些函数
   
   ```javascript
   fish.toast("message");
   fish.info("message");
   fish.confirm("message");
   fish.error("message");
   //以上都是对一些提示框的封装
   //-----------------------------------
   ```
   
   
   
   关于js的一些初次学习到的点
   
   ```javascript
   var obj={}  === var obj = new Object();
   var arr=[]  === var arr=new Array();
   $.extend(a,b);//将b对象内容合并到a对象中，如果a和b对象中存在同名属性，那么b对象属性值将会覆盖a对象值，这是一个jquery杂项方法
   ```
   
   ```html
   <!--javaScript允许函数声明时定义一个参数，但是传入时可传任意个数量的参数，其中，按顺序给声明参数赋给实际的传入参数值 demo如下，注意，arguments对象是一个数组，保存传入到函数的所有参数值-->
   <!DOCTYPE html>
   <html>
   <head>
   	<title></title>
   </head>
   <script type="text/javascript">
   function tt(aa)
   {
       alert(arguments.length+"--"+arguments[1]); //2--8
       alert(aa);//1
   }
   function $()
   {
       alert(arguments.length);//3
   }
   </script>
   
   
   <body>
   <input type="button" value="click me" onclick="tt(1,8)" />&nbsp;&nbsp;
   <input type="button" value="click me again" onclick="$(1,2,3)" />
   </body>
   </html>
   ```

4. 前台业务请求提交到后台处理流程

   前台封装请求数据，统一通过“/isa-web-dw/common/invokeRemoteAction/invoke.do”这个url发出，随后在后端的isa-service-iom的isaExecuteService.java中执行了execute、invokeMethod、route三个方法并输出了日志；
   
   通过反射获取serviceBean，进而通过反射和调用的方法名获取指定方法，最后通过反射调用方法；
   
   整个流程：
   
   > 前台发出请求后，通过isaisaExecuteService.java中的execute、invokeMethod、route的三个方法通过反射获取到要调用的获取到传入的serviceName和methodName对应的service和method
   >
   > 继而通过反射调用method；
   >
   > 在method中，通过工厂获取到需要的dao实例，依据传入的参数进行数据查询