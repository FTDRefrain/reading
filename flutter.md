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
                     )
                     ```

                  4. 同样netWorkImage也是上面的方式

            7. Cupertino widget，苹果的东西，大概的代码如下所示

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

            8. 

         

         

         

         

         

         

         

         

         

         

         

         

         

         

         

         

         











































































