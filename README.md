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



Your app can access its assets through an
[`AssetBundle`][] object.

The two main methods on an asset bundle allow you to load a
string/text asset (`loadString()`) or an image/binary asset (`load()`)
out of the bundle, given a logical key. The logical key maps to the path
to the asset specified in the `pubspec.yaml` file at build time.

### Loading text assets

Each Flutter app has a [`rootBundle`][]
object for easy access to the main asset bundle. It is possible to
load assets directly using the `rootBundle` global static from
`package:flutter/services.dart`.

However, it's recommended to obtain the AssetBundle for the current
BuildContext using [`DefaultAssetBundle`][].
Rather than the default asset bundle that was built with the app, this
approach enables a parent widget to substitute a different AssetBundle
at run time, which can be useful for localization or testing
scenarios.

Typically, you'll use `DefaultAssetBundle.of()` to indirectly load an
asset, for example a JSON file, from the app's runtime `rootBundle`.

{% comment %}
  Need example here to show obtaining the AssetBundle for the current
  BuildContext using DefaultAssetBundle.of
{% endcomment %}

Outside of a Widget context, or when a handle to an AssetBundle is not
available, you can use `rootBundle` to directly load such assets.
For example:

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


### Asset images in package dependencies {#from-packages}

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
스택 규칙을 사용하여 하위 위젯 세트를 관리하는 위젯입니다.

많은 앱은 가장 최근에 방문한 페이지와 함께 이전 페이지 위에 시각적으로 오버레이 를 사용하여 논리적 기록을 표시하기 위해 위젯 계층 구조 맨 위에 네비게이터가 있습니다 . 이 패턴을 사용하면 네비게이터가 오버레이에서 위젯을 움직여 한 페이지에서 다른 페이지로 시각적으로 전환 할 수 있습니다. 마찬가지로, 네비게이터를 사용하여 대화 상자 위젯을 현재 페이지 위에 배치하여 대화 상자를 표시 할 수 있습니다.

네비게이터 사용
모바일 앱은 일반적으로 '화면'또는 '페이지'라는 전체 화면 요소를 통해 콘텐츠를 공개합니다. Flutter에서 이러한 요소를 경로라고하며 네비게이터 위젯으로 관리합니다 . 네비게이터는 Route 객체의 스택을 관리하고 Navigator.push 및 Navigator.pop 과 같은 스택 관리 방법을 제공합니다 .

사용자 인터페이스가 스택의이 패러다임에 맞으면 사용자가 스택 의 이전 요소로 다시 탐색 할 수 있어야 하는 경우 경로와 탐색기를 사용하는 것이 적절합니다. Android와 같은 특정 플랫폼에서 시스템 UI는 사용자가 애플리케이션 스택에서 이전 경로로 다시 이동할 수있는 뒤로 버튼 (응용 프로그램 범위를 벗어난)을 제공합니다. 이 기본 제공 탐색 메커니즘이없는 플랫폼에서는 일반적으로 Scaffold.appBar 속성에 사용되는 AppBar를 사용하여 사용자 탐색을 위해 뒤로 버튼을 자동으로 추가 할 수 있습니다.

전체 화면 경로 표시
네비게이터를 직접 만들 수 있지만 WidgetsApp 또는 MaterialApp 위젯으로 만든 네비게이터를 사용하는 것이 가장 일반적 입니다. Navigator.of 로 해당 네비게이터를 참조 할 수 있습니다 .

MaterialApp는 세트 일까지 가장 간단한 방법입니다. MaterialApp 의 집은 하단의 경로가된다 네비게이터 의 스택 '. 앱이 시작될 때 표시됩니다.

void main() {
  runApp(MaterialApp(home: MyAppHome()));
}
스택에서 새 경로를 푸시하기 위해 화면에 표시 할 항목을 생성하는 빌더 기능을 사용하여 MaterialPageRoute 인스턴스를 만들 수 있습니다 . 예를 들면 다음과 같습니다.

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
라우트는 푸시 및 팝시기에 따라 다른 컨텍스트에서 빌드 및 재 빌드되므로 하위 위젯 대신 빌더 함수로 위젯을 정의합니다.

보시다시피, 네비게이터의 pop 메소드를 사용하여 앱의 홈 페이지를 표시하는 새로운 경로를 팝업 할 수 있습니다

Navigator.pop(context);
스캐 폴드는 자동으로 앱 바에 '뒤로'버튼을 추가하기 때문에 스캐 폴드 가있는 경로에서 네비게이터를 팝업시키는 위젯을 제공 할 필요는 없습니다 . 뒤로 버튼을 누르면 Navigator.pop 이 호출됩니다. Android에서는 시스템 뒤로 버튼을 눌러도 동일한 작업을 수행합니다.

명명 된 네비게이터 경로 사용
모바일 앱은 종종 많은 수의 경로를 관리하며 이름으로 가장 쉽게 참조 할 수 있습니다. 일반적으로 경로 이름은 경로와 유사한 구조를 사용합니다 (예 : '/ a / b / c'). 앱의 홈페이지 경로 이름은 기본적으로 '/'입니다.

MaterialApp는 만들 수 있습니다 Map<String, WidgetBuilder>를 생성하는 빌더 기능 경로의 이름에서지도한다. MaterialApp는 그 네비게이터의 값 만들려면이 맵을 사용 onGenerateRoute 콜백을.

void main() {
  runApp(MaterialApp(
    home: MyAppHome(), // becomes the route named '/'
    routes: <String, WidgetBuilder> {
      '/a': (BuildContext context) => MyPage(title: 'page A'),
      '/b': (BuildContext context) => MyPage(title: 'page B'),
      '/c': (BuildContext context) => MyPage(title: 'page C'),
    },
  ));
}
이름으로 경로를 표시하려면

Navigator.pushNamed(context, '/b');
경로는 값을 반환 할 수 있습니다
경로를 푸시하여 사용자에게 값을 요청하면 pop 메소드의 결과 매개 변수 를 통해 값을 리턴 할 수 있습니다 .

경로를 푸시하는 메소드는 Future를 리턴합니다 . 경로는 팝될 때 Future는 해결되며 Future 값은 pop 메소드의 result 매개 변수입니다.

예를 들어 사용자에게 '확인'을 눌러 작업을 확인하도록 요청하려면 Navigator.pushawait 의 결과를 얻을 수 있습니다 .

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
사용자가 'OK'를 누르면 값이 true가됩니다. 사용자가 비계의 뒤로 버튼을 눌러 경로를 벗어나면 값이 null이됩니다.

경로를 사용하여 값을 반환하는 경우 경로의 유형 매개 변수는 pop 의 결과 유형과 일치해야합니다 . 그렇기 때문에 또는 MaterialPageRoute<bool>대신에 사용 했습니다 . (유형을 지정하지 않으려면 괜찮습니다.)MaterialPageRoute<void>MaterialPageRoute

팝업 경로
경로가 전체 화면을 가리지 않아도됩니다. PopupRoute 는 현재 화면 이 보이 도록 부분적으로 만 불투명 할 수 있는 ModalRoute.barrierColor로 화면을 덮습니다 . 팝업 경로는 아래 위젯에 대한 입력을 차단하기 때문에 "모달"입니다.

팝업 경로를 생성하고 표시하는 기능이 있습니다. 예를 들어 showDialog , showMenu 및 showModalBottomSheet 입니다. 이 함수는 위에서 설명한대로 푸시 된 경로의 미래를 반환합니다. 발신자는 경로가 튀어 나올 때 조치를 취하거나 경로 값을 발견하기 위해 리턴 된 값을 기다릴 수 있습니다.

PopupMenuButton 및 DropdownButton 과 같은 팝업 경로를 생성하는 위젯도 있습니다 . 이 위젯은 PopupRoute의 내부 서브 클래스를 작성하고 Navigator의 push 및 pop 메소드를 사용하여이를 표시 및 해제합니다.

맞춤 경로
PopupRoute , ModalRoute 또는 PageRoute 와 같은 위젯 라이브러리 라우트 클래스 중 하나의 자체 서브 클래스를 작성하여 라우트, 라우트의 모달 배리어의 색상 및 동작 및 라우트의 다른 측면을 표시하는 데 사용되는 애니메이션 전환을 제어 할 수 있습니다.

PageRouteBuilder의 클래스는 가능한 콜백의 관점에서 사용자 지정 경로를 정의 할 수 있습니다. 다음은 경로가 나타나거나 사라질 때 자식을 회전하고 페이드하는 예제입니다. 이 경로는 opaque: false팝업 경로와 마찬가지로을 지정하기 때문에 전체 화면을 가리지 않습니다.

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
페이지 경로는 "페이지"와 "전환"의 두 부분으로 구성됩니다. 페이지는 transitionsBuilder함수에 전달 된 자식의 자손이 됩니다. 그것은 (과 생략의 애니메이션 매개 변수에 의존하지 않기 때문에 일반적으로 페이지는 한 번 구축 _ 및 __이 예에서). 전환은 지속 시간 동안 모든 프레임에 구축됩니다.

중첩 네비게이터
앱은 둘 이상의 네비게이터를 사용할 수 있습니다. 하나의 네비게이터를 다른 네비게이터 아래에 중첩하여 탭 내비게이션, 사용자 등록, 매장 체크 아웃 또는 전체 애플리케이션의 하위 섹션을 나타내는 기타 독립적 인 여정과 같은 "내부 여정"을 작성할 수 있습니다.

실제 예
iOS 앱은 각 탭이 자체 탐색 기록을 유지하는 탭 탐색을 사용하는 것이 일반적입니다. 따라서 각 탭에는 고유 한 네비게이터 가있어 일종의 "병렬 내비게이션"을 만듭니다.

탭의 병렬 탐색 외에도 탭을 완전히 덮는 전체 화면 페이지를 시작할 수 있습니다. 예를 들어, 온 보딩 흐름 또는 경고 대화 상자입니다. 따라서 탭 탐색 위에 있는 "루트" 네비게이터 가 있어야합니다 . 그 결과, 탭의 각 네비게이터 의 실제로 중첩되는 네비게이터 단일 루트 아래의 앉아 Navigator를 .

중첩 된 네비게이터 의 탭 탐색에 앉아에 WidgetApp와 CupertinoTabView 당신이 중첩에 대해 걱정할 필요가 없습니다, 네비게이터 이 상황에서의, 그러나 중첩 된 실제 예제 네비게이터 (S)가 사용된다.


링크
견본앱 샘플
다음 예제는 중첩 된 네비게이터 를 사용하여 독립형 사용자 등록 과정을 제시하는 방법을 보여줍니다 .
이 예제에서는 두 개의 Navigator 를 사용 하여 중첩 된 Navigator 를 보여 주지만 단일 Navigator 만을 사용하여 유사한 결과를 얻을 수 있습니다 .

flutter run --route=/signup홈 페이지 대신 가입 절차로 시작하려면 이 예제를 실행 하십시오.

할당
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
Navigator.of 는 지정된 BuildContext 에서 가장 가까운 상위 네비게이터 에서 작동합니다 . 중첩 된 네비게이터 가 생성 되는 대규모 메소드에서 특히 의도 된 네비게이터 아래 에 BuildContext 를 제공하십시오 . 작성기 액세스 (A)에 사용할 수있는 위젯 BuildContext 위젯 서브 트리의 원하는 위치.build


새로운 화면으로 이동하고, 되돌아오기
Named route로 화면 전환
새로운 화면으로 데이터 보내기
이전 화면에 데이터 반환하기
화면을 넘나드는 위젯 애니메이션



새로운 화면으로 이동하고, 되돌아오기
문서
Cookbook
Navigation
새로운 화면으로 이동하고, 되돌아오기
대부분의 앱은 여러 종류의 정보를 보여주기 위해 여러 화면을 갖고 있습니다. 예를 들어, 어떤 앱이 상품 목록을 보여주는 화면을 갖고 있다고 한다면, 사용자가 한 상품을 선택했을 때 새로운 화면에서 해당 상품의 상세 정보를 볼 수 있습니다.

용어: Flutter에서 screen 과 page 는 route 로 불립니다. 이 문서의 나머지 부분에서는 Route라고 언급합니다.

Route는 Android의 Activity, iOS의 ViewController와 동일합니다. Flutter에서는 Route 역시 위젯입니다.

새로운 route로 어떻게 이동할까요? Navigator를 사용하세요. 여기서는 아래와 같은 단계로 진행합니다:

다음 섹션에서 두 개의 route에서 화면 전환하는 방법을 보여주겠습니다. 아래 단계를 따라오세요:

두 개의 route를 생성합니다.
Navigator.push()를 사용하여 두 번째 route로 전환합니다.
Navigator.pop()을 사용하여 첫 번째 route로 되돌아 옵니다.
1. 두 개의 route를 생성합니다.
우선 두 개의 route를 생성합니다. 간단한 예제이기 때문에 각 route에는 버튼 하나 씩만 넣어줄 겁니다. 첫 번째 route의 버튼을 누르면 두 번째 route로 화면 전환되며, 두 번째 route의 버튼을 누르면 첫 번째 route로 되돌아 옵니다.

우선 시각적 구조를 세팅합니다.

content_copy
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
2. Navigator.push()를 사용하여 두 번째 route로 전환합니다.
새로운 route로 전환하기 위해 Navigator.push() 메서드를 사용하세요. push() 메서드는 Route를 Navigator에 의해 관리되는 route 스택에 추가합니다. Route는 어디서 오는 걸까요? 직접 생성하거나, 새로운 route로 이동 시 플랫폼 특화된 애니메이션을 사용하기 좋은 MaterialPageRoute의 을 사용할 수 있습니다.

FirstRoute 위젯의 build() 메서드에서 onPressed() 콜백을 수정하세요:

content_copy
// Within the `FirstRoute` widget
onPressed: () {
  Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => SecondRoute()),
  );
}
3. Navigator.pop()을 사용하여 첫 번째 route로 되돌아 옵니다.
두 번째 route를 닫고 이전 route로 어떻게 되돌아 갈까요? Navigator.pop() 메서드를 사용하세요. pop() 메서드는 Navigator에 의해 관리되는 route 스택에서 현재 Route를 제거합니다.

이전 route로 되돌아 가기 위해, SecondRoute 위젯의 onPressed() 콜백을 수정하세요:

content_copy
// Within the SecondRoute widget
onPressed: () {
  Navigator.pop(context);
}
완성된 예제
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



Named route로의 화면 전환
문서
Cookbook
Navigation
Named route로의 화면 전환
새로운 화면으로 이동하고, 되돌아오기 예제에서는 Route를 생성하고, Navigator 에 전달하여 새로운 화면으로 전환하는 방법을 배웠습니다.

하지만 만약 앱의 다른 많은 부분들에서 동일한 화면으로 이동하고자 한다면, 중복된 코드가 생기게 됩니다. 이러한 경우 named route 를 정의하여 화면 전환에 사용하는 방법이 해결책이 될 수 있습니다.

Named route를 사용하기 위해 Navigator.pushNamed 함수를 사용할 수 있습니다. 이 예제에서는 named route를 사용하는 방법을 보여주기 위해 기존 예제의 기능을 사용하고, 아래와 같은 순서로 진행합니다:

두 개의 화면 만들기.
Route 정의하기.
Navigator.pushNamed를 사용하여 두 번째 화면으로 전환하기.
Navigator.pop을 사용하여 첫 번째 화면으로 돌아가기.
1. 두 개의 화면 만들기
우선, 본 예제를 시작하기 위해 두 개의 화면이 필요합니다. 첫 번째 화면에는 두 번째 화면으로 이동하기 위한 버튼 하나가 있고, 두 번째 화면에는 다시 첫 번째 화면으로 돌아가기 위한 버튼이 있습니다.

content_copy
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
            // 클릭하면 두 번째 화면으로 전환합니다!
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
            // 클릭하면 첫 번째 화면으로 돌아갑니다!
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
2. Route 정의하기
다음으로, MaterialApp 생성자에 initialRoute와 routes 이름의 추가 프로퍼티를 제공하여 route를 정의하겠습니다.

initialRoute 프로퍼티는 앱의 시작점을 나타내는 route를 정의하고, routes 프로퍼티는 이용가능한 named route와 해당 route로 이동했을 때 빌드될 위젯을 정의합니다.

content_copy
MaterialApp(
  // "/"으로 named route와 함께 시작합니다. 본 예제에서는 FirstScreen 위젯에서 시작합니다.
  initialRoute: '/',
  routes: {
    // "/" Route로 이동하면, FirstScreen 위젯을 생성합니다.
    '/': (context) => FirstScreen(),
    // "/second" route로 이동하면, SecondScreen 위젯을 생성합니다.
    '/second': (context) => SecondScreen(),
  },
);
 경고: initialRoute를 사용한다면, home프로퍼티를 정의하지 마세요.

3. 두 번째 화면으로 전환하기
위젯과 route를 정의했다면,
Navigator.pushNamed() 메서드로 화면 전환을 호출하세요. 이 함수는 Flutter에게 앞서 routes 테이블에 정의한 위젯을 생성하고 그 화면을 시작하도록 요청합니다.

FirstScreen 위젯의 build() 메서드에 onPressed() 콜백을 다음과 같이 수정하세요:

content_copy
// `FirstScreen` 위젯의 콜백
onPressed: () {
  // Named route를 사용하여 두 번째 화면으로 전환합니다.
  Navigator.pushNamed(context, '/second');
}
4. 첫 번째 화면으로 돌아가기
첫 번째 페이지로 되돌아가기 위해 Navigator.pop() 함수를 사용합니다.

content_copy
// SecondScreen 위젯의 콜백
onPressed: () {
  // 현재 route를 스택에서 제거함으로써 첫 번째 스크린으로 되돌아 갑니다.
  Navigator.pop(context);
}
완성된 예제
content_copy
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
            // Named route를 사용하여 두 번째 화면으로 전환합니다.
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


새로운 화면으로 데이터 보내기
문서
Cookbook
Navigation
새로운 화면으로 데이터 보내기
종종 새로운 화면으로 단순히 이동하는 것 뿐만 아니라 데이터를 넘겨주어야 할 때도 있습니다. 예를 들어, 사용자가 선택한 아이템에 대한 정보를 같이 넘겨주고 싶은 경우가 있습니다.

기억하세요: 화면은 단지 위젯입니다. 이 예제에서 Todo 리스트를 만들 것입니다. Todo를 선택하면 새로운 화면(위젯)으로 이동하면서 선택한 todo에 대한 정보를 보여줄 것입니다. 여기서는 아래와 같은 단계로 진행합니다:

Todo 클래스를 정의합니다.
Todo 리스트를 보여줍니다.
Todo에 대한 상세 정보를 보여줄 수 있는 화면을 생성합니다.
상세 화면으로 이동하면서 데이터를 전달합니다.
1. Todo 클래스를 정의합니다.
우선 Todo를 표현하기 위한 간단한 정보가 필요합니다. 이 예제에서는 두 가지의 데이터를 갖고 있는 클래스를 정의할 것입니다: 제목과 상세 설명

content_copy
class Todo {
  final String title;
  final String description;

  Todo(this.title, this.description);
}
2. Todo 리스트를 보여줍니다.
두 번째로, Todo 리스트를 보여줄 것입니다. 이 예제에서는 20개의 todo를 생성하고 ListView를 사용하여 보여줄 것입니다. List에 대한 더 자세한 정보는 Use lists에서 확인할 수 있습니다.

Todo 리스트 생성하기
content_copy
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
여기까지 그런대로 잘 되었습니다. 이제 20개의 Todo를 생성하여 ListView에 보여줄 것입니다!

3. Todo에 대한 상세 정보를 보여줄 수 있는 화면을 생성합니다.
이제 두 번째 화면을 만들겠습니다. 화면의 제목은 Todo의 제목을 포함하며 본문에는 상세 설명을 보여줄 것입니다.

두 번째 화면은 일반적인 StatelssWidget이므로, 생성자 매개변수로 Todo를 받을 수 있게 간단한 방법으로 강제하겠습니다. 그러면 주어진 Todo를 활용하여 UI를 그릴 수 있습니다.

content_copy
class DetailScreen extends StatelessWidget {
  // Todo를 들고 있을 필드를 선언합니다.
  final Todo todo;

  // 생성자 매개변수로 Todo를 받도록 강제합니다.
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
4. 상세 화면으로 이동하면서 데이터를 전달합니다.
앞서 작성한 DetailsScreen으로 화면 전환할 준비가 되었습니다. 본 예제에서는 사용자가 Todo 리스트 중 하나를 선택했을 때, DetailsScreen으로 화면 전환하도록 할 것입니다. 그와 동시에 DetailsScreen에 Todo를 전달할 것입니다.

사용자의 탭 동작을 감지하기 위해, ListTile 위젯에 onTap 콜백을 작성할 것입니다. onTap 콜백 내에서 다시 한 번 Navigator.push 메서드를 사용할 것입니다.

content_copy
ListView.builder(
  itemCount: todos.length,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text(todos[index].title),
      // 사용자가 ListTile을 선택하면, DetailScreen으로 이동합니다.
      // DetailScreen을 생성할 뿐만 아니라, 현재 todo를 같이 전달해야
      // 한다는 것을 명심하세요.
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
완성된 예제
content_copy
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
            // 사용자가 ListTile을 선택하면, DetailScreen으로 이동합니다.
            // DetailScreen을 생성할 뿐만 아니라, 현재 todo를 같이 전달해야
            // 한다는 것을 명심하세요.
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
  // Todo를 들고 있을 필드를 선언합니다.
  final Todo todo;

  // 생성자는 Todo를 인자로 받습니다.
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



이전 화면에 데이터 반환하기
문서
Cookbook
Navigation
이전 화면에 데이터 반환하기
새로운 화면으로부터 이전 화면으로 데이터를 반환해야하는 경우가 있습니다. 예를 들어, 사용자에게 두 가지 옵션을 보여주는 화면이 있다고 합시다. 사용자가 한 옵션을 선택했을 때 그것을 첫 번째 화면에 알려주면 그에 맞는 동작을 할 수 있을 것입니다!

어떻게 구현할 수 있을까요? Navigator.pop()을 사용하여 아래 순서로 진행하세요:

홈 화면을 정의합니다.
선택 창을 띄우는 버튼을 추가합니다.
두 개의 버튼을 가진 선택 창을 보여줍니다.
하나의 버튼을 클릭하면 선택 창을 닫습니다.
선택된 정보를 홈 화면의 snackbar에 보여줍니다.
1. 홈 화면을 정의합니다.
홈 화면에서는 버튼 하나를 보여줄 것입니다. 버튼을 클릭하면 선택 창을 띄울 것입니다!

content_copy
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
2. 선택 창을 띄우는 버튼을 추가합니다.
이제 SelectionButton을 만들 차례입니다. 선택 버튼은:

사용자가 클릭했을 때, SelectionScreen을 띄울 것입니다.
SelectionScreen이 결과를 반환할 때까지 대기할 것입니다.
content_copy
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
3. 두 개의 버튼을 가진 선택 창을 보여줍니다.
이제 선택 창을 만들 차례입니다. 선택 창은 두 개의 버튼을 갖고 있으며, 사용자가 하나의 버튼을 클릭하면 선택 창을 닫고 그 결과를 홈 화면에 알려줄 것입니다.

이번 단계에서는 UI를 정의합니다. 다음 단계에서는 데이터를 반환하는 코드를 추가합니다.

content_copy
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
4. 하나의 버튼을 클릭하면 선택 창을 닫습니다.
이제 앞서 만든 두 개 버튼의 onPressed() 콜백을 수정할 차례입니다. 첫 번째 화면으로 데이터를 반환하기 위해, Navigator.pop() 메서드를 사용할 것입니다. 이 메서드 2번째 인자 result에 Future로 반환되는 SelectionButton의 결과를 추가합니다.

Navigator.pop는 result라고 명시된 두 번째 인자를 선택적으로 받습니다. 만약 결과 값을 인자로 제공한다면, SelectionButton의 Future에 실려 반환될 것입니다.

Yep 버튼
content_copy
RaisedButton(
  onPressed: () {
    // Yep 버튼은 결과로 "Yep!"을 반환합니다.
    Navigator.pop(context, 'Yep!');
  },
  child: Text('Yep!'),
);
Nope 버튼
content_copy
RaisedButton(
  onPressed: () {
    // Nope 버튼은 결과로 "Nope!"을 반환합니다.
    Navigator.pop(context, 'Nope!');
  },
  child: Text('Nope!'),
);
5. 선택된 정보를 홈 화면의 snackbar에 보여줍니다.
선택 창을 띄우고 결과를 기다리고 있습니다. 이제 결과 값을 갖고 무언가 할 차례입니다.

이 예제에서는 결과 값을 보여줄 수 있도록 Snackbar를 띄우겠습니다. 이 작업을 하기 위해 SelectionButton의 _navigateAndDisplaySelection 메서드를 수정할 것입니다.

content_copy
_navigateAndDisplaySelection(BuildContext context) async {
  final result = await Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => SelectionScreen()),
  );

  // 선택 창으로부터 결과 값을 받은 후, 이전에 있던 snackbar는 숨기고 새로운 결과 값을
  // 보여줍니다.
  Scaffold.of(context)
    ..removeCurrentSnackBar()
    ..showSnackBar(SnackBar(content: Text("$result")));
}
완성된 예제
content_copy
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
    // 보여줍니다.
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
                  // 창을 닫고 결과로 "Yep!"을 반환합니다.
                  Navigator.pop(context, 'Yep!');
                },
                child: Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // 창을 닫고 결과로 "Nope!"을 반환합니다.
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



화면을 넘나드는 위젯 애니메이션
문서
Cookbook
Navigation
화면을 넘나드는 위젯 애니메이션
한 화면에서 다른 화면으로 넘어갈 때, 사용자에게 어떠한 가이드를 주는 것은 종종 도움이 됩니다. 앱에서 사용할 수 있는 일반적인 기술은 다음 화면으로 전환할 때 위젯에 애니메이션 효과를 주는 것입니다. 이러한 방법은 두 화면을 이어주는 시각적 연결 고리를 만들어 줍니다.

한 화면에서 다음 화면으로 전환할 떄 애니메이션 효과를 주기 위해 Hero 위젯을 사용하세요. 여기서는 아래와 같은 단계로 진행합니다:

같은 이미지를 보여주는 2개의 화면을 만듭니다.
첫 번째 화면에 Hero 위젯을 추가합니다.
두 번째 화면에 Hero 위젯을 추가합니다.
1. 같은 이미지를 보여주는 2개의 화면을 만듭니다.
이 예제에서는 같은 이미지를 두 개의 화면 모두에 보여줄 것입니다. 첫 번째 화면에서 사용자가 이미지를 탭하면 두 번째 화면으로 전환되면서 애니메이션이 발생합니다. 일단 시각적 구조를 만들고, 본격적인 애니메이션은 다음 단계에서 다루겠습니다.

 참고: 이 예제는 새로운 화면으로 이동하고, 되돌아오기 와 탭 다루기 예제를 기반으로 합니다.

content_copy
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
2. 첫 번째 화면에 Hero 위젯을 추가합니다.
두 화면을 하나의 애니메이션으로 연결하기 위해, 각 화면에 존재하는 Image위젯을 Hero위젯으로 감쌉니다. Hero 위젯에 2개의 인자를 넘겨주어야 합니다:

`tag`
`Hero`위젯을 식별하기 위한 객체로 양쪽 모두 동일한 값을 가져야 합니다.
`child`
화면 전환 시 애니메이션 효과를 적용할 위젯입니다.
content_copy
Hero(
  tag: 'imageHero',
  child: Image.network(
    'https://picsum.photos/250?image=9',
  ),
);
3. 두 번째 화면에 Hero 위젯을 추가합니다.
첫 번째 화면과의 연결하기 위해, 두 번째 화면의 Image도 첫 번째 화면에 사용한 것과 동일한 tag를 사용한 Hero 위젯으로 감싸주어야 합니다.

두 번째 화면에 Hero 위젯을 적용하기만 하면, 화면 사이의 애니메이션이 동작합니다.

content_copy
Hero(
  tag: 'imageHero',
  child: Image.network(
    'https://picsum.photos/250?image=9',
  ),
);
 참고: 이 코드는 첫 번째 화면에 사용했던 것과 동일한 코드입니다. 코드를 반복하기보다는 재사용 가능한 위젯을 만들어 사용하는 게 모범 사례입니다. 이 예제에서는 단순화하기 위해 동일 코드를 양쪽 위젯에서 사용합니다.

완성된 예제
content_copy
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



슬라이 버는 스크롤 가능 영역의 일부입니다. 은색을 사용하여 사용자 정의 스크롤 효과를 얻을 수 있습니다.

SliverList , SliverGrid 및 SliverAppBar를 포함하여 Flutter에서 슬라이 버를 구현하는 방법에 대한 자세한 내용은 Medium 's Flutter Publication 에 대한 기사 인 Slivers , DeMystified 를 참조하십시오 .














 
 
