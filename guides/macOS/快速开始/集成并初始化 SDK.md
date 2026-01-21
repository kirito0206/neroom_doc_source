本文介绍 Windows 和 macOS 端快速集成并初始化网易云信房间组件（NERoom SDK）的操作步骤，帮助您快速集成并初始化 NERoom SDK。

## 开发环境

在开始运行工程之前，请您准备以下开发环境：

| 名称 | 要求 |
| :---- | :---- |
| IDE | Xcode 11.3.1 及以上 |
| OS | macOS 10.14 及以上 |

## 前提条件

根据本文操作前，请确保您已经在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上完成了以下设置：

- 创建至少一个应用。详细步骤请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。
- 为应用开通一个房间组建模板。详细步骤请参考 [开通或试用服务](https://doc.yunxin.163.com/console/concept/zc3NDYzNzc?platform=console)。

## 集成 SDK

1. 单击下载 [NERoom macOS C++ SDK Zip 文件](https://doc.yunxin.163.com/neroom/resource?platform=macOS)。

    macOS SDK 目录结构如下：

    ```
    ├─include       您需要引入的头文件目录
    ├─lib           包含 SDK 所需的 framework 和 dylib 文件
    ├─resource      基础美颜资源文件
    ├─resources     美颜滤镜、贴图等高级资源文件
    └─tests         库的单元测试执行程序及集成测试执行程序，用于验证功能稳定性
    ```

2. 根据您需要集成的能力，选择对应的 `XX.framework` 文件，并引入到您的工程中，以下为 Qt5 Qmake 配置示例：

    ```Qt5
    macx {
        INCLUDEPATH += $$PWD/roomkit/include
        # 如果是 debug 调试，请链接 libroomkitd.dylib，以下为 release 示例
        LIBS += -L$$PWD/roomkit/lib -lroomkit

        DESTDIR = $$PWD/bin
        DEPENDPATH += $$PWD/bin

        # 如果是 debug 调试，请拷贝打包 libroomkitd.dylib，以下为 release 示例
        ROOM_KIT_FRAMEWORK.files = $$PWD/roomkit/lib/libroomkit.dylib \
                                $$PWD/roomkit/lib/nertc_sdk_Mac.framework \
                                $$PWD/roomkit/lib/NERtcAiHowling.framework \
                                $$PWD/roomkit/lib/NERtcFaceDetect.framework \
                                $$PWD/roomkit/lib/NERtcVideoDenoise.framework \
                                $$PWD/roomkit/lib/NERtcFaceEnhance.framework \
                                $$PWD/roomkit/lib/NERtcPersonSegment.framework \
                                $$PWD/roomkit/lib/NERtcnn.framework \
                                $$PWD/roomkit/lib/NERtcBeauty.framework \
                                $$PWD/roomkit/lib/NERtcAiDenoise.framework \
                                $$PWD/roomkit/lib/NERtcScreenShareEnhance.framework \
                                $$PWD/roomkit/lib/libnim_chatroom.dylib \
                                $$PWD/roomkit/lib/libh_available.dylib \
                                $$PWD/roomkit/lib/libnim.dylib \
        ROOM_KIT_FRAMEWORK.path = /Contents/Frameworks
        QMAKE_BUNDLE_DATA += ROOM_KIT_FRAMEWORK
    }
    ```
    功能/插件 | `framework` 动态库
    ---- | ---- | ----
    所有能力 | 所有 `framework` 动态库
    音视频 | <ul><li> `libroomkit.dylib` <li> `nertc_sdk_Mac.framework`
    美颜 | <ul><li>美颜库：`NERtcBeauty.framework` <li>人脸检测库：`NERtcFaceDetect.framework` <li>机器学习库：`NERtcnn.framework`
    虚拟背景 | <ul><li>虚拟背景库：`NERtcPersonSegment.framework` <li>机器学习库：`NERtcnn.framework`
    音频 AI 降噪 | AI 降噪库：`NERtcAiDenoise.framework`
    AI 啸叫检测 | <ul><li>AI 啸叫检测库：`NERtcAiHowling.framework`<li>机器学习库：`NERtcnn.framework`
    人脸增强 | <ul><li>人脸增强库：`NERtcFaceEnhance.framework`<li>机器学习库：`NERtcnn.framework`
    视频降噪 | 视频降噪库：`NERtcVideoDenoise.framework`
    屏幕增强 | <ul><li>屏幕增强库：`NERtcScreenShareEnhance.framework`<li>机器学习库：`NERtcnn.framework`

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