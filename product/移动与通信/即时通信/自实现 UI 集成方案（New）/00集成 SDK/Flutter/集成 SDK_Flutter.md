本文主要介绍如何快速将腾讯云即时通信 IM SDK 集成到您的 Flutter 项目中。

## 环境要求

| 平台 | 版本 | 
|---------|---------|
| Flutter | 2.2.0 及以上版本。 | 
|Android|Android Studio 3.5及以上版本，App 要求 Android 4.1及以上版本设备。|
|iOS|Xcode 11.0及以上版本，真机调试请确保您的项目已设置有效的开发者签名。|

## 支持平台


| 平台 | 支持状态|
|---------|---------|
| iOS | 支持 |
| Android | 支持 |
| [Web](#web) | 支持，4.1.1+2版本起 |
| macOS | 即将上线 |
| Windows | 即将上线 |

> 我们致力于打造一套支持 Flutter 全平台的即时通信IM SDK及TUIKit，帮助您一套代码，全平台运行。

## 集成 IM SDK
您可以通过 [pub add](https://pub.dev/packages/tencent_im_sdk_plugin) 的方式直接集成腾讯云 IM SDK（Flutter），或者在 pubspec.yaml 中写入 IM SDK 的方式来集成。


### flutter pub add 安装
在终端窗口中输入如下命令（需要提前安装 Flutter 环境）：
```
flutter pub add tencent_im_sdk_plugin
```

### 在 pubspec.yaml 中写入
```
dependencies:
  tencent_im_sdk_plugin: "最新版本" //可在https://pub.dev/packages/tencent_im_sdk_plugin上查看im flutter sdk的最新版本并使用
```
此时您的 editor 或许会自动 flutter pub get，如果没有请您在命令行中手动输入 flutter pub get。

如果您的项目需要支持 Web，请在执行后续步骤前，[查看Web兼容说明章节](#web)，引入JS文件。


### 引入并初始化 SDK
```
import 'package:tencent_im_sdk_plugin/tencent_im_sdk_plugin.dart';

```
## Flutter for Web支持[](id:web)

IM SDK(tencent_im_sdk_plugin) 4.1.1+2版本起，可完美兼容Web端。

相比Android和iOS端，需要一些额外步骤。如下：

### 引入JS

>?
>
> 如果您现有的Flutter项目不支持Web，请在项目根目录下运行 `flutter create .` 添加Web支持。

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

### flutter pub get/add 失败如何解决？
请参见官网配置 [国内镜像](https://flutter.cn/community/china)。

## 联系我们
如果您在接入使用过程中有任何疑问，请加入 QQ 群：788910197 咨询。

![](https://qcloudimg.tencent-cloud.cn/raw/eacb194c77a76b5361b2ae983ae63260.png)
