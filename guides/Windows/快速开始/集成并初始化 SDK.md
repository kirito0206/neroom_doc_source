本文介绍 Windows 和 macOS 端快速集成并初始化网易云信房间组件（NERoom SDK）的操作步骤，帮助您快速集成并初始化 NERoom SDK。

## 开发环境

在开始运行工程之前，请您准备以下开发环境：

| 名称 | 要求 |
| :---- | :---- |
| IDE | Visual Studio 2017 及以上 |
| OS | Windows7 及以上 |

## 前提条件

根据本文操作前，请确保您已经在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上完成了以下设置：

- 创建至少一个应用。详细步骤请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。
- 为应用开通一个房间组建模板。详细步骤请参考 [开通或试用服务](https://doc.yunxin.163.com/console/concept/zc3NDYzNzc?platform=console)。

## 集成 SDK

1. 单击下载 [NERoom Windows C++ SDK Zip 文件](https://doc.yunxin.163.com/neroom/resource?platform=windows)。

    下载完成后，Windows SDK 目录结构如下：

    ```
    ├─bin     包含 SDK 的二进制文件
    ├─include 您需要引入的头文件目录
    └─lib     您需要引入的依赖库文件
    ```

    库文件的说明如下表所示。您可以根据您需要集成的能力，引入对应的库文件。

    功能/插件 | 动态库
    ---- | ---- | ----
    所有能力 | 所有 `dll` 动态库
    基础功能 | <ul> <li>`nertc_sdk.dll`<li>`protoopp.dll`<li> `SDL2.dll`<li> `nim.dll` <li> `nim_chatroom.dll` <li> `protoopp.dll` <li>`roomkit.dll` <li>`roomkitd.dll`（Debug 版本） <li>`h_available.dll`
    美颜 | <ul><li>美颜库：`NERtcBeauty.dll` <li>人脸检测库：`NERtcFaceDetect.dll` <li>机器学习库：`NERtcnn.dll`
    虚拟背景 | <ul><li>虚拟背景库：`NERtcPersonSegment.dll` <li>机器学习库：`NERtcnn.dll`
    音频 AI 降噪 | AI 降噪库：`NERtcAiDenoise.dll`
    AI 啸叫检测 | <ul><li>AI 啸叫检测库：`NERtcAiHowling.dll`<li>机器学习库：`NERtcnn.dll`
    人脸增强 | <ul><li>人脸增强库：`NERtcFaceEnhance.dll`<li>机器学习库：`NERtcnn.dll`
    视频降噪 | 视频降噪库：`NERtcVideoDenoise.dll`
    视频 AI 超分 | 视频 AI 超分库：`NERtcSuperResolution.dll`
    屏幕增强 | <ul><li>屏幕增强库：`NERtcScreenShareEnhance.dll`<li>机器学习库：`NERtcnn.dll`

2. 将 bin 目录设置为项目的依赖目录，或放置与您已有项目的可搜索环境变量中、将 include 文件夹添加为项目的头文件搜索路径、将 lib 文件夹添加为项目的库文件搜索路径并引入库文件到项目中。以下为 Qt5 Qmake 配置示例：

    ```Qt5
    win32 {
        DEPENDPATH += $$PWD/bin
        INCLUDEPATH += $$PWD/include
        LIBS += -L$$PWD/lib -lroomkit
    }
    ```
    
## 初始化 SDK

创建 NERoom 实例：

```C++
createNERoomKit()
```

销毁 NERoom 实例：

```C++
destroyNERoomKit()
```

实现初始化功能的示例代码如下：

```C++
auto roomkit =createNERoomKit();
NERoomKitOptions options;
options.appKey = "you appkey";
options.logPath = QStandardPaths::writableLocation(QStandardPaths::GenericDataLocation).toStdString().append("/Netease/RoomkitSample");
options.logLevel = NEVERBOSE;
roomkit->initialize(options, [&bInit](int errorCode, const std::string& errorMsg){
});
```