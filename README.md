# flutterstudy

Assets와 Image 추가하기

1. asset
플루터앱은 코드와 에셋(asset 또는 resources)으로 구성됨

asset의 종류
1) static data (예 JSON files) 
2) configuration files 
3) icons
4) images (JPEG, WebP, GIF, animated WebP/GIF, PNG, BMP, and WBMP)

2. Assets 지정
 `pubspec.yaml`을 사용하여 지정함. `pubspec.yaml` 파일에 있는 명시적 경로 (파일에 상대적인 경로)로 지정됨. assets의 선언 순서는 문제되지 않음.
 
```
  .../pubspec.yaml
  .../graphics/my_icon.png
  .../graphics/background.png
  .../graphics/dark/background.png
  ...etc.
```
  디렉토리 아래에 모든 assets을 포함하려면 /과 디렉토리 이름을 표시함. 


 ```
	flutter:
  assets:
    - assets/
 ```

직접 디렉토리에 위치한 assets만 포함되어 하위디렉토리인 경우 각 하위디렉토리를 추가 지정해야 함.

3. Asset 번들과 Asset 변형
Asset은 Asset Bundle이라는 특수 아카이브에 배치됨. 이 번들은 런타임시 읽을 수 있음.
빌드 프로세스는 자산 변형이라는 개념을 지원합니다. `pubspec.yaml`에 assets 경로가 지정되면 빌드 프로세스는 인접한 하위 디렉토리에서 이름이 같은 파일을 찾습니다. 그런 다음 이러한 파일은 지정된 자산과 함께 자산 번들에 포함됩니다.


예를 들어서 응용 프로그램 디렉토리에 다음 파일이있는 경우

```
  .../pubspec.yaml
  .../graphics/my_icon.png
  .../graphics/background.png
  .../graphics/dark/background.png
  ...etc.
```

 `pubspec.yaml` 파일에 다음 내용이 있는 경우

```yaml
flutter:
  assets:
    - graphics/background.png
```

`graphics/background.png` 와 `graphics/dark/background.png` 모두  asset bundle에 포함됨.
전자는 _main asset_  후자는  _variant_ 즉 asset 변형으로 간주됨.

반면에 아래와 같이 graphics directory가 지정된 경우에는

```yaml
flutter:
  assets:
    - graphics/
```

 `graphics/my_icon.png`, `graphics/background.png` ,`graphics/dark/background.png`파일들이 모두 포함됨. 해상도에 적합한 이미지를 선택할 때 asset 변형을 사용함.


Asset 로드



[`AssetBundle`][] 객체를 통해 asset에 엑세스할 수 있음. text asset(`loadString()`) 과 이미지 asset (`load()`) 모두 로드 가능하고`pubspec.yaml` 파일에 경로가 지정되어야 함.

text assets 

`package:flutter/services.dart`로부터 `rootBundle` 객체를  사용하여 text assets를 로드할 수 있음.


current BuildContext로부터는  `DefaultAssetBundle.of()`을 사용하는 게 권장되고 `DefaultAssetBundle.of()`은 예를 들어 JSON file과 같은 경우 우회적으로 로드할 때 사용가능함. 


`rootBundle`을 사용하면 직접 assets를 로드할 수 있음.

사례
available, you can use `rootBundle`을 사용하면 직접 assets를 로드할 수 있음.

```dart
import 'dart:async' show Future;
import 'package:flutter/services.dart' show rootBundle;

Future<String> loadAsset() async {
  return await rootBundle.loadString('assets/config.json');
}
```

이미지 로딩


해상도에 따르는 이미지 선언

현재 장치 픽셀 비율에 적합한 해상도의 이미지 로드 가능함.



`AssetImage` 은 asset을 현재 장치 픽셀 비율과 가장 일치하는 asset에 매핑함. 이 매핑이 작동하려면 특정 디렉토리 구조에 따라 asset을 정렬해야 함.

```
  .../image.png
  .../Mx/image.png
  .../Nx/image.png
  ...etc.
```

 _M_ 과 _N_ 은 포함된 이미지의 해상도에 해당하는 숫자임. 즉, 이미지가 지향하는장치 픽셀 비율을 지정함.

주요 asset은  1.0의 해상도에 해당하는 것으로 가정함. 
아래 사례에서 주요 asset은  `my_icon.png`임.

```
  .../my_icon.png
  .../2.0x/my_icon.png
  .../3.0x/my_icon.png
```

장치 픽셀 비율이 1.8 인 장치에서는`.../2.0x/my_icon.png` 이 채택됨.
장치 픽셀 비율이 2.7 인 경우 자산`.../3.0x/my_icon.png`이 채택됨.

렌더링 된 이미지의 너비와 높이가 `Image`  위젯에 지정되지 않은 경우 공칭 해상도는 기본 자산과 동일한 화면 공간을 차지하도록 더 높은 해상도로 자산의 크기를 조정하는 데 사용됨. 즉, 72 x 72가 `.../my_icon.png`  72 x 72 픽셀 인 경우 `.../3.0x/my_icon.png`의 경우 216 x 216 픽셀이어야 함. 그러나 너비와 높이를 지정하지 않으면 둘 다 72x72 픽셀로 렌더링됨.



이미지 로딩
이미지를 로드하려면 `AssetImage`  위젯의 build 메소드에서 클래스를 사용.

예를 들어 아래와 같은 코드에서 배경 이미지를 로드 할 수 있음.

```dart
Widget build(BuildContext context) {
  return Image(image: AssetImage('graphics/background.png'));
}
```


default asset 번들을 사용하는 모든 것은 이미지를 로드할 때 해상도 인식을 상속합니다.
하위 클래스인 `ImageStream`,`ImageCache`와 같은 클래스를 사용할 때도 상속함.


packages에서 이미지 

[package][] dependency에서 이미지를 로드하려면 `package`도 `AssetImage`에 같이 표시해주어야 함.

예를 들어 `my_icons` package에서 이미지를 로드하는 경우 

```
  .../pubspec.yaml
  .../icons/heart.png
  .../icons/1.5x/heart.png
  .../icons/2.0x/heart.png
  ...etc.
```

<!-- skip -->
```dart
 AssetImage('icons/heart.png', package: 'my_icons')
```
와 같이 패키지도 같이 표시해주어야 한다.

 package assets 번들링

 package에서 사용되는 assets의 경우 `pubspec.yaml`에서 지정되어야 함.


`fancy_backgrounds` 패키지가 다음의 파일들을 갖는 경우


```
  .../lib/backgrounds/background1.png
  .../lib/backgrounds/background2.png
  .../lib/backgrounds/background3.png
```
`pubspec.yaml` 에서 다음과 같이 지정해주어야 한다.


```yaml
flutter:
  assets:
    - packages/fancy_backgrounds/backgrounds/background1.png
```



## Sharing assets with the underlying platform

Flutter assets are readily available to platform code via
AssetManager on Android and NSBundle on iOS.

### Android

On Android the assets are available via the [AssetManager API][].
The lookup key used in, for instance [openFd][], is obtained from
`lookupKeyForAsset` on [PluginRegistry.Registrar][] or
`getLookupKeyForAsset` on [FlutterView][].
`PluginRegistry.Registrar` is available when developing a plugin
while `FlutterView` would be the choice when developing an
app including a platform view.

As an example, suppose you have specified the following
in your pubspec.yaml

```yaml
flutter:
  assets:
    - icons/heart.png
```

This reflects the following structure in your Flutter app.

```
  .../pubspec.yaml
  .../icons/heart.png
  ...etc.
```

To access `icons/heart.png` from your Java plugin code,
do the following:

```java
AssetManager assetManager = registrar.context().getAssets();
String key = registrar.lookupKeyForAsset("icons/heart.png");
AssetFileDescriptor fd = assetManager.openFd(key);
```

### iOS

On iOS the assets are available via the [mainBundle][].
The lookup key used in, for instance [pathForResource:ofType:][],
is obtained from `lookupKeyForAsset` or `lookupKeyForAsset:fromPackage:`
on [FlutterPluginRegistrar][], or `lookupKeyForAsset:` or
`lookupKeyForAsset:fromPackage:` on [FlutterViewController][].
`FlutterPluginRegistrar` is available when developing
a plugin while `FlutterViewController` would be the choice
when developing an app including a platform view.

As an example, suppose you have the Flutter setting from above.

To access `icons/heart.png` from your Objective-C plugin code you
would do the following:

```objective-c
NSString* key = [registrar lookupKeyForAsset:@"icons/heart.png"];
NSString* path = [[NSBundle mainBundle] pathForResource:key ofType:nil];
```

For a more complete example, see the implementation of the
Flutter [video_player plugin][].

## Platform assets

There are other occasions to work with assets in the
platform projects directly. Below are two common cases
where assets are used before the Flutter framework is
loaded and running.

### Updating the app icon

Updating a Flutter application's launch icon works the same
way as updating launch icons in native Android or iOS applications.

![Launch icon](/images/assets-and-images/icon.png)

#### Android

In your Flutter project's root directory, navigate to
`.../android/app/src/main/res`. The various bitmap resource
folders such as `mipmap-hdpi` already contain placeholder images named
`ic_launcher.png`. Replace them with your desired assets
respecting the recommended icon size per screen density
as indicated by the [Android Developer Guide][].

![Android icon location](/images/assets-and-images/android-icon-path.png)

{{site.alert.note}}
  If you rename the .png files, you must also update the
  correspondingname in your `AndroidManifest.xml`'s
  `<application>` tag's `android:icon` attribute.
{{site.alert.end}}

#### iOS

In your Flutter project's root directory,
navigate to `.../ios/Runner`. The
`Assets.xcassets/AppIcon.appiconset` directory already contains
placeholder images. Replace them with the appropriately
sized images as indicated by their filename as dictated by the
Apple [Human Interface Guidelines][].
Keep the original file names.

![iOS icon location](/images/assets-and-images/ios-icon-path.png)

### Updating the launch screen

<p align="center">
  <img src="/images/assets-and-images/launch-screen.png" alt="Launch screen" />
</p>

Flutter also uses native platform mechanisms to draw
transitional launch screens to your Flutter app while the
Flutter framework loads. This launch screen persists until
Flutter renders the first frame of your application.

{{site.alert.note}}
  This implies that if you don't call [runApp()][] in the
  `main()` function of your app (or more specifically, if you don't call
  [`window.render()`][] in response to [`window.onDrawFrame`][]),
  the launch screen persists forever.
{{site.alert.end}}

#### Android

To add a "splash screen" to your Flutter application,
navigate to `.../android/app/src/main`.
In `res/drawable/launch_background.xml`,
use this [layer list drawable][] XML to customize
the look of your launch screen. The existing template provides
an example of adding an image to the middle of a white splash
screen in commented code. You can uncomment it or use other
[drawables][] to achieve the intended effect.

#### iOS

To add an image to the center of your "splash screen",
navigate to `.../ios/Runner`.
In `Assets.xcassets/LaunchImage.imageset`,
drop in images named `LaunchImage.png`,
`LaunchImage@2x.png`, `LaunchImage@3x.png`.
If you use different filenames,
update the `Contents.json` file in the same directory.

You can also fully customize your launch screen storyboard
in Xcode by opening `.../ios/Runner.xcworkspace`.
Navigate to `Runner/Runner` in the Project Navigator and
drop in images by opening `Assets.xcassets` or do any
customization using the Interface Builder in
`LaunchScreen.storyboard`.



네비게이터 클래스

최근에 방문한 페이지와 함께 이전 페이지 위에 시각적으로 오버레이를 사용하여 위젯 계층 구조 맨 위에 네비게이터가 있음. 이 패턴을 사용하면 네비게이터가 오버레이에서 위젯을 움직여 한 페이지에서 다른 페이지로 전환해서 표시할 수 있음.

네비게이터 사용
모바일 앱은 일반적으로 '화면'또는 '페이지'라는 전체 화면 요소를 통해 콘텐츠를 표시함. Flutter에서는 이러한 요소를 경로라고하며 네비게이터 위젯으로 관리합니다 . 네비게이터는 Route 객체의 스택을 관리하고 `Navigator.push` 및 `Navigator.pop` 과 같은 스택 관리 방법을 제공함.

Android와 같은 특정 플랫폼에서 시스템 UI는 사용자가 애플리케이션 스택에서 이전 경로로 다시 이동할 수있는 뒤로 버튼 제공함. 이러한 뒤로 버튼이 자동 생성되지 않는 경우에는 `Scaffold.appBar` 속성에 사용되는 `AppBar`를 사용하여 뒤로 버튼을 자동으로 추가할 수 있음.

전체 화면 경로 표시
네비게이터를 직접 만들 수 있지만 `WidgetsApp` 또는 `MaterialApp` 위젯으로 만든 네비게이터를 사용하는 것이 가장 일반적임. `Navigator.of` 로 해당 네비게이터를 참조가능함.

`MaterialApp`을 사용하는 것이 가장 간단함.  `home: `다음에는 하단의 경로를 표시함. 

```
void main() {
  runApp(MaterialApp(home: MyAppHome()));
}
```

스택에서 새 경로를 푸시하기 위해 화면에 표시할 항목을 생성하는 빌더 기능을 사용하여 `MaterialPageRoute` 인스턴스를 만들 수 있음.

예시
```
Navigator.push(context, MaterialPageRoute<void>(
  builder: (BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('My Page')),
      body: Center(
        child: FlatButton(
          child: Text('POP'),
          onPressed: () {
            Navigator.pop(context);
          },
        ),
      ),
    );
  },
));
```

라우트는 하위 위젯 대신 빌더 함수로 위젯을 정의함. 네비게이터의 pop 메소드를 사용하여 앱의 홈 페이지를 표시하는 새로운 경로를 팝업 할 수 있음.

```
Navigator.pop(context);
```

스캐폴드는 자동으로 앱바에 '뒤로' 버튼을 추가하기 때문에 스캐 폴드가 있는 경로에서 네비게이터를 팝업시키는 위젯을 제공할 필요는 없음. 뒤로 버튼을 누르면 `Navigator.pop` 이 호출됨. Android에서는 시스템 뒤로 버튼을 눌러도 동일한 작업을 수행함.

네비게이터 경로 사용
경로 이름은 경로와 유사한 구조를 사용함. (예 : '/ a / b / c'). 앱의 경로 이름은 기본적으로 '/'임.

MaterialApp에서 `<String, WidgetBuilder>`을 route 이름과 관련하여 사용함.

```
void main() {
  runApp(MaterialApp(
    home: MyAppHome(), // route 이름은 '/'로 표시함
    routes: <String, WidgetBuilder> {
      '/a': (BuildContext context) => MyPage(title: 'page A'),
      '/b': (BuildContext context) => MyPage(title: 'page B'),
      '/c': (BuildContext context) => MyPage(title: 'page C'),
    },
  ));
}
```

이름으로 경로를 표시하려면

```
Navigator.pushNamed(context, '/b');
```

경로는 값을 반환 할 수 있음. 경로를 푸시하여 사용자에게 값을 요청하면 pop 메소드의 결과 매개 변수를 통해 값을 리턴할 수 있음. 경로를 푸시하는 메소드는 Future를 리턴함. Future 값은 pop 메소드의 result 매개 변수임.

예를 들어 사용자에게 '확인'을 눌러 작업을 확인하도록 요청하려면 Navigator.pushawait 의 결과를 얻을 수 있음.

```
bool value = await Navigator.push(context, MaterialPageRoute<bool>(
  builder: (BuildContext context) {
    return Center(
      child: GestureDetector(
        child: Text('OK'),
        onTap: () { Navigator.pop(context, true); }
      ),
    );
  }
));
```

사용자가 'OK'를 누르면 값이 true가 됨. 경로를 사용하여 값을 반환하는 경우 경로의 유형 매개 변수는 pop 의 결과 유형과 일치해야 함. 

팝업 경로
경로가 전체 화면을 가리지 않아도 됨. PopupRoute 는 현재 화면이 보이도록 부분적으로만 불투명한 ModalRoute.barrierColor로 화면을 덮음. 

PopupMenuButton 및 DropdownButton은 팝업 경로를 생성하는 위젯임. 이 위젯은 PopupRoute의 내부 서브 클래스를 작성하고 Navigator의 push 및 pop 메소드를 사용하여 이를 표시함.

맞춤 경로
PopupRoute , ModalRoute 또는 PageRoute와 같이 라우트 클래스 중 하나의 자체 서브 클래스를 작성하여 라우트, 색상 및 동작 및 애니메이션 전환을 제어 할 수 있음.

PageRouteBuilder의 클래스는 사용자 지정 경로를 정의 할 수 있음. 

예제

```
Navigator.push(context, PageRouteBuilder(
  opaque: false,
  pageBuilder: (BuildContext context, _, __) {
    return Center(child: Text('My PageRoute'));
  },
  transitionsBuilder: (___, Animation<double> animation, ____, Widget child) {
    return FadeTransition(
      opacity: animation,
      child: RotationTransition(
        turns: Tween<double>(begin: 0.5, end: 1.0).animate(animation),
        child: child,
      ),
    );
  }
));
```


중첩 네비게이터
앱은 둘 이상의 네비게이터를 사용할 수 있습니다. 하나의 네비게이터를 다른 네비게이터 아래에 중첩하여 탭 내비게이션 등을 작성할 수 있음.




중첩 네비게이터 사용 예제


```
import 'package:flutter/material.dart';

// ...

void main() => runApp(new MyApp());

// ...

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Code Sample for Navigator',
      // MaterialApp contains our top-level Navigator
      initialRoute: '/',
      routes: {
        '/': (BuildContext context) => HomePage(),
        '/signup': (BuildContext context) => SignUpPage(),
      },
    );
  }
}

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return DefaultTextStyle(
      style: Theme.of(context).textTheme.display1,
      child: Container(
        color: Colors.white,
        alignment: Alignment.center,
        child: Text('Home Page'),
      ),
    );
  }
}

class CollectPersonalInfoPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return DefaultTextStyle(
      style: Theme.of(context).textTheme.display1,
      child: GestureDetector(
        onTap: () {
          // This moves from the personal info page to the credentials page,
          // replacing this page with that one.
          Navigator.of(context)
            .pushReplacementNamed('signup/choose_credentials');
        },
        child: Container(
          color: Colors.lightBlue,
          alignment: Alignment.center,
          child: Text('Collect Personal Info Page'),
        ),
      ),
    );
  }
}

class ChooseCredentialsPage extends StatelessWidget {
  const ChooseCredentialsPage({
    this.onSignupComplete,
  });

  final VoidCallback onSignupComplete;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onSignupComplete,
      child: DefaultTextStyle(
        style: Theme.of(context).textTheme.display1,
        child: Container(
          color: Colors.pinkAccent,
          alignment: Alignment.center,
          child: Text('Choose Credentials Page'),
        ),
      ),
    );
  }
}

class SignUpPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // SignUpPage builds its own Navigator which ends up being a nested
    // Navigator in our app.
    return Navigator(
      initialRoute: 'signup/personal_info',
      onGenerateRoute: (RouteSettings settings) {
        WidgetBuilder builder;
        switch (settings.name) {
          case 'signup/personal_info':
          // Assume CollectPersonalInfoPage collects personal info and then
          // navigates to 'signup/choose_credentials'.
            builder = (BuildContext _) => CollectPersonalInfoPage();
            break;
          case 'signup/choose_credentials':
          // Assume ChooseCredentialsPage collects new credentials and then
          // invokes 'onSignupComplete()'.
            builder = (BuildContext _) => ChooseCredentialsPage(
              onSignupComplete: () {
                // Referencing Navigator.of(context) from here refers to the
                // top level Navigator because SignUpPage is above the
                // nested Navigator that it created. Therefore, this pop()
                // will pop the entire "sign up" journey and return to the
                // "/" route, AKA HomePage.
                Navigator.of(context).pop();
              },
            );
            break;
          default:
            throw Exception('Invalid route: ${settings.name}');
        }
        return MaterialPageRoute(builder: builder, settings: settings);
      },
    );
  }
}

```




새로운 화면으로 이동하고, 되돌아오기


대부분의 앱은 여러 종류의 정보를 보여주기 위해 여러 화면을 갖고 있dma. 예를 들어, 어떤 앱이 상품 목록을 보여주는 화면을 갖고 있다고 한다면, 사용자가 한 상품을 선택했을 때 새로운 화면에서 해당 상품의 상세 정보를 볼 수 있음.
용어: Flutter에서 screen 과 page 는 route 로 불림.

Route는 Android의 Activity, iOS의 ViewController와 동일함. Flutter에서는 Route 역시 위젯임.

새로운 route로 Navigator를 사용하여 아래와 같은 단계로 진행하여 이동함.



1. 두 개의 route를 생성합니다.
2. Navigator.push()를 사용하여 두 번째 route로 전환합니다.
3. Navigator.pop()을 사용하여 첫 번째 route로 되돌아 옵니다.


1. 두 개의 route를 생성합니다.
우선 두 개의 route를 생성함. 
예제에서는 첫 번째 route의 버튼을 누르면 두 번째 route로 화면 전환되며, 두 번째 route의 버튼을 누르면 첫 번째 route로 되돌아 옴.



```
class FirstRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Route'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('Open route'),
          onPressed: () {
            // 눌렀을 때 두 번째 route로 이동합니다.
          },
        ),
      ),
    );
  }
}

class SecondRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Route"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            // 눌렀을 때 첫 번째 route로 되돌아 갑니다.
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```

2. Navigator.push()를 사용하여 두 번째 route로 전환합니다.
새로운 route로 전환하기 위해 Navigator.push() 메서드를 사용함. push() 메서드는 Route를 Navigator에 의해 관리되는 route 스택에 추가함. Route는 직접 생성하거나, 새로운 route로 이동시 MaterialPageRoute를 사용할 수 있음.



```
// Within the `FirstRoute` widget
onPressed: () {
  Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => SecondRoute()),
  );
}
```

3. Navigator.pop()을 사용하여 첫 번째 route로 되돌아 옴.
두 번째 route를 닫고 이전 route로  Navigator.pop() 메서드를 사용여 되돌아감. pop() 메서드는 Navigator에 의해 관리되는 route 스택에서 현재 Route를 제거함.

```
// Within the SecondRoute widget
onPressed: () {
  Navigator.pop(context);
}
```

완성된 예제

```
content_copy
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Navigation Basics',
    home: FirstRoute(),
  ));
}

class FirstRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Route'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('Open route'),
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => SecondRoute()),
            );
          },
        ),
      ),
    );
  }
}

class SecondRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Route"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            Navigator.pop(context);
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```


Named route로의 화면 전환

만약 앱의 다른 많은 영역에서 동일한 화면으로 이동하고자 한다면, 중복된 코드가 생기게 됩니다. 이러한 경우 named route를 정의하여 화면 전환에 사용하는 방법이 있음.
Named route를 사용하기 위해 Navigator.pushNamed 함수를 사용할 수 있음. 


1. 두 개의 화면 만들기.
2. Route 정의하기.
3. Navigator.pushNamed를 사용하여 두 번째 화면으로 전환하기.
4. Navigator.pop을 사용하여 첫 번째 화면으로 돌아가기.

1. 두 개의 화면 만들기
우선 두 개 이상의 화면이 있어야 함. 
예제에서는 첫 번째 화면에는 두 번째 화면으로 이동하기 위한 버튼 하나가 있고, 두 번째 화면에는 다시 첫 번째 화면으로 돌아가기 위한 버튼이 있음.

```

class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Screen'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('Launch screen'),
          onPressed: () {
            // 클릭하면 두 번째 화면으로 전환.
          },
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Screen"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            // 클릭하면 첫 번째 화면으로 돌아감.
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```

2. Route 정의하기
 MaterialApp 생성자에 initialRoute와 routes 이름의 추가 프로퍼티를 제공하여 route를 정의함. initialRoute 프로퍼티는 앱의 시작점을 나타내는 route를 정의하고, routes 프로퍼티는 이용가능한 named route와 해당 route로 이동했을 때 빌드될 위젯을 정의함.

```

MaterialApp(
  // "/"으로 named route와 함께 시작함. 본 예제에서는 FirstScreen 위젯에서 시작함.
  initialRoute: '/',
  routes: {
    // "/" Route로 이동하면, FirstScreen 위젯을 생성함.
    '/': (context) => FirstScreen(),
    // "/second" route로 이동하면, SecondScreen 위젯을 생성함.
    '/second': (context) => SecondScreen(),
  },
);
```
 주의: initialRoute를 사용한다면, home 프로퍼티를 정의하지 않음.

3. 두 번째 화면으로 전환하기
위젯과 route를 정의했다면, Navigator.pushNamed() 메서드로 화면 전환을 호출함. 이 함수는 Flutter에게 앞서 routes 테이블에 정의한 위젯을 생성하고 그 화면을 시작하도록 요청함.

```
// `FirstScreen` 위젯의 콜백
onPressed: () {
  // Named route를 사용하여 두 번째 화면으로 전환함.
  Navigator.pushNamed(context, '/second');
}
```

4. 첫 번째 화면으로 돌아가기
첫 번째 페이지로 되돌아가기 위해 Navigator.pop() 함수를 사용.

```
// SecondScreen 위젯의 콜백
onPressed: () {
  // 현재 route를 스택에서 제거함으로써 첫 번째 스크린으로 되돌아감.
  Navigator.pop(context);
}
```

완성된 예제

```
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Named routes Demo',
    // "/"을 앱이 시작하게 될 route로 지정합니다. 본 예제에서는 FirstScreen 위젯이 첫 번째 페이지가
    // 될 것입니다.
    initialRoute: '/',
    routes: {
      // When we navigate to the "/" route, build the FirstScreen Widget
      // "/" Route로 이동하면, FirstScreen 위젯을 생성합니다.
      '/': (context) => FirstScreen(),
      // "/second" route로 이동하면, SecondScreen 위젯을 생성합니다.
      '/second': (context) => SecondScreen(),
    },
  ));
}

class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Screen'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('Launch screen'),
          onPressed: () {
            // Named route를 사용하여 두 번째 화면으로 전환함.
            Navigator.pushNamed(context, '/second');
          },
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Screen"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            // 현재 route를 스택에서 제거함으로써 첫 번째 스크린으로 되돌아 갑니다.
            Navigator.pop(context);
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```

새로운 화면으로 데이터 보내기

문서
종종 새로운 화면으로 단순히 이동하는 것 뿐만 아니라 데이터를 넘겨주어야 할 때도 있음. 예를 들어, 사용자가 선택한 아이템에 대한 정보를 같이 넘겨주는 경우가 있음.
예제에서는 Todo 리스트를 만들고 Todo를 선택하면 새로운 화면(위젯)으로 이동하면서 선택한 to do에 대한 정보를 표시함.
1. Todo 클래스를 정의함.
2. Todo 리스트를 표시함.
3. Todo에 대한 상세 정보를 보여줄 수 있는 화면을 생성.
4. 상세 화면으로 이동하면서 데이터를 전달.


1. Todo 클래스를 정의.
Todo를 표현하기 위한 간단한 정보를 표시함. 이 예제에서는 두 가지의 데이터를 갖고 있는 클래스를 정의함. 

```
class Todo {
  final String title;
  final String description;

  Todo(this.title, this.description);
}
```

2. Todo 리스트를 표시함
 예제에서는 20개의 todo를 생성하고 ListView를 사용하여 Todo 리스트 생성함.
 
```
final todos = List<Todo>.generate(
  20,
  (i) => Todo(
        'Todo $i',
        'A description of what needs to be done for Todo $i',
      ),
);
ListView를 사용하여 Todo 리스트 보여주기
content_copy
ListView.builder(
  itemCount: todos.length,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text(todos[index].title),
    );
  },
);
```
20개의 Todo를 생성하여 ListView에 표시함.

3. Todo에 대한 상세 정보를 보여줄 수 있는 화면을 생성.
예제에서 두 번째 화면의 제목은 Todo의 제목을 포함하며 본문에는 상세 설명을 표시함.


```

class DetailScreen extends StatelessWidget {
  // Todo를 들고 있을 필드를 선언.
  final Todo todo;

  // 생성자 매개변수로 Todo를 받도록 함.
  DetailScreen({Key key, @required this.todo}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    // UI를 그리기 위해 Todo를 사용.
    return Scaffold(
      appBar: AppBar(
        title: Text(todo.title),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Text(todo.description),
      ),
    );
  }
}
```
4. 상세 화면으로 이동하면서 데이터를 전달함.

예제에서는 사용자가 Todo 리스트 중 하나를 선택했을 때, DetailsScreen으로 화면 전환하고 동시에 DetailsScreen에 Todo를 전달.


사용자의 탭 동작을 감지하기 위해, ListTile 위젯에 onTap 콜백을 작성하고 onTap 콜백 내에서 다시 한 번 Navigator.push 메서드를 사용하.

```
ListView.builder(
  itemCount: todos.length,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text(todos[index].title)
      // DetailScreen을 생성하고 현재 todo를 같이 전달.
      onTap: () {
        Navigator.push(
          context,
          MaterialPageRoute(
            builder: (context) => DetailScreen(todo: todos[index]),
          ),
        );
      },
    );
  },
);
```

완성된 예제

```
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

class Todo {
  final String title;
  final String description;

  Todo(this.title, this.description);
}

void main() {
  runApp(MaterialApp(
    title: 'Passing Data',
    home: TodosScreen(
      todos: List.generate(
        20,
        (i) => Todo(
              'Todo $i',
              'A description of what needs to be done for Todo $i',
            ),
      ),
    ),
  ));
}

class TodosScreen extends StatelessWidget {
  final List<Todo> todos;

  TodosScreen({Key key, @required this.todos}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Todos'),
      ),
      body: ListView.builder(
        itemCount: todos.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(todos[index].title),
            
           
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => DetailScreen(todo: todos[index]),
                ),
              );
            },
          );
        },
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  // Todo를 들고 있을 필드를 선언.
  final Todo todo;

  // 생성자는 Todo를 인자로 받음.
  DetailScreen({Key key, @required this.todo}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    // UI를 그리기 위해 Todo를 사용합니다.
    return Scaffold(
      appBar: AppBar(
        title: Text(todo.title),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Text(todo.description),
      ),
    );
  }
}
```


이전 화면에 데이터 반환하기


새로운 화면으로부터 이전 화면으로 데이터를 반환해야하는 경우가 있음. 예를 들어, 사용자에게 두 가지 옵션을 보여주는 화면에서 사용자가 한 옵션을 클릭하면 첫 번째 화면에 알려주고 그에 따른 실행을 하려는 경우 Navigator.pop()을 사용하여 다음과 같이 진행할 수 있음.


1. 홈 화면을 정의함.
2. 선택 창을 띄우는 버튼을 추가.
3. 두 개의 버튼을 가진 선택 창을 보여줍니다.
4. 하나의 버튼을 클릭하면 선택 창을 닫습니다.
5. 선택된 정보를 홈 화면의 snackbar에 보여줍니다.

1. 홈 화면을 정의.
예제에서는 홈 화면에서는 버튼 하나가 있고 버튼을 클릭하면 연동 창을 띄움.

```
class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Returning Data Demo'),
      ),
      // 다음 단계에서 SelectionButton 위젯을 만들 것입니다.
      body: Center(child: SelectionButton()),
    );
  }
}
```

2. 연동 창을 띄우는 버튼을 추가.
 SelectionButton을 만들고 사용자가 클릭했을 때, SelectionScreen을 띄움. SelectionScreen이 결과를 반환할 때까지 대기.
 
 ```
class SelectionButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: Text('Pick an option, any option!'),
    );
  }

  // SelectionScreen을 띄우고 navigator.pop으로부터 결과를 기다리는 메서드
  _navigateAndDisplaySelection(BuildContext context) async {
    // Navigator.push는 Future를 반환합니다. Future는 선택 창에서 
    // Navigator.pop이 호출된 이후 완료될 것입니다.
    final result = await Navigator.push(
      context,
      // 다음 단계에서 SelectionScreen를 만들 것입니다.
      MaterialPageRoute(builder: (context) => SelectionScreen()),
    );
  }
}
```

3. 두 개의 버튼을 가진 선택 창을 표시.

두 개의 버튼을 다른 화면에서 사용자가 하나의 버튼을 클릭하면 현재 창을 닫고 그 결과를 홈 화면에 알려줌.  UI를 정의하고 다음 단계에서는 데이터를 반환하는 코드를 추가.

```
class SelectionScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Pick an option'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // "Yep" 문자열과 함께 이전 화면으로 돌아갑니다...
                },
                child: Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // "Nope" 문자열과 함께 이전 화면으로 돌아갑니다.
                },
                child: Text('Nope.'),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```
4. 하나의 버튼을 클릭하면 창을 닫음.
첫 번째 화면으로 데이터를 반환하기 위해, Navigator.pop() 메서드를 사용. 
```
//Yep 버튼

RaisedButton(
  onPressed: () {
    // Yep 버튼은 결과로 "Yep!"을 반환.
    Navigator.pop(context, 'Yep!');
  },
  child: Text('Yep!'),
);

//Nope 버튼


RaisedButton(
  onPressed: () {
    // Nope 버튼은 결과로 "Nope!"을 반환.
    Navigator.pop(context, 'Nope!');
  },
  child: Text('Nope!'),
);
```

5. 선택된 정보를 홈 화면의 snackbar에 표시함.
 예제에서는 결과 값을 보여줄 수 있도록 Snackbar를 띄우기 위해 SelectionButton의 _navigateAndDisplaySelection  메서드를 수정.

```
_navigateAndDisplaySelection(BuildContext context) async {
  final result = await Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => SelectionScreen()),
  );

  // 선택 창으로부터 결과 값을 받은 후, 이전에 있던 snackbar는 숨기고 새로운 결과 값을
  // 표시.
  Scaffold.of(context)
    ..removeCurrentSnackBar()
    ..showSnackBar(SnackBar(content: Text("$result")));
}
```
완성된 예제

```
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Returning Data',
    home: HomeScreen(),
  ));
}

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Returning Data Demo'),
      ),
      body: Center(child: SelectionButton()),
    );
  }
}

class SelectionButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: Text('Pick an option, any option!'),
    );
  }

  // SelectionScreen을 띄우고 navigator.pop으로부터 결과를 기다리는 메서드
  _navigateAndDisplaySelection(BuildContext context) async {
    // Navigator.push는 Future를 반환합니다. Future는 선택 창에서 
    // Navigator.pop이 호출된 이후 완료될 것입니다.
    final result = await Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => SelectionScreen()),
    );

    // 선택 창으로부터 결과 값을 받은 후, 이전에 있던 snackbar는 숨기고 새로운 결과 값을
    // 표시.
    Scaffold.of(context)
      ..removeCurrentSnackBar()
      ..showSnackBar(SnackBar(content: Text("$result")));
  }
}

class SelectionScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Pick an option'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // 창을 닫고 결과로 "Yep!"을 반환.
                  Navigator.pop(context, 'Yep!');
                },
                child: Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // 창을 닫고 결과로 "Nope!"을 반환.
                  Navigator.pop(context, 'Nope.');
                },
                child: Text('Nope.'),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```


화면을 넘나드는 위젯 애니메이션

한 화면에서 다음 화면으로 전환할 때 애니메이션 효과를 주기 위해 Hero 위젯을 사용함.

1. 같은 이미지를 보여주는 2개의 화면을 만듬.
2. 첫 번째 화면에 Hero 위젯을 추가.
3. 두 번째 화면에 Hero 위젯을 추가.

1. 같은 이미지를 보여주는 2개의 화면을 만듭니다.
이 예제에서는  첫 번째 화면에서 사용자가 이미지를 탭하면 두 번째 화면으로 전환되면서 애니메이션이 발생. 
이 예제는 새로운 화면으로 이동하고, 되돌아오기와 탭 다루기를 사용함.

```
class MainScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Main Screen'),
      ),
      body: GestureDetector(
        onTap: () {
          Navigator.push(context, MaterialPageRoute(builder: (_) {
            return DetailScreen();
          }));
        },
        child: Image.network(
          'https://picsum.photos/250?image=9',
        ),
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GestureDetector(
        onTap: () {
          Navigator.pop(context);
        },
        child: Center(
          child: Image.network(
            'https://picsum.photos/250?image=9',
          ),
        ),
      ),
    );
  }
}
```
2. 첫 번째 화면에 Hero 위젯을 추가.
두 화면을 하나의 애니메이션으로 연결하기 위해, 각 화면에 존재하는 Image 위젯을 Hero 위젯으로 감싸야 함. Hero 위젯에 2개의 인자를 넘겨주어야 함.

`tag`는`Hero` 위젯을 식별하기 위한 객체로 양쪽 모두 동일한 값을 가져야 함.
`child`는 화면 전환 시 애니메이션 효과를 적용할 위젯입니다.

```
Hero(
  tag: 'imageHero',
  child: Image.network(
    'https://picsum.photos/250?image=9',
  ),
);
```
3. 두 번째 화면에 Hero 위젯을 추가함.
첫 번째 화면과의 연동하기 위해, 두 번째 화면의 Image도 첫 번째 화면에 사용한 것과 동일한 tag를 사용한 Hero 위젯으로 감싸주어야 함. 두 번째 화면에 Hero 위젯을 적용하면, 화면 사이의 애니메이션이 동작함.

```
Hero(
  tag: 'imageHero',
  child: Image.network(
    'https://picsum.photos/250?image=9',
  ),
);
 



import 'package:flutter/material.dart';

void main() => runApp(HeroApp());

class HeroApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Transition Demo',
      home: MainScreen(),
    );
  }
}

class MainScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Main Screen'),
      ),
      body: GestureDetector(
        child: Hero(
          tag: 'imageHero',
          child: Image.network(
            'https://picsum.photos/250?image=9',
          ),
        ),
        onTap: () {
          Navigator.push(context, MaterialPageRoute(builder: (_) {
            return DetailScreen();
          }));
        },
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GestureDetector(
        child: Center(
          child: Hero(
            tag: 'imageHero',
            child: Image.network(
              'https://picsum.photos/250?image=9',
            ),
          ),
        ),
        onTap: () {
          Navigator.pop(context);
        },
      ),
    );
  }
}

```

슬라이 버는 스크롤 가능 영역의 일부입니다. 은색을 사용하여 사용자 정의 스크롤 효과를 얻을 수 있습니다.

SliverList , SliverGrid 및 SliverAppBar를 포함하여 Flutter에서 슬라이 버를 구현하는 방법에 대한 자세한 내용은 Medium 's Flutter Publication 에 대한 기사 인 Slivers , DeMystified 를 참조하십시오 .


https://medium.com/flutter/slivers-demystified-6ff68ab0296f














 
 
