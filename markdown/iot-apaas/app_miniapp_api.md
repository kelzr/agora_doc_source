本文提供客户端微信小程序 SDK 的 API 参考。

## 账号管理

<a name="registeraccount"></a>

### registerAccount

在第三方平台注册用户账号。

该方法适用于用户已经自行实现第三方账号管理系统的情况。声网不会保存用户名和密码，用户需要自行处理保存逻辑。

#### 方法原型

```javascript
(property) AccountManager.registerAccount: (username: any, password: any) => Promise<any>
```

#### 示例代码

```javascript
CallKitSDK.getAccountManager().registerAccount(username, password)
```

#### 参数

该方法参数为

- `username`：用户名。数据类型为 String。请确保同一频道内的 `username` 是唯一的。支持的字符集包含：

  - 10 个数字 0-9
  - 26 个大写英文字母 A-Z
  - 26 个小写英文字母 a-z

- `password`：密码。数据类型为 String。支持的字符集包含：

  - 10 个数字 0-9
  - 26 个大写英文字母 A-Z
  - 26 个小写英文字母 a-z

#### 返回值

该方法返回值为 `Promise<any>`。

<a name="initsdk"></a>

### initSdk

初始化 SDK 并登陆用户账号。

该方法需在 [registerAccount](#registeraccount) 之后调用。该方法中设置的 `username` 和 `password` 参数需与 `registerAccount` 中的对应参数保持一致，否则无法登陆。

#### 方法原型

```javascript
function initSdk(config: any, username: any, password: any): Promise<void>
```

#### 示例代码

```javascript
CallKitSDK.getAccountManager().initSdk(config, username, password)
```

#### 参数

该方法参数为

- `config`：包含以下参数：

  - `APPID`：声网的 App ID。详见[开通并配置声网灵隼服务](https://docs.agora.io/cn/iot-apaas/enable_agora_link)。
  - `PRODUCT_KEY`：Product Key。详见[开通并配置声网灵隼服务](https://docs.agora.io/cn/iot-apaas/enable_agora_link)。
  - `PROJECT_ID`：声网项目 ID。详见[开通并配置声网灵隼服务](https://docs.agora.io/cn/iot-apaas/enable_agora_link)。

- `username`：用户名。数据类型为 String。请确保同一频道内的 `username` 是唯一的。支持的字符集包含：

  - 10 个数字 0-9
  - 26 个大写英文字母 A-Z
  - 26 个小写英文字母 a-z

- `password`：密码。数据类型为 String。支持的字符集包含：

  - 10 个数字 0-9
  - 26 个大写英文字母 A-Z
  - 26 个小写英文字母 a-z

#### 返回值

该方法返回值为 `Promise<void>`。

<a name="destroysdk"></a>

### destroySdk

退出登陆用户账号并销毁 SDK。

#### 方法原型

```javascript
function destroySdk(): void
```

#### 示例代码

```javascript
CallKitSDK.getAccountManager().destroySdk();
```

#### 返回值

该方法返回值为 `void`。

## 用户管理

### getUserData

获取缓存中当前登录的用户信息。

#### 方法原型

```javascript
function getUserData()
```

#### 示例代码

```javascript
const userData = CallkitSDK.getInstanceManager().getUserData()
```

#### 返回值

该方法返回一个用户信息的 Object，其中包含如下字段：
- `userId`: 当前登录的用户 ID。
- `clientId`: 当前登录的用户的 client ID。

## 呼叫管理

### userPublishStream

发布音视频流。

#### 方法原型

```javascript
function userPublishStream()
```

#### 示例代码

```javascript
CallKitSDK.getInstanceManager().userPublishStream().then(
    (pushUrl) => {
        console.log("发布音视频流成功", pushUrl)
    }).catch((err) => {
        console.log("发布音视频流失败", err)
    })
```

#### 返回值

该方法返回一个 `Promise` 对象，包含如下参数：
- `url`: 发布的音视频流的 URL 地址，数据类型为 String。

### callDevice

发出呼叫邀请。

#### 方法原型

```javascript
async function callDevice(deviceMac, msg)
```

#### 示例代码

```javascript
CallKitSDK.getInstanceManager().callDevice({
    deviceId: "mydoorbell",
    attachMsg: "Hello World",
}).then(() => {
    console.log("呼叫设备成功")
}).catch((err) => {
    console.log("呼叫设备失败")
})
```

#### 参数

该方法参数为包含了被叫端设备信息的 Object 对象，包含如下字段：
- `deviceMac`: 被叫设备端的 ID，数据类型为 String。
- `msg`: 发送给被叫设备端的呼叫邀请消息，数据类型为 String。

#### 返回值

该方法返回值为 `Promise<void>`。

### answerDevice

接听来电。

#### 方法原型

```javascript
async function answerDevice()
```

#### 示例代码

```javascript
CallKitSDK.getInstanceManager().answerDevice().then((res) => {
    log.e("接听成功")
}).catch((err) => {
    log.e("接听失败")
})
```

#### 返回值

该方法返回值为 `Promise<void>`。

### hangupDevice

挂断来电。

#### 方法原型

```javascript
async function hangupDevice()
```

#### 示例代码

```javascript
CallKitSDK.getInstanceManager().hangupDevice().then((res) => {
    log.e("挂断成功")
}).catch((err) => {
    log.e("挂断失败")
})
```

#### 返回值

该方法返回值为 `Promise<void>`。

### muteLocal

停止或恢复发送本地音视频流。

该方法需要在加入频道后调用。

#### 方法原型

```javascript
(property) CallkitManager.muteLocal: (target: any, isMuted: any) => void
```

#### 示例代码

```javascript
CallKitSDK.getCallkitManager().muteLocal('video', true);
```

#### 参数

该方法参数为

- `target`：数据内容。数据类型为 String。支持的字符如下：

  - `video`：视频
  - `audio`：音频
  - `all`：音频和视频

- `isMuted`：是否停止发送本地音视频流：

  - `true`：停止发送
  - `false`：恢复发送

#### 返回值

该方法返回值为 `void`。

### setAudioCodec

设置音频编码格式。

SDK 默认使用 G722 编码格式，如需修改音频编码格式，你可以调用该方法。

#### 方法原型

```javascript
(property) CallkitManager.setAudioCodec: (audioCodec: any) => void
```

#### 示例代码

```javascript
CallKitSDK.getCallkitManager().setAudioCodec(e.detail.value);
```

#### 参数

该方法参数为 `audioCodec`，表示音频编码属性。数据类型为 String。支持的字符如下：

- `g711u`：G711U
- `g722`：G722

#### 返回值

该方法返回值为 `void`。

### getAudioCodec

获取音频编码格式。

该方法可以获取 SDK 当前设置的音频编码属性。

#### 方法原型

```javascript
(property) CallkitManager.getAudioCodec: () => "" | "g711u" | "g722"
```

#### 示例代码

```javascript
const currentAudioCodec = CallKitSDK.getCallkitManager().getAudioCodec();
```

#### 返回值

该方法返回值为 SDK 当前设置的音频编码属性：

- `g711u`：G711U
- `g722`：G722

## 事件管理

### peerRequestEventCallback

接收到对端用户发起的呼叫邀请时，SDK 会触发该回调。

#### 方法原型

```javascript
function peerRequestEventCallback(callback)
```

#### 示例代码

```javascript
onLoad: function() {
    this.peerRequestEventCallbackUnsubscribe = CallKitSDK.getInstanceManager().peerRequestEventCallback((event) => {
        // 代码
    })
},
onUnload: function() {
    // 取消订阅事件
    this.peerRequestEventCallbackUnsubscribe()
}
```

#### 参数

该方法参数为一个 `callback` 对象 `function<event>`，其中 `event` 对象为呼叫时必需的参数，包含：
-  `attachMsg`，即对端发起呼叫邀请时发出的消息。

#### 返回值

该方法会返回一个同步的 `peerRequestEventCallbackUnsubscribe` 函数，用于取消监听 `peerRequestEventCallback` 事件。当小程序退出、页面被卸载，或者不想再监听该事件时，可以调用该方法。


### peerStreamAddedEventCallback

接收到对端用户发布的音视频流时，SDK 会触发该回调。

#### 方法原型

```javascript
function peerStreamAddedEventCallback(callback)
```

#### 示例代码

```javascript
onLoad: function() {
    this.peerStreamAddedEventCallbackUnsubscribe = CallKitSDK.getInstanceManager().peerStreamAddedEventCallback((playUrl) => {
        // 代码
    })
},
onUnload: function() {
    // 取消订阅事件
    this.peerStreamAddedEventCallbackUnsubscribe()
}
```

#### 参数

该方法参数为一个 `callback` 对象 `function<playUrl>`，其中 `playUrl` 为用于音视频流下行播放的 URL 地址。

#### 返回值

该方法会返回一个同步的 `peerStreamAddedEventCallbackUnsubscribe` 函数，用于取消监听 `peerStreamAddedEventCallback` 事件。当小程序退出、页面被卸载，或者不想再监听该事件时，可以调用该方法。


### peerAnswerEventCallback

对端用户接听发出的呼叫邀请时，SDK 会触发该回调。

#### 方法原型

```javascript
function peerAnswerEventCallback(callback)
```

#### 示例代码

```javascript
onLoad: function() {
    this.peerAnswerEventCallbackUnsubscribe = CallKitSDK.getInstance().peerAnswerEventCallback(() => {
        // 代码
    })
},
onUnload: function() {
    // 取消订阅事件
    this.peerAnswerEventCallbackUnsubscribe()
}
```

#### 参数

该方法参数为一个 `callback` 对象 `function<void>`。

#### 返回值

该方法会返回一个同步的 `peerAnswerEventCallbackUnsubscribe` 函数，用于取消监听 `peerAnswerEventCallback` 事件。当小程序退出、页面被卸载，或者不想再监听该事件时，可以调用该方法。

### peerHangupEventCallback

对端用户挂断电话时，SDK 会触发该回调。

#### 方法原型

```javascript
function peerHangupEventCallback(callback)
```

#### 示例代码

```javascript
onLoad: function() {
    this.peerHangupEventCallbackUnsubscribe = CallKitSDK.getInstanceManager().peerHangupEventCallback(() => {
        // 代码
    })
},
onUnload: function() {
    // 取消订阅事件
    this.peerHangupEventCallbackUnsubscribe()
}
```

#### 参数

该方法参数为一个 `callback` 对象 `function<void>`。

#### 返回值

该方法会返回一个同步的 `peerHangupEventCallbackUnsubscribe` 函数，用于取消监听 `peerHangupEventCallback` 事件。当小程序退出、页面被卸载，或者不想再监听该事件时，可以调用该方法。

### peerBusyEventCallback

对端用户占线时，SDK 会触发该回调。

#### 方法原型

```javascript
function peerBusyEventCallback(callback)
```

#### 示例代码

```javascript
onLoad: function() {
    this.peerBusyEventCallbackUnsubscribe = CallKit.getInstanceManager().peerBusyEventCallback(() => {
        // 代码
    })
},
onUnload: function() {
    // 取消订阅事件
    this.peerBusyEventCallbackUnsubscribe()
}
```

#### 参数

该方法参数为一个 `callback` 对象 `function<void>`。

#### 返回值

该方法会返回一个同步的 `peerBusyEventCallbackUnsubscribe` 函数，用于取消监听 `peerBusyEventCallback` 事件。当小程序退出、页面被卸载，或者不想再监听该事件时，可以调用该方法。

### userSessionEndEventCallback

MQTT 连接状态或用户登录状态发生改变时，SDK 会触发该回调。你需要提醒用户退出登录。

#### 方法原型

```javascript
function userSessionEndEventCallback(callback)
```

#### 示例代码

```javascript
onLoad: function() {
    this.userSessionEndEventCallbackUnsubscribe = CallKitSDK.getInstanceManager().userSessionEndEventCallback(() => {
        // 代码
    })
},
onUnload: function() {
    // 取消订阅事件
    this.userSessionEndEventCallbackUnsubscribe()
}
```

#### 参数

该方法参数为一个 `callback` 对象 `function<void>`。

#### 返回值

该方法会返回一个同步的 `userSessionEndEventCallbackUnsubscribe` 函数，用于取消监听 `userSessionEndEventCallback` 事件。当小程序退出、页面被卸载，或者不想再监听该事件时，可以调用该方法。

### mqttReconnectedEvent

重连 MQTT 时，SDK 会触发该回调。

#### 方法原型

```javascript
function mqttReconnectedEvent(callback: any): () => void
```

#### 示例代码

```javascript
CallKitSDK.getCallkitManager().mqttReconnectedEvent(() => {
        log.i('mqtt reconnected event')
    })
```

#### 参数

该方法参数为一个 `callback` 对象 `function<void>`。

#### 返回值

该方法返回值为 `void`。

### mqttDisconnectedEvent

与 MQTT 断开连接时，SDK 会触发该回调。

#### 方法原型

```javascript
function mqttReconnectedEvent(callback: any): () => void
```

#### 示例代码

```javascript
CallKitSDK.getCallkitManager().mqttDisconnectedEvent(() => {
        log.i('mqtt disconnected event')
    })
```

#### 参数

该方法参数为一个 `callback` 对象 `function<void>`。

#### 返回值

该方法返回值为 `void`。