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
 `pubspec.yaml`을 사용하여 지정함.
 
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
	
	직접 디렉토리에 위치한 assets만 포함되어 하위디렉토리인 경우 각 하위디렉토리를 다시 지정해야 함.
 
 
