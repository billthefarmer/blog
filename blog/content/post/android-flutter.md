---
title: Android Flutter
author: Bill Farmer
type: post
date: 2021-07-15T13:37:07+01:00
url: /android-flutter/
categories:
    - Hacking
keywords:
    - android
    - dart
    - kotlin
---

Google have made a big [thing][0] of their [flutter][1] development
platform, so I thought I ought to give it a try. It's easy to install,
just download the [sdk][2], unzip it in a suitable place and add the
`bin` folder to your `path`. I had to tell it where I had installed
Android Studio.

```shell
$ flutter config --android-studio-dir 'D:\adt\Android Studio'
Setting "android-studio-dir" value to "D:\adt\Android Studio".

You may need to restart any open editors for them to read new settings.

$ flutter doctor -v
[√] Flutter (Channel stable, 2.2.3, on Microsoft Windows [Version 10.0.19043.1110], locale en-GB)
    • Flutter version 2.2.3 at D:\adt\flutter
    • Framework revision f4abaa0735 (2 weeks ago), 2021-07-01 12:46:11 -0700
    • Engine revision 241c87ad80
    • Dart version 2.13.4

[√] Android toolchain - develop for Android devices (Android SDK version 30.0.3)
    • Android SDK at D:\adt\android-sdk-windows
    • Platform android-30, build-tools 30.0.3
    • Java binary at: D:\adt\Android Studio\jre\bin\java
    • Java version OpenJDK Runtime Environment (build 11.0.8+10-b944.6842174)
    • All Android licenses accepted.

[√] Chrome - develop for the web
    • Chrome at C:\Program Files (x86)\Google\Chrome\Application\chrome.exe

[√] Android Studio
    • Android Studio at D:\adt\Android Studio
    • Flutter plugin can be installed from:
       https://plugins.jetbrains.com/plugin/9212-flutter
    • Dart plugin can be installed from:
       https://plugins.jetbrains.com/plugin/6351-dart
    • android-studio-dir = D:\adt\Android Studio
    • Java version OpenJDK Runtime Environment (build 11.0.8+10-b944.6842174)

[√] Connected device (2 available)
    • Moto G (mobile) • TA93003J7P • android-arm    • Android 7.1.2 (API 25)
    • Chrome (web)    • chrome     • web-javascript • Google Chrome 91.0.4472.124

• No issues found!
```

Having installed flutter and got it working, create a sample app.

```shell
$ flutter create --org org.billthefarmer --project-name hello --platforms android -t app hello
Creating project hello...
  hello\.gitignore (created)
  hello\.idea\libraries\Dart_SDK.xml (created)
  hello\.idea\libraries\KotlinJavaRuntime.xml (created)
  hello\.idea\modules.xml (created)
  hello\.idea\runConfigurations\main_dart.xml (created)
  hello\.idea\workspace.xml (created)
  hello\.metadata (created)
  hello\android\app\build.gradle (created)
  hello\android\app\src\main\kotlin\org\billthefarmer\hello\MainActivity.kt (created)
  hello\android\build.gradle (created)
  hello\android\hello_android.iml (created)
  hello\android\.gitignore (created)
  hello\android\app\src\debug\AndroidManifest.xml (created)
  hello\android\app\src\main\AndroidManifest.xml (created)
  hello\android\app\src\main\res\drawable\launch_background.xml (created)
  hello\android\app\src\main\res\drawable-v21\launch_background.xml (created)
  hello\android\app\src\main\res\mipmap-hdpi\ic_launcher.png (created)
  hello\android\app\src\main\res\mipmap-mdpi\ic_launcher.png (created)
  hello\android\app\src\main\res\mipmap-xhdpi\ic_launcher.png (created)
  hello\android\app\src\main\res\mipmap-xxhdpi\ic_launcher.png (created)
  hello\android\app\src\main\res\mipmap-xxxhdpi\ic_launcher.png (created)
  hello\android\app\src\main\res\values\styles.xml (created)
  hello\android\app\src\main\res\values-night\styles.xml (created)
  hello\android\app\src\profile\AndroidManifest.xml (created)
  hello\android\gradle\wrapper\gradle-wrapper.properties (created)
  hello\android\gradle.properties (created)
  hello\android\settings.gradle (created)
  hello\lib\main.dart (created)
  hello\hello.iml (created)
  hello\pubspec.yaml (created)
  hello\README.md (created)
  hello\test\widget_test.dart (created)
Running "flutter pub get" in hello...                            1,323ms
Wrote 35 files.

All done!
In order to run your application, type:

  $ cd hello
  $ flutter run

Your application code is in hello\lib\main.dart.
```

And run it...

```shell
$ flutter run
Launching lib\main.dart on Moto G in debug mode...
Running Gradle task 'assembleDebug'...                             14.3s
√  Built build\app\outputs\flutter-apk\app-debug.apk.
Syncing files to device Moto G...                                  127ms

Flutter run key commands.
r Hot reload.
R Hot restart.
h Repeat this help message.
d Detach (terminate "flutter run" but leave application running).
c Clear the screen
q Quit (terminate the application on the device).

 Running with sound null safety

An Observatory debugger and profiler on Moto G is available at:
http://127.0.0.1:54755/SJvWpFhJNaY=/
The Flutter DevTools debugger and profiler on Moto G is available at:
http://127.0.0.1:9101?uri=http%3A%2F%2F127.0.0.1%3A54755%2FSJvWpFhJNaY%3D%2F
q

Application finished.
```

Screenshot of resulting app.

![Flutter app][3]

Flutter created a minimal kotlin file, MainActivity.kt.

```kotlin
package org.billthefarmer.hello

import io.flutter.embedding.android.FlutterActivity

class MainActivity: FlutterActivity() {
}
```

And a dart file, main.dart.

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        // This is the theme of your application.
        //
        // Try running your application with "flutter run". You'll see the
        // application has a blue toolbar. Then, without quitting the app, try
        // changing the primarySwatch below to Colors.green and then invoke
        // "hot reload" (press "r" in the console where you ran "flutter run",
        // or simply save your changes to "hot reload" in a Flutter IDE).
        // Notice that the counter didn't reset back to zero; the application
        // is not restarted.
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key? key, required this.title}) : super(key: key);

  // This widget is the home page of your application. It is stateful, meaning
  // that it has a State object (defined below) that contains fields that affect
  // how it looks.

  // This class is the configuration for the state. It holds the values (in this
  // case the title) provided by the parent (in this case the App widget) and
  // used by the build method of the State. Fields in a Widget subclass are
  // always marked "final".

  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      // This call to setState tells the Flutter framework that something has
      // changed in this State, which causes it to rerun the build method below
      // so that the display can reflect the updated values. If we changed
      // _counter without calling setState(), then the build method would not be
      // called again, and so nothing would appear to happen.
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    // This method is rerun every time setState is called, for instance as done
    // by the _incrementCounter method above.
    //
    // The Flutter framework has been optimized to make rerunning build methods
    // fast, so that you can just rebuild anything that needs updating rather
    // than having to individually change instances of widgets.
    return Scaffold(
      appBar: AppBar(
        // Here we take the value from the MyHomePage object that was created by
        // the App.build method, and use it to set our appbar title.
        title: Text(widget.title),
      ),
      body: Center(
        // Center is a layout widget. It takes a single child and positions it
        // in the middle of the parent.
        child: Column(
          // Column is also a layout widget. It takes a list of children and
          // arranges them vertically. By default, it sizes itself to fit its
          // children horizontally, and tries to be as tall as its parent.
          //
          // Invoke "debug painting" (press "p" in the console, choose the
          // "Toggle Debug Paint" action from the Flutter Inspector in Android
          // Studio, or the "Toggle Debug Paint" command in Visual Studio Code)
          // to see the wireframe for each widget.
          //
          // Column has various properties to control how it sizes itself and
          // how it positions its children. Here we use mainAxisAlignment to
          // center the children vertically; the main axis here is the vertical
          // axis because Columns are vertical (the cross axis would be
          // horizontal).
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.headline4,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}
```

The generated application file is approximately 50Mb in size, nearly
all of which is flutter run time libraries, kotlin, androidx,
etc. Rather a lot of bloat, considering that most of my apps are about
200Kb in size.

 [0]: https://medium.com/flutter/announcing-flutter-2-2-at-google-i-o-2021-92f0fcbd7ef9
 [1]: https://flutter.dev/
 [2]: https://storage.googleapis.com/flutter_infra_release/releases/stable/windows/flutter_windows_2.2.3-stable.zip
 [3]: images/2021/07/Screenshot_20210715-213310.png
