## 动画续集

1. Hero动画，指路由切换的时候有共享的widget，此时的动画就是hero动画

   1. 使用的方式如下

      ```dart
      // 路由A
      class HeroAnimationRoute extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
          return Container(
            alignment: Alignment.topCenter,
            child: InkWell(
              // 共享的目标位置，通过tag标记
              child: Hero(
                tag: "avatar", //唯一标记，前后两个路由页Hero的tag必须相同
                child: ClipOval(
                  child: Image.asset("images/avatar.png",
                    width: 50.0,
                  ),
                ),
              ),
              onTap: () {
                //打开B路由  
                Navigator.push(context, PageRouteBuilder(
                    pageBuilder: (BuildContext context, Animation animation,
                        Animation secondaryAnimation) {
                      return new FadeTransition(
                        opacity: animation,
                        child: Scaffold(
                          title: Text("原图"),
                          body: HeroAnimationRouteB(),
                        ),
                      );
                    })
                );
              },
            ),
          );
        }
      }
      // 展现的路由
      class HeroAnimationRouteB extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
          return Center(
            child: Hero(
                tag: "avatar", //唯一标记，前后两个路由页Hero的tag必须相同
                child: Image.asset("images/avatar.png"),
            ),
          );
        }
      }
      ```

2. 交错动画，指的是复数的动画放在一起，要点就是共有一个controller

   1. 代码如下

      ```dart
      class StaggerAnimation extends StatelessWidget {
        StaggerAnimation({ Key key, this.controller }): super(key: key){
          //高度动画
          height = Tween<double>(
            begin:.0 ,
            end: 300.0,
          ).animate(
            CurvedAnimation(
              parent: controller,
              curve: Interval(
                0.0, 0.6, //间隔，前60%的动画时间
                curve: Curves.ease,
              ),
            ),
          );
      		// 颜色
          color = ColorTween(
            begin:Colors.green ,
            end:Colors.red,
          ).animate(
            CurvedAnimation(
              parent: controller,
              curve: Interval(
                0.0, 0.6,//间隔，前60%的动画时间
                curve: Curves.ease,
              ),
            ),
          );
      		// 控制间隔
          padding = Tween<EdgeInsets>(
            begin:EdgeInsets.only(left: .0),
            end:EdgeInsets.only(left: 100.0),
          ).animate(
            CurvedAnimation(
              parent: controller,
              curve: Interval(
                0.6, 1.0, //间隔，后40%的动画时间
                curve: Curves.ease,
              ),
            ),
          );
        }
      
        final Animation<double> controller;
        Animation<double> height;
        Animation<EdgeInsets> padding;
        Animation<Color> color;
      
        Widget _buildAnimation(BuildContext context, Widget child) {
          return Container(
            alignment: Alignment.bottomCenter,
            // 下面分别绑定了padding, color and height的值
            padding:padding.value ,
            child: Container(
              color: color.value,
              width: 50.0,
              height: height.value,
            ),
          );
        }
      
        @override
        Widget build(BuildContext context) {
          return AnimatedBuilder(
            builder: _buildAnimation,
            // 绑定controller
            animation: controller,
          );
        }
      }
      ```

   2. 下面是启动动画的路由

      ```dart
      class StaggerDemo extends StatefulWidget {
        @override
        _StaggerDemoState createState() => _StaggerDemoState();
      }
      
      class _StaggerDemoState extends State<StaggerDemo> with TickerProviderStateMixin {
        AnimationController _controller;
      
        @override
        void initState() {
          super.initState();
      		// 声明集中的controller
          _controller = AnimationController(
              duration: const Duration(milliseconds: 2000),
              vsync: this
          );
        }
      
        Future<Null> _playAnimation() async {
          try {
            //先正向执行动画
            await _controller.forward().orCancel;
            //再反向执行动画
            await _controller.reverse().orCancel;
          } on TickerCanceled {
            // the animation got canceled, probably because we were disposed
          }
        }
      
        @override
        Widget build(BuildContext context) {
          return  GestureDetector(
            behavior: HitTestBehavior.opaque,
            onTap: () {
              _playAnimation();
            },
            child: Center(
              child: Container(
                width: 300.0,
                height: 300.0,
                decoration: BoxDecoration(
                  color: Colors.black.withOpacity(0.1),
                  border: Border.all(
                    color:  Colors.black.withOpacity(0.5),
                  ),
                ),
                //调用我们定义的交错动画Widget，将controller传进去
                child: StaggerAnimation(
                    controller: _controller
                ),
              ),
            ),
          );
        }
      }
      ```

---

1. 自定义Widget，没有看

2. 文件操作和网络请求

   1. 文件操作

      1. APP目录有三类：临时，文档，外部存储

      2. 事例代码如下

         ```dart
         import 'dart:io';
         import 'dart:async';
         import 'package:flutter/material.dart';
         import 'package:path_provider/path_provider.dart';
         
         class FileOperationRoute extends StatefulWidget {
           FileOperationRoute({Key key}) : super(key: key);
         
           @override
           _FileOperationRouteState createState() => new _FileOperationRouteState();
         }
         
         class _FileOperationRouteState extends State<FileOperationRoute> {
           int _counter;
         
           @override
           void initState() {
             super.initState();
             //从文件读取点击次数
             _readCounter().then((int value) {
               setState(() {
                 _counter = value;
               });
             });
           }
         	// 操作都是同步执行；
           Future<File> _getLocalFile() async {
             // 获取应用目录
             String dir = (await getApplicationDocumentsDirectory()).path;
             return new File('$dir/counter.txt');
           }
         
           Future<int> _readCounter() async {
             try {
               File file = await _getLocalFile();
               // 读取点击次数（以字符串）
               String contents = await file.readAsString();
               return int.parse(contents);
             } on FileSystemException {
               return 0;
             }
           }
         
           Future<Null> _incrementCounter() async {
             setState(() {
               _counter++;
             });
             // 将点击次数以字符串类型写到文件中
             await (await _getLocalFile()).writeAsString('$_counter');
           }
         
           @override
           Widget build(BuildContext context) {
             return new Scaffold(
               appBar: new AppBar(title: new Text('文件操作')),
               body: new Center(
                 child: new Text('点击了 $_counter 次'),
               ),
               floatingActionButton: new FloatingActionButton(
                 onPressed: _incrementCounter,
                 tooltip: 'Increment',
                 child: new Icon(Icons.add),
               ),
             );
           }
         }
         ```

   2. HTTP请求，比较麻烦，直接使用Dio库

   3. Dio库[使用方法](https://github.com/flutterchina/dio/blob/master/README-ZH.md)

      1. 代码事例如下

         ```dart
         // get
         Response response;
         response=await dio.get("/test?id=12&name=wendu")
         print(response.data.toString());
         // post
         response=await dio.post("/test",data:{"id":12,"name":"wendu"})
         // 并发
         response= await Future.wait([dio.post("/info"),dio.get("/token")]);
         // 下载文件
         response=await dio.download("https://www.google.com/",_savePath);
         // FormData进行文件上传
         FormData formData = new FormData.from({
            "name": "wendux",
            "age": 25,
            "file1": new UploadFileInfo(new File("./upload.txt"), "upload1.txt"),
            "file2": new UploadFileInfo(new File("./upload.txt"), "upload2.txt"),
              // 支持文件数组上传
            "files": [
               new UploadFileInfo(new File("./example/upload.txt"), "upload.txt"),
               new UploadFileInfo(new File("./example/upload.txt"), "upload.txt")
             ]
         });
         response = await dio.post("/info", data: formData)
         ```

      2. 分段传输

         1. 测试是否能分段传输：`curl -H "Range: bytes=0-10" url`，然后看返回的头，状态码是206 Partial Content，有content-range则表示能分段
         2. [具体代码](https://book.flutterchina.club/chapter10/download_with_chunks.html) 
         3. 不一定提高速度，但是可以分段处理，保证了断点传输

      3. webSocket

         1. http中的keep-alive用于用户重复请求资源的时候，链接复用，不能用于实时通信
         2. 使用http发送特殊的链接，对方支持webSocket的时候，使用构建好的tcp链接进行长时间传输；

      4. JSON构建，使用的是json.decode的方式，将对应的string字段变成了json格式；带来的问题就是打错key的字段没有提醒，所以推荐modal化，但是flutter本身没有modal化的库

---

## Flutter核心原理

1. UI系统

   1. 1600万色指的是每一个像素点可以产生1600万色，由RGB构成，即2的24次方，所以每个值是8 bits，
   2. 将操作系统API封装成对象，然后给出规则描述UI；Flutter底层使用的是openGL进行封装；

2. Element

   1. 整个流程是：根据widget生成element，创建相应的renderObject关联到element.renderObject上，这样就能获得element对应的由widget构建出的对象，最后通过renderObject完成布局和绘制，且复数组成渲染树，完成页面

   2. 生命周期如下

      1. Framework 调用`Widget.createElement` 创建一个Element实例，记为`element`
      2. Framework 调用 `element.mount(parentElement,newSlot)` ，mount方法中首先调用`elment`所对应Widget的`createRenderObject`方法创建与`element`相关联的RenderObject对象，然后调用`element.attachRenderObject`方法将`element.renderObject`添加到渲染树中插槽指定的位置（这一步不是必须的，一般发生在Element树结构发生变化时才需要重新attach）。插入到渲染树后的`element`就处于“active”状态，处于“active”状态后就可以显示在屏幕上了（可以隐藏）。
      3. 当`element`父Widget的配置数据改变时，为了进行Element复用，Framework在决定重新创建Element前会先尝试复用相同位置旧的element：调用Element对应Widget的`canUpdate()`方法，如果返回`true`，则复用旧Element，旧的Element会使用新的Widget配置数据更新，反之则会创建一个新的Element，不会复用。`Widget.canUpdate()`主要是判断`newWidget`与`oldWidget`的`runtimeType`和`key`是否同时相等，如果同时相等就返回`true`，否则就会返回`false`。根据这个原理，当我们需要强制更新一个Widget时，可以通过指定不同的Key来禁止复用。
      4. 当有父Widget的配置数据改变时，同时其`State.build`返回的Widget结构与之前不同，此时就需要重新构建对应的Element树。和前面的更新状态一样
      5. 当有祖先Element决定要移除`element` 时（如Widget树结构发生了变化，导致`element`对应的Widget被移除），这时该祖先Element就会调用`deactivateChild` 方法来移除它，移除后`element.renderObject`也会被从渲染树中移除，然后Framework会调用`element.deactivate` 方法，这时`element`状态变为“inactive”状态。
      6. “inactive”态的element将不会再显示到屏幕。为了避免在一次动画执行过程中反复创建、移除某个特定element，“inactive”态的element在当前动画最后一帧结束前都会保留，如果在动画执行结束后它还未能重新变成”active“状态，Framework就会调用其`unmount`方法将其彻底移除，这时element的状态为`defunct`,它将永远不会再被插入到树中。
      7. 如果`element`要重新插入到Element树的其它位置，如`element`或`element`的祖先拥有一个GlobalKey（用于全局复用元素），那么Framework会先将element从现有位置移除，然后再调用其`activate`方法，并将其`renderObject`重新attach到渲染树。

   3. Global key进行全局的复用

   4. Build里面传参的BulidContext是widget对应的element，可以用如下的使用方法

      ````dart
      Theme.of(context) //获取主题
      Navigator.push(context, route) //入栈新路由
      Localizations.of(context, type) //获取Local
      context.size //获取上下文大小
      context.findRenderObject() //查找当前或最近的一个祖先RenderObject
      ````

3. RenderObject

---

## 补充内容

1. 跨平台开发技术
   1. H5+原生
      1. 微信小程序，本身基于webView；JsBridge进行js和原生之间的沟通，比如蓝牙，网络，手机型号等原生的东西必须要调用；
      2. 对于动画或者复杂的用户界面不好用
   2. JS+原生
      1. React，响应式编程；native就是将布局信息给原生，然后原生渲染
      2. Weex，语法和上面不同，本身使用vue
      3. 快应用，支持vue
      4. 缺点：通信频繁导致的性能差；版本更新带来的控件落后
   3. 自绘UI+原生















































