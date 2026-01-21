本文介绍 Android 端快速集成并初始化网易云信房间组件（NERoom SDK）的操作步骤，帮助您快速集成并初始化 NERoom SDK。

## 开发环境

在开始运行工程之前，请您准备以下开发环境：

- Android SDK API 等级 21 或以上。
- Android Studio 3.0 或以上版本。
- Android 系统 5.0 或以上版本的移动设备。

## 前提条件

根据本文操作前，请确保您已经在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上完成了以下设置：

- 创建至少一个应用。详细步骤请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。
- 为应用开通一个房间组建模板。详细步骤请参考 [开通或试用服务](https://doc.yunxin.163.com/console/concept/zc3NDYzNzc?platform=console)。

## 集成 SDK

1. 在网易云信文档中心的 [更新日志](https://doc.yunxin.163.com/neroom/concept/zgzMDM3MTM?platform=android) 页面，查看 NERoom SDK 最新版本的版本号。

2. 在项目对应模块的 `build.gradle` 中加入以下行，其中 `x.x.x` 表示您在上一步查看的版本号。

    ```Groovy
    implementation 'com.netease.yunxin.kit.room:roomkit:x.x.x'
    ```

3. （可选）若您需要使用 **音视频通话** 的 **虚拟背景** 功能和 **美颜** 功能，需要单独添加对应依赖。

    ::: note notice
    按照 NERoom SDK 适配的对应版本引入相关的依赖，具体适配的版本号 `x.x.x` 请参考 [更新日志](https://doc.yunxin.163.com/neroom/concept/zgzMDM3MTM?platform=android)。

    例如，引入 NERoom 1.22.2，那么适配的 `nertc`（音视频通话）版本为 `5.5.203-SNAPSHOT`。
    :::

    ```Java
    //美颜
    implementation 'com.netease.yunxin:nertc-beauty:x.x.x'
    implementation 'com.netease.yunxin:nertc-facedetect:x.x.x'

    //虚拟背景
    implementation 'com.netease.yunxin:nertc-segment:x.x.x'
    ```

4. 在项目对应的根目录的 `build.gradle` 中加入以下行。

    ```Groovy
    allprojects {
        repositories {
            mavenCentral()
            google()

            // 该仓库为 Maven Snapshot 仓库，按需添加
            maven {
                url "https://oss.sonatype.org/content/repositories/snapshots/"
            }
        }
    }
    ```

    ::: note notice
    如果当前版本的依赖库中包含有 SNAPSHOT 版本依赖，则还需要配置添加 Maven Snapshot 仓库地址。

    例如，引入 NERoom 1.22.2，其适配的 NERTC（音视频通话）版本为 `5.5.203-SNAPSHOT`，所以需要添加 Maven Snapshot 仓库地址，否则会造成构建失败。
    :::

5. 在 `app/src/main/AndroidManifest.xml` 文件中添加类似如下代码，添加相应的设备权限。

    ```Java
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
    //获取设备信息
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>

    //允许应用程序使用 camera 硬件资源
    <uses-feature android:name="android.hardware.camera"/>
    //自动对焦
    <uses-feature android:name="android.hardware.camera.autofocus"/>
            ......//App 需要的其他设备权限
    ```

    ::: note notice
    您需要在代码中动态申请 `android.permission.BLUETOOTH_CONNECT` 权限，否则 Android 12 及以上系统版本的设备会无法使用蓝牙功能，具体信息请参考 [Android 官方说明](https://developer.android.google.cn/about/versions/12/features/bluetooth-permissions?hl=zh-cn)。
    :::

## 初始化 SDK

### 公有云部署

若您使用 **公有云** 环境，则参考以下示例代码进行初始化。

:::::: div linked-codes
::: code 中国大陆环境
如果您的 AppKey 对应的服务区域是中国大陆，初始化 NERoom SDK 的示例代码如下：

```Java
String appKey = "your appKey";
NERoomKitOptions options = new NERoomKitOptions(appKey);

NERoomKit.getInstance().initialize(this, options, new NECallback2<Unit>() {
    @Override
    public void onSuccess(@Nullable Unit unit) {
        super.onSuccess(unit);
    }

    @Override
    public void onError(int code, @Nullable String message) {
        super.onError(code, message);
    }
});
```
:::
::: code 其他国家或地区环境
如果您的 AppKey 对应的服务区域是海外，初始化 NERoom SDK 的示例代码如下：

```Java
String appKey = "your appKey";
String roomServerUrl = "https://roomkit-sg.yunxinapi.com/";
HashMap<String, Object> extras = new HashMap<>();
NEServerConfig serverConfig = new NEServerConfig();
NEIMServerConfig imServerConfig = new NEIMServerConfig();
imServerConfig.setLink("link-sg.netease.im:7000");
imServerConfig.setLbs("https://lbs.netease.im/lbs/conf.jsp");
imServerConfig.setNosLbs("http://wannos.127.net/lbs");
imServerConfig.setNosDownloader("{bucket}-nosdn.netease.im/{object}");
imServerConfig.setNosUploader("https://nosup-hz1.127.net");
imServerConfig.setNosUploaderHost("nosup-hz1.127.net");
imServerConfig.setHttpsEnabled(true);
serverConfig.setImServerConfig(imServerConfig);
NERoomKitServerConfig roomKitServerConfig = new NERoomKitServerConfig();
roomKitServerConfig.setRoomServer(roomServerUrl);
serverConfig.setRoomKitServerConfig(roomKitServerConfig);
NERoomKitOptions options = new NERoomKitOptions(appKey, false, serverConfig, "", extras);
NERoomKit.getInstance()
    .initialize(
        this, options, new NECallback2<Unit>() {
            @Override
            public void onError(int code, @Nullable String message) {
                super.onError(code, message);
            }

            @Override
            public void onSuccess(@Nullable Unit data) {
                super.onSuccess(data);
            }
        });
```
:::
::::::

### 私有化部署

若您使用 **私有化** 环境，则参考以下步骤进行初始化。

:::::: div linked-codes
::: code 方式一：通过 serverUrl 设置（推荐）

调用 `initialize` 方法初始化 NERoom 时，配置 `NERoomKitOptions` 的 `serverUrl` 参数为私有化服务器地址。示例代码如下:

```Java
String appKey = "your appKey";
HashMap<String, Object> extras = new HashMap<>();
NERoomKitOptions options = new NERoomKitOptions(appKey, false, null, serverUrl, extras);
NERoomKit.getInstance()
                .initialize(
                        this, options, new NECallback2<Unit>() {
                            @Override
                            public void onError(int code, @Nullable String message) {
                                super.onError(code, message);
                            }

                            @Override
                            public void onSuccess(@Nullable Unit data) {
                                super.onSuccess(data);
                            }
                        });
```
:::
::: code 方式二：通过配置文件设置

私有云服务器部署完成后，网易云信会提供给您对应的 NERoom 私有化配置文件（JSON 类型）。您在初始化 NERoom SDK 时，需要进行相关的配置，以便 SDK 能接入到目标私有云服务器上。具体配置步骤如下：

1. 将配置文件重命名为 `xkit_server.config`，并在构建项目前将配置文件添加到工程中 `app` 模块的 `assets` 目录下。

    <img alt="私有化配置文件.png" src="https://yx-web-nosdn.netease.im/common/def5b0be00c2519426f49ac90b2a23b1/私有化配置文件.png" style="width:80%;border: 1px solid #BFBFBF;">

2. 调用 `initialze` 方法初始化 NERoom SDK 时，在 `xkit_server.config` 配置文件中，将 `NERoomKitOptions` 的第二个参数 `useAssetServerConfig` 设置为 `true`。

    示例代码如下:

    ```Java
    String appKey = "your appKey";
    HashMap<String, Object> extras = new HashMap<>();
    NERoomKitOptions options = new NERoomKitOptions(appKey, true, null, "", extras);
    NERoomKit.getInstance()
                    .initialize(
                            this, options, new NECallback2<Unit>() {
                                @Override
                                public void onError(int code, @Nullable String message) {
                                    super.onError(code, message);
                                }

                                @Override
                                public void onSuccess(@Nullable Unit data) {
                                    super.onSuccess(data);
                                }
                            });

    ```
:::
::::::