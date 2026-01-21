本文介绍 Web 端快速集成并初始化网易云信房间组件（NERoom SDK）的操作步骤，帮助您快速集成并初始化 NERoom SDK。

## 开发环境

在开始运行工程之前，请您准备以下开发环境：

- 安全环境：HTTPS 环境或者本地连接 localhost（127.0.0.1）环境。
- 浏览器：谷歌 Chrome 72 及以上版本、苹果 Safari 12 及以上版本。

## 前提条件

根据本文操作前，请确保您已经在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上完成了以下设置：

- 创建至少一个应用。详细步骤请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。
- 为应用开通一个房间组建模板。详细步骤请参考 [开通或试用服务](https://doc.yunxin.163.com/console/concept/zc3NDYzNzc?platform=console)。

## 集成 SDK

本文主要介绍通过 npm 集成 NERoom SDK。

1. 安装 npm。操作步骤请参考 [npm 官方文档](https://www.npmjs.cn/getting-started/installing-node/)。

2. 在项目中使用 npm 安装 SDK 包。

    ```NPM
    npm install neroom-web-sdk --save
    ```

3. 在项目脚本里引入模块。

    ```JavaScript
    import WebRoomkit from 'neroom-web-sdk'
    ```

## 初始化 SDK

请参考以下示例代码进行初始化实例或者销毁实例。

完整的初始化配置文档请参考 [`getService`](https://doc.yunxin.163.com/neroom/references/web/typedoc/Latest/zh/html/classes/platform_web.default.html#getService)。

```JavaScript
import WebRoomkit from 'neroom-web-sdk'

// 初始化
const roomkitInstance = new WebRoomkit()
roomkitInstance.initialize({
    appKey: 'appKey', // NERoom 应用的 appkey
});

// 获取 authService 和 roomService
const NEAuthService = roomkitInstance.authService;
const NERoomService = roomkitInstance.roomService;

// 销毁 SDK
roomkitInstance.release()
```