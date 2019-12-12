## Flutter

1. 环境搭建和配置

   1. 终端使用open打开文件夹
   2. 需要准备的东西：
      1. 网上下载flutter，studio(google官方)
      2. Bash_profile里面配置flutter的全局命令，使用`flutter doctor`进行环境监测
      3. studio里面模拟机的搭建，同时要安装flutter和dart两个插件，这样就能进行dart环境下的开发了；
   3. flutter create my_app创建项目，然后flutter run运行项目，且studio上面有模拟器的启动和IDE的管理；
   4. shift+command+.展现mac里面.开头的文件

2. Flutter

   1. Dart

      1. DartPad线上运行内容

      2. main作为程序的入口；print打印内容；类型可以指定也可以不指定（即常规类型和var声明的方式，var声明的时候是自动检测后面的类型然后定型了）；

      3. 没有初始化的变量是null，且数字类型被当做对象，未初始化是null；

      4. 对象声明使用object和dynamic都可以，前者调用的时候只能访问提供的方法，后者则是所有可能，例如length，前者没有声明则不会返回

      5. final和const都能声明常量，final表示在第一次使用的时候才会初始化

      6. 只有为true的时候才是true

      7. 函数式编程的特征，即函数可以赋值给变量或者作为参数传递进函数

      8. ?.类似js中的&&，左边为null时候右边就不调用了；??则是在左边为null时候设置默认值

      9. 函数不用关键字function，直接括号声明就行或者前面加上void，类里面的方法可以通过@override进行重写；声明时候可以根据return的类型进行函数类型的声明，没有则使用void；单表达式的函数可以使用箭头函数

      10. 字符表达式：$a的方式；\$[func()]的方式进行函数的引用

      11. 函数参数里面[]表示可选位置参数，{}表示可选的命令参数，即调用的时候再赋值，实现声明式的感觉；例子如下

          ```dart
          void test({bool a, String b}){}
          test({a:true, b:'123'})
          ```

      12. 异步实现有两个对象，future和stream两个对象实现，future类似于promise，stream则是不断监听；future的wait方法就是promise里面的all，whenComplete就是finally；Stream方式如下

          ```dart
          // 里面的结果会不断的放到第一个里面
          Stream.fromFutures([p1, p2]).listen((data){},onError(){},onDone(){})
          ```

      13. 没有export，import后面三种形式，没有表示都引入，show表示引入特定的，hide即取反

      14. 单线程的模型，微任务到任务队列，微任务队列优先度最高且来源于Dart内部，可以通过future.microtask插入任务；异常的时候只会终止当前的代码，而不会终止所有的任务

      15. Dart本身也是单线程，异步请求操作如下

          ```dart
          reFunc(){
            final url = 'http://pbin.org/ip';
            HttpRequest.request(url).then((value){
              json.decode(value.responseText)['origion'];
            }).catchError((error) => print(error))
          }
          ```

      16. Async/await函数的使用；

          ```dart
          getIpAddress() async {
            final url = 'http';
            // 不展示类型
            var request = await HttpRequest.request(url);
            // String类型
            String ip = json.decode(request.responseText)['origion'];
            print(ip)
          }
          ```

   2. Flutter

      1. 声明式UI：看下面的场景，更改B的颜色和B里面C的数量

         1. 运行UI：更改B的颜色，删除B里面的内容，new一个C，C链接到B上面

         2. 声明式：代码如下

            ```dart
            return {
              color:'change',
              child:viewC(...)
            }
            ```

         3. 即每次的widget也是全部刷新，和setState一样的东西；

      2. 不同的库内容如下

         ```dart
         //导入系统的material库
         import 'package:flutter/material.dart';
         //导入ios样式的widget库
         import 'package:flutter/cupertino.dart';
         //导入基本的窗口widget集
         import 'package:flutter/widgets.dart';
         //导入自己的库
         import 'package:flutter/myOwn.dart';
         ```

      3. yaml里面是所有的配置，assets放的就是静态文件

      4. android里面有不同像素密度和flutter像素的对应关系，这样放图片的时候`images/1.0x/icon.png`然后在yaml里面声明如下再使用`return AssetImage("images/icon.png")`就可以访问到不同像素的图片了，或者直接使用Image这个widget弄就好了

         ```javascript
         ldpi	0.75x
         mdpi	1.0x
         hdpi	1.5x
         xhdpi	2.0x
         xxhdpi	3.0x
         xxxhdpi	4.0x
         assets
          - images/icon.png
         ```

      5. Flutter 插件使用，首先不同平台文件下有自己的依赖，大家都用的时候才是放到yaml里面

      6. Widget

         1. View是安卓和ios里面展示东西，在调用invalidate/setNeedsDisplay之前不会重绘
         2. Widget本身不可变，通过操作状态，状态改变的时候整个widget重新绘制，是flutter的东西；
         3. widget类似于傻瓜和smart组件，本身分为有状态和无状态，依赖于state里面的内容时候就是有状态；
         4. 条件控制不同的widget通过类似renderSub的方式，通过state里面的值返回不同的组件来实现渲染内容的不同；

      7. Widget例子如下

         1. 先看总体的代码

            ```dart
            //material是移动端和web端标准的设计语言，这边是接口
            import 'package:flutter/material.dart';
            // 主程序入口
            void main() => runApp(new MyApp());
            
            class MyApp extends StatelessWidget {
              // 重写build方法进行widget构建
              @override
              Widget build(BuildContext context) {
                // materialApp是Flutter App框架
                return new MaterialApp(
                  // 应用名称
                  title: 'Flutter Demo',
                  // style
                  theme: new ThemeData(
                    primarySwatch: Colors.blue,
                  ),
                  // 首页路由
                  home: new MyHomePage(title: 'Flutter Demo Home Page'),
                );
              }
            }
            // 一个有状态组件
            // 本身不可变，通过分离出去的state控制自身的改变
            class MyHomePage extends StatefulWidget {
              // key用于之后时候缓存
              MyHomePage({Key key, this.title}) : super(key: key);
              final String title;
            	// state的创建，而不是重写build
              @override
              _MyHomePageState createState() => new _MyHomePageState();
            }
            // 分离出state，利用state的改变来影响有状态组件的改变
            class _MyHomePageState extends State<MyHomePage> {
              int _counter = 0;
            
              void _incrementCounter() {
                setState(() {
                  _counter++;
                });
              }
            
              @override
              Widget build(BuildContext context) {
                // 提供默认的导航栏，标题，包含主屏幕的widget的body属性
                return new Scaffold(
                  appBar: new AppBar(
                    title: new Text(widget.title),
                  ),
                  // center表示居中
                  body: new Center(
                    // 按照列排
                    child: new Column(
                      mainAxisAlignment: MainAxisAlignment.center,
                      children: <Widget>[
                        new Text(
                          'You have pushed the button this many times:',
                        ),
                        new Text(
                          '$_counter',
                          style: Theme.of(context).textTheme.display1,
                        ),
                      ],
                    ),
                  ),
                  // 悬浮按钮
                  floatingActionButton: new FloatingActionButton(
                    onPressed: _incrementCounter,
                    tooltip: 'Increment',
                    child: new Icon(Icons.add),
                  ), // This trailing comma makes auto-formatting nicer for build methods.
                );
              }
            }
            ```

         2. 路由代码实现如下

            ```dart
            child: new Column(
            	mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                  new Text(
                    'You have pushed the button this many times:',
                  ),
                  new Text(
                    '$_counter',
                    style: Theme.of(context).textTheme.display1,
                   ),
              ],
              FlatButton(
              	  child: Text("open new route"),
                  textColor: Colors.blue,
                  onPressed: () {
                      //导航到新路由   
                      Navigator.push( context,
                      new MaterialPageRoute(builder: (context) {
                          return new NewRoute();
                      }));
                  },
              ),
            )
            // 路由类
            MaterialPageRoute({
              	// 返回的实例
                WidgetBuilder builder,
              	// 参数，路径名称等
                RouteSettings settings,
              	// 原来路由是否缓存
                bool maintainState = true,
              	// 表示路由是否全屏
                bool fullscreenDialog = false,
            })
            ```

         3. 在MaterialApp里面进行路由的注册

            ```dart
            return new MaterialApp(
            	routes:{
                "new_page":(context) => NewRoutes()
              }
            )
            //打开新路由的时候
            onPressed: () {
              // 使用的是自定义名字的路由
              Navigator.pushNamed(context, "new_page");
              //Navigator.push(context,
              //  new MaterialPageRoute(builder: (context) {
              //  return new NewRoute();
              //}));  
            },
            ```

         4. 路由传参

            ```dart
            class EchoRoute extends StatelessWidget {
            
              @override
              Widget build(BuildContext context) {
                //获取路由参数  
                var args=ModalRoute.of(context).settings.arguments
                //...省略无关代码
              }
            }
            // 路由传参
            Navigator.of(context).pushNamed("new_page", arguments: "hi");
            ```

         5. DebugDumpApp展现当前widget的状态；

         6. debugDumpRenderTree则是展示布局上的内容；看size和constraints字段；具体还是找网站内容；

         7. 框架的错误捕获内容

            ```dart
            @override
            void performRebuild() {
             ...
              try {
                //执行build方法  
                built = build();
              } catch (e, stack) {
                // 有异常时则弹出错误提示  
                built = ErrorWidget.builder(_debugReportException('building $this', e, stack));
              } 
              ...
            }
            ```

         8. Widget介绍

            1. 本身是配置内容，用于生成element

            2. 声明周期

               1. initState：初始化的时候调用，一般赋值写在这里面，且state里面的要继承执之前的state变量内容；
               2. `didChangeDependencies()`：当State对象的依赖发生变化时会被调用；例如：在之前`build()` 中包含了一个`InheritedWidget`，然后在之后的`build()`中`InheritedWidget`发生了变化，那么此时`InheritedWidget`的子widget的`didChangeDependencies()`回调都会被调用。典型的场景是当系统语言Locale或应用主题改变时，Flutter framework会通知widget调用此回调。
               3. `build()`：此回调读者现在应该已经相当熟悉了，它主要是用于构建Widget子树的，会在如下场景被调用：
                  1. 在调用`initState()`之后。
                  2. 在调用`didUpdateWidget()`之后。
                  3. 在调用`setState()`之后。
                  4. 在调用`didChangeDependencies()`之后。
                  5. 在State对象从树中一个位置移除后（会调用deactivate）又重新插入到树的其它位置之后。
               4. `reassemble()`：此回调是专门为了开发调试而提供的，在热重载(hot reload)时会被调用，此回调在Release模式下永远不会被调用。
               5. `didUpdateWidget()`：在widget重新构建时，Flutter framework会调用`Widget.canUpdate`来检测Widget树中同一位置的新旧节点，然后决定是否需要更新，如果`Widget.canUpdate`返回`true`则会调用此回调。正如之前所述，`Widget.canUpdate`会在新旧widget的key和runtimeType同时相等时会返回true，也就是说在在新旧widget的key和runtimeType同时相等时`didUpdateWidget()`就会被调用。
               6. `deactivate()`：当State对象从树中被移除时，会调用此回调。在一些场景下，Flutter framework会将State对象重新插到树中，如包含此State对象的子树在树的一个位置移动到另一个位置时（可以通过GlobalKey来实现）。如果移除后没有重新插入到树中则紧接着会调用`dispose()`方法。
               7. `dispose()`：当State对象从树中被永久移除时调用；通常在此回调中释放资源。

            3. GestureDetector监控事件

            4. 赋值和输入时候都可以用三元

            5. 基础widget：

               1. Text：创建文本内容

                  1. textAlign控制文字排列方式，注意文字过短时候，没有撑开text，居中有问题

                  2. maxLines，overflow控制文字的省略方式

                  3. textScaleFactor：表示文字的缩放程度，基于fontSize

                  4. style字段，代码如下

                     ```dart
                     Text("Hello world",
                       style: TextStyle(
                         color: Colors.blue,
                         // 定值，而之前的缩放因子会因为系统的文字大小进行控制
                         fontSize: 18.0,
                         // 高度是因子，相对于fontSize来说的
                         height: 1.2,  
                         fontFamily: "Courier",
                         background: new Paint()..color=Colors.yellow,
                         decoration:TextDecoration.underline,
                         decorationStyle: TextDecorationStyle.dashed
                       ),
                     );
                     ```

                  5. 富文本使用和不同字段样式设置

                     ```dart
                     // 构建出的是富文本
                     Text.rich(TextSpan(
                         children: [
                           // 第一段
                          TextSpan(
                            text: "Home: "
                          ),
                           // 第二段
                          TextSpan(
                            text: "https://flutterchina.club",
                            style: TextStyle(
                              color: Colors.blue
                            ),  
                            recognizer: _tapRecognizer
                          ),
                         ]
                     ))
                     ```

                  6. 在widget节点设置之后的默认样式

                     ```dart
                     DefaultTextStyle(
                       //1.设置文本默认样式  
                       style: TextStyle(
                         color:Colors.red,
                         fontSize: 20.0,
                       ),
                       textAlign: TextAlign.start,
                       child: Column(
                         crossAxisAlignment: CrossAxisAlignment.start,
                         children: <Widget>[
                           Text("hello world"),
                           Text("I am Jack"),
                           // 像下面进行样式的覆盖
                           Text("I am Jack",
                             style: TextStyle(
                               inherit: false, //2.不继承默认样式
                               color: Colors.grey
                             ),
                           ),
                         ],
                       ),
                     );
                     ```

               2. Row, Column：水平和垂直进行布局，基于flex布局

               3. Stack：许可子widget重叠，利用position进行位置的确定；

               4. Container：矩形元素，包括background, margins, padding, 大小约束

            6. Material Widget，安卓风格的widget内容

               1. 以MaterialApp widget起手，利用根部创建，包含theme，

               2. Scaffold, appBar, FlatButton等都是里面的，引入方式如下

                  ```dart
                  import 'package:flutter/material.dart';
                  ```

               3. button相关

                  1. 原始的是RawMaterialButton，其他的RaisedButton、FlatButton、OutlineButton都是对上面东西的包装；默认的有两个特点：按下时后水波动画，onPressed来监听回调，不设置的时候不可以点击

                  2. RaisedButton，漂浮按钮，默认阴影和灰色背景

                     ```dart
                     RaisedButton(
                       child: Text("normal"),
                       onPressed: () => {},
                     );
                     ```

                  3. FlatButton，默认背景透明不带阴影，按下有背景色

                  4. OutlineButton，点击后会有边框

                  5. IconButton，可点击的icon

                     ```dart
                     IconButton(
                       icon: Icon(Icons.thumb_up),
                       onPressed: () => {},
                     )
                     ```

                  6. 自定义button设置如下，还是利用api去做

                     ```dart
                     FlatButton(
                       // Colors(0x000000)设置背景颜色全透明
                       color: Colors.blue,
                       highlightColor: Colors.blue[700],
                       colorBrightness: Brightness.dark,
                       splashColor: Colors.grey,
                       child: Text("Submit"),
                       shape:RoundedRectangleBorder(borderRadius: BorderRadius.circular(20.0)),
                       onPressed: () => {},
                     )
                     ```

               4. 图片和Icon

                  1. ImageProvider是读取文件的抽象类，AssetImage和NetworkImage分别基于这个实现了从本地和网络读取图片；

                  2. asset加载图片，在yaml里面进行配置，注意两个空格严格要求的

                     ```dart
                     assets:
                       - images/avatar.png
                     ```

                  3. 加载图片下面两个写法都可以

                     ```dart
                     Image(
                       image: AssetImage("images/avatar.png"),
                       width: 100.0
                     );
                     Image.asset("images/avatar.png",
                       width: 100.0,
                       // repeat表示重复
                       repeat: ImageRepeat.repeatY,
                       // 表示的是混合色
                       color: Colors.blue,
                     )
                     ```

                  4. 同样netWorkImage也是上面的方式

                  5. 通过width, height指定或者设定fit，包括fill, cover, contain, fitHeight, fitWidth, none

                  6. IconFont即把图像当做字体使用，使用例子如下

                     ```dart
                     // 配置引入本地内容
                     fonts:
                       - family: myIcon  #指定一个字体名
                         fonts:
                           - asset: fonts/iconfont.ttf
                     // 声明对应的图标
                     class MyIcons{
                       // book 图标，对应的编码可以查
                       static const IconData book = const IconData(
                           0xe614, 
                           fontFamily: 'myIcon', 
                           matchTextDirection: true
                       );
                       // 微信图标
                       static const IconData wechat = const IconData(
                           0xec7d,  
                           fontFamily: 'myIcon', 
                           matchTextDirection: true
                       );
                     }
                     // 使用方式
                     Row(
                       mainAxisAlignment: MainAxisAlignment.center,
                       children: <Widget>[
                         Icon(MyIcons.book,color: Colors.purple,),
                         Icon(MyIcons.wechat,color: Colors.green,),
                       ],
                     )
                     ```

               5. 单选开关和复选框

                  1. 本身继承自无状态组件，所以使用的话要自己封装成有状态的；可以控制激活状态，但是不可控大小

                     ```dart
                     class SwitchAndCheckBoxTestRoute extends StatefulWidget {
                       @override
                       _SwitchAndCheckBoxTestRouteState createState() => new _SwitchAndCheckBoxTestRouteState();
                     }
                     
                     class _SwitchAndCheckBoxTestRouteState extends State<SwitchAndCheckBoxTestRoute> {
                       bool _switchSelected=true; //维护单选开关状态
                       bool _checkboxSelected=true;//维护复选框状态
                       @override
                       Widget build(BuildContext context) {
                         return Column(
                           children: <Widget>[
                             Switch(
                               value: _switchSelected,//当前状态
                               onChanged:(value){
                                 //重新构建页面  
                                 setState(() {
                                   _switchSelected=value;
                                 });
                               },
                             ),
                             Checkbox(
                               value: _checkboxSelected,
                               activeColor: Colors.red, //选中时的颜色
                               onChanged:(value){
                                 setState(() {
                                   _checkboxSelected=value;
                                 });
                               } ,
                             )
                           ],
                         );
                       }
                     }
                     ```

               6. 输入框和表单

                  1. TextField进行文本内容输入

                     1. 直接上例子

                        ```dart
                        Column(
                                children: <Widget>[
                                  TextField(
                                    autofocus: true,
                                    decoration: InputDecoration(
                                        labelText: "用户名",
                                        hintText: "用户名或邮箱",
                                        prefixIcon: Icon(Icons.person)
                                    ),
                                  ),
                                  TextField(
                                    decoration: InputDecoration(
                                        labelText: "密码",
                                        hintText: "您的登录密码",
                                        prefixIcon: Icon(Icons.lock)
                                    ),
                                    obscureText: true,
                                  ),
                                ],
                        );
                        ```

                     2. 获取输入两种方式，一种是直接赋值，然后监控onChange事件；另一种是绑定controller，之后的内容直接访问controller就可以了

                        ```dart
                        //定义一个controller
                        TextEditingController _unameController=new TextEditingController();
                        TextField(
                            autofocus: true,
                            controller: _unameController, //设置controller
                            ...
                        )
                        ```

                     3. 监听文本变化

                        ```dart
                        // onChange回调
                        TextField(
                            autofocus: true,
                            onChanged: (v) {
                              print("onChange: $v");
                            }
                        )
                        // control绑定
                        @override
                        void initState() {
                          //监听输入改变  
                          _unameController.addListener((){
                            print(_unameController.text);
                          });
                        }
                        ```

                     4. Focus相关内容没有看

                  2. Form

                     1. 待验证的表单如下

                        ```dart
                        class FormTestRoute extends StatefulWidget {
                          @override
                          _FormTestRouteState createState() => new _FormTestRouteState();
                        }
                        
                        class _FormTestRouteState extends State<FormTestRoute> {
                          // 两个表单的controller
                          TextEditingController _unameController = new TextEditingController();
                          TextEditingController _pwdController = new TextEditingController();
                          // 用于ref，这样btn就能回调验证
                          GlobalKey _formKey= new GlobalKey<FormState>();
                        
                          @override
                          Widget build(BuildContext context) {
                            return Scaffold(
                              appBar: AppBar(
                                title:Text("Form Test"),
                              ),
                              body: Padding(
                                padding: const EdgeInsets.symmetric(vertical: 16.0, horizontal: 24.0),
                                child: Form(
                                  key: _formKey, //设置globalKey，用于后面获取FormState
                                  autovalidate: true, //开启自动校验
                                  child: Column(
                                    children: <Widget>[
                                      TextFormField(
                                          autofocus: true,
                                          controller: _unameController,
                                          decoration: InputDecoration(
                                              labelText: "用户名",
                                              hintText: "用户名或邮箱",
                                              icon: Icon(Icons.person)
                                          ),
                                          // 校验用户名
                                          validator: (v) {
                                            return v
                                                .trim()
                                                .length > 0 ? null : "用户名不能为空";
                                          }
                        
                                      ),
                                      TextFormField(
                                          controller: _pwdController,
                                          decoration: InputDecoration(
                                              labelText: "密码",
                                              hintText: "您的登录密码",
                                              icon: Icon(Icons.lock)
                                          ),
                                          obscureText: true,
                                          //校验密码
                                          validator: (v) {
                                            return v
                                                .trim()
                                                .length > 5 ? null : "密码不能少于6位";
                                          }
                                      ),
                                      // 登录按钮
                                      Padding(
                                        padding: const EdgeInsets.only(top: 28.0),
                                        child: Row(
                                          children: <Widget>[
                                            Expanded(
                                              child: RaisedButton(
                                                padding: EdgeInsets.all(15.0),
                                                child: Text("登录"),
                                                color: Theme
                                                    .of(context)
                                                    .primaryColor,
                                                textColor: Colors.white,
                                                onPressed: () {
                                                  //在这里不能通过此方式获取FormState，context不对
                                                  //print(Form.of(context));
                        
                                                  // 通过_formKey.currentState 获取FormState后，
                                                  // 调用validate()方法校验用户名密码是否合法，校验
                                                  // 通过后再提交数据。 
                                                  if((_formKey.currentState as FormState).validate()){
                                                    //验证通过提交数据
                                                  }
                                                },
                                              ),
                                            ),
                                          ],
                                        ),
                                      )
                                    ],
                                  ),
                                ),
                              ),
                            );
                          }
                        }
                        ```

                     2. 或者是通过builder来获得正确的context

               7. 布局类widget

                  1. widget节点分三类
      
                     1. leaf：没有子节点，Text, Image这种
                     2. SingleChild： 单个的子节点，用child表示，如ConstrainedBox，DecoratedBox
                     3. MultiChild：复数的子节点，children后面接的是个数组
                     4. Flutter里面，上述的widget都继承于有状态和无状态组件，然后再将这些widget作为参数，传到对应的renderObject，然后渲染成element展示；具体的渲染算法在renderObject里面
                     
                  2. 线性布局，Row和Column
                  
                     1. 两个继承自flex；
                  
                     2. 主轴和纵轴的问题：和web一样，具体看下面的例子
                  
                        ```dart
                        Column(
                          //测试Row对齐方式，排除Column默认居中对齐的干扰
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: <Widget>[
                            Row(
                              // 主轴居中
                              mainAxisAlignment: MainAxisAlignment.center,
                              children: <Widget>[
                                Text(" hello world "),
                                Text(" I am Jack "),
                              ],
                            ),
                            Row(
                                  // 限制了主轴大小，所以居中以长度为准
                            mainAxisSize: MainAxisSize.min,
                            mainAxisAlignment: MainAxisAlignment.center,
                            children: <Widget>[
                                    Text(" hello world "),
                             				Text(" I am Jack "),
                                  ],
                             ),
                            Row(
                        // 因为是右排序，所以主轴end在左边，和上一个一样结果
                              mainAxisAlignment: MainAxisAlignment.end,
                        textDirection: TextDirection.rtl,
                              children: <Widget>[
                         Text(" hello world "),
                                Text(" I am Jack "),
                        ],
                            ),
                         Row(
                              // 因为是up，所以start对齐的时候是根据底部了
                        crossAxisAlignment: CrossAxisAlignment.start,  
                              verticalDirection: VerticalDirection.up,
                        children: <Widget>[
                                Text(" hello world ", style: TextStyle(fontSize: 30.0),),
                         Text(" I am Jack "),
                              ],
                         ),
                          ],
                        );
                        ```
                  
                     3. 注意column和row里面再嵌套的时候，里面设置成max，展现的也是实际长度；
                  
                     4. Flex布局，和web差不多，直接看代码
                  
                     ```dart
                     class FlexLayoutTestRoute extends StatelessWidget {
                       @override
                       Widget build(BuildContext context) {
                         return Column(
                           children: <Widget>[
                             //Flex的两个子widget按1：2来占据水平空间  
                             Flex(
                               direction: Axis.horizontal,
                               children: <Widget>[
                                 // 表示扩大，没有的话就不按照flex
                                 Expanded(
                                   flex: 1,
                                   child: Container(
                                     height: 30.0,
                                     color: Colors.red,
                                   ),
                                 ),
                                 Expanded(
                                   flex: 2,
                                   child: Container(
                                     height: 30.0,
                                     color: Colors.green,
                                   ),
                                 ),
                               ],
                             ),
                             Padding(
                               padding: const EdgeInsets.only(top: 20.0),
                               child: SizedBox(
                                 // 指定了垂直的高度
                                 height: 100.0,
                                 //Flex的三个子widget，在垂直方向按2：1：1来占用100像素的空间  
                                 child: Flex(
                                   direction: Axis.vertical,
                                   children: <Widget>[
                                     Expanded(
                                       flex: 2,
                                       child: Container(
                                         height: 30.0,
                                         color: Colors.red,
                                       ),
                                     ),
                                     Spacer(
                                       flex: 1,
                                     ),
                                     Expanded(
                                       flex: 1,
                                       child: Container(
                                         height: 30.0,
                                         color: Colors.green,
                                       ),
                                     ),
                                   ],
                                 ),
                               ),
                             ),
                           ],
                         );
                       }
                     }
                     ```
                  
                     1. Wrap，流式布局，即超出部分会自动换行的布局
                  
                     1. 代码如下，感觉上和栅格差不多，结果是一行三个，第二行内容居中
                  
                        ```dart
                        Wrap(
                          spacing: 8.0, // 主轴(水平)方向间距
                          runSpacing: 4.0, // 纵轴（垂直）方向间距
                          alignment: WrapAlignment.center, //沿主轴方向居中
                          children: <Widget>[
                            new Chip(
                              avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('A')),
                              label: new Text('Hamilton'),
                            ),
                            new Chip(
                              avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('M')),
                              label: new Text('Lafayette'),
                            ),
                            new Chip(
                              avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('H')),
                              label: new Text('Mulligan'),
                            ),
                            new Chip(
                              avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('J')),
                              label: new Text('Laurens'),
                            ),
                          ],
                        )
                        ```
                  
                     1. Flow布局，同上面一样，但是要自己计算子节点的位置然后控制布局位置
                  
                     2. Stack布局
                  
                        1. 还是直接上代码
                  
                           ```dart
                           //通过ConstrainedBox来确保Stack占满屏幕
                           ConstrainedBox(
                             constraints: BoxConstraints.expand(),
                             child: Stack(
                               alignment:Alignment.center , //指定未定位或部分定位widget的对齐方式
                               fit: StackFit.expand, //未定位widget占满Stack整个空间
                               children: <Widget>[
                                 // 顺序有关系，因为是第一个且之后都是绝对布局，所以不会挡住
                                 // 当container下调之后，本身又不是绝对布局，会产生覆盖的；
                                 Container(child: Text("Hello world",style: TextStyle(color: Colors.white)),
                                   color: Colors.red,
                                 ),
                                 Positioned(
                                   left: 18.0,
                                   child: Text("I am Jack"),
                                 ),
                                 Positioned(
                                   top: 18.0,
                                   child: Text("Your friend"),
                                 )        
                               ],
                             ),
                           );
                           ```
                  
                     3. 容器类widget：本身类似于border，里面是child，而布局类是children，一般用于添加样式修饰，变换或者限制大小等
                  
                        1. Padding使用，代码如下
                  
                           ```dart
                           class PaddingTestRoute extends StatelessWidget {
                             @override
                             Widget build(BuildContext context) {
                               return Padding(
                                 //上下左右各添加16像素补白
                                 padding: EdgeInsets.all(16.0),
                                 child: Column(
                                   //显式指定对齐方式为左对齐，排除对齐干扰
                                   crossAxisAlignment: CrossAxisAlignment.start,
                                   children: <Widget>[
                                     Padding(
                                       //左边添加8像素补白
                                       padding: const EdgeInsets.only(left: 8.0),
                                       child: Text("Hello world"),
                                     ),
                                     Padding(
                                       //上下各添加8像素补白
                                       padding: const EdgeInsets.symmetric(vertical: 8.0),
                                       child: Text("I am Jack"),
                                     ),
                                     Padding(
                                       // 分别指定四个方向的补白
                                       padding: const EdgeInsets.fromLTRB(20.0,.0,20.0,20.0),
                                       child: Text("Your friend"),
                                     )
                                   ],
                                 ),
                               );
                             }
                           }
                           
                           ```
                  
                        2. ConstrainBox和sizeBox
                  
                           1. 直接看代码
                  
                              ```dart
                              Widget redBox=DecoratedBox(
                                decoration: BoxDecoration(color: Colors.red),
                              );
                              ConstrainedBox(
                                constraints: BoxConstraints(
                                  // maxWidth指定最大宽度
                                  minWidth: double.infinity, //宽度尽可能大
                                  minHeight: 50.0 //最小高度为50像素
                                ),
                                child: Container(
                                  	// 高度没有用，因为限制了最小高度
                                    height: 5.0, 
                                    child: redBox 
                                ),
                              )
                              ```
                  
                           2. SizeBox则是直接指定大小
                  
                              ```dart
                              SizedBox(
                                width: 80.0,
                                height: 80.0,
                                child: redBox
                              )
                              ```
                  
                           3. 多重限制的时候，min取复数中大的，max取小的，这样就没有冲突
                  
                           4. 去除父组件的限制，下面就是因为appBar的actions本身有大小限制，所以使用UnconstrainedBox去除父组件的限制
                  
                              ```dart
                              AppBar(
                                title: Text(title),
                                actions: <Widget>[
                                    UnconstrainedBox(
                                          child: SizedBox(
                                            width: 20,
                                            height: 20,
                                            child: CircularProgressIndicator(
                                              strokeWidth: 3,
                                              valueColor: AlwaysStoppedAnimation(Colors.white70),
                                            ),
                                        ),
                                    )
                                ],
                              )
                              
                              ```
                  
                        3. DecoratedBox则是进行样式的修改
                  
                           1. 样式使用如下
                  
                              ```dart
                              DecoratedBox(
                                	// 直接使用BoxDecoration，本身是上面box的子类
                                  decoration: BoxDecoration(
                                    gradient: LinearGradient(colors:[Colors.red,Colors.orange[700]]), //背景渐变
                                    borderRadius: BorderRadius.circular(3.0), //3像素圆角
                                    boxShadow: [ //阴影
                                      BoxShadow(
                                          color:Colors.black54,
                                          offset: Offset(2.0,2.0),
                                          blurRadius: 4.0
                                      )
                                    ]
                                  ),
                                child: Padding(padding: EdgeInsets.symmetric(horizontal: 80.0, vertical: 18.0),
                                  child: Text("Login", style: TextStyle(color: Colors.white),),
                                )
                              )
                              ```
                  
                        4. 变换，transform和rotate，例子如下
                  
                           ```dart
                           Row(
                             mainAxisAlignment: MainAxisAlignment.center,
                             children: <Widget>[
                               DecoratedBox(
                                 decoration:BoxDecoration(color: Colors.red),
                                 // 在绘制阶段，即先定好后才变形
                                 child: Transform.scale(
                                     scale: 1.5,
                                     child: Text("Hello world")
                                 )
                               ),
                               Text("你好", style: TextStyle(color: Colors.green, fontSize: 18.0),)
                             ],
                           )
                           // 在layout层面构建，所以会因为变形而适应  
                           Row(
                             mainAxisAlignment: MainAxisAlignment.center,
                             children: <Widget>[
                               DecoratedBox(
                                 decoration: BoxDecoration(color: Colors.red),
                                 //将Transform.rotate换成RotatedBox  
                                 child: RotatedBox(
                                   quarterTurns: 1, //旋转90度(1/4圈)
                                   child: Text("Hello world"),
                                 ),
                               ),
                               Text("你好", style: TextStyle(color: Colors.green, fontSize: 18.0),)
                             ],
                           ),
                           ```
                  
                        5. Container，相当于上面的整合，直接看代码
                  
                           ```dart
                           Container(
                             margin: EdgeInsets.only(top: 50.0, left: 120.0), //容器外补白
                             constraints: BoxConstraints.tightFor(width: 200.0, height: 150.0), //卡片大小，或者使用width, height限制
                             decoration: BoxDecoration(//背景装饰
                                 gradient: RadialGradient( //背景径向渐变
                                     colors: [Colors.red, Colors.orange],
                                     center: Alignment.topLeft,
                                     radius: .98
                                 ),
                                 boxShadow: [ //卡片阴影
                                   BoxShadow(
                                       color: Colors.black54,
                                       offset: Offset(2.0, 2.0),
                                       blurRadius: 4.0
                                   )
                                 ]
                             ),
                             transform: Matrix4.rotationZ(.2), //卡片倾斜变换
                             alignment: Alignment.center, //卡片内文字居中
                             color:Colors.orange, // 这里是背景色
                             child: Text( //卡片文字
                               "5.20", style: TextStyle(color: Colors.white, fontSize: 40.0),
                             ),
                           );
                           ```
                  
                        6. Scaffold，TabBar和底部导航；依旧还是直接上代码
                  
                        ```dart
                        static GlobalKey<ScaffoldState> _globalKey= new GlobalKey();
                        class ScaffoldRoute extends StatefulWidget {
                          @override
                          _ScaffoldRouteState createState() => _ScaffoldRouteState();
                        }
                        
                        class _ScaffoldRouteState extends State<ScaffoldRoute> {
                          int _selectedIndex = 1;
                        
                          @override
                          Widget build(BuildContext context) {
                            // 导航栏
                            return Scaffold(
                              key: _globalKey //设置key，之后方便方法调用
                              appBar: AppBar( //导航栏
                                title: Text("App Name"), 
                                actions: <Widget>[ //导航栏右侧菜单
                                  IconButton(icon: Icon(Icons.share), onPressed: () {}),
                                ],
                              ),
                              // 可以通过重写leading来更改，本身和下面的drawer是一个东西
                              leading: Builder(builder: (context) {
                                return IconButton(
                                  icon: Icon(Icons.dashboard, color: Colors.white), //自定义图标
                                  onPressed: () {
                                    // 打开抽屉菜单  
                                    Scaffold.of(context).openDrawer(); 
                                  },
                                );
                              }),
                              drawer: new MyDrawer(), //抽屉，默认的左上角内容
                              bottomNavigationBar: BottomNavigationBar( // 底部导航
                                items: <BottomNavigationBarItem>[
                                  BottomNavigationBarItem(icon: Icon(Icons.home), title: Text('Home')),
                                  BottomNavigationBarItem(icon: Icon(Icons.business), title: Text('Business')),
                                  BottomNavigationBarItem(icon: Icon(Icons.school), title: Text('School')),
                                ],
                                currentIndex: _selectedIndex,
                                fixedColor: Colors.blue,
                                onTap: _onItemTapped,
                              ),
                              floatingActionButton: FloatingActionButton( //悬浮按钮
                                  child: Icon(Icons.add),
                                  onPressed:_onAdd
                              ),
                            );
                          }
                          void _onItemTapped(int index) {
                            setState(() {
                              _selectedIndex = index;
                            });
                          }
                          void _onAdd(){
                          }
                        }
                        ```
                  
                     4. appBar下面的行级菜单
                  
                        ```dart
                        class _ScaffoldRouteState extends State<ScaffoldRoute>
                            with SingleTickerProviderStateMixin {
                        
                          TabController _tabController; //需要定义一个Controller
                          List tabs = ["新闻", "历史", "图片"];
                        
                          @override
                          void initState() {
                            super.initState();
                            // 创建Controller  
                            _tabController = TabController(length: tabs.length, vsync: this);
                          }
                        
                          @override
                          Widget build(BuildContext context) {
                            return Scaffold(
                              appBar: AppBar(
                                ... //省略无关代码
                                bottom: TabBar(   //生成Tab菜单
                                  controller: _tabController,
                                  // tab可以自定义设置的
                                  tabs: tabs.map((e) => Tab(text: e)).toList()
                                ),
                              ),
                              // 实现了下面内容和上面东西联动
                              body: TabBarView(
                                controller: _tabController,
                                children: tabs.map((e) { //创建3个Tab页
                                  return Container(
                                    alignment: Alignment.center,
                                    child: Text(e, textScaleFactor: 5),
                                  );
                                }).toList(),
                              ),
                        
                          }
                        ```
                  
                     5. Drawer，抽屉即左边划出的内容
                  
                     6. Cupertino widget，苹果的东西，大概的代码如下所示
                  
                        ```dart
                        //导入cupertino widget库
                        import 'package:flutter/cupertino.dart';
                        
                        class CupertinoTestRoute extends StatelessWidget {
                          @override
                          Widget build(BuildContext context) {
                            return CupertinoPageScaffold(
                              navigationBar: CupertinoNavigationBar(
                                middle: Text("Cupertino Demo"),
                              ),
                              child: Center(
                                child: CupertinoButton(
                                    color: CupertinoColors.activeBlue,
                                    child: Text("Press"),
                                    onPressed: () {}
                                ),
                              ),
                            );
                          }
                        }
                        
                        ```
                  
                        
         
