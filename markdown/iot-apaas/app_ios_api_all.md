本页提供声网灵隼客户端 iOS API 参考。

## 概述

| 类 | 描述 |
| --- | --- |
| [IAgoraIotAppSdk](#sdk) | 提供总的服务接口，各子功能通过 `getInstance` 方法获取 |
| [IAccountMgr](#accountmgr) | 账号注册、登录与登出 |
| [ICallkitMgr](#callkitmgr) | 来电呼叫管理，包括拨号、接听、挂断等 |
| [IDeviceMgr](#devicemgr) | 设备管理接口，包括设备绑定、设备解绑、设备列表查询、设备分享管理等 |
| [IDeviceMgrCallback](#devicecallback) | 设备管理事件 |
| [IAlarmMgr](#alarmmgr)  | 设备告警信息管理，包括移动侦察、有人经过等 |
| [INotificationMgr](#notificationmgr) | 设备系统信息管理，包括设备上线、下线、绑定、解绑等事件管理 |
| [INotificationMgrCallback](#notificationcallback) | 设备系统信息管理事件 |
| [ErrCode](#errcode) | Iot SDK 在使用过程中的报错信息 |

<a name="sdk"></a>

## IAgoraIotAppSdk

SDK 引擎接口。

<a name="getmqttisconnected"></a>

#### getMqttIsConnected

```swift
func getMqttIsConnected() -> Bool
```

获取当前 MQTT 模块的连接状态。

**返回**

- `true`: 已连接。
- `false`: 已断开连接。

<a name="initialize"></a>

#### initialize

```swift
func initialize(initParam: InitParam,sdkStatus:@escaping(SdkStatus,String)->Void,callbackFilter:@escaping(Int,String)->(Int,String)) -> Int
```

初始化 SDK。

**参数**

| 参数 | 描述 |
| --- | --- |
| `initParam` | SDK 初始化参数，详见 [InitParam](#initparam)。 |
| `sdkStatus` | SDK 状态，详见 [SdkStatus](#SdkStatus)。 |
| `callbackFilter` | 回调函数返回错误码/错误消息的集中过滤。所有带有 result 回调的接口，都会在调用 result 回调前触发该回调，返回 (ErrCode,ErrMessage)。 |

<a name="release"></a>

#### release

```
 func release()
```

释放 SDK 所有资源。

#### accounrMgr

```swift
var accountMgr: IAccountMgr{get}
```

获取账号管理接口。

**返回**

- 账号管理接口，详见 [IAccountMgr](#accountmgr)。

#### callkitMgr

```swift
var callkitMgr: ICallkitMgr{get}
```

获取呼叫系统接口。

**返回**

- 呼叫系统接口，详见 [ICallkitMgr](#callmgr)。

#### deviceMgr

```swift
var deviceMgr: IDeviceMgr{get}
```

获取设备管理接口。

**返回**

- 设备管理接口，详见 [IDeviceMgr](#devicemgr)。

#### alarmMgr

```swift
var alarmMgr: IAlarmMgr{get}
```

获取告警信息管理接口。

**返回**

- 告警信息接口，详见 [IAlarmMgr](#alarmmgr)。

#### notificationMgr

```swift
var notificationMgr: INotificationMgr{get}
```

获取通知信息管理接口。

**返回**

- 通知信息接口，详见 [INotificationMgr](#notificationmgr)。

<a name="accountmgr"></a>

## IAccountMgr

账号管理接口。

<a name="logout"></a>

#### logout

```swift
func logout(result:@escaping (Int,String)->Void)
```

登出当前账号。

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="getuserid"></a>

#### getUserId

```swift
func getUserId()->String
```

获取当前用户 ID。

**返回**

- user ID。

- 错误码，详见 [ErrCode](#errcode)。

<a name="getaccount"></a>

#### getAccountInfo

```swift
func getAccountInfo(result:@escaping(Int,String,AccountInfo?)->Void)
```

获取当前用户账号信息。

**返回**

- 账户信息，详见 [AccountInfo](#accountinfo)。

- 错误码，详见 [ErrCode](#errcode)。

<a name="updateaccount"></a>

#### updateAccountInfo

```swift
func updateAccountInfo(info:AccountInfo,result:@escaping(Int,String)->Void)
```

更新当前用户账号信息。

**参数**

| 参数   | 描述                                         |
| ------ | -------------------------------------------- |
| `info` | 账号信息，详见 [AccountInfo](#accountinfo)。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="updatehead"></a>

#### updateHeadIcon

```swift
func updateHeadIcon(image:UIImage,result:@escaping(Int,String,String?)->Void)
```

上传用户头像。

**参数**

| 参数    | 描述           |
| ------- | -------------- |
| `image` | 头像图片文件。 |

**返回**

- 头像的 URL 地址。

- 错误码，详见 [ErrCode](#errcode)。

<a name="login"></a>

#### login

```swift
func login(param:LoginParam,result:@escaping(Int,String)->Void)
```

通过第三方账号的登陆参数登陆用户账号。

该方法适用于用户已经自行实现第三方账号管理系统的情况。用户可以直接通过该方法登陆灵隼物联网平台。

**参数**

| 参数  | 描述                                  |
| :---- | :------------------------------------ |
| `param` | 第三方登陆参数。详见 [LoginParam](#loginparam)。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="publickeyset"></a>

#### publicKeySet

```swift
func publicKeySet(publicKey:String, _ result:@escaping (Int,String)->Void)
```

设置云存储视频加密的公钥。

为加强云存储视频加密的安全性，你可以在应用层自行生成公钥、私钥，再通过该方法将公钥传入 SDK。例如，只有 SDK 的公钥和你 app 的私钥都正确，用户才能查看加密视频。

<div class="alert note">声网不会保存你传入的公钥，你需要自行保存并管理。</div>

**参数**

| 参数  | 描述                                  |
| :---- | :------------------------------------ |
| `publicKey` | 待设置的公钥。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。


<a name="callkitmgr"></a>

## ICallkitMgr

呼叫系统管理接口。

<a name="register"></a>

#### register

```swift
func register(incoming: @escaping (String,String, ActionAck) -> Void,memberState:((MemberState,[UInt])->Void)?)
```

注册来电通知。

通过该方法定义 `incoming` 和 `memberState` 回调函数后，如果你收到来电，SDK 会通过 `incoming` 和 `memberState` 回调通知你来电的设备、用户 ID 等相关信息。

声网推荐你在 [login](#login) 之后调用该方法。

**参数**

| 参数        | 描述                                             |
| ----------- | ------------------------------------------------ |
| `incoming`    | 来电设备的信息，包含以下内容：<li>第一个参数：来电设备的名称。</li><li>第二个参数：来电时附带的信息。</li><li>第三个参数：来电时产生的事件，详见 [ActionAck](#actionack)。</li>     |
| `memberState` | 来电用户的信息，包含以下内容：<li>第一个参数：来电用户的状态，详见 [MemberState](#memberstate)。</li><li>第二个参数：来电用户的用户 ID。 |

<a name="calldial"></a>

#### callDial

```swift
func callDial(
    device: IotDevice,
    attachMsg: String,
    result:@escaping(Int,String)->Void,
    actionAck:@escaping(ActionAck)->Void,
    memberState:((_ state:MemberState,_ uid:[UInt])->Void)?)
```

呼叫设备。

**参数**

| 参数        | 描述                                             |
| ----------- | ------------------------------------------------ |
| `device`    | 要呼叫的设备，详见 [IotDevice](#iotdevice)。     |
| `attachMsg` | 呼叫时附带的信息。                               |
| `actionAck` | 通话中产生的事件，详见 [ActionAck](#actionack)。 |
| `memberState` | 其他用户的信息。<li>`state`: 其他用户的状态，详见 [MemberState](#memberstate)。</li><li>`uid`: 其他用户的用户 ID。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="callhang"></a>

#### callHangup

```swift
func callHangup(result:@escaping(Int,String)->Void)
```

挂断当前通话。

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="callanswer"></a>

#### callAnswer

```swift
func callAnswer(result:@escaping(Int,String)->Void,
                actionAck:@escaping(ActionAck)->Void,
                memberState:((_ state:MemberState,_ uid:[UInt])->Void)?)
```

接听当前来电。

**参数**

| 参数        | 描述               |
| ----------- | ------------------ |
| `actionAck` | 通话中产生的事件。 |
| `memberState` | 其他用户的信息。<li>`state`: 其他用户的状态，详见 [MemberState](#memberstate)。</li><li>`uid`: 其他用户的用户 ID。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="setlocalvideo"></a>

#### setLocalVideoView

```swift
func setLocalVideoView(localView: UIView?) -> Int
```

设置本地视频显示控件。如果不设置则不显示本地视频。

**参数**

| 参数        | 描述               |
| ----------- | ------------------ |
| `localView` | 本地视频显示控件。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="setpeervideo"></a>

#### setPeerVideoView

```swift
func setPeerVideoView(peerView: UIView?) -> Int
```

设置对端视频显示控件。如果不设置则不显示对端视频。

**参数**

| 参数        | 描述               |
| ----------- | ------------------ |
| `localView` | 本地视频显示控件。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="mutelocalvideo"></a>

#### muteLocalVideo

```swift
func muteLocalVideo(mute: Bool,result:@escaping(Int,String)->Void)
```

停止/恢复发布本地视频流。

**参数**

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| `mute` | 是否停止发布本地视频流：<br/><ul><li>true: 是 </li><li>false: 否</li></ul> |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="mutelocalaudio"></a>

#### muteLocalAudio

```swift
func muteLocaAudio(mute: Bool,result:@escaping(Int,String)->Void)
```

停止/恢复发布本地音频流。

**参数**

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| `mute` | 是否停止发布本地音频流：<br/><ul><li>true: 是 </li><li>false: 否</li></ul> |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="mutepeervideo"></a>

#### mutePeerVideo

```swift
func mutePeerVideo(mute: Bool,result:@escaping(Int,String)->Void)
```

停止/恢复订阅远端视频流。

**参数**

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| `mute` | 是否停止发布远端视频流：<br/><ul><li>true: 是 </li><li>false: 否</li></ul> |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="mutepeeraudio"></a>

#### mutePeerAudio

```swift
func mutePeerAudio(mute: Bool,result:@escaping(Int,String)->Void)
```

停止/恢复订阅远端音频流。

**参数**

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| `mute` | 是否停止发布远端音频流：<br/><ul><li>true: 是 </li><li>false: 否</li></ul> |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="setvolume"></a>

#### setVolume

```swift
func setVolume(volumeLevel: Int,result:@escaping(Int,String)->Void)
```

设置音频播放的音量。

**参数**

| 参数          | 描述                           |
| ------------- | ------------------------------ |
| `volumeLevel` | 音量级别，取值范围为 [0,100]。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="setvolumeeffect"></a>

#### setVolumeEffect

```swift
func setAudioEffect(effectId: AudioEffectId,result:@escaping(Int,String)->Void)
```

设置音效效果（通常是变声等音效）。

**参数**

| 参数       | 描述                                                  |
| ---------- | ----------------------------------------------------- |
| `effectId` | 音效文件的 ID。详见 [AudioEffectId](#AudioEffectId)。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="talkstart"></a>

#### talkingRecordStart

```swift
 func talkingRecordStart(result:@escaping(Int,String)->Void)
```

开始录制当前通话（包括音视频流），仅在通话状态下才能调用。

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="talkstop"></a>

#### talkingRecordStop

```swift
 func talkingRecordStop(result:@escaping(Int,String)->Void)
```

停止录制当前通话，仅在通话状态下才能调用。

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="cappeervideo"></a>

#### capturePeerVideoFrame

```swift
func capturePeerVideoFrame(result:@escaping(Int,String,UIImage?)->Void)
```

截屏对端视频帧图像，仅在通话状态下才能调用。

**返回**

- 截取到的视频帧图像。

- 错误码，详见 [ErrCode](#errcode)。

<a name="getnetworkstatus"></a>

#### getNetworkStatus

```swift
func getNetworkStatus()->RtcNetworkStatus
```

获取当前通话的统计信息。

**返回**

当前通话的统计信息。详见 [RtcNetworkStatus](#rtcnetworkstatus)。


<a name="devicemgr"></a>

## IDeviceMgr

设备管理接口。

<a name="deviceregister"></a>

#### register

```swift
func register(listener:IDeviceStateListener)
```

注册回调接口。

**参数**

| 参数       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| `listener` | 设备状态回调接口，详见 [IDeviceStateListener](#devicestate)。 |

**返回**

错误码，详见 [ErrCode](#errcode)。

<a name="queryprodlist"></a>

#### queryProductList

```swift
func queryProductList(result:@escaping(Int,String,[ProductInfo])->Void)
```

查询产品列表。

**返回**

- 产品信息，详见 [ProductInfo](#productinfo)。

- 错误码，详见 [ErrCode](#errcode)。

<a name="queryalldevices"></a>

#### queryAllDevices

```swift
func queryAllDevices(result:@escaping(Int,String,[IotDevice])->Void)
```

查询当前用户名下所有设备。

**返回**

- 设备信息，详见 [IoTDevice](#iotdevice)类。

- 错误码，详见 [ErrCode](#errcode)。


<a name="removedevice"></a>

#### removeDevice

```swift
func removeDevice(deviceId:String,result:@escaping(Int,String)->Void)
```

移除设备。

**参数**

| 参数     | 描述                                         |
| -------- | -------------------------------------------- |
| `deviceId` | 要移除的设备。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="renamedevice"></a>

#### renameDevice

```swift
func renameDevice(deviceId:String,newName:String,result:@escaping(Int,String)->Void)
```

修改设备名。

**参数**

| 参数      | 描述                                           |
| --------- | ---------------------------------------------- |
| `deviceId`  | 要重命名的设备。 |
| `newName` | 修改后的新设备名。                             |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="getpropertydescription"></a>

#### getPropertyDescription

```swift
func getPropertyDescription(deviceId:String,productNumber:String,result:@escaping(Int,String,[Property])->Void)
```

获取设备的属性描述符。

该方法支持通过 `deviceId` 或 `productNumber` 查询，选择其中一个参数查询时，另一个参数需要设置为 `nil`。

不要同时填入 `deviceId` 和 `productNumber` 参数，否则该方法只会通过 `deviceId` 进行查询。

**参数**

| 参数            | 描述           |
| :-------------- | :------------- |
| `deviceId`      | 设备 ID。      |
| `productNumber` | 产品唯一标识。 |

**返回**

设备的属性描述符。详见 [Property](#property)。

<a name="setdeviceprop"></a>

#### setDeviceProperty

```swift
func setDeviceProperty(deviceId:String, properties:Dictionary<String,Any>,result:@escaping(Int,String)->Void)
```

设置设备属性。

**参数**

| 参数         | 描述                                             |
| ------------ | ------------------------------------------------ |
| `deviceId`     | 要设置属性的设备。 |
| `properties` | 要设置的属性列表。                               |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="getdeviceprop"></a>

#### getDeviceProperty

```swift
func getDeviceProperty(deviceId:String,result:@escaping(Int,String,_ desired:Dictionary<String, Any>?,_ reported:Dictionary<String, Any>?)->Void)
```

查询设备属性。

**参数**

| 参数     | 描述                                             |
| -------- | ------------------------------------------------ |
| `deviceId` | 要设置属性的设备。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="sendcommand"></a>

#### sendCommandToDevice

```swift
    func sendCommandToDevice(
        productKey: String?,
        deviceId: String?,
        command: String?,
        result:(Int,String)->Void
    )
```

向设备发送命令。

**参数**

| 参数         | 描述                                 |
| ------------ | ------------------------------------ |
| `productKey` | 产品编号，即作为产品唯一标识的数字。 |
| `deviceId`   | 设备 ID，必须是唯一的。         |
| `command`    | 发送给设备的命令。                   |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="sharedev"></a>

#### shareDeviceTo

```swift
func shareDeviceTo(deviceId:String,userId:String,type:String,result:@escaping(Int,String)->Void)
```

分享设备给他人。

**参数**

| 参数           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `deviceId` | 被分享的设备编号。                                           |
| `userId`      | 分享对象的用户 ID。                                             |
| `type`         | 分享对象的权限：<ul><li>2：管理员。</li><li>3：成员（默认）。</li></ul> |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="getsharable"></a>

#### shareGetOwnDevices

```swift
func shareGetOwnDevices(result:@escaping(Int,String,[DeviceShare]?)->Void)
```

查询可分享的设备。

**返回**

- 可分享的设备列表，详见 [DeviceShare](#deviceshare) 类。

- 错误码，详见 [ErrCode](#errcode)。

<a name="getreceived"></a>

#### shareWithMe

```swift
func shareWithMe(result:@escaping(Int,String,[DeviceShare]?)->Void)
```

查询分享给自己的设备。

**返回**

- 分享给自己的设备列表，详见 [DeviceShare](#deviceshare) 类。

- 错误码，详见 [ErrCode](#errcode)。

<a name="getshared"></a>

#### shareCancelable

```swift
func shareCancelable(deviceId:String,result:@escaping(Int,String,[DeviceCancelable]?)->Void)
```

查询分享给他人的设备。

**参数**

| 参数           | 描述               |
| -------------- | ------------------ |
| `deviceId` | 被分享的设备编号。 |

**返回**

- 分享给他人的设备列表，详见 [DeviceCancelable](#devicecancel) 类。

- 错误码，详见 [ErrCode](#errcode)。

<a name="deshare"></a>

#### shareRemoveMember

```swift
func shareRemoveMember(deviceId:String,userId:String,result:@escaping(Int,String)->Void)
```

设备所有者解除分享对象的权限，同时发送消息给分享对象。

**参数**

| 参数           | 描述                      |
| -------------- | ------------------------- |
| `deviceId` | 被分享的设备编号。        |
| `userId`       | 要解除权限的分享对象 ID。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="otagetinfo"></a>

#### otaGetInfo

```swift
˚func otaGetInfo(deviceId:String,result:@escaping(Int,String,FirmwareInfo?)->Void)func otaGetInfo(device:IotDevice,result:@escaping(Int,String,FirmwareInfo?)->Void)
```

获取设备固件信息。

**参数**

| 参数          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| `deviceId`      | 设备 ID。                                               |

**返回**

- 固件信息，详见 [FirmwareInfo](#FirmwareInfo) 类。
- 错误码，详见 [ErrCode](#errcode)。

<a name="otaupgrade"></a>

#### otaUpgrade

```swift
func otaUpgrade(upgradeId:String,result:@escaping(Int,String)->Void)
```

使用 OTA 升级设备固件。

**参数**

| 参数          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| `upgradeId`      | 升级 ID。你可以通过 [otaGetInfo](#otagetinfo) 返回的 `FirmwareInfo.upgradeId` 获取。                                              |

**返回**

- 错误码，详见 [ErrCode](#errcode)。


<a name="otaquery"></a>

#### otaQuery

```swift
func otaQuery(upgradeId:String,result:@escaping(Int,String,FirmwareStatus?)->Void)
```

查询设备 OTA 固件的升级状态。

**参数**

| 参数          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| `upgradeId`      | OTA 固件的升级 ID。                                              |

**返回**

设备 OTA 固件的升级状态。详见 [FirmwareStatus](#firmwarestatus)。

<a name="sendmessagebegin"></a>

#### sendMessageBegin

```swift
func sendMessageBegin(deviceId:String,result:@escaping(Int,String)->Void,statusUpdated:@escaping(_ status:MessageChannelStatus,_ msg:String,_ data:Data?)->Void)
```

连接 RTM 服务器通道。

**参数**

| 参数            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| `deviceId`      | 设备 ID。                                                    |
| `statusUpdated` | <li>`status`：RTM 状态。详见 `MessageChannelStatus`。</li><li>`msg`：提示信息。</li><li>`data`：提示描述。</li> |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="sendmessageend"></a>

#### sendMessageEnd

```swift
func sendMessageEnd()
```

关闭通道，退出 RTM 服务器。

<a name="sendmessage"></a>

#### sendMessage

```swift
func sendMessage(data:Data,description:String,result:@escaping(Int,String)->Void)
```

向设备发送消息。

该方法的调用需要同时满足以下时序：

- 在调用 [sendMessageBegin](#sendmessagebegin) 并且收到 `Connected` 消息状态之后。
- 在调用 [sendMessageEnd](#sendmessageend) 之前。

**参数**

| 参数          | 描述                                                  |
| :------------ | :---------------------------------------------------- |
| `data`        | 待发送的 RTM 消息数据。声网推荐发送小于 1 KB 的数据。 |
| `description` | 消息描述。                                            |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="startplayback"></a>

#### startPlayback

```swift
func startPlayback(channelName:String,result:@escaping(Int,String)->Void,stateChanged:@escaping(PlaybackStatus,String)->Void)
```

开始播放 SD 卡记录的视频。

**参数**

| 参数           | 描述                                                  |
| :------------- | :---------------------------------------------------- |
| `channelName`  | 频道名。                                              |
| `stateChanged` | 播放 SD 卡记录的视频时的状态。详见 [PlaybackStatus](#playbackstatus)。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="setplaybackview"></a>

####  setPlaybackView

```swift
func setPlaybackView(peerView: UIView?) -> Int
```

设置播放视图。

该方法建立待播放视频和 `UIView` 的关联。

**参数**

| 参数       | 描述                  |
| :--------- | :-------------------- |
| `peerView` | 需要关联的 `UIView`。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="stopplayback"></a>

#### stopPlayback

```swift
func stopPlayback()
```

停止播放 SD 卡记录的视频。

<a name="devicecallback"></a>

## IDeviceMgrCallback

设备状态改变回调。

<a name="deviceonoffline"></a>

#### onDeviceOnOffline

```swift
func onDeviceOnOffline(online:Bool,deviceId:String,productId:String)
```

设备上下线回调。

**参数**

| 参数        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| `online`    | 设备上线状态：<ul><li>true: 上线。</li><li>false: 下线。</li></ul> |
| `deviceId`  | 设备 ID，必须是唯一的。                                 |
| `productId` | 产品 ID。                                                    |

<a name="deviceactionupdated"></a>

#### onDeviceActionUpdated

```swift
func onDeviceActionUpdated(deviceId:String,actionType:String)
```

设备发生行为改变回调。

**参数**

| 参数         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| `deviceId`   | 设备 ID，必须是唯一的。                                 |
| `actionType` | 行为类型：<ul><li>add: 绑定。</li><li>delete: 解绑。</li></ul> |

<a name="devicepropertyupdated"></a>

#### onDevicePropertyUpdated

```swift
func onDevicePropertyUpdated(deviceId:String,deviceNumber:String,props:[String:Any]?)
}
```

设备属性更新回调。

**参数**

| 参数           | 描述                         |
| -------------- | ---------------------------- |
| `deviceId`     | 设备 ID，必须是唯一的。 |
| `deviceNumber` | 设备编号。                   |
| `props`        | 发生变化的属性。             |

<a name="alarmmgr"></a>

## IAlarmMgr

告警信息管理接口。

<a name="querybyid"></a>

#### queryById

```swift
func queryById(alertMessageId:UInt64, result:@escaping (Int,String,IotAlarm?) -> Void)
```

根据 ID 查询告警信息。

**参数**

| 参数             | 描述          |
| ---------------- | ------------- |
| `alertmessageId` | 告警信息 ID。 |

**返回**

- 告警信息，详见 [IotAlarm](#iotalarm) 类。

- 错误码，详见 [ErrCode](#errcode)。

<a name="querybyparam"></a>

#### queryByParam

```swift
func queryByParam(queryParam:QueryParam,result:@escaping(Int,String,[IotAlarm]?)->Void)
```

根据参数查询告警信息。

**参数**

| 参数         | 描述                                                   |
| ------------ | ------------------------------------------------------ |
| `queryParam` | 告警信息查询参数，详见  [QueryParam](#queryparam) 类。 |

**返回**

- 告警信息，详见 [IotAlarm](#iotalarm) 类。

- 错误码，详见 [ErrCode](#errcode)。

<a name="deletealarm"></a>

#### delete

```swift
func delete(alarmIdList: [UInt64],result:@escaping(Int,String)->Void)
```

删除告警信息。

**参数**

| 参数          | 描述                         |
| ------------- | ---------------------------- |
| `alarmIdList` | 要删除的告警信息的 ID 列表。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="markalarm"></a>

#### mark

```swift
func mark(alarmIdList: [UInt64],result:@escaping(Int,String)->Void)
```

将告警信息标记为已读。

**参数**

| 参数          | 描述                         |
| ------------- | ---------------------------- |
| `alarmIdList` | 要标记的告警信息的 ID 列表。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="addalarm"></a>

#### addAlarm

```swift
func addAlarm(device:IotDevice,desc:String,result:@escaping(Int,String)->Void)
```

新增告警信息。

**参数**

| 参数     | 描述                             |
| :------- | :------------------------------- |
| `device` | 告警设备信息。详见 [IotDevice](#iotdevice)。 |
| `desc`   | 告警描述。                       |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="queryalarmimage"></a>

#### queryAlarmImage

```swift
func queryAlarmImage(alertImageId:String,result:@escaping(Int,String,String?)->Void)
```

根据图片 ID 查询告警图片信息。

**参数**

| 参数           | 描述          |
| :------------- | :------------ |
| `alertImageId` | 告警图片 ID。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="queryalarmvideourl"></a>

#### queryAlarmVideoUrl

```swift
func queryAlarmVideoUrl(deviceId:String,tenantId:String, beginTime:UInt64,result:@escaping(Int,String,AlarmVideoInfo?)->Void)
```

根据告警时间戳查询告警视频信息。

**参数**

| 参数        | 描述                                                |
| :---------- | :-------------------------------------------------- |
| `deviceId`  | 设备 ID。                                           |
| `tenantId`  | 设备所属用户的账号 ID。`null` 表示当前用户的账号 ID。 |
| `beginTime` | 告警的时间戳。                                      |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="querycount"></a>

#### queryCount

```swift
func queryCount(productId:String?,deviceId:String?,messageType:Int?,status:Int?,createDateBegin:Date?,createDateEnd:Date? , result:@escaping(Int,String,UInt)->Void)
```

根据过滤条件查询告警信息数量。

| 参数               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `productId`        | 要查询的产品 ID。                                            |
| `deviceId`         | 要查询的设备 ID。                                            |
| `messageType`      | 设备告警信息类型：<ul><li>0: 声音检测。</li><li>1: 移动侦测。</li><li>99: 其他检测。</li><li>nil: 所有设备告警信息。</li></ul> |
| `status`           | 设备告警消息状态：<ul><li>0: （默认）未读</li><li> 1: 已读。</li></ul> |
| `createdDateBegin` | 开始查询的时间。                                             |
| `createdDateEnd`   | 结束查询的时间。                                             |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="querysysbyid"></a>

#### querySysById

```swift
func querySysById(alertMessageId:UInt64, result:@escaping (Int,String,IotAlarm?) -> Void)
```

根据 ID 查询系统告警信息。

**参数**

| 参数             | 描述          |
| ---------------- | ------------- |
| `alertmessageId` | 告警信息 ID。 |

**返回**

- 告警信息，详见 [IotAlarm](#iotalarm) 类。

- 错误码，详见 [ErrCode](#errcode)。

<a name="querybyparam"></a>

#### querySysByParam

```swift
func querySysByParam(queryParam:SysQueryParam,result:@escaping(Int,String,[IotAlarm]?)->Void)
```

根据参数查询告警信息。

**参数**

| 参数         | 描述                                                   |
| ------------ | ------------------------------------------------------ |
| `queryParam` | 告警信息查询参数，详见  [QueryParam](#queryparam) 类。 |

**返回**

- 告警信息，详见 [IotAlarm](#iotalarm) 类。

- 错误码，详见 [ErrCode](#errcode)。

<a name="marksysalarm"></a>

#### markSys

```swift
func markSys(alarmIdList: [UInt64],result:@escaping(Int,String)->Void)
```

将系统告警信息标记为已读。

**参数**

| 参数          | 描述                         |
| ------------- | ---------------------------- |
| `alarmIdList` | 要标记的告警信息的 ID 列表。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="querysyscount"></a>

#### querySysCount

```swift
func querySysCount(productId:String?,deviceIds:[String],messageType:Int?,status:Int?,createDateBegin:Date?,createDateEnd:Date? , result:@escaping(Int,String,UInt)->Void)
```

根据过滤条件查询系统告警信息数量。

| 参数               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `productId`        | 要查询的产品 ID。                                            |
| `deviceIds`        | 要查询的设备 ID。                                            |
| `messageType`      | 系统告警信息类型：<ul><li>1: 设备上线。</li><li>2:设备下线。</li><li>3:设备绑定。</li><li>4:设备解绑。</li><li>99: 其他系统告警信息。</li></ul> |
| `status`           | 设备告警消息状态：<ul><li>0: （默认）未读</li><li> 1: 已读。</li></ul> |
| `createdDateBegin` | 开始查询的时间。                                             |
| `createdDateEnd`   | 结束查询的时间。                                             |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="notificationmgr"></a>

## INotificationMgr

通知消息管理接口。

<a name="updatetoken"></a>

#### updateToken

```swift
func updateToken(_ deviceToken:Data)
```

离线推送时需要更新 Token。

**参数**

| 参数          | 描述         |
| ------------- | ------------ |
| `deviceToken` | 设备 Token。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="queryallnoti"></a>

#### queryAll

```swift
func queryAll(result:@escaping(Int,String)->Void)
```

查询所有通知消息。

查询成功会触发 [onAllNotificationQueryDone](#allnotificationquery) 回调。

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="querynotibydev"></a>

#### queryByDevice

```swift
func queryByDevice(productKey: String, deviceId: String,result:@escaping(Int,String)->Void)
```

查询指定设备的所有通知消息。

查询成功会触发 [onDeviceNotificationQueryDone](#devicenotificationquery) 回调。

**参数**

| 参数         | 描述       |
| ------------ | ---------- |
| `productKey` | 产品编号。 |
| `deviceId`   | 设备 ID。  |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="deletenoti"></a>

#### delete

```swift
func delete(notificationIdList: [String],result:@escaping(Int,String)->Void)
```

删除多条通知消息。

删除成功会触发 [onNotificationDeleteDone](#notificationdelete) 回调。

**参数**

| 参数                 | 描述                       |
| -------------------- | -------------------------- |
| `notificationIdList` | 要删除的通知消息 ID 列表。 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="marknoti"></a>

#### mark

```swift
func mark(markFlag: Int, notificationIdList: [String],result:@escaping(Int,String)->Void)
```

标记多条通知消息。

标记成功会触发 [onNotificationMarkDone](#notificationmark) 回调。

**参数**

| 参数                 | 描述                       |
| -------------------- | -------------------------- |
| `notificationIdList` | 要删除的通知消息 ID 列表。 |
| `markFlag`           | 标记信息。                 |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="enablenoti"></a>

#### enableNotify

```swift
func enableNotify(enable:Bool,result:@escaping(Int,String)->Void)
```

开启/关闭接收推送消息。

**参数**

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| `bool` | 是否要接收推送消息。<ul><li>true: 接收。</li><li>false: 不接收。</li></ul> |

**返回**

- 错误码，详见 [ErrCode](#errcode)。

<a name="enablenotiquery"></a>

#### notifyEnabled

```swift
func notifyEnabled()->Bool
```

查询推送消息接收状态。

**返回**

- true: 接收。
- false: 不接收。

<a name="geteid"></a>

#### getEid

```swift
func getEid()->String
```

查询推送 eid。

**返回**

eid。

<a name="errcode"></a>

## ErrCode

### 全局错误

| 错误码 | 错误代码 | 描述 |
| --- | --- | --- |
| `0` | `XERR_NONG` | 无错误。|
| `0` | `XOK` | 无错误。 |
| `-3` | `XERR_TOKEN_INVALID` | Token 过期。 |

### 通用错误

| 错误码   | 错误代码                | 描述                       |
| -------- | ----------------------- | -------------------------- |
| `-10000` | `XERR_BASE`             | 通用错误。                 |
| `-10001` | `XERR_UNKNOWN`          | 未知错误。                 |
| `-10002` | `XERR_INVALID_PARAM`    | 参数错误。                 |
| `-10003` | `XERR_UNSUPPORTED`      | 当前操作不支持。           |
| `-10004` | `XERR_BAD_STATE`        | 当前状态不正确，无法操作。 |
| `-10005` | `XERR_NOT_FOUND`        | 没有找到相关数据。         |
| `-10006` | `XERR_NO_MEMORY`        | 内存不足。                 |
| `-10007` | `XERR_BUFFER_OVERFLOW`  | 缓冲区中数据不足。         |
| `-10008` | `XERR_BUFFER_UNDERFLOW` | 缓冲区中数据过多。         |
| `-10009` | `XERR_TIMEOUT`          | 操作超时。                 |
| `-10012` | `XERR_NETWORK`          | 网络错误。                 |
| `-10015` | `XERR_TOKEN_INVALID`    | Token 无效。               |

### 账号相关错误

| 错误码   | 错误代码                      | 描述               |
| -------- | ----------------------------- | ------------------ |
| `-30000` | `XERR_ACCOUNT_BASE`           | 账号相关错误。     |
| `-30001` | `XERR_ACCOUNT_NOT_EXIST`      | 账号不存在。       |
| `-30002` | `XERR_ACCOUNT_ALREADY_EXIST`  | 账号已存在。       |
| `-30003` | `XERR_ACCOUNT_PASSWORD_ERR`   | 密码错误。         |
| `-30004` | `XERR_ACCOUNT_NOT_LOGIN`      | 账号未登录。       |
| `-30005` | `XERR_ACCOUNT_REGISTER`       | 账号注册失败。     |
| `-30006` | `XERR_ACCOUNT_UNREGISTER`     | 账号注销失败。     |
| `-30007` | `XERR_ACCOUNT_LOGIN`          | 账号登录失败。     |
| `-30008` | `XERR_ACCOUNT_LOGOUT`         | 账号登出失败。     |
| `-30009` | `XERR_ACCOUNT_CHGPSWD`        | 账号更换密码失败。 |
| `-30010` | `XERR_ACCOUNT_RESETPSWD`      | 账号重置密码失败。 |
| `-30011` | `XERR_ACCOUNT_GETCODE`        | 获取验证码失败。   |
| `-30013` | `XERR_ACCOUNT_USRINFO_QUERY`  | 查询用户信息失败。 |
| `-30014` | `XERR_ACCOUNT_USRINFO_UPDATE` | 查询用户信息失败。 |

### 呼叫系统相关错误

| 错误码   | 错误代码                   | 描述                  |
| -------- | -------------------------- | --------------------- |
| `-40000` | `XERR_CALLKIT_BASE`        | 呼叫系统相关错误。    |
| `-40001` | `XERR_CALLKIT_TIMEOUT`     | 呼叫超时无响应。      |
| `-40002` | `XERR_CALLKIT_DIAL`        | 呼叫拨号失败。        |
| `-40003` | `XERR_CALLKIT_HANGUP`      | 呼叫挂断失败。        |
| `-40004` | `XERR_CALLKIT_ANSWER`      | 呼叫接听失败。        |
| `-40005` | `XERR_CALLKIT_REJECT`      | 呼叫拒绝失败。        |
| `-40006` | `XERR_CALLKIT_PEER_BUSY`   | 对端忙。              |
| `-40007` | `XERR_CALLKIT_PEERTIMEOUT` | 对端超时无响应。      |
| `-40008` | `XERR_CALLKIT_LOCAL_BUSY`  | 本地端忙。            |
| `-40009` | `XERR_CALLKIT_ERR_OPT`     | 不支持的错误操作。    |
| `-40010` | `XERR_CALLKIT_PEER_UNREG`  | 对端未注册。          |
| `-40011` | `XERR_CALLKIT_NO_APPID`    | 未上报 App ID。       |
| `-40012` | `XERR_CALLKIT_SAME_ID`     | 主叫和被叫同一个 ID。 |
| `-4001` | `XERR_CALLKIT_APPID_DIFF`     | 客户端和设备端使用的 App ID 不一致。 |

### 设备管理相关错误

| 错误码   | 错误代码                             | 描述                   |
| -------- | ------------------------------------ | ---------------------- |
| `-50000` | `XERR_DEVMGR_BASE`                   | 设备管理相关错误。     |
| `-50001` | `XERR_DEVMGR_NO_DEVICE`              | 没有找到设备。         |
| `-50002` | `XERR_DEVMGR_ONLINE`                 | 设备已经在线。         |
| `-50003` | `XERR_DEVMGR_OFFLINE`                | 设备不在线。           |
| `-50004` | `XERR_DEVMGR_QUEYR`                  | 设备查询失败。         |
| `-50005` | `XERR_DEVMGR_ADD`                    | 设备添加失败。         |
| `-50006` | `XERR_DEVMGR_DEL`                    | 设备删除失败。         |
| `-50007` | `XERR_DEVMGR_CMD`                    | 向设备发出命令失败。   |
| `-50008` | `XERR_DEVMGR_PROPERTY`               | 设备属性查询失败       |
| `-50009` | `XERR_DEVMGR_RENAME`                 | 设备重命名失败。       |
| `-50010` | `XERR_DEVMGR_SHARE_ALREADY_BIND`     | 共享的设备已经被绑定。 |
| `-50011` | `XERR_DEVMGR_SHARE_TARGET_NOT_EXIST` | 共享的用户不存在。     |

### 告警管理相关错误

| 错误码   | 错误代码               | 描述               |
| -------- | ---------------------- | ------------------ |
| `-60001` | `XERR_ALARM_NOT_FOUND` | 没有找到告警信息。 |

## 数据类型

<a name="initparam"></a>

### InitParam 类

Iot SDK 初始化参数。

```swift
public class InitParam : NSObject{
    @objc public var rtcAppId: String = ""
    @objc public var logFilePath : String? = nil
    @objc public var publishAudio = true
    @objc public var publishVideo = true
    @objc public var subscribeAudio = true
    @objc public var subscribeVideo = true
    @objc public var ntfAppKey: String = ""
    @objc public var ntfApnsCertName:String = ""
    @objc public var masterServerUrl:String = ""
    @objc public var slaveServerUrl:String = ""
    @objc public var projectId:String = ""
}
```

| 参数  | 描述 |
| --- | --- |
| `rtcAppId` | 声网的 App ID。 |
| `logFilePath` | 日志文件路径。如果不设置该参数，则日志不会输出到本地文件。 |
| `publishAudio` | 通话时是否自动发布本地音频：<ul><li>true: 是</li><li>false: 否</li></ul><br/> |
| `publishVideo` | 通话时是否自动发布本地视频：<ul><li>true: 是</li><li>false: 否</li></ul><br/> |
| `subscribeAudio` | 通话时是否自动订阅对端音频：<ul><li>true: 是</li><li>false: 否</li></ul><br/> |
| `subscribeVideo` | 通话时是否自动订阅对端视频：<ul><li>true: 是</li><li>false: 否</li></ul><br/> |
| `ntfAppKey` | 离线推送使用的 AppKey。 |
| `ntfApnsCertName` | 离线推送使用的 ApnsCertName。 |
| `masterServerUrl` | 主服务器后台地址。 |
| `slaveServerUrl` | 副服务器后台地址。 |
| `projectId` | 项目 ID，作为查询产品列表的过滤条件。 |

<a name="userinfo"></a>

### UserInfo 类

```swift
public class UserInfo : NSObject{
    @objc public var name:String?
    @objc public var avatar:String?
    @objc public var sex:Int = 0
    @objc public var age:Int = 0
    @objc public var birthday:String?
    @objc public var height:NSNumber?
    @objc public var weight:NSNumber?
    @objc public var countryId:String?
    @objc public var country:String?
    @objc public var provinceId:String?
    @objc public var province:String?
    @objc public var cityId:String?
    @objc public var city:String?
    @objc public var areaId:String?
    @objc public var area:String?
    @objc public var address:String?
    @objc public var background:String?

    @objc let email:String?
    @objc let phone:String?
public override init(){
        self.email = ""
        self.phone = ""
    }
    public init( name:String?,
                 avatar:String?,
                 sex:Int,
                 age:Int,
                 birthday:String?,
                 height:UInt?,
                 weight:UInt?,

                 countryId:String?,
                 country:String?,
                 provinceId:String?,
                 province:String?,
                 cityId:String?,
                 city:String?,
                 areaId:String?,
                 area:String?,
                 address:String?,
                 background:String?,

                 email:String?,
                 phone:String?
                 ) {

        self.name = name
        self.avatar = avatar

        self.age = age
        self.sex = sex
        self.birthday = birthday
        self.height = (height) as? NSNumber
        self.weight = (weight) as? NSNumber

        self.countryId = countryId
        self.country = country
        self.province = province
        self.provinceId = provinceId
        self.cityId = cityId
        self.city = city
        self.areaId = areaId
        self.area = area

        self.address = address
        self.background = background

        self.email = email
        self.phone = phone
    }
}

```

用户信息。

**参数**

| 参数         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| `name`       | 用户名。                                                     |
| `avatar`     | 用户头像。                                                   |
| `sex`        | 性别。<ul><li>1：男。</li><li>2：女。</li><li>0：未知。</li></ul> |
| `age`        | 年龄。0：未知                                                |
| `birthday`   | 生日。                                                       |
| `height`     | 身高，单位：cm。                                             |
| `weight`     | 体重，单位：kg                                               |
| `countryId`  | 国家编号。                                                   |
| `country`    | 国家名。                                                     |
| `provinceId` | 省份编号。                                                   |
| `province`   | 省份名。                                                     |
| `cityId`     | 城市编号。                                                   |
| `city`       | 城市名。                                                     |
| `areaId`     | 区县编号。                                                   |
| `area`       | 区县名。                                                     |
| `address`    | 详细地址。                                                   |
| `background` | 背景图片。                                                   |
| `email`      | 邮箱地址。                                                   |
| `phone`      | 手机号码。                                                   |

<a name="loginparam"></a>

### LoginParam

```swift
public class LoginParam : NSObject{

    public var tokenType:String = ""
    public var accessToken:String = ""
    public var refreshToken:String = ""
    public var expireIn:UInt = 0
    public var scope:String = ""

    public var grawin_token:String = ""
    public var expiration : UInt64 = 0
    public var endPoint:String = ""
    public var region:String = ""
    public var account:String = ""

    public var proof_sessionToken:String = ""
    public var proof_secretKey:String = ""
    public var proof_accessKeyId:String = ""
    public var proof_sessionExpiration:UInt64 = 0

    public var pool_token:String = ""
    public var pool_identityId:String = ""
    public var pool_identityPoolId:String = ""
    public var pool_identifier = ""
}
```

第三方账号的登陆参数。

**参数**

| 参数                    | 描述                          |
| :---------------------- | :---------------------------- |
| `tokenType`               | 预留字段。                    |
| `accessToken`             | 灵隼平台账号的 Token。        |
| `refreshToken`            | 更新 Token。                  |
| `expireIn`                | Token 过期时间。              |
| `scope`                   | 预留字段。                    |
| `grawin_token`            | 第三方平台的 Token。          |
| `expiration`              | `grawin_token` 的过期时间。     |
| `endPoint`                | 灵隼服务器的边缘节点。        |
| `region`                  | 灵隼服务器所在区域。          |
| `account`                 | 用户的第三方账号。            |
| `proof_sessionToken`      | IOT 临时账号的 Token。        |
| `proof_secretKey`         | IOT 临时账号的密钥。          |
| `proof_accessKeyId`       | IOT 临时账号的凭证。          |
| `proof_sessionExpiration` | `proof_sessionToken` 的过期时间 |
| `pool_token`              | 用户身份 Token。              |
| `pool_identityId`         | 用户身份 ID。                 |
| `pool_identityPoolId`     | 用户身份池 ID。               |
| `pool_identifier`         | 用户身份。                    |

<a name="iotalarm"></a>

### IotAlarm 类

```swift
public class IotAlarm : NSObject{
    @objc public let alertMessageId:UInt64
    @objc public var messageType:UInt = 0
    @objc public var desc:String = ""
    @objc public var fileUrl:String = ""
    @objc public var status:UInt = 0
    @objc public var tenantId:String = ""
    @objc public var productId:String? = ""
    @objc public var deviceId:String = ""
    @objc public var deviceName:String = ""
    @objc public var deleted:Bool = false
    @objc public var imageId:String = ""
    @objc public var beginTime:UInt64 = 0
    @objc public var createdBy:UInt = 0
    @objc public var createdDate:UInt64 = 0
    @objc public var changedBy:UInt = 0
    @objc public var changedDate:UInt64 = 0
    @objc public init(messageId:UInt64){
        alertMessageId = messageId
    }

    @objc public var readed:Bool{get{return status == 1}set{status = newValue ? 1 : 0}}
}
```

告警信息。

**参数**

| 参数             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| `alertMessageId` | 告警信息 ID。                                                |
| `messageType`    | 设备告警信息类型：<ul><li>0: 声音检测。</li><li>1: 移动侦测。</li><li>99: 其他检测。</li><li>nil: 所有设备告警信息。</li></ul>系统告警信息类型：<ul><li>1: 设备上线。</li><li>2:设备下线。</li><li>3:设备绑定。</li><li>4:设备解绑。</li><li>99: 其他系统告警信息。</li></ul> |
| `desc`           | 告警描述。                                                   |
| `fileUrl`        | 告警视频 URL。                                               |
| `status`         | 告警信息状态：<ul><li>0: （默认）未读</li><li> 1: 已读。</li></ul> |
| `tenantId`       | 租户 ID。                                                    |
| `productId`      | 产品 ID。                                                    |
| `deviceId`       | 设备 ID。                                                    |
| `deviceName`     | 设备名称。                                                   |
| `deleted`        | 是否已删除。<ul><li>true: 是</li><li>false: 否</li></ul>     |
| `imageId`      | 告警图片 ID。                                                  |
| `beginTime`      | 告警视频开始录制的时间戳。                                                  |
| `createdBy`      | 创建人 ID。                                                  |
| `createdDate`    | 创建日期。                                                   |
| `changedBy`      | 最近修改人 ID。                                              |
| `changedDate`    | 最近修改日期。                                               |

<a name="alarmqueryparam"></a>

### AlarmQueryParam 类

```swift
public class AlarmQueryParam : NSObject{
    public var productId:String?
    public var deviceId:String?
    public var messageType : Int? = nil
    public var status : Int? = nil
    public var createdDateBegin:Date? = nil
    public var createdDateEnd:Date? = nil
    public var currentPage:Int = 1
    public var pageSize:Int = 5
    public var desc:Bool = true

    public init(dateBegin:Date? = nil){
        self.createdDateBegin = dateBegin
    }
}
```

设备告警信息查询参数。

| 参数               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `productId`      | 产品 ID。 |
| `deviceId`      | 设备 ID。 |
| `messageType`      | 设备告警信息类型：<ul><li>0: 声音检测。</li><li>1: 移动侦测。</li><li>99: 其他检测。</li><li>nil: 所有设备告警信息。</li></ul> |
| `status`           | 设备告警消息状态：<ul><li>0: （默认）未读</li><li> 1: 已读。</li></ul> |
| `createdDateBegin` | 开始查询的时间。                                             |
| `createdDateEnd`   | 结束查询的时间。                                             |
| `currentPage`      | 要查询的页面索引，从 1 开始。                                |
| `pageSize`         | 查询结果中每页告警消息的数量。                               |
| `desc`             | 是否按上报时间升序排序：<ul><li>true: 是。</li><li> false: 否。</li></ul> |

<a name="sysalarmqueryparam"></a>

### SysAlarmQueryParam 类

```swift
public class SysAlarmQueryParam : NSObject{
    public var messageType : Int? = nil
    public var status : Int? = nil
    public var createdDateBegin:Date? = nil
    public var createdDateEnd:Date = Date()
    public var currentPage:Int = 1
    public var pageSize:Int = 5
    public var desc:Bool = true
    public var deviceIds:[String] = []

    public init(dateBegin:Date? = nil){
        self.createdDateBegin = dateBegin
    }
}
```

系统告警信息查询参数。

| 参数               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `messageType`      | 系统告警信息类型：<ul><li>1: 设备上线。</li><li>2:设备下线。</li><li>3:设备绑定。</li><li>4:设备解绑。</li><li>99: 其他系统告警信息。</li></ul> |
| `status`           | 告警消息状态：<ul><li>0: （默认）未读</li><li> 1: 已读。</li></ul> |
| `createdDateBegin` | 开始查询的时间。                                             |
| `createdDateEnd`   | 结束查询的时间。                                             |
| `currentPage`      | 要查询的页面索引，从 1 开始。                                |
| `pageSize`         | 查询结果中每页告警消息的数量。                               |
| `desc`             | 是否按上报时间升序排序：<ul><li>true: 是。</li><li> false: 否。</li></ul> |
| `deviceIds`        | 要查询的设备 ID。当查询所有设备的信息时，需要填上所有设备的 ID。 |

<a name="iotdevice"></a>

### AlarmVideoInfo 类

```swift
public class AlarmVideoInfo : NSObject{
    public var url : String = ""
    public var videoSecretKey : String = ""
}
```

告警视频信息。

| 参数             | 描述                       |
| :--------------- | :------------------------- |
| `url`            | 视频文件在云存储中的地址。 |
| `videoSecretKey` | 播放视频时需要传入的密钥。 |

<a name="iotdevice"></a>

### IotDevice 类

```swift
public class IotDevice : NSObject {
    @objc public var userId : String
    @objc public var userType : Int
    @objc public var deviceId : String
    @objc public var deviceName : String
    @objc public var deviceNumber : String
    @objc public var tenantId : String
    @objc public var productId : String
    @objc public var productNumber : String
    @objc public var sharer : String
    @objc public var createTime : UInt64
    @objc public var updateTime : UInt64
    @objc public var productInfo : ProductInfo?
    @objc public var connected : Bool
    @objc public var alias : String
    @objc public var props: Dictionary<String,Any>? = nil

    public init(userId:String ,
         userType:Int ,
         deviceId:String,
         deviceName:String,
         deviceNumber:String,
         tenantId:String,

         productId:String,
         productNumber:String,

         sharer:String,
         createTime:UInt64,
         updateTime:UInt64,

         alias:String,
         connected:Bool,
         props:Dictionary<String,String>?){
        self.userId = userId
        self.userType = userType
        self.deviceId = deviceId
        self.deviceName = deviceName
        self.deviceNumber = deviceNumber
        self.tenantId = tenantId

        self.productId = productId
        self.productNumber = productNumber


        self.sharer = sharer
        self.createTime = createTime
        self.updateTime = updateTime
        self.connected = connected
        self.alias = alias
        self.props = props
    }
}
```

设备信息。

| 参数           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `userId`       | 用户 ID。                                                    |
| `userType`     | 用户角色：<ul><li> 1：所有者。</li><li>2：管理员。</li><li>3：成员。</li></ul> |
| `deviceId`     | 设备 ID。                                                    |
| `deviceName`   | 设备名称。                                                   |
| `deviceNumber` | 设备编号。                                                   |
| `tenantId`     | 租户 ID。                                                    |
| `productId`    | 产品 ID。                                                    |
| `productNumbe` | 产品编号。                                                   |
| `sharer`       | 分享者的用户 ID。                                            |
| `createTime`   | 创建时间。                                                   |
| `updateTime`   | 最近更新时间。                                               |
| `productInfo`  | 产品信息，详见 [ProductInfo](#productinfo)。                 |
| `connected`    | 是否在线。<ul><li> true：在线。</li><li>false：离线。</li></ul> |
| `alias`        | 产品别名。                                                   |
| `props`        | 设备属性。                                                   |

<a name="productinfo"></a>

### ProductInfo 类

```swift
public class ProductInfo:NSObject{
    @objc public var alias : String = ""
    @objc public var bindType:UInt = 0
    @objc public var connectType : UInt = 0
    @objc public var createTime : UInt64 = 0
    @objc public var deleted : UInt = 0
    @objc public var id : String = ""
    @objc public var imgBig : String = ""
    @objc public var imgSmall : String = ""
    @objc public var merchantId : UInt64 = 0
    @objc public var merchantName : String = ""
    @objc public var name : String = ""
    @objc public var productKey : String = ""
    @objc public var productTypeId : UInt64  = 0
    @objc public var productTypeName : String = ""
    @objc public var status : UInt = 0
    public var updateBy : UInt64 = 0
    public var updateTime : UInt64 = 0
    public init(alias:String,
                bindType:UInt,
                connectType:UInt,
                createTime:UInt64,
                deleted:UInt,
                id:String,
                imgBig:String,
                imgSmall:String,
                merchantId:UInt64,
                merchantName:String,
                name:String,
                productKey:String,
                productTypeId:UInt64,
                productTypeName:String,
                status:UInt,
                updateBy:UInt64,
                updateTime:UInt64) {
        self.alias = alias
        self.bindType = bindType
        self.connectType = connectType
        self.createTime = createTime
        self.deleted = deleted
        self.id = id
        self.imgBig = imgBig
        self.imgSmall = imgSmall
        self.merchantId = merchantId
        self.merchantName = merchantName
        self.name = name
        self.productKey = productKey
        self.productTypeId = productTypeId
        self.productTypeName = productTypeName
        self.status = status
        self.updateBy = updateBy
        self.updateTime = updateTime
    }
}
```

产品信息。

| 参数              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `alias`           | 产品别名。                                                   |
| `bindType`        | 绑定类型：<ul><li>1: 可重复绑定。</li><li>2: 不可重复绑定。</li></ul> |
| `connectType`     | 网络连接类型：<ul><li>1: WiFi 连接。</li><li>2: 蜂窝连接。</li></ul> |
| `createTime`      | 创建时间。                                                 |
| `deleted`         | 是否已删除。<ul><li>true: 是</li><li>false: 否</li></ul>     |
| `id`              | 产品 ID。                                                    |
| `imgBig`          | 产品大图。                                                   |
| `imgSmall`        | 产品小图。                                                   |
| `merchantId`      | 商户 ID。                                                    |
| `merchantName`    | 商户名称。                                                   |
| `name`            | 产品名称                                                     |
| `productKey`      | 产品编号，即作为产品唯一标识的数字。                         |
| `productTypeId`   | 产品型号 ID。                                                |
| `productTypeName` | 产品型号名称。                                               |
| `status`          | 产品状态：<ul><li>1: 正常。</li><li>0: 停用。</li></ul>      |
| `updateBy`        | 更新人 ID。                                                  |
| `updateTime`      | 最后更新时间。                                               |

<a name="property"></a>

## Property 类

```swift
public class Property : NSObject{
    @objc public var productId:UInt64 = 0
    @objc public var markName = ""
    @objc public var maxValue = ""
    @objc public var minValue = ""
    @objc public var params = ""
    @objc public var pointName = ""
    @objc public var pointType:UInt = 0
    @objc public var readType:UInt = 0
    @objc public var remark = ""
    @objc public var status:UInt = 0
    @objc public var createBy:UInt64 = 0
    @objc public var createTime:UInt64 = 0
    @objc public var deleted:UInt = 0
    @objc public var tip:String = ""
}
```

设备的属性描述符。

| 参数            | 描述           |
| :-------------- | :------------- |
| `productId`      |   产品 ID。    |
| `markName` | 数据点标识。 |
| `maxValue`      |   最大值。    |
| `minValue` | 最小值。 |
| `params`      |   预留参数。    |
| `pointName` | 数据点名称。 |
| `pointType`      |  数据点类型。<li>1: 整型</li><li>2: 布尔型</li><li>3: 枚举型</li><li>4: 字符串型</li><li>5: 浮点型</li><li>6: 字节型</li><li>7: 原始数据型</li>     |
| `readType` | 读写类型。<li>1: 只读</li><li>2: 读写</li> |
| `remark`      |  备注     |
| `status` | 属性描述符的启用情况。<li>1: 启用</li><li>2: 停用</li> |
| `createBy`      |   创建人 ID。    |
| `createTime` | 创建时间。 |
| `deleted`      |   描述符是否已删除。<li>0: 未删除</li><li>1: 已删除</li>   |
| `tip` | 描述。 |

<a name="deviceshare"></a>

### DeviceShare 类

```swift
public class DeviceShare : NSObject{
    @objc public var nickName:String = ""
    @objc public var count:Int = 0
    @objc public var time:UInt64 = 0
    @objc public var deviceNumber:String = ""
    @objc public var deviceId:String = ""
}
```

分享的设备信息。

| 参数           | 描述                         |
| -------------- | ---------------------------- |
| `nickName`     | 设备昵称。                   |
| `count`        | 设备被分享次数。             |
| `time`         | 创建时间。                   |
| `deviceNumber` | 设备编号。                   |
| `deviceId`     | 设备 ID，必须是唯一的。 |

<a name="devicecancelable"></a>

### DeviceCancelable 类

```swift
public class DeviceCancelable : NSObject{
    @objc public var appuserId:String  = ""
    @objc public var avatar:String = ""
    @objc public var connect:Bool = false
    @objc public var createTime:UInt64 = 0
    @objc public var deviceNumber:String = ""
    @objc public var deviceNickname:String = ""
    @objc public var email:String = ""
    @objc public var deviceId:String = ""
    @objc public var nickName:String = ""
    @objc public var phone:String = ""
    @objc public var productId:String = ""
    @objc public var productKey:String = ""
    @objc public var sharer:String = ""
    @objc public var uType:String = ""
    @objc public var updateTime:UInt64 = 0
}
```

分享给他人设备详情。

| 参数             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| `appuserId`      | 用户 ID。                                                    |
| `avatar`         | 用户头像。                                                   |
| `connect`        | 是否在线。<ul><li> true：在线。</li><li>false：离线。</li></ul> |
| `createTime`     | 创建时间。                                                   |
| `deviceNumber`   | 设备编号。                                                   |
| `deviceNickname` | 设备昵称。                                                   |
| `email`          | 用户邮箱.                                                    |
| `deviceId`       | 设备 ID，必须是唯一的。                                 |
| `nickName`       | 用户昵称。                                                   |
| `phone`          | 用户手机。                                                   |
| `productId`      | 产品 ID。                                                    |
| `productKey`     | 产品编号。                                                   |
| `sharer`         | 分享人 ID。                                                  |
| `uType`          | 用户角色：<ul><li> 1：所有者。</li><li>2：管理员。</li><li>3：成员。</li></ul> |
| `updateTime`     | 更新时间。                                                   |

<a name="sharedetail"></a>

### ShareDetail 类

```swift
public class ShareDetial : NSObject{
    @objc public var auditStatus:Bool = false
    @objc public var content:String = ""
    @objc public var createBy:UInt64 = 0
    @objc public var createTime:UInt64 = 0
    @objc public var deleted:Int = 0
    @objc public var id:UInt64 = 0
    @objc public var merchantId:UInt64 = 0
    @objc public var merchantName:String = ""
    @objc public var msgType:Int = 0
    @objc public var para:String = ""
    @objc public var permission:Int = 0
    @objc public var status:Int = 0
    @objc public var title:String = ""
    @objc public var type:Int = 0
    @objc public var updateBy:UInt64 = 0
    @objc public var updateTime:UInt64 = 0
    @objc public var userId:UInt64 = 0
}
```

分享详情。

| 参数           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `auditStatus`  | 消息处理状态：<ul><li>t: 已处理。</li><li>f: 未处理。</li></ul> |
| `content`      | 推送内容。                                                   |
| `createBy`     | 创建人 ID。                                                  |
| `createTime`   | 创建时间。                                                   |
| `deleted`      | 是否已删除。<ul><li>1: 已删除。</li><li>0: 未删除。</li></ul> |
| `id`           | 该条消息 ID。                                                |
| `merchantId`   | 商户 ID。                                                    |
| `merchantName` | 商户名称。                                                   |
| `msgType`      | 消息类型：<ul><li>1: 设备分享消息。</li></ul>                |
| `para`         | 分享口令。                                                   |
| `permission`   | 推送权限：<ul><li>2：管理员。</li><li>3：成员。</li></ul>    |
| `status`       | 状态：<ul><li>1：发送成功。</li><li>2：发送失败。</li><li>3：待发送。</li></ul> |
| `title`        | 推送标题。                                                   |
| `type`         | 推送类型：<ul><li>1：App 消息。</li><li>2：短信消息。</li><li>3：邮箱信息。</li></ul> |
| `updateBy`     | 更新人 ID。                                                  |
| `updateTime`   | 更新时间。                                                   |
| `userId`       | 被分享者 ID。                                                |

<a name="shareitem"></a>

### ShareItem 类

```swift
public class ShareItem : NSObject{
    @objc public var auditStatus:Bool = false
    @objc public var content:String = ""
    @objc public var createBy:UInt64 = 0
    @objc public var createTime:UInt64 = 0
    @objc public var deleted:Int = 0
    @objc public var deviceNumber:UInt64 = 0
    @objc public var id:UInt64 = 0
    @objc public var img:String = ""
    @objc public var merchantId:UInt64 = 0
    @objc public var merchantName:String = ""
    @objc public var msgType:Int = 0
    @objc public var para:String = ""
    @objc public var permission:Int = 0
    @objc public var productName:String = ""
    @objc public var pushTime:UInt64 = 0
    @objc public var shareName:String = ""
    @objc public var status:Int = 0
    @objc public var title:String = ""
    @objc public var type:Int = 0
    @objc public var userId:UInt64 = 0
}
```

分享条目。

| 参数           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `auditStatus`  | 消息处理状态：<ul><li>t: 已处理。</li><li>f: 未处理。</li></ul> |
| `content`      | 推送内容。                                                   |
| `createBy`     | 创建人 ID。                                                  |
| `createTime`   | 创建时间。                                                   |
| `deleted`      | 是否已删除。<ul><li>1: 已删除。</li><li>0: 未删除。</li></ul> |
| `deviceNumber` | 设备编号。                                                   |
| `id`           | 该条消息 ID。                                                |
| `img`          | 产品图片地址。                                               |
| `merchantId`   | 商户 ID。                                                    |
| `merchantName` | 商户名称。                                                   |
| `msgType`      | 消息类型：<ul><li>1: 设备分享消息。</li></ul>                |
| `para`         | 分享口令。                                                   |
| `permission`   | 推送权限：<ul><li>2：管理员。</li><li>3：成员。</li></ul>    |
| `productName`  | 产品名称。                                                   |
| `pushTime`     | 推送时间。                                                   |
| `shareName`    | 分享设备的名称。                                             |
| `status`       | 状态：<ul><li>1：发送成功。</li><li>2：发送失败。</li><li>3：待发送。</li></ul> |
| `title`        | 推送标题。                                                   |
| `type`         | 推送类型：<ul><li>1：App 消息。</li><li>2：短信消息。</li><li>3：邮箱信息。</li></ul> |
| `userId`       | 被分享者 ID。                                                |

<a name="pageturn"></a>

### PageTurn 类

```swift
public class PageTurn:NSObject{
    @objc var currentPage : Int = 0
    @objc var end : Int = 0
    @objc var firstPage : Int = 0
    @objc var nextPage : Int = 0
    @objc var page : Int = 0
    @objc var pageCount : Int = 0
    @objc var pageSize : Int = 0
    @objc var prevPage : Int = 0
    @objc var rowCount : Int = 0
    @objc var start : Int = 0
    @objc var startIndex : Int = 0
}
```

产品查询翻页信息。。

| 参数          | 描述             |
| ------------- | ---------------- |
| `currentPage` | 当前页码。       |
| `end`         | 当前页结束条数。 |
| `firstPage`   | 第一页页码。     |
| `nextPage`    | 下一页页码。     |
| `page`        | 查询的页码。     |
| `pageCount`   | 页数。           |
| `pageSize`    | 分页大小。       |
| `prevPage`    | 前一页页码。     |
| `rowCount`    | 条数             |
| `start`       | 当前页开始条数。 |
| `startIndex`  | 开始翻页的索引。 |

<a name="firmwareinfo"></a>

### FirmwareInfo 类

```swift
public class FirmwareInfo : NSObject{
    @objc public var  releaseTime:UInt64 = 0
    @objc public var  size:UInt = 0
    @objc public var  currentVersion:String = ""
    @objc public var  upgradeVersion:String = ""
    @objc public var  remark:String = ""
    @objc public var  isUpgrade:Bool = false
    @objc public var  deviceNumber:UInt64 = 0
    @objc public var  upgradeId:String = ""
    @objc public var  deviceId:String = ""
}
```

固件信息。

| 参数              | 描述                              |
| ----------------- | --------------------------------- |
| `releaseTime` | 发布时间。 |
| `size`      | 文件大小。                        |
| `currentVersion`          | 设备当前固件版本。                        |
| `upgradeVersion`       | 最新固件版本。                        |
| `remark`       | 备注说明。                        |
| `isUpgrade`       | 是否可以升级。                        |
| `deviceNumber`       | 设备号。                        |
| `upgradeId`       | 升级记录 ID。                        |
| `deviceId`       | 设备 ID。                        |

<a name="firmwarestatus"></a>

### FirmwareStatus 类


```swift
public class FirmwareStatus : NSObject{
    @objc public var  deviceNumber:String = ""
    @objc public var  deviceName:String = ""
    @objc public var  deviceId:String = ""
    @objc public var  currentVersion:String = ""
    @objc public var  status:Int = 0
}
```

设备 OTA 固件的升级状态。

| 参数              | 描述                              |
| ----------------- | --------------------------------- |
| `deviceNumber`       | 设备号码。                        |
| `deviceName`       | 设备名称。                        |
| `deviceId`       | 设备 ID。                        |
| `currentVersion`       | 设备当前的 OTA 固件版本。                        |
| `status`       | 设备 OTA 固件的升级状态。<ul><li>1：升级完成。</li><li>2：升级失败。</li><li>3：升级取消。</li><li>4：待升级。</li><li>5：升级中。</li></ul>                        |

<a name="messagechannelstatus"></a>

#### MessageChannelStatus 类

```swift
@objc public enum MessageChannelStatus : Int{
    case DataArrived
    case Disconnected
    case Connecting
    case Connected
    case Reconnecting
    case Aborted
    case TokenWillExpire
    case TokenDidExpire
    case UnknownError
}
```

RTM 状态。

| 参数            | 描述                                            |
| :-------------- | :---------------------------------------------- |
| `DataArrived`     | 已接收设备端发送的消息。                        |
| `Disconnected`    | 未连接 RTM 服务器。                             |
| `Connecting`      | 正在连接 RTM 服务器。                           |
| `Connected`       | 已连接 RTM 服务器。                             |
| `Reconnecting`    | 正在重新连接 RTM 服务器。可能原因是网络有问题。 |
| `Aborted`         | 与 RTM 服务器中断连接。                         |
| `TokenWillExpire` | RTM 将要过期。                                  |
| `TokenDidExpire`  | RTM 已过期。                                    |
| `UnknownError`    | 未知错误。                                      |

<a name="playbackstatus"></a>

## PlaybackStatus 类

```swift
@objc public enum PlaybackStatus : Int{
    case RemoteJoin
    case RemoteLeft
    case LocalReady
    case LocalError
    case VideoReady
}
```

播放 SD 卡记录的视频时的状态。

| 参数         | 描述             |
| :----------- | :--------------- |
| `RemoteJoin` | 设备连接成功。   |
| `RemoteLeft` | 设备断开连接。   |
| `LocalReady` | 本地播放已就绪。 |
| `LocalError` | 本地播放出错。   |
| `VideoReady` | 收到设备的视频。 |

<a name="rtcnetworkstatus"></a>

### RtcNetworkStatus 类

```swift
public class RtcNetworkStatus : NSObject{
    public var isBusy : Bool             = false
    public var totalDuration : UInt      = 0
    public var txBytes : UInt            = 0
    public var rxBytes : UInt            = 0
    public var txKBitRate : UInt         = 0
    public var txAudioBytes : UInt       = 0
    public var rxAudioBytes : UInt       = 0
    public var txVideoBytes : UInt       = 0
    public var rxVideoBytes : UInt       = 0
    public var rxKBitRate : UInt         = 0
    public var txAudioKBitRate : UInt    = 0
    public var rxAudioKBitRate : UInt    = 0
    public var txVideoKBitRate : UInt    = 0
    public var rxVideoKBitRate : UInt    = 0
    public var lastmileDelay : UInt      = 0
    public var cpuTotalUsage : Double    = 0
    public var cpuAppUsage : Double      = 0
    public var users : UInt              = 0
    public var connectTimeMs : Int       = 0
    public var txPacketLossRate : Int    = 0
    public var rxPacketLossRate : Int    = 0
    public var memoryAppUsageRatio : Double = 0
    public var memoryTotalUsageRatio : Double = 0
    public var memoryAppUsageInKbytes : Int = 0
}
```

通话相关的统计信息。

| 参数                   | 描述                                                         |
| :--------------------- | :----------------------------------------------------------- |
| `isBusy`                 | 是否在呼叫中：<li>`true`: 在呼叫中。</li><li>`false`: 未进行呼叫。</li>            |
| `totalDuration`          | 本地用户通话时长（秒），累计值。                             |
| `txBytes`                | 发送字节数（bytes）。                                        |
| `rxBytes`                | 接收字节数（bytes）。                                        |
| `txKBitRate`             | 发送码率（Kbps）。                                           |
| `txAudioBytes`           | 发送音频字节数（bytes），累计值。                            |
| `rxAudioBytes`           | 接收音频字节数（bytes），累计值。                            |
| `txVideoBytes`           | 发送视频字节数（bytes），累计值。                            |
| `rxVideoBytes`           | 接收视频字节数（bytes），累计值。                            |
| `rxKBitRate`             | 接收码率（Kbps）。                                           |
| `txAudioKBitRate`        | 音频发送码率 (Kbps）。                                       |
| `rxAudioKBitRate`        | 音频接收码率 (Kbps）。                                       |
| `txVideoKBitRate`        | 视频发送码率 (Kbps）。                                       |
| `rxVideoKBitRate`        | 视频接收码率 (Kbps）。                                       |
| `lastmileDelay`          | 客户端到接入服务器的延时 (毫秒)。                            |
| `cpuTotalUsage`          | 当前系统的 CPU 使用率 (%)。                                  |
| `cpuAppUsage`            | 当前 App 的 CPU 使用率 (%)。                                 |
| `users`                  | 当前频道内的用户人数。                                       |
| `connectTimeMs`          | 从开始建立连接到成功连接的时间（毫秒）。如报告 0，则表示无效。 |
| `txPacketLossRate`       | 使用抗丢包技术前，客户端上行发送到服务器丢包率 (%)。         |
| `rxPacketLossRate`       | 使用抗丢包技术前，服务器下行发送到客户端丢包率 (%)。         |
| `memoryAppUsageRatio`    | 当前 App 的内存占比 (%)。该值仅作参考。受系统限制可能无法获取。 |
| `memoryTotalUsageRatio`  | 当前系统的内存占比 (%)。该值仅作参考。受系统限制可能无法获取。 |
| `memoryAppUsageInKbytes` | 当前 App 的内存大小 (KB)。该值仅作参考。受系统限制可能无法获取。 |

<a name="memberstate"></a>

### MemberState 类

```swift
@objc public enum MemberState : Int{
    case Exist
    case Enter
    case Leave
}
```

用户状态。

| 参数    | 描述                     |
| :------ | :----------------------- |
| `Exist` | 当前通话中已有其他用户。 |
| `Enter` | 其他用户加入通话。       |
| `Leave` | 其他用户退出通话。       |


<a name="messagechannelstatus"></a>

### MessageChannelStatus 枚举

```swift
@objc public enum MessageChannelStatus : Int{
    case DataArrived
    case Disconnected
    case Connecting
    case Connected
    case Reconnecting
    case Aborted
    case TokenWillExpire
    case TokenDidExpire
    case UnknownError
}
```

消息频道连接状态。

|  枚举值              | 描述                              |
| ----------------- | --------------------------------- |
| `DataArrived`       |   收到数据。        |
| `Disconnected`       |    连接断开。                     |
| `Connecting`       |      连接中。                   |
| `Connected`       |       连接成功。                 |
| `Reconnecting`       |      重连中。             |
| `Aborted`       |       连接中断。            |
| `TokenWillExpire`       |     RTM Token 即将过期。              |
| `TokenDidExpire`       |     RTM Token 已经过期。               |
| `UnknownError`       |        未知错误           |

<a name="sdkstatus"></a>

### SdkStatus 枚举

```objc
@objc public enum SdkStatus : Int{
    case NotReady
    case InitCallFail
    case InitMqttFail
    case InitPushFail
    case AllReady
    case Reconnected
    case Disconnected
}
```

SDK 状态。

**参数**

| 参数           | 描述                                                 |
| -------------- | ---------------------------------------------------- |
| `NotReady`     | 登录成功，但还在初始化各个子模块中，处于未就绪状态。 |
| `InitCallFail` | 登录成功后，初始化呼叫模块出错。                     |
| `InitMqttFail` | 登录成功后，初始化 MQTT 模块出错。                   |
| `InitPushFail` | 登录成功后，初始化推送模块出错。                     |
| `AllReady`     | 登录成功后，初始化过程完毕，处于就绪状态。           |
| `Reconnected`  | 登录成功后，MQTT 重连成功。                          |
| `Disconnected` | 登录成功后，MQTT 断开连接。                          |


<a name="actionack"></a>

### ActionAck 枚举

通话中产生的事件。

```objc
@objc public enum ActionAck:Int{

    case StateInited
    case LocalHangup
    case LocalAnswer
    case RemoteHangup
    case RemoteAnswer
    case RemoteTimeout
    case RecordEnd
    case LocalTimeout

    case RemoteVideoReady
    case RemoteAudioReady

    case RemoteBusy
    case CallIncoming
    case CallForward
    case CallOutgoing
    case UnknownAction
}
```

**参数**

| 参数               | 描述               |
| ------------------ | ------------------ |
| `StateInited`      | 初始化呼叫状态。   |
| `LocalHangup`      | 本地挂断。         |
| `LocalAnswer`      | 本地接听。         |
| `RemoteHangup`     | 设备挂断。         |
| `RemoteAnswer`     | 设备接听。         |
| `RemoteTimeout`    | 对端超时。         |
| `RecordEnd`        | 云录制停止。       |
| `LocalTimeout`     | 呼叫超时。         |
| `RemoteVideoReady` | 首次收到设备视频。 |
| `RemoteAudioReady` | 首次收到设备音频。 |
| `RemoteBusy`       | 设备忙。           |
| `CallIncoming`     | 设备来电振铃。     |
| `CallForward`      | 本地去电中继。     |
| `CallOutgoing`     | 本地去电振铃。     |
| `UnknownAction`    | 未知错误。         |

<a name="audioeffectid"></a>

### AudioEffectId 枚举

音频特效。

```objc
@objc public enum AudioEffectId:Int{
    case NORMAL
    case OLDMAN
    case BABYBOY
    case BABYGIRL
    case ZHUBAJIE
    case ETHEREAL
    case HULK
}
```

**参数**

| 参数       | 描述                                                     |
| ---------- | -------------------------------------------------------- |
| `NORMAL`   | （默认）原声，即关闭本地语音的变声、美音或语聊美声效果。 |
| `OLDMAN`   | 变声：老男孩。                                           |
| `BABYBOY`  | 变声：小男孩。                                           |
| `BABYGIRL` | 变声：小女孩。                                           |
| `ZHUBAJIE` | 变声：猪八戒                                             |
| `ETHEREAL` | 变声：空灵。                                             |
| `HULK`     | 变声：绿巨人。                                           |