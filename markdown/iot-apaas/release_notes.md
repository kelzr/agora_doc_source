## 已知问题

SDK 在 1.5.0 版本对呼叫功能进行了重构，由此对 SDK 版本之间的互通产生了以下影响：

- 1.5.0 版本仅能与 1.5.0 及以上版本互通。
- 1.3.0 版本仅能与 1.3.0 及以下版本互通。

声网强烈建议你升级至 1.5.0 版本，以获取更好的体验。

## 1.5.0 版

该版本于 2023 年 6 月 28 日发布。

#### 升级必看

为提高弱网环境下呼叫出图成功率、缩短呼叫出图响应时间，该版本重构了呼叫功能。重构后的 SDK 无法与之前版本的 SDK 互通，声网推荐你升级所有的设备端和客户端。

#### 新增特性

**1. H.265 视频格式**

该版本新增了对 H.265 视频格式的支持。你可以在设备端调用 `agora_iot_cloud_record_start` 方法并将 `video_type` 设置为 `AGO_VIDEO_DATA_TYPE_H265`，从而将 H.265 格式的视频推送到云存储中。

**2. 云存储视频加密公钥**

该版本在客户端上新增了以下 API，用于加强云存储视频加密的安全性：
- Android: `setPublicKey` 方法和 `onSetPublicKeyDone` 回调
- iOS: `publicKeySet` 方法

你可以在应用层自行生成公钥和私钥，再通过该方法将公钥传入 SDK。例如，只有 SDK 的公钥和你 app 的私钥都正确，用户才能查看加密视频。

**3. MQTT 连接状态**

为方便获取 MQTT（消息队列遥测传输协议）模块的连接状态，该版本在 iOS 客户端新增了 `getMqttIsConnected` 方法。

**4. 错误码**

为方便排查问题，该版本在 Android 客户端新增如下错误码：

- `XERR_FILE_NO_STREAM` (`-20009`)：文件里没有数据流。
- `XERR_ACCOUNT_SET_PUBLICKEY` (`-30017`)：设置公钥失败。
- `XERR_CALLKIT_CALLDEV_FAILURE` (`-40011`)：呼叫设备失败。
- `XERR_CALLKIT_RTCTOKEN_FAILURE` (`-40012`)：生成 RTC Token 失败。

#### 改进

**1. 云存储**

为提升云存储的易用性，该版本在设备端进行了以下改进：
- 删除了 `agora_iot_cloud_record_start` 方法中的 `record_id` 和 `end_time` 参数，并在 SDK 内部添加了对应状态参数的处理逻辑，因此你不再需要自行处理这些参数。
- 在 `ago_video_frame_t` 中增加了 `fps` 参数。SDK 会根据该参数的设置调整推送视频的速度，从而实现视频预录制，不再依赖 `agora_iot_push_video_frame` 方法的调用时间戳。

  <div class="alert note">只有在 <code>agora_iot_push_video_frame</code> 方法中设置 <code>push_type</code> 参数为 <code>AGO_AV_PUSH_TYPE_MASK_OSS</code> 时，<code>ago_video_frame_t</code> 中的 <code>fps</code> 参数才有效。</div>

**2. 24 小时连续通话**

该版本将单个视频通话的最大时长由 1 小时升级至 24 小时，超过上限后 SDK 会自动结束通话。

**3. 来电通知**

该版本在 iOS 客户端的 `ICallKitMgr.register` 方法中新增了 `incoming` 和 `memberState` 参数，你可以通过该参数获取来电的设备、用户 ID 等相关信息。

**4. 音视频流录制**

该版本在 Android 客户端中优化了开始录制的方法并新增了状态回调，具体如下：
- 在 `talkingRecordStart` 方法中新增了 `outFilePath` 参数，你可以指定录制文件的存储路径。
- 提供了 `isTalkingRecording` 方法，获取是否正在录制当前通话。
- 提供了 `onRecordingError` 回调，报告录制失败的错误码。

**5. DP 属性点**

该版本在设备端中支持使用数组指针赋值 string 类型的 DP 属性点，你可以通过 `agora_dp_value_t` 的 `dp_str` 参数实现。同时，SDK 将不再自动释放 `dp_str` 参数的内存，你需要手动管理内存以避免内存泄露。

**6. 最小带宽探测码率**

该版本在设备端的 `agora_iot_config_t` 中新增了 `min_possible_bitrate` 参数，你可以设置带宽探测的最小码率。当带宽探测的实际码率比 `min_possible_bitrate` 参数的设置值低时，`cb_target_bitrate_changed` 回调会返回 `min_possible_bitrate` 参数的设置值。

**7. 实时消息**

该版本在设备端将实时消息的长度扩展至 4096 字节，你可以通过 `agora_iot_send_rtm` 方法的 `msg_len` 参数进行设置。此外，该版本还把 `peer_uid` 参数改名为 `peer_id`。

**8. 本地回放**

该版本在设备端上扩展了本地回放的以下能力：
- 为支持同一设备的多个客户端同时观看不同的视频回放文件，该版本在设备端 `agora_iot_file_player_callback` 类的 `cb_start_push_frame` 和 `cb_stop_push_frame` 回调中新增 `channel_name` 参数。
- 为支持发送多种格式的音视频帧到本地回放频道，该版本删除了 `agora_iot_file_video_t`、`agora_iot_file_audio_t` 和 `agora_iot_file_video_e` 数据类型，并将 `agora_iot_file_player_push_video_frame` 和 `agora_iot_file_player_push_audio_frame` 方法的 `frame` 参数分别改为 `ago_video_frame_t` 和 `ago_audio_frame_t` 类型。


## 1.3.0 版

该版本于 2023 年 2 月  16 日发布，新增了对云存储视频文件的加密。你可以下载[灵隼 Demo](https://docs.agora.io/cn/iot-apaas/downloads?platform=All%20Platforms) 进行体验。

## 1.2.0 版

该版本于 2022 年 11 月  23 日发布，主要新增如下功能：

- 推送和查询告警消息、图片、视频。详见[告警服务](./agora_link_alram/)。
- 通过云存储服务录制告警视频。详见[云存储服务](./agora_link_cloud_storage/)。

## 1.1.0 版

该版本于 2022 年 9 月  19 日发布，主要新增如下功能：

- [通过 OTA 升级设备端固件](./agora_link_ota/)。
- [使用 RESTful API 对接第三方用户账号](./agora_link_restful_api?platform=All%20Platforms#对接自研或第三方账户系统)。

## 1.0.0 版

该版本于 2022 年 6 月  27 日发布。

该版本是声网灵隼物联网云平台（简称声网灵隼）的第一个版本，主要为音视频设备一站式上云和自主智能化开发提供连接管理、设备管理、上下行链路以及基于声网的实时音视频的呼叫管理能力。