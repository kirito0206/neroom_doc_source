本文介绍 iOS 端快速集成并初始化网易云信房间组件（NERoom SDK）的操作步骤，帮助您快速集成并初始化 NERoom SDK。

## <span id="开发环境要求">开发环境</span>

在开始运行工程之前，请您准备以下开发环境：

- Xcode 10 及以上版本。
- iOS 12.0 及以上版本的 iOS 设备。

## 前提条件

根据本文操作前，请确保您已经在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上完成了以下设置：

- 创建至少一个应用。详细步骤请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。
- 为应用开通一个房间组建模板。详细步骤请参考 [开通或试用服务](https://doc.yunxin.163.com/console/concept/zc3NDYzNzc?platform=console)。

## 目录结构

NERoom SDK 动态库推荐放置在 `libs/` 目录。

:::note notice
从 1.36.0 版本起，NERoom SDK 支持跨平台开发的 XCFramework 框架格式。SDK 目录下的 `.framework` 文件后缀名修改为 `.xcframework`。如果您集成 NERoom SDK 后从低版本升级至 1.36.0 及以上，请重新添加 XCFramework 依赖。<!-- 详情请参考《常见问题》[升级 NERoom SDK 版本后，Xcode 编译报错文件找不到](https://doc.yunxin.163.com/neroom/faq/jg1MjMwMjg?platform=client)。 -->
:::

| 文件/文件夹名称 | 是否必选 | 说明 |
| --- | --- | --- |
| NERtcSDK.xcframework | 是 | 音视频库。 |
| NERtcnn.xcframework | 是 | 神经网络库（自 1.14.0 起提供，以实现插件化）。 |
| NIMSDK.framework | 是 | IM 即时通讯基础模块库。 |
| NIMNOS.framework | 是 | IM NOS 文件存储库。 |
| NERtcBeauty.xcframework | 否 | 美颜（自 1.14.0 起提供，以实现插件化）。 |
| NERtcFaceDetect.xcframework | 否 | 人脸检测（自 1.14.0 起提供，以实现插件化）。 |
| NERtcPersonSegment.xcframework | 否 | 背景分割（自 1.14.0 起提供，以实现插件化）。 |
| NERtcAiDenoise.xcframework | 否 | AI 降噪（自 1.14.0 起提供，以实现插件化）。 |
| NERtcAiHowling.xcframework | 否 | AI 啸叫检测（自 1.14.0 起提供，以实现插件化）。 |

## 第一步：集成 NERoom SDK

通过 CocoaPods 集成 NERoom SDK。

::: note notice
请确保您的 Mac 已经安装 Ruby 环境。
:::

1. 安装 CocoaPods。

    在终端窗口中输入如下命令：

    ```
    sudo gem install cocoapods
    ```
2. 创建 Podfile 文件。

    从 Terminal 中进入您所创建项目所在路径，运行以下命令创建 `Podfile` 文件。

    ```CocoaPods
    pod init
    ```
3. 编辑 Podfile 文件。

    ```CocoaPods
    # platform :ios, '10.0'
    target '{YourApp}' do
        pod 'NERoomKit', '~> {version}'
    end
    ```

    - YourApp：您的 Target 名称。
    - version：待集成的 NERoom SDK 版本号。

    NERoom 1.14.0 版本起，支持以插件化的方式按需加载子模块，以便 **缩小包体积**。

    包含：基础模块、美颜、音频等。

    方式 1：

    ```CocoaPods
    pod 'NERoomKit', '{version}', :subspecs => ['Base', 'Beauty', 'Segment', 'Audio']
    ```

    方式 2:

    ```CocoaPods
    pod 'NERoomKit/Base', '{version}'
    pod 'NERoomKit/Beauty', '{version}'
    pod 'NERoomKit/Segment', '{version}'
    pod 'NERoomKit/Audio', '{version}'
    ```

    `subspecs` 中请填入待引入的动态库对应的值，具体说明如下表所示。

    功能/插件 | `subspecs` 的值 | `xcframework` 动态库
    ---- | ---- | ----
    音视频 + IM | `Base` | 必选基础库：<ul> <li>`NERtcSDK.xcframework`<li>`NERtcnn.xcframework`<li>`NIMSDK.framework`<li>`NIMNOS.framework` |
    美颜 + 人脸识别 | `Beauty` | <ul><li>美颜库：`NERtcBeauty.xcframework`<li>人脸检测库：`NERtcFaceDetect.xcframework` |
    虚拟背景 | `Segment` | 背景分割库：`NERtcPersonSegment.xcframework` |
    AI 降噪 + AI 啸叫检测 | `Audio` | <ul><li>AI 降噪库：`NERtcAiDenoise.xcframework`<li>AI 啸叫检测库：`NERtcAiHowling.xcframework`

4. 执行以下命令查询本地库版本。

    ```CocoaPods
    pod search NERoomKit
    ```
5. 若不是最新版本，可以执行以下命令更新本地库版本。

    ```CocoaPods
    pod repo update
    ```

6. 执行以下命令安装安装 NERoom SDK。

    ```CocoaPods
    pod install
    ```

    命令执行完毕后，会生成 `*.xcworkspace` 文件，表示 SDK 集成完成。

## <span id="添加媒体设备权限">第二步：添加媒体设备权限</span>

1. 在 **Signing & Capabilities** 页面，打开后台音频权限。

    ::: note note
    为保障应用进入手机后台之后，SDK 在后台能继续处理音频流，使通话不中断，请打开后台音频权限。
    :::

2. 编辑 `info.plist` 文件，授权麦克风、摄像头。

    使用 SDK 的音视频功能，需要授权麦克风和摄像头的使用权限。请在 App 的 `Info.plist` 中设置以下两项。

    - **Privacy - Microphone Usage Description**，并填入麦克风使用目的提示语。
    - **Privacy - Camera Usage Description**，并填入摄像头使用目的提示语。

## <span id="导入 NERoom SDK">第三步：导入 NERoom SDK</span>

在项目需要使用 NERoom API 的文件里引入 NERoom 的头文件。

- Objective-C 代码中，引入 Swift 头文件的示例代码如下：

    ```
    #import <NERoomKit/NERoomKit-Swift.h>
    ```

- Swift 代码中，引入 Swift 头文件的示例代码如下：

    ```
    import NERoomKit
    ```

## <span id="初始化 NERoom SDK">第四步：初始化 NERoom SDK</span>

### 公有云部署

若您使用 **公有云** 环境，则参考以下示例代码进行初始化。

:::::: div linked-codes
::: code 中国大陆环境

如果您的 AppKey 对应的服务区域是中国大陆，初始化 NERoom SDK 的示例代码如下：

```Swift
let options = NERoomKitOptions(appKey: "appKey")
NERoomKit.shared().initialize(options: options) { code, str, _ in
    if code == 0 {
        // 初始化成功
    } else {
        // 初始化失败
    }
}
```
:::
::: code 其他国家或地区环境
如果您的 AppKey 对应的服务区域是海外，初始化 NERoom SDK 的示例代码如下：

```Swift
let options = NERoomKitOptions(appKey: "appKey")
options.extras = ["serverUrl": "https://roomkit-sg.yunxinapi.com"]
let serverConfig = NEServerConfig()
serverConfig.imServerConfig = NEIMServerConfig()
serverConfig.roomKitServerConfig = NERoomKitServerConfig()
serverConfig.roomKitServerConfig?.roomServer = "https://roomkit-sg.netease.im"
serverConfig.imServerConfig?.lbs = "https://lbs.netease.im/lbs/conf.jsp"
serverConfig.imServerConfig?.link = "link-sg.netease.im:7000"
options.serverConfig = serverConfig
NERoomKit.shared().initialize(options: options) { code, str, _ in
    if code == 0 {
        // 初始化成功
    } else {
        // 初始化失败
    }
}
```
::: note note
- 在调用初始化方法时，需要启动应用并监听到网络连接后，再调用初始化方法，否则会调用失败。
- 公共云的初始化 SDK 不能在 AppDelegate 的 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 方法中进行初始化。
:::
:::
::::::

### 私有化部署

若您使用 **私有化** 环境，则参考以下步骤进行初始化。

:::::: div linked-codes
::: code 方式一：通过 serverUrl 设置（推荐）
调用 `initialize` 方法初始化 NERoom 时，配置 `NERoomKitOptions` 的 `serverUrl` 参数为私有化服务器地址。

示例代码如下:

```Swift
let options = NERoomKitOptions(appKey: "appKey")
options.serverUrl = "私有化 NERoom URL"
NERoomKit.shared().initialize(options: options) { code, msg, _ in
    if code == 0 {
    // 初始化成功
    } else {
    // 初始化失败
    }
}
```
:::
::: code 方式二：通过配置文件设置
私有云服务器部署完成后，网易云信会提供给您对应的 NERoom 私有化配置文件（JSON 类型）。您在初始化 NERoom SDK 时，需要进行相关的配置，以便 SDK 能接入到目标私有云服务器上。具体配置步骤如下：

1. 将配置文件重命名为 `xkit_server.config`，并在构建项目前将配置文件添加到工程中 App Target 所在的根目录下（即 `Info.plist` 文件所在目录）。

    同时，您也需要将上述配置文件添加进主 Target 的打包资源配置项中，以确保该文件会打包至 App 中，否则运行时将无法找到该文件，具体操作方法如下图所示。

    <img alt="私有化配置.png" src="https://yx-web-nosdn.netease.im/common/cf13f34b99855d1c0527f7c18208c23c/私有化配置.png" style="width:80%;border: 1px solid #BFBFBF;">

2. 调用 `initialze` 方法初始化 NERoom SDK 时，配置 `xkit_server.config` 的 `useAssetServerConfig` 参数为 `true`。

    示例代码如下：

    ```Swift
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    /// 读取本地私有化配置文件的形式进行设置，需要在 bundle 中放置'xkit_server.config'文件
    let options = NERoomKitOptions(appKey: "appKey")
    options.useAssetServerConfig = true
    return options
    NERoomKit.shared().initialize(options: fetchPrivateOptionsWithReadLocalFile()) { code, msg, _ in
        if code == 0 {
        // 初始化成功
        } else {
        // 初始化失败
        }
    }
    }
    ```
::: note note
在私有化场景中，请在 Appdelete 的 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 方法中，进行初始化。
:::
:::
::::::

## 常见问题

自 1.14.0 版本起，如果项目内单独引用了 NERtcSDK、NIMSDK_LITE 库，与 NERoomKit 依赖的库引发冲突，您可以手工指定 NERtcSDK、NIMSDK_LITE 的版本号。

示例代码如下：

```CocoaPods
// 使用方式 1
pod 'NERoomKit', '~> 1.14.0', :subspecs => ['Base_Special', 'Beauty_Special', 'Segment_Special', 'Audio_Special']
// 使用方式 2
pod 'NERoomKit/Base_Special', '~> 1.14.0'
pod 'NERoomKit/Beauty_Special', '~> 1.14.0'
pod 'NERoomKit/Segment_Special', '~> 1.14.0'
pod 'NERoomKit/Audio_Special', '~> 1.14.0'

pod 'NERtcSDK', '{指定的版本号}', :subspecs => ['RtcBasic', ...]

pod 'NIMSDK_LITE', '{指定的版本号}'
```