如果您的 App 之前已集成了 IM SDK，需要再通过 NERoom 集成其他能力，例如音视频通话、白板等，您可以在 NERoom 中开启 IM 复用功能，帮助您在应用中同时集成并使用 NERoom SDK 和 IM SDK。

## 技术原理

IM SDK 是网易云信提供的 IM 即时通讯 SDK，NERoom 在底层实现上，依赖了 IM SDK 提供的 IM 长连接通道。建立起长连接通道时，需要使用 IM 账号完成 IM SDK的登录。因此，在 NERoom 中，一个 NERoom 账号会绑定一个 IM 账号。


当 App 调用 NERoom 的 <a href="https://doc.yunxin.163.com/neroom/docs/DA5MTk3NDk?platform=iOS" target="_blank">login 接口</a>进行登录时，NERoom 内部会查询并获取到该 NERoom 账号唯一绑定的 IM 账号，并自动登录 IM SDK 以建立起长连接通道。此时，如果 App 同时也集成了 IM SDK，并已经进行了 IM SDK 登录操作，那么由于 IM SDK 同时只允许登入一个账号，因此会造成此次 NERoom 登录失败。


默认场景中，NERoom 服务端在创建 NERoom Account 时，会自动创建对应的 IM Account，因此您一般无需关心。

**若您之前已经存在 IM 账号，需要在应用中同时集成 NERoom SDK 和 IM SDK， 您需要开启 IM 复用功能，将 NERoom Account 绑定到已存在的 IM Account**。


## 步骤1 绑定 NERoom 账号
1. 开通 NERoom 房间组件。

    
    若您已经创建了IM SDK 的应用，您只需要在该应用下开通 NERoom 房间组件即可，操作步骤请参见<a href="https://doc.yunxin.163.com/neroom/docs/zQ3NzI3OTE?platform=iOS" target="_blank">开通 NERoom 房间组件</a>。

2. <span id="创建一个与 IM 账号绑定的 NERoom 账号">创建一个与 IM 账号绑定的 NERoom 账号</span>。

    在登录 IM SDK 前，需要先完成 IM 账号的注册，其中每个 IM 账号对应唯一的 imAccid 和 imToken。

    假设当前已有一个 IM 账号，其中 imAccid 和 imToken 值分别为 "myAccid" 和 "myToken"。若现在需要复用该 IM 账号，则需要调用 <a href="https://doc.yunxin.163.com/neroom/docs/TY1NzM5MjQ?platform=server" target="_blank">创建 NERoom 账号</a> 的服务端接口创建一个与该 IM 账号绑定的 NERoom 账号，请求时需要在请求参数中将 imAccid 和 imToken 分别设置为该 IM 账号的 imAccid 和 imToken，即 "myAccid" 和 "myToken" 。
 

    至此您就创建了一个与该 IM 账号绑定的 NERoom 账号。同时该服务端接口返回 NERoom 账号的 userUuid 和 userToken，可用来登录 NERoom。



## 步骤2 复用 IM 账号

完成步骤 1 后，您还需要在客户端上开启 IM 复用功能，具体步骤如下：

1. 初始化 IM SDK 与 NERoom。
    
    请在 `Application` 中初始化 IM SDK 与 NERoom，并在初始化 NERoom 时，通过 `NERoomKitOptions#reuseIM` 开启 IM 复用功能。

    ::: note notice
    开启 IM 复用功能后，NERoom 不会自动初始化 IM SDK，需要您在应用层单独进行 IM SDK 初始化。
    :::

    **示例代码**如下：

    ```
    // 1. 初始化 IM SDK
    std::string imAppKey;
    std::string appDataDir;
    std::string appInstallDir;
    nim::SDKConfig sdkConfig;
    nim::Client::Init(imAppKey, appDataDir, appInstallDir, sdkConfig));

    // 2. 初始化 NERoom
    NERoomKitOptions options;
    options.reuseIM = true;
    neroom::createNERoomKit()->initialize(options, [=](int code, const std::string& msg) {});

    ```

2. 登录 IM SDK。

    开启复用功能时，应用层需要确保已先使用**与该 NERoom 账号绑定的 IM 账号**登录 IM SDK。登录时机由业务决定，只需要确保在进行 NERoom 登录前完成 IM 登录即可。
 
    请通过 IM 账号的 imAccid 和 imToken 完成登录，**示例代码**如下：

    ```
    nim::Client::Login(imAppKey, imAccid, imToken, [=](const LoginRes& loginResult) {});
    ```

3. 登录 NERoom。

    使用对应的 NERoom 账号登录 NERoom，请通过<a href="#创建一个与 IM 账号绑定的NERoom 账号">创建 NERoom 账号</a>时返回的 userUuid（对应 accountId） 和 userToken（对应 accountToken） 完成登录，**示例代码**如下：

    ```
    //以使用账号 ID 和 Token 登录为例
    neroom::createNERoomKit()->getAuthService()->login(accountId, accountToken, [=](int code, const std::string& msg) {});
    ```

    ::: note note
    您可以通过此接口回调中返回的错误码判断是否登录成功。若登录成功则说明 IM 复用功能启用成功，否则启用失败。
    :::

## 相关参考

### **复用 IM 前后 NERoom 的行为差异**

启用或关闭 IM 复用功能时，NERoom 的行为表现存在不一致的情况，具体请参考如下表格。

| <div style="width:80px">IM 复用功能状态</div>	| <div style="width:80px">初始化</div> 	| <div style="width:80px">登录</div> 	| 
|---	|---	|---	|---	|
| 关闭 	| NERoom 会自动初始化 IM SDK 	| 会获取NERoom 账号对应的 IM 账号自动登录 IM SDK 	| 
| 启用 	| NERoom 不会自动初始化 IM SDK，由应用层负责初始化 	| 不会自动登录 IM SDK，会判断 NERoom 账号与当前已登录的 IM 账号是否是绑定关系，以决定 NERoom 是否登录成功 	| 

### **错误码**

| 错误码 	| 说明 	| 解决办法 	|
|---	|---	|---	|
| 100004 	| 开启了 IM 复用，请先登录 IM 	| 确保 IM SDK 已经登录后再登录 NERoom 	|
| 100005 	| 开启了 IM 复用，IM 账号不匹配 	| 重新创建 NERoom 账号，并传入对应需绑定 IM 账号的 imAccid与 imToken 	|