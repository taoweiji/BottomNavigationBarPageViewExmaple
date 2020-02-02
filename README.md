# [Flutter：BottomNavigationBar + PageView 解决切换页面被重置](https://www.jianshu.com/p/0f272418a931)

BottomNavigationBar可以非常方便我们实现类似微信的首页的布局方式，使用 BottomNavigationBar 配合 ListView 或者 ScrollView开发，切换页面会导致ListView 又回到了顶部，实际上是需要配合 PageView 才能解决这个问题。
#### 效果

![](https://upload-images.jianshu.io/upload_images/2431302-ac57d6a2d1d7df14.gif?imageMogr2/auto-orient/strip)


#### 代码
```dart
import 'package:flutter/material.dart';
import 'package:flutter_bottom_navigation_bar/list_demo.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _selectedIndex = 0;
  List _pages = List();
  PageController _controller;

  @override
  void initState() {
    super.initState();
    _controller = PageController(initialPage: 0);
    _pages.add(HomeTabPage());
    _pages.add(HomeTabPage());
    _pages.add(HomeTabPage());
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('BottomNavigationBar Demo'),
      ),
      body: PageView.builder(
        itemBuilder: (context, index) => _pages[index],
        // physics: NeverScrollableScrollPhysics(),// 禁止左右滑动
        itemCount: _pages.length,
        onPageChanged: _onPageChanged,
        controller: _controller,
      ),
      bottomNavigationBar: BottomNavigationBar(
        items: const <BottomNavigationBarItem>[
          BottomNavigationBarItem(
            icon: Icon(Icons.home),
            title: Text('首页'),
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.message),
            title: Text('消息'),
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.person),
            title: Text('我'),
          ),
        ],
        currentIndex: _selectedIndex,
        selectedItemColor: Colors.amber[800],
        onTap: _onTap,
      ),
    );
  }

  void _onTap(int index) {
    setState(() {
      _selectedIndex = index;
    });
    _controller.jumpToPage(index);
  }

  void _onPageChanged(int index) {
    setState(() {
      _selectedIndex = index;
    });
  }
}
```
创建 HomeTabPage，关键的代码是` with AutomaticKeepAliveClientMixin `，`bool get wantKeepAlive => true`。
```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

class HomeTabPage extends StatefulWidget {
  @override
  State<StatefulWidget> createState() {
    return _HomeTabPageState();
  }
}
class _HomeTabPageState extends State<HomeTabPage> with AutomaticKeepAliveClientMixin {
  final List<String> entries = <String>["0","1","2","3","4","5","6","7","8","9"];
  final List<int> colorCodes = <int>[0,100,200,300,400,500,600,700,800,900];

  @override
  void initState() {
    super.initState();
  }
  @override
  Widget build(BuildContext context) {
    super.build(context);
    return ListView.builder(
        padding: const EdgeInsets.all(8),
        itemCount: entries.length,
        itemBuilder: (BuildContext context, int index) {
          return Container(
            height: 100,
            color: Colors.amber[colorCodes[index]],
            child: Center(child: Text('Entry ${entries[index]}')),
          );
        });
  }

  @override
  bool get wantKeepAlive => true;
}
```
