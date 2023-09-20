# AUIKitKaraoke swift API（iOS）

本文介绍 AUIKitKaraoke 组件的 API。

源码文件？

## KaraokeUIKit

提供 `AUIKitKaraoke` 的核心类，可用于实现组件初始化、创建、销毁房间等基础功能。

### setup

```swift
func setup(roomConfig: AUICommonConfig,
           ktvApi: KTVApiDelegate? = nil,
           rtcEngine: AgoraRtcEngineKit? = nil,
           rtmClient: AgoraRtmClientKit? = nil) 
```

初始化 `scenekit`。

在调用 `KaraokeUIKit` 类下的其他 API 前，你需要先调用该方法进行初始化。

**参数**

- `roomConfig`：初始化设置，详见 [AUICommonConfig](#AUICommonConfig)。
- `ktvApi`：[KTVApiDelegate](https://docportal.shengwang.cn/cn/online-ktv/ktv_api_oc?platform=iOS#ktvapidelegate) 对象。如果你的项目中还未集成场景化 API，请传入 `nil`，`scenekit` 内部会自行创建 `KTVApiDelegate` 对象。
- `rtcEngine`：[AgoraRtcEngineKit](https://docportal.shengwang.cn/cn/online-ktv/API%20Reference/ios_ng/API/rtc_interface_class.html#class_irtcengine) 对象。如果的你的项目中还未集成声网 RTC SDK，请传入 `nil`，`scenekit` 内部会自行创建 `AgoraRtcEngineKit` 对象。
- `rtmClient`：[AgoraRtmClientKit](https://doc.shengwang.cn/doc/rtm2/oc/landing-page) 对象。如果的你的项目中还未集成声网 RTM SDK，请传入 `nil`，`scenekit` 内部会自行创建 `AgoraRtmClientKit` 对象。

### createRoom

```swift
func createRoom(roomInfo: AUICreateRoomInfo,
                success: ((AUIRoomInfo?)->())?,
                failure: ((Error)->())?)
```

创建 K 歌房间。

在 K 歌场景下，创建房间的人自动成为房主。成功创建房间后，会返回创建的房间信息。//TODO

**参数**

- `roomInfo`：一个 `AUICreateRoomInfo` 对象，包含要创建的房间信息 。
- `success`：（可选）成功回调闭包，当房间创建成功时将被调用。闭包参数为一个 `AUIRoomInfo` 对象，表示创建的房间信息。
- `failure`：（可选）失败回调闭包，当房间创建失败时将被调用。闭包参数为一个 `Error` 对象，表示创建失败的错误信息。

### getRoomList

```swift
func getRoomInfoList(lastCreateTime: Int64?, 
                     pageSize: Int, 
                     callback: @escaping AUIRoomListCallback)
```

获取 K 歌房间列表。

你可以调用该方法获取当前的已创建的 K 歌房间列表。

**参数**

- `lastCreateTime`：（可选）开始时间 //TODO 是指获取某一特定时间之后创建的房间列表吗？
- `pageSize`：房间列表页数，默认从 0 还是从 1开始？//TODO
- `callback`：用于处理获取房间列表结果的回调闭包，该闭包在函数执行完成后仍然可以被调用。回调闭包接受一个 `AUIRoomListCallback` 对象作为参数。//TODO

### launchRoom

```swift
func launchRoom(roomInfo: AUIRoomInfo,
                appId: String? = nil,
                config: AUIRoomConfig,
                karaokeView: AUIKaraokeRoomView) 
```

拉起 K 歌房间。

当你成功调用 `createRoom` 创建房间后，你可以调用该方法拉起房间。

**参数**

- `roomInfo`：需要拉起的房间信息，详见 [AUIRoomInfo](#AUIRoomInfo)。
- `appId`：（可选）你的项目在控制台注册的 App ID。如果在调用 `setup` 进行初始化时设置了 `appId`，则此处为必填，否则可传入 `nil`。
- `config`：房间配置，详见 [AUIRoomConfig](#AUIRoomConfig)。
- `karaokeView`： `AUIKaraokeRoomView` 对象，用于显示 K 歌房间的界面。

### destroyRoom

```kotlin
func destoryRoom(roomId: String)
```

销毁 K 歌房间。

**参数**

- `roomId`：要销毁的房间的 ID。

## 数据模型

### <h3 className="anchor" id="AUICommonConfig"> AUICommonConfig</h3>

```swift
open class AUICommonConfig: NSObject {
    public var appId: String = ""
    public var host: String = ""
    public var userId: String = ""
    public var userName: String = ""
    public var userAvatar: String = ""
}
```

通用设置。

**参数**

- `appId`：你的项目在控制台注册的 App ID，详见[获取 App ID](https://docportal.shengwang.cn/cn/Agora%20Platform/get_appid_token?platform=All%20Platforms#获取-app-id)。
- `host`：业务后端服务域名。
- `userId`：用户 ID。
- `userName`：用户名。
- `userAvatar`：用户头像。

### <h3 className="anchor" id="AUIRoomInfo">AUIRoomInfo</h3>

```kotlin
open class AUIRoomInfo: AUICreateRoomInfo {
    public var roomId: String = "" 
    public var owner: AUIUserThumbnailInfo? 
    public var memberCount: UInt = 0 
    public var createTime: Int64 = 0 
}
```

K 歌房间的相关信息。

**参数**

- `roomId`：房间 ID。
- `roomOwner`：房主信息，详见 [AUIUserThumbnailInfo](#AUIUserThumbnailInfo)。
- `onlineUsers`：房间内的人数。
- `createTime`：房间创建的时间（单位？//TODO）

### <h3 className="anchor" id="AUIUserThumbnailInfo">AUIUserThumbnailInfo</h3>

```swift
open class AUIUserThumbnailInfo: NSObject,AUIUserCellUserDataProtocol {
    
    public var userId: String = ""      //用户Id
    public var userName: String = ""    //用户名
    public var userAvatar: String = ""  //用户头像
    public var seatIndex: Int = -1 //用户是否在麦上
    public var isOwner: Bool = false //是否是owner

    public func isEmpty() -> Bool {
        guard userId.count > 0, userName.count > 0 else {return true}
        
        return false
    }
}
```

K 歌中房主的相关信息。//TODO 原型和input给的参数不一致

**参数**

- `userId`：房主的 ID。
- `userName`：房主的用户名。
- `userAvatar`：房主的头像。

### <h3 className="anchor" id="AUIException">AUIException</h3>

//TODO iOS没有这个

### <h3 className="anchor" id="AUIRoomConfig">AUIRoomConfig</h3>

K 歌房间设置。

```kotlin
open class AUIRoomConfig: NSObject {
    public var channelName: String = ""     //正常rtm使用的频道
    public var rtmToken007: String = ""     //rtm login用，只能007
    public var rtcToken007: String = ""     //rtm join用
    public var rtcChannelName: String = ""  //rtc使用的频道
    public var rtcRtcToken: String = ""  //rtc join使用
    public var rtcRtmToken: String = ""  //rtc mcc使用
    public var rtcChorusChannelName: String = ""  //rtc 合唱使用的频道
    public var rtcChorusRtcToken: String = ""  //rtc 合唱join使用
}
```

**参数**

- `channelName`：主频道名，一般为 `roomId`。在合唱场景下，领唱需要加入两个频道，在主频道发布人声和播放器的混流，在合唱频道发布麦克风采集的音频流，伴唱需要加入合唱频道来同步领唱的人声。

- `rtmToken007`：加入主频道时的 RTM Token。//TODO 这个 token 是用于什么鉴权？

  <Admonition type="caution" title="注意">

  请确保你使用的 RTM Token 是 AccessToken2。

  </Admonition>

- `rtcToken007`：加入主频道时用于鉴权的 RTC Token。

- `rtcChannelName`：音视频频道名，一般为{roomId}_rtc。//TODO 音视频频道和主频道之间有什么区别？

- `rtcRtcToken`：

- `rtcRtmToken`：用于音乐内容中心鉴权的 RTM Token。

  <Admonition type="caution" title="注意">

  请确保你使用的 RTM Token 是 AccessToken。//rtc mcc 使用是啥意思？

  </Admonition>

- `rtcChorusChannelName`：合唱频道名。在合唱场景下，领唱需要加入两个频道，在主频道发布人声和播放器的混流，在合唱频道发布麦克风采集的音频流，伴唱需要加入合唱频道来同步领唱的人声。//TODO 独唱场景下，这个参数为空？

- `rtcChorusRtcToken`：根据合唱频道名和用户 ID 生成的 RTC Token，用于加入合唱频道时进行鉴权。//TODO 在独唱场景下，该参数可以为空？





 