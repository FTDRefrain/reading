## 接之前的容器类布局的Drawer

1. Scaffold内容

   1. Drawer，就是app左边划出的东西，类似隐藏的左边栏

      1. 看一下代码例子

         ```dart
         class MyDrawer extends StatelessWidget {
           const MyDrawer({
             Key key,
           }) : super(key: key);
         
           @override
           Widget build(BuildContext context) {
             return Drawer(
               child: MediaQuery.removePadding(
                 context: context,
                 // DrawerHeader consumes top MediaQuery padding.
                 removeTop: true,
                 child: Column(
                   crossAxisAlignment: CrossAxisAlignment.start,
                   children: <Widget>[
                     Padding(
                       padding: const EdgeInsets.only(top: 38.0),
                       child: Row(
                         children: <Widget>[
                           Padding(
                             padding: const EdgeInsets.symmetric(horizontal: 16.0),
                             child: ClipOval(
                               // 头像的位置
                               child: Image.asset(
                                 "imgs/avatar.png",
                                 width: 80,
                               ),
                             ),
                           ),
                           Text(
                             "Wendux",
                             style: TextStyle(fontWeight: FontWeight.bold),
                           )
                         ],
                       ),
                     ),
                     Expanded(
                       // listView本身是一个可滚动widget
                       child: ListView(
                         children: <Widget>[
                           ListTile(
                             leading: const Icon(Icons.add),
                             title: const Text('Add account'),
                           ),
                           ListTile(
                             leading: const Icon(Icons.settings),
                             title: const Text('Manage accounts'),
                           ),
                         ],
                       ),
                     ),
                   ],
                 ),
               ),
             );
           }
         }
         ```

   2. FloatingActionButton：本身是浮动按钮，用于快速进入

   3. bottomNavigationBar底部导航栏，比较有意思的一个打洞

      ```dart
      bottomNavigationBar: BottomAppBar(
        color: Colors.white,
        shape: CircularNotchedRectangle(), // 底部导航栏打一个圆形的洞
        child: Row(
          children: [
            IconButton(icon: Icon(Icons.home)),
            SizedBox(), //中间位置空出
            IconButton(icon: Icon(Icons.business)),
          ],
          mainAxisAlignment: MainAxisAlignment.spaceAround, //均分底部导航栏横向空间
        ),
      )
      // 根据floatBtn的位置进行打洞适配，为了美观预留了中间位置
      floatingActionButtonLocation:FloatingActionButtonLocation.centerDocked,
      ```

2. 可滚动widget

   1. 因为flutter会在内容超出viewport的时候提示错误

   2. 简单的例子

      ```dart
      class SingleChildScrollViewTestRoute extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
          String str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
          return Scrollbar(
            child: SingleChildScrollView(
              padding: EdgeInsets.all(16.0),
              child: Center(
                child: Column( 
                  //动态创建一个List<Widget>  
                  children: str.split("") 
                      //每一个字母都用一个Text显示,字体为原来的两倍
                      .map((c) => Text(c, textScaleFactor: 2.0,)) 
                      .toList(),
                ),
              ),
            ),
          );
        }
      }
      ```

   3. ListView实现无限下拉列表功能

      ```dart
      class InfiniteListView extends StatefulWidget {
        @override
        _InfiniteListViewState createState() => new _InfiniteListViewState();
      }
      
      class _InfiniteListViewState extends State<InfiniteListView> {
        static const loadingTag = "##loading##"; //表尾标记
        var _words = <String>[loadingTag];
      
        @override
        void initState() {
          // 模拟的获取内容函数
          _retrieveData();
        }
      
        @override
        Widget build(BuildContext context) {
          return ListView.separated(
            // 总的index数量
            itemCount: _words.length,
            // 每个index时的函数
            itemBuilder: (context, index) {
              //如果到了表尾，自己做的标记，每次都是往这个前面插内容
              if (_words[index] == loadingTag) {
                //不足100条，继续获取数据
                if (_words.length - 1 < 100) {
                  //获取数据，发送请求和setState都在这里
                  _retrieveData();
                  //加载时显示loading
                  return Container(
                    padding: const EdgeInsets.all(16.0),
                    alignment: Alignment.center,
                    child: SizedBox(
                        width: 24.0,
                        height: 24.0,
                        child: CircularProgressIndicator(strokeWidth: 2.0)
                    ),
                  );
                } else {
                  //已经加载了100条数据，不再获取数据。
                  return Container(
                      alignment: Alignment.center,
                      padding: EdgeInsets.all(16.0),
                      child: Text("没有更多了", style: TextStyle(color: Colors.grey),)
                  );
                }
              }
              //显示单词列表项，写在builder里面
              return ListTile(title: Text(_words[index]));
            },
            // 分割线
            separatorBuilder: (context, index) => Divider(height: .0),
          );
        }
      
        void _retrieveData() {
          Future.delayed(Duration(seconds: 2)).then((e) {
            _words.insertAll(_words.length - 1,
                //每次生成20个单词
                generateWordPairs().take(20).map((e) => e.asPascalCase).toList()
            );
            setState(() {
              //重新构建列表
            });
          });
        }
      
      }
      ```

   4. 添加自定义表头

      ```dart
      @override
      Widget build(BuildContext context) {
        return Column(children: <Widget>[
          ListTile(title:Text("商品列表")),
          // 不expanded的话，会因为不确定高度而出现问题
          Expanded(
            child: ListView.builder(itemBuilder: (BuildContext context, int index) {
              return ListTile(title: Text("$index"));
            }),
          ),
        ]);
      }
      
      ```

   5. gridView，栅格式的listView

      ```dart
      class InfiniteGridView extends StatefulWidget {
        @override
        _InfiniteGridViewState createState() => new _InfiniteGridViewState();
      }
      
      class _InfiniteGridViewState extends State<InfiniteGridView> {
      
        List<IconData> _icons = []; //保存Icon数据
      
        @override
        void initState() {
          // 初始化数据  
          _retrieveIcons();
        }
      
        @override
        Widget build(BuildContext context) {
          return GridView.builder(
              gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
                  crossAxisCount: 3, //每行三列
                  childAspectRatio: 1.0 //显示区域宽高相等
              ),
              itemCount: _icons.length,
              itemBuilder: (context, index) {
                //如果显示到最后一个并且Icon总数小于200时继续获取数据
                if (index == _icons.length - 1 && _icons.length < 200) {
                  _retrieveIcons();
                }
                return Icon(_icons[index]);
              }
          );
        }
      
        //模拟异步获取数据
        void _retrieveIcons() {
          Future.delayed(Duration(milliseconds: 200)).then((e) {
            setState(() {
              _icons.addAll([
                Icons.ac_unit,
                Icons.airport_shuttle,
                Icons.all_inclusive,
                Icons.beach_access, Icons.cake,
                Icons.free_breakfast
              ]);
            });
          });
        }
      }
      // flutter_staggered_grid_view，一个可以实现不同宽高交错布局的包
      ```

   6. CustomScrollView：用来粘合不同滚动区域的，例如上面是list下面是grid，是通过自己的sliver链接起来的，代码如下

      ```dart
      import 'package:flutter/material.dart';
      
      class CustomScrollViewTestRoute extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
          //因为本路由没有使用Scaffold，为了让子级Widget(如Text)使用
          //Material Design 默认的样式风格,我们使用Material作为本路由的根。
          return Material(
            child: CustomScrollView(
              slivers: <Widget>[
                //AppBar，包含一个导航栏
                SliverAppBar(
                  pinned: true,
                  expandedHeight: 250.0,
                  flexibleSpace: FlexibleSpaceBar(
                    title: const Text('Demo'),
                    background: Image.asset(
                      "./images/avatar.png", fit: BoxFit.cover,),
                  ),
                ),
      
                SliverPadding(
                  padding: const EdgeInsets.all(8.0),
                  sliver: new SliverGrid( //Grid
                    gridDelegate: new SliverGridDelegateWithFixedCrossAxisCount(
                      crossAxisCount: 2, //Grid按两列显示
                      mainAxisSpacing: 10.0,
                      crossAxisSpacing: 10.0,
                      childAspectRatio: 4.0,
                    ),
                    delegate: new SliverChildBuilderDelegate(
                          (BuildContext context, int index) {
                        //创建子widget      
                        return new Container(
                          alignment: Alignment.center,
                          color: Colors.cyan[100 * (index % 9)],
                          child: new Text('grid item $index'),
                        );
                      },
                      childCount: 20,
                    ),
                  ),
                ),
                //List
                new SliverFixedExtentList(
                  itemExtent: 50.0,
                  delegate: new SliverChildBuilderDelegate(
                          (BuildContext context, int index) {
                        //创建列表项      
                        return new Container(
                          alignment: Alignment.center,
                          color: Colors.lightBlue[100 * (index % 9)],
                          child: new Text('list item $index'),
                        );
                      },
                      childCount: 50 //50个列表项
                  ),
                ),
              ],
            ),
          );
        }
      }
      ```

   7. 滚动监听和控制

      ```dart
      class ScrollControllerTestRoute extends StatefulWidget {
        @override
        ScrollControllerTestRouteState createState() {
          return new ScrollControllerTestRouteState();
        }
      }
      
      class ScrollControllerTestRouteState extends State<ScrollControllerTestRoute> {
        ScrollController _controller = new ScrollController();
        bool showToTopBtn = false; //是否显示“返回到顶部”按钮
      
        @override
        void initState() {
          //监听滚动事件，打印滚动位置
          _controller.addListener(() {
            print(_controller.offset); //打印滚动位置
            if (_controller.offset < 1000 && showToTopBtn) {
              setState(() {
                showToTopBtn = false;
              });
            } else if (_controller.offset >= 1000 && showToTopBtn == false) {
              setState(() {
                showToTopBtn = true;
              });
            }
          });
        }
      
        @override
        void dispose() {
          //为了避免内存泄露，需要调用_controller.dispose
          _controller.dispose();
          super.dispose();
        }
      
        @override
        Widget build(BuildContext context) {
          return Scaffold(
            appBar: AppBar(title: Text("滚动控制")),
            body: Scrollbar(
              child: ListView.builder(
                	// 显式指定key，可以拿到对应的offset了，用于一个路由一堆滚动的widget
                  key: PageStorageKey(1),
                  itemCount: 100,
                  itemExtent: 50.0, //列表项高度固定时，显式指定高度是一个好习惯(性能消耗小)
                  controller: _controller,
                	// 用来控制重新构建的时候是否使用之前的位置，false不记忆
                  keepScrollOffset:true,
                  itemBuilder: (context, index) {
                    return ListTile(title: Text("$index"),);
                  }
              ),
            ),
            floatingActionButton: !showToTopBtn ? null : FloatingActionButton(
                child: Icon(Icons.arrow_upward),
                onPressed: () {
                  //返回到顶部时执行动画
                  _controller.animateTo(.0,
                      duration: Duration(milliseconds: 200),
                      curve: Curves.ease
                  );
                }
            ),
          );
        }
      }
      ```

   8. 事件监听，采用的是冒泡的方式

      ```dart
      import 'package:flutter/material.dart';
      
      class ScrollNotificationTestRoute extends StatefulWidget {
        @override
        _ScrollNotificationTestRouteState createState() =>
            new _ScrollNotificationTestRouteState();
      }
      
      class _ScrollNotificationTestRouteState
          extends State<ScrollNotificationTestRoute> {
        String _progress = "0%"; //保存进度百分比
      
        @override
        Widget build(BuildContext context) {
          return Scrollbar( //进度条
            // 监听滚动通知
            child: NotificationListener<ScrollNotification>(
              // 冒泡执行的回调方法
              onNotification: (ScrollNotification notification) {
                double progress = notification.metrics.pixels /
                    notification.metrics.maxScrollExtent;
                //重新构建
                setState(() {
                  _progress = "${(progress * 100).toInt()}%";
                });
                print("BottomEdge: ${notification.metrics.extentAfter == 0}");
                //return true; //放开此行注释后，进度条将失效
              },
              child: Stack(
                alignment: Alignment.center,
                children: <Widget>[
                  ListView.builder(
                      itemCount: 100,
                      itemExtent: 50.0,
                      itemBuilder: (context, index) {
                        return ListTile(title: Text("$index"));
                      }
                  ),
                  CircleAvatar(  //显示进度百分比
                    radius: 30.0,
                    child: Text(_progress),
                    backgroundColor: Colors.black54,
                  )
                ],
              ),
            ),
          );
        }
      }
      ```

3. 功能型，不影响布局，用来存内容和实现功能

   1. willPopScope

      ```dart
      import 'package:flutter/material.dart';
      
      class WillPopScopeTestRoute extends StatefulWidget {
        @override
        WillPopScopeTestRouteState createState() {
          return new WillPopScopeTestRouteState();
        }
      }
      
      class WillPopScopeTestRouteState extends State<WillPopScopeTestRoute> {
        DateTime _lastPressedAt; //上次点击时间
      
        @override
        Widget build(BuildContext context) {
          return new WillPopScope(
              onWillPop: () async {
                if (_lastPressedAt == null ||
                    DateTime.now().difference(_lastPressedAt) > Duration(seconds: 1)) {
                  //两次点击间隔超过1秒则重新计时
                  _lastPressedAt = DateTime.now();
                  // 阻止点击事件
                  return false;
                }
                return true;
              },
              child: Container(
                alignment: Alignment.center,
                child: Text("1秒内连续按两次返回键退出"),
              )
          );
        }
      }
      ```

   2. InheritedWidget实现数据的传递和共享，类似context可以跨级传递数据

      ```dart
      class ShareDataWidget extends InheritedWidget {
        ShareDataWidget({
          @required this.data,
          Widget child
        }) :super(child: child);
      
        int data; //需要在子树中共享的数据，保存点击次数
      
        //定义一个便捷方法，方便子树中的widget获取共享数据  
        static ShareDataWidget of(BuildContext context) {
          return context.inheritFromWidgetOfExactType(ShareDataWidget);
        }
      
        //该回调决定当data发生变化时，是否通知子树中依赖data的Widget  
        @override
        bool updateShouldNotify(ShareDataWidget old) {
          //如果返回true，则子树中依赖(build函数中有调用)本widget
          //的子widget的`state.didChangeDependencies`会被调用
          return old.data != data;
        }
      }
      // 子组件使用
      class _TestWidget extends StatefulWidget {
        @override
        __TestWidgetState createState() => new __TestWidgetState();
      }
      
      class __TestWidgetState extends State<_TestWidget> {
        @override
        Widget build(BuildContext context) {
          //使用InheritedWidget中的共享数据，of方法是之前定义的获取内容的方式
          return Text(ShareDataWidget
              .of(context)
              .data
              .toString());
        }
      	// 一般用于依赖改变执行网络操作，因为重新build的原因，网络请求不适合放外面
        @override
        void didChangeDependencies() {
          super.didChangeDependencies();
          //父或祖先widget中的InheritedWidget改变(updateShouldNotify返回true)时会被调用。
          //如果build中没有依赖InheritedWidget，则此回调不会被调用。
          print("Dependencies change");
        }
      }
      // 使用
      class InheritedWidgetTestRoute extends StatefulWidget {
        @override
        _InheritedWidgetTestRouteState createState() => new _InheritedWidgetTestRouteState();
      }
      
      class _InheritedWidgetTestRouteState extends State<InheritedWidgetTestRoute> {
        int count = 0;
      
        @override
        Widget build(BuildContext context) {
          return  Center(
            child: ShareDataWidget( //使用ShareDataWidget
              // 接受内容变成数据初始值
              data: count,
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: <Widget>[
                  Padding(
                    padding: const EdgeInsets.only(bottom: 20.0),
                    // 数据传递且展示
                    child: _TestWidget(),//子widget中依赖ShareDataWidget
                  ),
                  RaisedButton(
                    child: Text("Increment"),
                    //每点击一次，将count自增，然后重新build,ShareDataWidget的data将被更新  
                    onPressed: () => setState(() => ++count),
                  )
                ],
              ),
            ),
          );
        }
      }
      ```

   3. Theme主题内容，路由换肤方式

      ```dart
      class ThemeTestRoute extends StatefulWidget {
        @override
        _ThemeTestRouteState createState() => new _ThemeTestRouteState();
      }
      
      class _ThemeTestRouteState extends State<ThemeTestRoute> {
        Color _themeColor = Colors.teal; //当前路由主题色
      
        @override
        Widget build(BuildContext context) {
          ThemeData themeData = Theme.of(context);
          return Theme(
            data: ThemeData(
                primarySwatch: _themeColor, //用于导航栏、FloatingActionButton的背景色等
                iconTheme: IconThemeData(color: _themeColor) //用于Icon颜色
            ),
            child: Scaffold(
              appBar: AppBar(title: Text("主题测试")),
              body: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: <Widget>[
                  //第一行Icon使用主题中的iconTheme
                  Row(
                      mainAxisAlignment: MainAxisAlignment.center,
                      children: <Widget>[
                        Icon(Icons.favorite),
                        Icon(Icons.airport_shuttle),
                        Text("  颜色跟随主题")
                      ]
                  ),
                  //为第二行Icon自定义颜色（固定为黑色)，子级覆盖父级
                  Theme(
                    data: themeData.copyWith(
                      iconTheme: themeData.iconTheme.copyWith(
                          color: Colors.black
                      ),
                    ),
                    child: Row(
                        mainAxisAlignment: MainAxisAlignment.center,
                        children: <Widget>[
                          Icon(Icons.favorite),
                          Icon(Icons.airport_shuttle),
                          Text("  颜色固定黑色")
                        ]
                    ),
                  ),
                ],
              ),
              floatingActionButton: FloatingActionButton(
                  onPressed: () =>  //切换主题
                      setState(() =>
                      _themeColor =
                      _themeColor == Colors.teal ? Colors.blue : Colors.teal
                      ),
                  child: Icon(Icons.palette)
              ),
            ),
          );
        }
      }
      ```

4. 事件处理和通知

   1. 两层内容，原始的pointer和手势

   2. pointer

   3. 手势识别

      1. 手势

         ```dart
         class GestureDetectorTestRoute extends StatefulWidget {
           @override
           _GestureDetectorTestRouteState createState() =>
               new _GestureDetectorTestRouteState();
         }
         
         class _GestureDetectorTestRouteState extends State<GestureDetectorTestRoute> {
           String _operation = "No Gesture detected!"; //保存事件名
           @override
           Widget build(BuildContext context) {
             return Center(
               // 包了一层
               child: GestureDetector(
                 child: Container(
                   alignment: Alignment.center,
                   color: Colors.blue,
                   width: 200.0, 
                   height: 100.0,
                   child: Text(_operation,
                     style: TextStyle(color: Colors.white),
                   ),
                 ),
                 onTap: () => updateText("Tap"),//点击
                 onDoubleTap: () => updateText("DoubleTap"), //双击，单击之后200ms检测
                 onLongPress: () => updateText("LongPress"), //长按
               ),
             );
           }
         
           void updateText(String text) {
             //更新显示的事件名
             setState(() {
               _operation = text;
             });
           }
         }
         ```

      2. 拖动和滑动，依旧还是手势，通过两个回调方法看移动的信息

         ```dart
         class _Drag extends StatefulWidget {
           @override
           _DragState createState() => new _DragState();
         }
         
         class _DragState extends State<_Drag> with SingleTickerProviderStateMixin {
           double _top = 0.0; //距顶部的偏移
           double _left = 0.0;//距左边的偏移
         
           @override
           Widget build(BuildContext context) {
             return Stack(
               children: <Widget>[
                 Positioned(
                   top: _top,
                   left: _left,
                   child: GestureDetector(
                     child: CircleAvatar(child: Text("A")),
                     //手指按下时会触发此回调
                     onPanDown: (DragDownDetails e) {
                       //打印手指按下的位置(相对于屏幕)
                       print("用户手指按下：${e.globalPosition}");
                     },
                     //手指滑动时会触发此回调
                     onPanUpdate: (DragUpdateDetails e) {
                       //用户手指滑动时，更新偏移，重新构建
                       setState(() {
                         _left += e.delta.dx;
                         _top += e.delta.dy;
                       });
                     },
                     onPanEnd: (DragEndDetails e){
                       //打印滑动结束时在x、y轴上的速度
                       print(e.velocity);
                     },
                     //垂直方向拖动事件，只能垂直移动
                     onVerticalDragUpdate: (DragUpdateDetails details) {
                       setState(() {
                         _top += details.delta.dy;
                       });
                     }
                   ),
                 )
               ],
             );
           }
         }
         ```

      3. 缩放效果

         ```dart
         class _ScaleTestRouteState extends State<_ScaleTestRoute> {
           double _width = 200.0; //通过修改图片宽度来达到缩放效果
         
           @override
           Widget build(BuildContext context) {
            return Center(
              child: GestureDetector(
                 //指定宽度，高度自适应
                 child: Image.asset("./images/sea.png", width: _width),
                	// 实现一个回调就行
                 onScaleUpdate: (ScaleUpdateDetails details) {
                   setState(() {
                     //缩放倍数在0.8到10倍之间
                     _width=200*details.scale.clamp(.8, 10.0);
                   });
                 },
               ),
            );
           }
         }
         ```

      4. 手势冲突和手势竞争

   4. 事件总线，即广播机制
   
      1. 代码如下，注意的是，首先确保单例，然后实现订阅发布，发布中注意反向遍历，取消了自身清除的影响，再用全局暴露出去；或者就是直接redux
   
         ```dart
         //订阅者回调签名
         typedef void EventCallback(arg);
         
         class EventBus {
           //私有构造函数
           EventBus._internal();
         
           //保存单例
           static EventBus _singleton = new EventBus._internal();
         
           //工厂构造函数
           factory EventBus()=> _singleton;
         
           //保存事件订阅者队列，key:事件名(id)，value: 对应事件的订阅者队列
           var _emap = new Map<Object, List<EventCallback>>();
         
           //添加订阅者
           void on(eventName, EventCallback f) {
             if (eventName == null || f == null) return;
             _emap[eventName] ??= new List<EventCallback>();
             _emap[eventName].add(f);
           }
         
           //移除订阅者
           void off(eventName, [EventCallback f]) {
             var list = _emap[eventName];
             if (eventName == null || list == null) return;
             if (f == null) {
               _emap[eventName] = null;
             } else {
               list.remove(f);
             }
           }
         
           //触发事件，事件触发后该事件所有订阅者会被调用
           void emit(eventName, [arg]) {
             var list = _emap[eventName];
             if (list == null) return;
             int len = list.length - 1;
             //反向遍历，防止在订阅者在回调中移除自身带来的下标错位 
             for (var i = len; i > -1; --i) {
               list[i](arg);
             }
           }
         }
         
         //定义一个top-level变量，页面引入该文件后可以直接使用bus
         var bus = new EventBus();
         ```
   
   5. 通知
   
      1. 不同于点击冒泡，通知冒泡可以被取消，代码如下
   
         ```dart
         NotificationListener(
           onNotification: (notification){
             //print(notification);
             switch (notification.runtimeType){
               case ScrollStartNotification: print("开始滚动"); break;
               case ScrollUpdateNotification: print("正在滚动"); break;
               case ScrollEndNotification: print("滚动停止"); break;
               case OverscrollNotification: print("滚动到边界"); break;
             }
           },
           child: ListView.builder(
               itemCount: 100,
               itemBuilder: (context, index) {
                 return ListTile(title: Text("$index"),);
               }
           ),
         );
         // 下面的方式自定义通知，使用dispatch进行内容的分发
         Builder(
                       builder: (context) {
                         return RaisedButton(
                           //按钮点击时分发通知  
                           onPressed: () => MyNotification("Hi").dispatch(context),
                           child: Text("Send Notification"),
                         );
                       },
                     ),
         ```
   
5. 动画相关

   1. 类似web的动画，通过保存不同时间段的数值，实现动画

   2. 相关类

      1. Curve：动画曲线；实例代码如下

         ```dart
         final CurvedAnimation curve =
             new CurvedAnimation(parent: controller, curve: Curves.easeIn);
         ```

      2. Controller，控制动画

         ```dart
         final AnimationController controller = new AnimationController( 
          duration: const Duration(milliseconds: 2000), 
          lowerBound: 10.0,
          upperBound: 20.0,
          vsync: this
         );
         ```

      3. Ticker，用于刷新动画，作为参数传到vsync里面，本身通过SchedulerBinging实现刷新，锁频时候不会执行，减少消耗；

      4. Tween，生成自定义的值区间

         ```dart
         final Tween doubleTween = new Tween<double>(begin: -200.0, end: 0.0);
         ```

   3. 基本结构如下

      ```dart
      class AnimatedImage extends AnimatedWidget {
        AnimatedImage({Key key, Animation<double> animation})
            : super(key: key, listenable: animation);
      
        Widget build(BuildContext context) {
          // 省去了自己给animation添加事件响应和setState的步骤
          final Animation<double> animation = listenable;
          return new Center(
            child: Image.asset("images/avatar.png",
                width: animation.value,
                height: animation.value
            ),
          );
        }
      }
      
      
      class ScaleAnimationRoute extends StatefulWidget {
        @override
        _ScaleAnimationRouteState createState() => new _ScaleAnimationRouteState();
      }
      
      class _ScaleAnimationRouteState extends State<ScaleAnimationRoute>
          with SingleTickerProviderStateMixin {
      
        Animation<double> animation;
        AnimationController controller;
      
        initState() {
          super.initState();
          controller = new AnimationController(
              duration: const Duration(seconds: 3), vsync: this);
          //使用弹性曲线
          animation=CurvedAnimation(parent: controller, curve: Curves.bounceIn);
          //图片宽高从0变到300
          animation = new Tween(begin: 0.0, end: 300.0).animate(animation);
          //启动动画
          controller.forward();
        }
      
        @override
        Widget build(BuildContext context) {
          // return AnimatedImage(animation: animation,);
          // 下面的方式是直接渲染，即不用去写最上面的类
          return AnimatedBuilder(
            animation: animation,
            child: Image.asset("images/avatar.png"),
            builder: (BuildContext ctx, Widget child) {
              return new Center(
                child: Container(
                    height: animation.value, 
                    width: animation.value, 
                  	// 上面的child传进来
                    child: child,
                ),
              );
            },
          );
        }
      
        dispose() {
          //路由销毁时需要释放动画资源
          controller.dispose();
          super.dispose();
        }
      }
      ```

   4. 封装动画的方式

      ```dart
      class GrowTransition extends StatelessWidget {
        GrowTransition({this.child, this.animation});
      
        final Widget child;
        final Animation<double> animation;
      
        Widget build(BuildContext context) {
          return new Center(
            child: new AnimatedBuilder(
                animation: animation,
                builder: (BuildContext context, Widget child) {
                  return new Container(
                      height: animation.value, 
                      width: animation.value, 
                      child: child
                  );
                },
                child: child
            ),
          );
        }
      }
      // 封装内容
      ...
      Widget build(BuildContext context) {
          return GrowTransition(
          child: Image.asset("images/avatar.png"), 
          animation: animation,
          );
      }
      ```

   5. 动画监听，在最开始的时候进行状态的判断，手动设定之后的动画效果

      ```dart
      initState() {
          super.initState();
          controller = new AnimationController(
              duration: const Duration(seconds: 1), vsync: this);
          //图片宽高从0变到300
          animation = new Tween(begin: 0.0, end: 300.0).animate(controller);
          // 状态监听
        	animation.addStatusListener((status) {
            if (status == AnimationStatus.completed) {
              //动画执行结束时反向执行动画
              controller.reverse();
            } else if (status == AnimationStatus.dismissed) {
              //动画恢复到初始状态时执行动画（正向）
              controller.forward();
            }
          });
      
          //启动动画（正向）
          controller.forward();
        }
      ```

   6. 路由动画

      ```dart
      // 自带的动画
      Navigator.push(context, CupertinoPageRoute(
                  builder: (context){
                    return PageB(); //路由B
                  }
       ));
      // 自定义动画
      Navigator.push(context, PageRouteBuilder(
            transitionDuration: Duration(milliseconds: 500), //动画时间为500毫秒
            pageBuilder: (BuildContext context, Animation animation,
                Animation secondaryAnimation) {
              return new FadeTransition( //使用渐隐渐入过渡, 
                opacity: animation,
                child: PageB() //路由B
              );
            }));
      }),
      ```



































