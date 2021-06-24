---
title: 使用 react-native-amap-geolocation
date: 2020-09-28 13:31:36
tags: react-native
---

# 背景

同时使用

```
"react-native-amap-geolocation": "^1.1.2",
"react-native-amap3d": "^2.0.1"

```

# 配置

## 安卓

**android 文件夹下**
settings.gradle 添加

```
include ':react-native-amap-geolocation'
project(':react-native-amap-geolocation').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-amap-geolocation/lib/android')

```

复制代码 app/src/main/项目名/MainApplication.java 添加

```
import cn.qiuxiang.react.geolocation.AMapGeolocationPackage;

```

复制代码添加

```
@Override
protected List getPackages() {
  return Arrays.<ReactPackage>asList(
         new AMapGeolocationPackage(),
         ...
         );
}
```

复制代码 app/build.gradle 添加

```
dependencies {
          implementation project(':react-native-amap-geolocation')
     ...
     }

```

## IOS

**ios 文件夹下**
Podfile 添加

```
   pod 'react-native-amap-geolocation', :path => '../node_modules/react-native-amap-geolocation/lib/ios'

```

复制代码进入 ios 文件夹下

```
pod install

```

进入 Xcode 引入 Pods 文件夹下的 AMap 相关
![xcode](xcode.png)

App.js

```
import {
  init,
  Geolocation,
  addLocationListener,
  start,
  stop,
  setLocatingWithReGeocode,
  setNeedAddress,
} from "react-native-amap-geolocation";

  async componentDidMount() {
      this.initLocation();
  }
  async initLocation() {
    if (Platform.OS === "ios") {
      // ios
      setLocatingWithReGeocode(true);
    } else {
      // 对于 Android 需要自行根据需要申请权限
      await PermissionsAndroid.requestMultiple([
        PermissionsAndroid.PERMISSIONS.ACCESS_FINE_LOCATION,
        PermissionsAndroid.PERMISSIONS.ACCESS_COARSE_LOCATION,
      ]);
    }
    // 使用自己申请的高德 App Key 进行初始化
    await init({
      ios: "ioskey",
      android: "ankey",
    });


    Geolocation.getCurrentPosition(({ coords }) => {
      console.log(coords, "llllll000000000");
    });
    // 添加定位监听函数
    addLocationListener((location) => console.log(location, "??????oooo"));

    // 开始连续定位
    start();
  }

```

重新运行 App
![use](use.png)
