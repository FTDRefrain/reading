## 零散知识

1. MediaQuery.removePadding去除顶部的padding。里面有removeTop之类的四个方向，需要哪个用哪个
2. NotificationListener去监听滚动事件，里面包裹住listView就能监听了。通过depth属性进行复数监听元素的区分
3. 顶部背景随着上滑消失显示title的方法
   1. 使用stack，将title和背景图放在一起。监听背景图的滚动事件，滚动距离除以滚动最大值，来控制title的opacity值。这样滚到顶部后就是opacity为1
   2. 使用CustomScrollView下面的SliverAppBar。本身自带下拉区间的实现
4. 几种插件
   1. 访问位置信息：geolocator
   2. 访问相册和相机: image_picker
   3. 本地存储: Shared_Preferences
   4. 访问数据: SQFlite
   5. 唤起第三方facebook: flutter_facebook_login
   6. 推送通知: firebase_messaging
   7. 使用firebase: firebase plugin
5. JSON数据都是通过构建modal后进行展示。即拿到response，使用json.decode，然后new Modal.fromJson构建对应的实例。本身使用的是dart:convert包。
6. json_to_dart在线可以转换成modal，这样直接复制就行了。
7. Shared_preferences，本地持久化的key-value存储。
   1. getInstance获取实例
   2. set和get方法进行内容的存取；remove进行删除；
8. ExpansionTile实现展开栏；内部的children使用FractionallySizedBox进行展开适配
9. 分步渲染函数拆分：
   1. 使用一个List，将所有需要渲染的widgets经过自定义的_buildWidget放进去，然后返回这个list
   2. _buildWidget里面构建children的时候，使用map函数进行单个item的渲染，记得最后加一个toList()
   3. 单个子widget渲染
10. 下拉刷新使用RefreshIndicator，实现内部的onRefresh方法就行。没有返回必须是Future类型。
11. 无限滚动两种方式：
    1. 监听controller.position.pixels和controller.position.maxScrollExtent。
    2. 监听当前访问到的index和数组length大小关系

---

## 混合开发实现

1. 安卓部分

   1. 两种使用方式：相互嵌套或者相互跳转

   2. 大体流程：创建flutter module, 添加flutter module依赖，在JAVA / Object-c中使用调用module

   3. 下面进行创建

      ```sh
      flutter create -t module flutter_module
      ```

   4. 创建原生的安卓项目，找到setting.gradle，帖进去下面的代码。即有两个目录，一个是flutter创建的module，一个是原生的安卓目录

      ```dart
      setBinding(new Binding([gradle: this]))
        evaluate(new File(settingsDir.parentFile, 'your_path/.android/include_flutter.groovy'))
      ```

   5. 然后是build.gradle里面的内容，下面都是要修改或者补上的内容

      ```dart
      android {
        defaultConfig {
          // 因为flutter最低支持16
          minSdkVersion 16
        }
        compileOptions {
          sourceCompatibility JavaVersion.VERSION_1_8
          targetCompatibility JavaVersion.VERSION_1_8
        }
        //添加依赖
        dependencies {
          implementation project(':flutter')
        }
      }
      ```

   6. 使用方式

      1. Flutter.createView创建一个view来使用

      2. 在布局文件xml里面使用Fragment站位符，绑定对应的id。代码类似下面。即通过占位符的方式将flutter展示在特定的位置，通过id查找layout，通过route查找对应的flutter内容

         ```java
         onCreate(Bundle savedInstanceState){
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
           findViewById(R.id.test).setOnClickListener(
           	void onClick(View v){
               FragmentTransaction tx = getSupportFragmentManager()
                 .beginTransaction();
               tx.replace(R.id.flutter_name, Flutter.createFragment(
               	"route1"
               ));
               tx.commit();
             }
           )
         }
         ```

      3. 上面'route1'的内容可以变幻出呢个参数。flutter中使用的时候如下，这样就将参数传进去了；

         ```dart
         import 'dart:ui';
         MyApp(initParams: window.defaultRouteName)
         ```

   7. 混合开发下的热加载：flutter attach

2. ios部分

   1. 使用CocoaPods

   2. 找到目录下的PodFile文件，添加下面的代码，然后pod install一下。之后每次flutter多加一个插件的时候，这边都要install一下才行。且入口文件改变成xcworkspace文件了

      ```objc
      target 'FlutterHybridiOS' do
        # pods for FlutterHybridiOS
        flutter_application_path = '../module_path/'
          eval(File.read(File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')), binding)
        
        target 'FlutterHybridiOSTests' do
        	inherit! :search_paths
            #Pods for testing
         end
            
         target 'FlutterHybridiOSUITests' do
            inherit! :search_paths
            #Pods for testing
         end
      end
      ```

   3. 要禁用BitCode

   4. sh脚本的运行

   5. 调用方式

      1. FlutterViewController方式
      2. 没有细看

---

## Native通信

1. channel为主体进行通信，本身在flutter的service.dart包里面
2. 三种类型，下面是安卓端实现方式，ios类似没有细看
   1. BasicMessageChannel：字符串和半结构化信息。收到消息可以回复
      1. Native遍历文件信息后给flutter
      2. flutter从服务端拿到数据后给native
      3. 将数据编码成二进制然后到了platform后进行解码变成string
      4. `BasicMessageChannel('BasicMessageChannelPlugin', StringCodec())`进行实例化。
      5. Codec四种类型：
         1. BinaryCodec：二进制省去编解码过程中的复制过程
         2. StringCodec
         3. JSONMessageCodec：列表字典等
         4. StandardMessageCodec：都能用
      6. Dart端，本身由监听和发送两个方法
         1. setMessageHandler，回调方法，Future类型
         2. Send
      7. Native端
         1. 将flutter view实例传进去，还是构建一个channel。使用setMessageHandler(this)进行绑定
         2. onMessage监听，reply进行回复
         3. send发送数据，里面有cb可以进行回复
      8. 两者声明的名字要一致
   2. MethodChannel
      1. 传递方法调用。一次性
      2. 拍照
      3. Dart端使用代码
         1. MethodChannel('MethodChannelPlugin')
         2. mcp.invokeMethod('send', value)
      4. Native端
         1. onMethodCall进行监听，接受两个参数，call和result
         2. 对于call.method去switch，call.arguments拿到参数。返回的是return.sucess
   3. EventChannel，[网址细节](http://github.com/crazycodeboy)
      1. 数据流信息，不可回复
      2. 手机电量变化，网络连接变化，陀螺仪，传感器等
      3. Dart端
         1. EventChannel('EventChannelPlugin')
         2. streamSubscription.receiveBroadcastStream().listen(_onToDart, onError: _onToDartError)
         3. 在dispose的时候，上面的stream要cancel掉且变量指向null进行释放
         4. _onToDart是回调方法
      4. Native
         1. StreamHandler构建实例
         2. onListen和onCancel两个方法

---

## 实战相关

1. DAO层构建，即JSON内容对应的modal类，大概代码如下

   ```dart
   factroy Modal.fromJson(Map<String, dynamic> json){
     return Modal(
     	searchUrl: json['searchUrl'];
     )
   }
   ```

2. DAO层数据获取

   ```dart
   class HomeDAO {
     static Future<HomeModal> fetch() async {
       final response = await http.get(HOME_URL);
       if (response.status == 200){
         Utf8Decoder utf8decoder = Utf8Decoder();
         var result = json.decode(utf8decoder.convert(response.bodyBytes));
         return HomeModal.fromJson(result)
       }
     }
   }
   ```

3. 使用方式

   ```dart
   HomeDAO.fetch().then((result){
     setState(){
       // 解析成了string格式
       resultString = json.encode(result.toJson())
     }
   })
   // 另一种方式就是async / await方法去做
   ```

4. 网页载入

   1. Flutter_webview_plugin
   2. onUrlChanged.listen((String url){})
   3. onStateChanged.listen，监控状态的变化
   4. 使用webViewScaffold包裹住url就行了
   5. 里面的initialChild可以设置加载时候显示的组件
   6. backForbid禁止返回
   7. h5页面可能会去到其他页面。可以指定域名，当url不在域名内，就使用Navigator.pop返回到上一个页面，这样一个一个就跳出去了。

5. 首页页面开发

   1. borderSide进行四周的样式和宽度
   2. BoxDecoration里面加上线性渐变。gradient : LinearGradient(colors: [start, end])
   3. 宽度撑满使用FractionallySizedBox；高度撑满使用Expand包裹一下
   4. PhysicalModel包裹住后进行修饰，如颜色，边角
   5. 不对齐的时候考虑expand一下，使用flex: 1扩展后再看
   6. MediaQuery.of(context)获取屏幕实例

6. 搜索框实现

   1. Color里面要用int.parse解析8位的16进制字符串
   2. hintText设置提示文字，hintStyle设置提示字体大小
   3. opacity控制样式变化；或者使用color(opacity * 255)
   4. 搜索目标高亮显示：用搜索词去分割目标，然后加到list里面，搜索词部分是变色且高亮的；外面一个richText，里面是textSpan -> children将所有内容放进去
   5. 动画执行过程中长宽改变，使用stack设置同层撑开布局

7. Native集成

   1. 用安卓或者ios去调用native的内容，然后通过混合开发插件去实现通信，做到flutter调用native的方式
   2. 将两个项目都依赖于一个flutter sdk，且native项目依赖于主项目；