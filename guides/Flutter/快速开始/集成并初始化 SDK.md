本文为您介绍 Flutter 端快速集成并初始化 NERoom SDK 的操作步骤。

## <span id="开发环境要求">开发环境</span>

<style>
table th:first-of-type {
    width: 33%;
}
table th:nth-of-type(2) {
    width: 33%;
}
table th:nth-of-type(3) {
    width: 34%;
}
</style>

在开始运行工程之前，请您准备以下开发环境：

Flutter | Android | iOS |
--- | --- | --- |
- [Flutter](https://docs.flutter.dev/development/tools/sdk/releases?tab=windows#macos) 2.0.0 及以上版本。 | - [Android Studio](https://developer.android.com/studio/releases?hl=zh-cn) 4.1 及以上版本。 | - Xcode 11.0 及以上版本。 |\
- [安装 Flutter 和 Dart 插件](https://docs.flutter.dev/get-started/editor?)。 | - App 要求 Android 5.0 及以上版本 Android 设备。 | - App 要求 iOS 11 及以上版本 iOS 设备。 |\
- Dart 2.12.0 及以上版本。 | - 使用 Java 作为开发语言。 | - 请确保您的项目已设置有效的开发者签名。 |\
  | | - 使用 Objective-C 作为开发语言。 |

## 前提条件

根据本文操作前，请确保您已经在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上完成了以下设置：

- 创建至少一个应用。详细步骤请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。
- 为应用开通一个房间组建模板。详细步骤请参考 [开通或试用服务](https://doc.yunxin.163.com/console/concept/zc3NDYzNzc?platform=console)。

## 第一步：创建项目

:::note note
您可以参考此步骤创建新项目，若是需要集成到已有的项目中，请忽略该步骤。
:::

创建 Flutter 项目的详细操作步骤请参考 [Flutter get-started](https://docs.flutter.dev/get-started/test-drive?tab=androidstudio)。

:::::: div linked-codes
::: code Android

1. 在 Android Studio 的顶部菜单依次选择 **File** > **New** > **New Flutter Project** 新建工程。选择 **Flutter Application**，单击 **Next**。

    <img alt="Flutter_Application.png" src="https://yx-web-nosdn.netease.im/common/7fd6e6c3f3725827bb0fe2900342e651/Flutter_Application.png" style="width:60%;border: 0px solid #BFBFBF;">

2. 配置 Project 信息。

    设置项目名称、项目位置和 Flutter SDK 位置路径。
    ::: note note
    - **Flutter SDK path** 请选择 Flutter SDK 根目录。
    - 项目名称和路径请设置为小写英文字母，且用下划线分割开。
    :::

    <img alt="Project 配置 Flutter_.png" src="https://yx-web-nosdn.netease.im/common/c6db6c5a25554dc3364fbea930aac093/Project配置Flutter_.png" style="width:60%;border: 0px solid #BFBFBF;">

3. 设置包名，单击 **Finish**。

    ::: note note
    如果您的应用计划上架商店，建议一开始的时候把这个全网唯一的包名设置好，因为应用上架之后就不能再修改了。
    :::

:::
::: code iOS

1. 打开 Xcode，从菜单栏中选择 **File** > **New** > **Project**。

2. 在弹出的 **Choose a template for your new project** 窗口中，选择 **Flutter**，单击 **Next**。

3. 填写项目名称、组织名称和组织标识符，选择项目存储位置，并选择所需的语言和界面，单击 **Next**。

    ::: note note

    项目名称和路径请设置为小写英文字母，且用下划线分割开。
    :::

4. 选择 Flutter SDK 的路径，并选择所需的项目文件夹。

:::
::::::

## 第二步：集成 SDK

NERoom Flutter SDK 已正式发布到 [pub.dev](https://pub.dev/packages/netease_roomkit)，您可以在 pub 库中查询最新版本，通过配置 `pubspec.yaml` 自动下载更新。

在项目的 `pubspec.yaml` 中添加以下依赖：

```YAML
dependencies:
  netease_roomkit: ^0.0.1-rc.7
```

## 第三步：添加权限

通过 Flutter SDK 实现音视频通话之前，需要开通摄像头和麦克风的使用权限，以开启视频和语音通话功能。

:::::: div linked-codes
::: code Android

打开 `app/src/main/AndroidManifest.xml` 文件，添加必要的设备权限。例如：

```XML
//网络相关
 <uses-permission android:name="android.permission.INTERNET"/>
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
 <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
 <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
//防止通话过程中锁屏
 <uses-permission android:name="android.permission.WAKE_LOCK"/>
//视频权限
 <uses-permission android:name="android.permission.CAMERA"/>
//录音权限
 <uses-permission android:name="android.permission.RECORD_AUDIO"/>
//修改音频设置
 <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
//蓝牙权限
 <uses-permission android:name="android.permission.BLUETOOTH"/>
//蓝牙连接权限，此权限还需在运行应用时动态申请，否则 Android 12 及以上的设备蓝牙无法正常工作
 <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
//外置存储卡写入权限
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
//蓝牙 startBluetoothSco 会用到此权限
 <uses-permission android:name="android.permission.BROADCAST_STICKY"/>
// V5.4.0 及之后版本需要，更改设备的网络状态，如打开或关闭网络连接、更改网络类型等。
 <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
 //获取设备信息
 <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
//允许应用程序使用 camera 硬件资源
 <uses-feature android:name="android.hardware.camera"/>
 //自动对焦
 <uses-feature android:name="android.hardware.camera.autofocus"/>
 // V5.3.0 及之后版本需要添加以下标签
 <application>
 <uses-native-library android:name="libOpenCL.so" android:required="false"/>
 </application>
 ......//App 需要的其他设备权限
 ```

权限说明如下表所示。

| 必要性 | 获取的权限 | 使用目的 |
| --- | --- | --- |
| 必要权限 | 访问网络权限（INTERNET） | SDK 基本功能都需要在联网的情况下才可以使用 |
| ^^ | 网络连接状态（ACCESS_NETWORK_STATE） | 判断网络连接状态及网络是否可用 |
| ^^ | Wifi 网络状态（ACCESS_WIFI_STATE 和 CHANGE_WIFI_STATE） | 判断网络连接状态及网络是否可用 |
| ^^ | 摄像头（CAMERA 和 camera.autofocus） | 视频通话时，用于采集视频画面 |
| ^^ | 麦克风（RECORD_AUDIO） | 音视频通话时，用于采集声音 |
| ^^ | 修改音频设置（MODIFY_AUDIO_SETTINGS） | 修改音频设备配置时需要使用该权限 |
| 非必要权限 | 设备存储（WRITE_EXTERNAL_STORAGE） | 存储 SDK 配置文件和日志文件 |
| ^^ | 设备存储（READ_EXTERNAL_STORAGE） | 读取 SDK 配置文件和日志文件 |
| ^^ | 蓝牙（BLUETOOTH 和 BLUETOOTH_CONNECT） | 连接蓝牙耳机和耳麦时需要该权限<note type="notice"><ul><li>BLUETOOTH_CONNECT 权限：您需要在代码中动态申请 `android.permission.BLUETOOTH_CONNECT` 权限，否则 Android 12 及以上系统版本的设备会无法使用蓝牙功能，具体信息请参考 [Android 官方说明](https://developer.android.google.cn/about/versions/12/features/bluetooth-permissions?hl=zh-cn)。<li>BLUETOOTH_CONNECT 权限：SDK V5.4.0 及之后版本，若用户未赋予该权限，SDK 会通过手机麦克风采集声音。但通话时需要要离手机比较近，对方才能听清声音。<li>BLUETOOTH 权限：仅 Android 6.0 以下版本需要声明，Android 6.0 及以上版本无需声明。</note> |
| ^^ | WAKE_LOCK | 防止通话过程中锁屏 |
| ^^ | 设备信息（READ_PHONE_STATE） | SDK 需要监听电话的打断，在电话呼入时，停止音频的采集，电话结束时，恢复音频采集 |
| ^^ | 更改设备的网络状态（CHANGE_NETWORK_STATE） | SDK 操作多网卡<note type="notice">集成 SDK V5.4.0 及之后版本，需要该权限。</note> |
::: note notice

Android 6.0 及以上版本要求部分重要权限必须申请动态权限，不能只通过 `AndroidMainfest.xml` 文件申请静态权限。请自行在 pub 上寻找第三方 Flutter 插件来实现动态权限，或者参考如下示例代码。

```Java
    final permissions = [Permission.camera, Permission.microphone];
    if (Platform.isAndroid) {
      permissions.add(Permission.storage);
    }
    List<Permission> missed = [];
    for (var permission in permissions) {
      PermissionStatus status = await permission.status;
      if (status != PermissionStatus.granted) {
        missed.add(permission);
      }
    }

    bool allGranted = missed.isEmpty;
    if (!allGranted) {
      List<Permission> showRationale = [];
      for (var permission in missed) {
        bool isShown = await permission.shouldShowRequestRationale;
        if (isShown) {
          showRationale.add(permission);
        }
      }

    if (showRationale.isNotEmpty) {
        ConfirmAction? action = await showDialog<ConfirmAction>(
            context: context,
            builder: (BuildContext context) {
              return AlertDialog(
                content: const Text('You need to allow some permissions'),
                actions: <Widget>[
                  TextButton(
                    child: const Text('Cancel'),
                    onPressed: () {
                      Navigator.of(context).pop(ConfirmAction.CANCEL);
                    },
                  ),
                  TextButton(
                    child: const Text('Accept'),
                    onPressed: () {
                      Navigator.of(context).pop(ConfirmAction.ACCEPT);
                    },
                  )
                ],
              );
            });
        if (action == ConfirmAction.ACCEPT) {
          Map<Permission, PermissionStatus> allStatus = await missed.request();
          allGranted = true;
          for (var status in allStatus.values) {
            if (status != PermissionStatus.granted) {
              allGranted = false;
            }
          }
        }
      } else {
        Map<Permission, PermissionStatus> allStatus = await missed.request();
        allGranted = true;
        for (var status in allStatus.values) {
          if (status != PermissionStatus.granted) {
            allGranted = false;
          }
        }
      }
    }
```

:::

::: code iOS
请给 App 授权麦克风、摄像头和 Wi-Fi 的使用权限。

编辑 `info.plist` 文件，添加以下三项。

- **Privacy - Microphone Usage Description**，并填入麦克风使用目的提示语。
- **Privacy - Camera Usage Description**，并填入摄像头使用目的提示语。
- **Application uses Wi-Fi**，设置为 **YES**。

<img alt="Xnip2022-12-02_16-46-19.jpg" src="https://yx-web-nosdn.netease.im/common/6bb4a91a0cb760c799ac2921ae41b942/Xnip2022-12-02_16-46-19.jpg" style="width:80%;border: 1px solid #BFBFBF;">

::: note note
如果使用 Platform View，并且 Flutter 版本低于 1.22，请在 **Info** > **Custom iOS Target Properties** 中，增加字段 `io.flutter.embedded_views_preview`，并设置 Value 为 YES。
:::

:::
::::::

## 第四步：防代码混淆（Android）

代码混淆是指使用简短无意义的名称重命名类、方法、属性等，增加逆向工程的难度，保障 Android 程序源码的安全性。为了避免因重命名类，导致调用 NERTC SDK 异常，您需要配置防代码混淆。

1. 在 `proguard-rules.pro` 文件中，为 NERoom SDK 添加 -keep 类的配置，可以防止混淆 NERoom SDK 公共类名称。

    ```
    -dontwarn com.netease.**

    -keep class com.netease.** {*;}

    //如果您使用全文检索插件，需要加入

    -dontwarn org.apache.lucene.**

    -keep class org.apache.lucene.** {*;}

    //如果您开启数据库功能，需要加入

    -keep class net.sqlcipher.** {*;}

    -keep class com.netease.lava.** {*;}

    -keep class com.netease.yunxin.** {*;}
    ```

1. 打开 `app/src/main/AndroidManifest.xml` 文件，添加必要的设备权限。

    ```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.BROADCAST_STICKY"/>
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>

    <!-- Android Q(10.0) 及后续版本还需要额外添加以下权限 -->
    <uses-permission android:name="android.permission.READ_PRIVILEGED_PHONE_STATE" />

    <uses-feature android:name="android.hardware.camera"/>
    <uses-feature android:name="android.hardware.camera.autofocus"/>
    ```

## 初始化 SDK

实现初始化 NERoom SDK 的示例代码如下：

```Java
String appKey="your appKey";
NERoomKit.instance.initialize(NERoomKitOptions(appKey: appkey)).then((result){
if(result.isSuccess()){
  debugPrint('initialize success');
} else {
  debugPrint('initialize error code:${result.code},msg:${result.msg}');
}
});
```