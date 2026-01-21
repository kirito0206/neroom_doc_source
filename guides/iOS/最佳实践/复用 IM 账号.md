如果您的 App 之前已集成了 IM SDK，需要再通过 NERoom 集成其他能力，例如音视频通话、白板等，您可以在 NERoom 中开启 IM 复用功能，帮助您在应用中同时集成并使用 NERoom SDK 和 IM SDK。

## 技术原理

IM SDK 是网易云信提供的 IM 即时通讯 SDK，NERoom 在底层实现上，依赖了 IM SDK 提供的 IM 长连接通道。建立起长连接通道时，需要使用 IM 账号完成 IM SDK的登录。因此，在 NERoom 中，一个 NERoom 账号会绑定一个 IM 账号。


当 App 调用 NERoom 的 <a href="https://doc.yunxin.163.com/neroom/references/iOS/jazzy/Latest/zh/html/Classes/NEAuthService.html#/c:@M@NERoomKit@objc(cs)NEAuthService(im)loginWithAccount:token:callback:" target="_blank">login 接口</a>进行登录时，NERoom 内部会查询并获取到该 NERoom 账号唯一绑定的 IM 账号，并自动登录 IM SDK 以建立起长连接通道。此时，如果 App 同时也集成了 IM SDK，并已经进行了 IM SDK 登录操作，那么由于 IM SDK 同时只允许登入一个账号，因此会造成此次 NERoom 登录失败。


默认场景中，NERoom 服务端在创建 NERoom Account 时，会自动创建对应的 IM Account，因此您一般无需关心。

**若您之前已经存在 IM 账号，需要在应用中同时集成 NERoom SDK 和 IM SDK， 有两种方式可以实现：**

## 方式1： 直接使用 NERoom 提供的 `loginByIM` 接口登录

**示例代码**如下：

```
    NERoomKit.shared().authService.loginByIM(account: "your account", staticToken: "your token", dynamicToken: nil, imAuthType: .static) { code, msg, obj in
      if code == 0 {
        print("login 成功")
      } else {
       print("login 失败 \(msg)")
      }
    }
```    


## 方式2： 绑定 IM Account 后，使用 login 接口登录

将 NERoom Account 绑定到已存在的 IM Account，然后使用 NERoom 提供的 `login` 接口登录。

1. 绑定 NERoom 账号。
    1. 开通 NERoom 房间组件。

        
        若您已经创建了IM SDK 的应用，您只需要在该应用下开通 NERoom 房间组件即可，操作步骤请参见<a href="https://doc.yunxin.163.com/neroom/concept/DQzNTEwMDE?platform=client" target="_blank">开通 NERoom 房间组件</a>。


    2. <span id="创建一个与 IM 账号绑定的 NERoom 账号">创建一个与 IM 账号绑定的 NERoom 账号</span>。

        在登录 IM SDK 前，需要先完成 IM 账号的注册，其中每个 IM 账号对应唯一的 imAccid 和 imToken。

        假设当前已有一个 IM 账号，其中 imAccid 和 imToken 值分别为 "myAccid" 和 "myToken"。若现在需要复用该 IM 账号，则需要调用 <a href="https://doc.yunxin.163.com/neroom/server-apis/TY1NzM5MjQ?platform=server" target="_blank">创建 NERoom 账号</a> 的服务端接口创建一个与该 IM 账号绑定的NERoom账号，请求时需要在请求参数中将 imAccid 和 imToken 分别设置为该 IM 账号的 imAccid 和 imToken，即 "myAccid" 和 "myToken" 。
    

        至此您就创建了一个与该 IM 账号绑定的 NERoom 账号。同时该服务端接口返回 NERoom 账号的 userUuid 和 userToken，可用来登录 NERoom。

2. 登录 NERoom。

    使用对应的 NERoom 账号登录 NERoom，请通过<a href="#创建一个与 IM 账号绑定的NERoom 账号">创建 NERoom 账号</a>时返回的 userUuid（对应 accountId） 和 userToken（对应 accountToken） 完成登录。
    
    **示例代码**如下：

    ```
    //以使用账号 ID 和 Token 登录为例
    NERoomKit.shared().authService.login(account: account,
                                         token: token) { code, msg, _ in
      if code == 0 {
        print("login 成功")
      } else {
        print("login 失败 \(msg)")
      }
    }
    
    ```

    ::: note note
    您可以通过此接口回调中返回的错误码判断是否登录成功。若登录成功则说明 IM 复用功能启用成功，否则启用失败。
    :::

