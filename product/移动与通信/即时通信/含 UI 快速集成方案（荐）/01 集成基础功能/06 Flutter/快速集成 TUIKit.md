## 什么是 TUIKit
Flutter TUIKit 是基于 Flutter IM SDK 实现的一套 UI 组件，其中包含会话、聊天、关系链、群组等功能，基于 UI 组件您可以像搭积木一样快速搭建起自己的业务逻辑。其构架图如下：
![](https://qcloudimg.tencent-cloud.cn/raw/f704b22c049c9915ffa5ccdca2aeab85.png)

本 TUIKit 组件库及配套业务逻辑代码 [im-flutter-uikit](https://github.com/tencentyun/TIMSDK/tree/master/Flutter/Demo/im-flutter-uikit) 已开源，您可引入在线版本，也可 fork 后本地引入使用。

目前包含的组件如下：

- [TIMUIKitCore](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitCore/) 核心
- [TIMUIKitConversation](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitConversation/) 会话列表
- [TIMUIKitChat](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitChat/) 聊天区域，发送消息+历史消息列表
- [TIMUIKitContact](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitContact/) 联系人列表
- [TIMUIKitProfile](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitProfile/) 用户资料查看及关系管理
- [TIMUIKitGroupProfile](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitGroupProfile/) 群资料展示与管理
- [TIMUIKitGroup](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitGroup/) 我的群聊
- [TIMUIKitBlackList](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitBlackList/) 黑名单
- [TIMUIKitNewContact](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitNewContact/) 新的联系人申请
- [TIMUIKitSearch](https://cloud.tencent.com/document/product/269/79121) 本地搜索

![](https://qcloudimg.tencent-cloud.cn/raw/f140dd76be01a65abfb7e6ba2bf50ed5.png)

>?
>
> 上图 TUIKit 界面语言支持自动或手动在 **简体中文/繁体中文/英文** 间切换。如有多语言相关咨询，可 [联系我们](#contact)。

## 操作步骤
如下会介绍如何使用 Flutter TUIKit 快速构建一个简单的即时通信应用。

### 步骤1: 创建 Flutter 应用并添加权限
请参见 [Flutter 文档](https://flutter.cn/docs/get-started/test-drive?tab=terminal) 快速创建一个 Flutter 应用。
#### 配置权限[](id:permission)

由于 TUIKit 运行，需要拍摄/相册/录音/网络等权限，需要您在 Native 的文件中手动声明，才可正常使用相关能力。

**Android**

打开 `android/app/src/main/AndroidManifest.xml` ，在 `<manifest></manifest>`中，添加如下权限。
```xml
    <uses-permission
        android:name="android.permission.INTERNET"/>
    <uses-permission
        android:name="android.permission.RECORD_AUDIO"/>
    <uses-permission
        android:name="android.permission.FOREGROUND_SERVICE"/>
    <uses-permission
        android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission
        android:name="android.permission.VIBRATE"/>
    <uses-permission
        android:name="android.permission.ACCESS_BACKGROUND_LOCATION"/>
    <uses-permission
        android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission
        android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission
        android:name="android.permission.CAMERA"/>
```

**iOS**

打开 `ios/Podfile` ，在文件末尾新增如下权限代码。
```
post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
    target.build_configurations.each do |config|
          config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
            '$(inherited)',
            'PERMISSION_MICROPHONE=1',
            'PERMISSION_CAMERA=1',
            'PERMISSION_PHOTOS=1',
          ]
        end
  end
end
```

>?
> 
> 如您需要用到推送能力，还需要添加推送相关权限，详情可查看 [Flutter 厂商消息推送插件集成指南](https://cloud.tencent.com/document/product/269/75430)。

#### 安装 IM TUIkit

我们的 TUIkit 已经内含 IM SDK，因此仅需安装`tim_ui_kit`，不需要再安装基础 IM SDK。

```shell
#在命令行执行：
flutter pub add tim_ui_kit
```

如果您的项目需要支持 Web，请在执行后续步骤前，[查看 Web 兼容说明章节](#web)，引入JS文件。

### 步骤2: 初始化

1. 在您应用启动时，初始化 TUIKit。
2. 请务必保证先执行 [`TIMUIKitCore.getInstance()`](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitCore/getInstance.html) ，再调用初始化函数 [`init()`](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitCore/init.html) ，并将您的`sdkAppID`传入。
```dart
/// main.dart
import 'package:tim_ui_kit/tim_ui_kit.dart';

final CoreServicesImpl _coreInstance = TIMUIKitCore.getInstance();
  @override
 void initState() {
   _coreInstance.init(
     sdkAppID: 0, // Replace 0 with the SDKAppID of your IM application when integrating
     loglevel: LogLevelEnum.V2TIM_LOG_DEBUG,
     listener: V2TimSDKListener());    
   super.initState();
 }
}
```

>?
>
> 请在本步骤 await 初始化完成后，才可执行后续步骤。

#### 登录测试账户
1. 此时，您可以使用最开始的时候，在控制台生成的测试账户，完成登录验证。
2. 调用 [`_coreInstance.login`](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitCore/login.html) 方法，登录一个测试账户。
```dart
import 'package:tim_ui_kit/tim_ui_kit.dart';

final CoreServicesImpl _coreInstance = TIMUIKitCore.getInstance();
_coreInstance.login(userID: userID, userSig: userSig);
```

>? 该账户仅限开发测试使用。应用上线前，正确的 `UserSig` 签发方式是由服务器端生成，并提供面向 App 的接口，在需要 `UserSig` 时由 App 向业务服务器发起请求获取动态 `UserSig`。更多详情请参见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688#GeneratingdynamicUserSig)。

### 步骤3: 实现 - 会话列表页面

您可以以会话列表作为您的 IM 功能首页，其涵盖了与所有有聊天记录的用户及群聊的会话。

<img style="width:300px; max-width: inherit;" src="https://qcloudimg.tencent-cloud.cn/raw/279da6d5d41ec1ce0b0cf7fca9a697b8.jpg" />

请创建一个 `Conversation` 类，`body` 中使用 [`TIMUIKitConversation`](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitConversation/) 组件，渲染会话列表。

您仅需传入一个 `onTapItem` 事件的处理函数，用于跳转至具体会话聊天页的导航。关于 `Chat` 类，会在下一步讲解。

```dart
import 'package:flutter/material.dart';
import 'package:tim_ui_kit/tim_ui_kit.dart';

class Conversation extends StatelessWidget {
const Conversation({Key? key}) : super(key: key);
@override
Widget build(BuildContext context) {
return Scaffold(
  appBar: AppBar(
    title: const Text(
      "Message",
      style: TextStyle(color: Colors.black),
    ),
  ),
  body: TIMUIKitConversation(
    onTapItem: (selectedConv) {
      Navigator.push(
          context,
          MaterialPageRoute(
            builder: (context) => Chat(
              selectedConversation: selectedConv,
            ),
          ));
    },
  ),
);
}
}
```

### 步骤4: 实现 - 会话聊天页面

该页面由顶部主体聊天历史记录及底部发送消息模块组成。
<img style="width:300px; max-width: inherit;" src="https://qcloudimg.tencent-cloud.cn/raw/0c361254fa5117f7580f39e8b523e472.png" />

请创建一个 `Chat` 类，`body` 中使用 [`TIMUIKitChat`](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitChat/) 组件，渲染聊天页面。

您最好传入一个 `onTapAvatar` 事件的处理函数，用于跳转至联系人的详细信息页。关于 `UserProfile` 类，会在下一步讲解。

```dart
import 'package:flutter/material.dart';
import 'package:tim_ui_kit/tim_ui_kit.dart';

class Chat extends StatelessWidget {
final V2TimConversation selectedConversation;
const Chat({Key? key, required this.selectedConversation}) : super(key: key);
String? _getConvID() {
return selectedConversation.type == 1
    ? selectedConversation.userID
    : selectedConversation.groupID;
}
@override
Widget build(BuildContext context) {
return TIMUIKitChat(
  conversationID: _getConvID() ?? '', // groupID or UserID
  conversationType: selectedConversation.type ?? 1, // Conversation type
  conversationShowName: selectedConversation.showName ?? "", // Conversation display name
  onTapAvatar: (_) {
        Navigator.push(
            context,
            MaterialPageRoute(
             builder: (context) => UserProfile(userID: userID),
        ));
  }, // Callback for the clicking of the message sender profile photo. This callback can be used with `TIMUIKitProfile`.
);
}
```

### 步骤5: 实现 - 用户详情页面

该页面默认，可在只传入一个 `userID` 的情况下，自动根据是否是好友，生成用户详情页。

请创建一个 `UserProfile` 类，`body` 中使用 [`TIMUIKitProfile`](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitProfile/) 组件，渲染用户详情及关系链页面。

>? 如果您希望自定义该页面，请优先考虑使用 [`profileWidgetBuilder`](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitProfile/ProfileWidgetBuilder.html) 传入需自定义的 profile 组件并配合 `profileWidgetsOrder` 确定纵向排列顺序；如果无法满足，才可使用 `builder` 。

<img style="width:300px; max-width: inherit;" src="https://qcloudimg.tencent-cloud.cn/raw/5f2e67ffb31adc738165e2c4ce58218c.jpg" />

```dart
import 'package:flutter/material.dart';
import 'package:tim_ui_kit/tim_ui_kit.dart';

class UserProfile extends StatelessWidget {
    final String userID;
    const UserProfile({required this.userID, Key? key}) : super(key: key);

    @override
    Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
       title: const Text(
         "Message",
         style: TextStyle(color: Colors.black),
       ),
     ),
     body: TIMUIKitProfile(
          userID: widget.userID,
     ),
    );
  }
}
```

此时，您的应用已经可以完成消息收发，管理好友关系，展示用户详情及展示会话列表。

### 更多能力

您还可以继续使用以下 TUIKit 插件快速实现完整 IM 功能。

[TIMUIKitContact](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitContact/): 联系人列表页面。

[TIMUIKitGroupProfile](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitGroupProfile/): 群资料页面，使用方式与 `TIMUIKitProfile` 基本一致。

[TIMUIKitGroup](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitGroup/): 群列表界面。

[TIMUIKitBlackList](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitBlackList/): 黑名单列表界面。

[TIMUIKitNewContact](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitNewContact/): 联系人（好友）申请列表。如需在外部显示小红点，可使用 `TIMUIKitUnreadCount` 小红点组件，其会自动挂载监听。

[本地搜索](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitSearch/): `TIMUIKitSearch` 全局搜索组件，支持全局搜索联系人/群组/聊天记录，也支持使用 `TIMUIKitSearchMsgDetail` 在特定会话中搜索聊天记录。两种模式取决于是否传入 `conversation`。

UI组件全貌可参见 [本全览文档](https://cloud.tencent.com/document/product/269/70747) 或 [详细文档](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/)。

## Flutter for Web支持[](id:web)

TUIKit(tim_ui_kit) 0.1.4版本起，可完美兼容Web端。

相比 Android 和 iOS 端，需要一些额外步骤。如下：

### 引入JS

>?
>
> 如果您现有的 Flutter 项目不支持 Web，请在项目根目录下运行 `flutter create .` 添加 Web 支持。

从 GitHub 下载下方两个 JS 文件，放置于项目的 `web` 路径内。

- [tim-js-friendship.js](https://github.com/TencentCloud/TIMSDK/blob/master/Web/IMSDK/tim-js-friendship.js)
- [将此文件重命名成 tim-upload-plugin.js](https://github.com/TencentCloud/TIMSDK/blob/master/Web/IMSDK/tim-upload-plugin/index.js)

打开 `web/index.html` ，在 `<head> </head>` 间引入这两个JS文件。如下：

```html
<script src='./tim-upload-plugin.js'></script>
<script src="./tim-js-friendship.js"></script>
```
![](https://qcloudimg.tencent-cloud.cn/raw/f88ddfbdc79fb7492f3ce00c2c583246.png)

## 常见问题

### 支持哪些平台？
- [IM SDK(tencent_im_sdk_plugin)](https://cloud.tencent.com/document/product/269/75286) 支持 iOS 、Android 和 Web 三个平台。（从 tencent_im_sdk_plugin 4.1.1+2 版本起支持WEB）
- [TUIKit](https://cloud.tencent.com/document/product/269/70746) 及 [配套完整版交互 Demo](https://github.com/TencentCloud/TIMSDK/tree/master/Flutter/Demo/im-flutter-uikit) 支持 iOS 、Android 和 Web 三个平台。（从 tim_ui_kit 0.1.4 版本起支持WEB）
此外 Windows 和 Mac 版正在开发中，敬请期待。

### Android 单击 Build And Run 报错找不到可用设备？

确保设备没被其他资源占用，或单击 **Build** 生成 APK 包，再拖动进模拟器里运行。

### iOS 第一次运行报错？

配置运行后，如果报错，可以单击 **Product** > **Clean Build Folder**，清除产物后重新 `pod install` 或 `flutter run`。

![](https://qcloudimg.tencent-cloud.cn/raw/d495b2e8be86dac4b430e8f46a15cef4.png)

### 佩戴 Apple Watch 时，真机调试 iOS 报错

![](https://qcloudimg.tencent-cloud.cn/raw/1ffcfe39a18329c86849d7d3b34b9a0e.png)

请将您的 Apple Watch 调整至飞行模式，并将 iPhone 的蓝牙功能通过 `设置 => 蓝牙` 彻底关闭。

重新启动 Xcode（若打开），并重新 `flutter run` 即可。

### Flutter 环境问题

如您需得知 Flutter 的环境是否存在问题，请运行 Flutter doctor 检测 Flutter 环境是否装好。

### 使用 Flutter 自动生成的项目，引入TUIKit 后，运行 Android 端报错

![](https://qcloudimg.tencent-cloud.cn/raw/d95efdd4ae50f13f38f4c383ca755ae7.png)

1. 打开 `android\app\src\main\AndroidManifest.xml`，根据如下，补全 `xmlns:tools="http://schemas.android.com/tools"` / `android:label="@string/android_label"` 及 `tools:replace="android:label"`。
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="替换成您的 Android 端包名"
    xmlns:tools="http://schemas.android.com/tools">
    <application
        android:label="@string/android_label"
        tools:replace="android:label"
        android:icon="@mipmap/ic_launcher" // 指定一个 icon 路径
        android:usesCleartextTraffic="true"
        android:requestLegacyExternalStorage="true">
``` 

2. 打开 `android\app\build.gradle`，补全 `defaultConfig` 中 `minSdkVersion` 及 `targetSdkVersion`。
```gradle
defaultConfig {
  applicationId "" // 替换成您的Android端包名
  minSdkVersion 21
  targetSdkVersion 30
}
```

### 如何搭建直播间？

欢迎查看 [直播间搭建指南](https://cloud.tencent.com/document/product/269/77764#.E7.BE.A4.E7.B1.BB.E5.9E.8B.E9.80.89.E6.8B.A9)，基于腾讯云 IM/TRTC/腾讯云直播，搭建一套完整的直播间系统。

### 错误码如何查询？

- IM SDK 的 API 层面错误码，请查看 [错误码](https://cloud.tencent.com/document/product/269/1671)。
- TUIKit的场景码，用于界面弹窗提示，通过 [onTUIKitCallbackListener 监听](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitCore/init.html) 获得。请查看 [该文档](https://comm.qq.com/im/doc/flutter/uikit-sdk-api/TIMUIKitCore/TIMCallback.html#inforecommendtext)。


## 联系我们[](id:contact)
如果您在接入使用过程中有任何疑问，请加入 QQ 群：788910197 咨询。
