<!--
 * @Descripttion:
 * @version:
 * @Author: lianglli
 * @Date: 2021-06-24 19:57:23
 * @LastEditors: lianglli
 * @LastEditTime: 2021-06-24 19:58:43
-->

---

title: 短信/H5 调起 APP
date: 2021-06-24 19:57:23
tags:

---

#### 需求

5G 短信消息中的链接需要点击调起 APP，可先只支持 Andriod
测试从 H5 调起 App，如用户未装 APP，则跳转下载页面。可先只支持 Andriod

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2491a1d8dd2b4f949f7a455c1fa57442~tplv-k3u1fbpfcp-watermark.image)

#### 技术栈

ReactNative App

#### 实现步骤

- Andriod 包中设置支持 brower 打开(host/scheme 可随意设置，后面使用这两个配置打开)
  ```
  <intent-filter>
                   <action android:name="android.intent.action.VIEW" />
                   <category android:name="android.intent.category.DEFAULT" />
                   <category android:name="android.intent.category.BROWSABLE" />
                   <data
                       android:host="com.xxx"
                       android:scheme="app" />
               </intent-filter>
  ```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec0d0372d1164101b0e040efea5037a0~tplv-k3u1fbpfcp-watermark.image)

- 打安卓包
- 短信中配置链接  
  app://com.xxx/
- H5 页面
  需手动点击

```
<!DOCTYPE html>
<html>
  <head>
    <title>调起App</title>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"
    />

  </head>

  <body>
    <div onclick="check()" id="check" style="font-size: 18px">
      <i style="color: #469be9; font-style: normal">点击此处</i>
    </div>
    <script type="text/javascript">

      function check() {
        if (navigator.userAgent.match(/(iPhone|iPod|iPad);?/i)) {
          alert("暂不支持苹果系统");
        } else if (navigator.userAgent.match(/android/i)) {
          try {
            window.location.href = "app://com.xxx/"; //schema链接或者universal link
            window.setTimeout(function () {
              window.location.href =
                "https://downloadurlp="; //android下载地址
            }, 3000);
          } catch (e) {
            alert(JSON.stringify(e));
          }
        }
      }
    </script>
  </body>
</html>

```

#### 常见问题

- **打包报错：glog-0.3.4/test-driver Couldn't follow symbolic link.**  
  删除 test-driver
- **MACBOOK(OSX)最升到 11.0.1 后 android 打包报错 Could not find tools.jar. Please check that /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home contains a valid JDK installation.**  
   手动复制/Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home/lib/tools.jar 到/Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib

```
sudo cp /Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home/lib/tools.jar /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib
```

#### 参考文章

- https://ld246.com/article/1497235254333
- https://www.cnblogs.com/johnjackson/p/14040958.html
- https://blog.csdn.net/qq_37788558/article/details/77232528
