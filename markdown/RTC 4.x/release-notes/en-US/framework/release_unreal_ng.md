## v4.2.1 Beta

This version was released on August xx, 2023.
#### Compatibility changes

If you use the features mentioned in this section, ensure that you modify the implementation of the relevant features after upgrading the SDK.

If you have already integrated the 3.x version of the SDK and wish to upgrade to this version, please refer to [Migration Guide]() for upgrade instructions.

**1. Video capture (Windows, iOS)**

This release optimizes the APIs for camera and screen capture function. As of v4.2.1, ensure you use the alternative methods listed in the table below and specify the video source by setting the `sourceType` parameter.

| Deleted methods | Alternative methods |
|:---------|:--------|
| <li>`startPrimaryCameraCapture` (Windows)</li><li>`startSecondaryCameraCapture` (Windows, iOS)</li> | `startCameraCapture` |
| <li>`stopPrimaryCameraCapture` (Windows)</li><li>`stopSecondaryCameraCapture` (Windows, iOS)</li> | `stopCameraCapture` |
| <li>`startPrimaryScreenCapture` (Windows)</li><li>`startSecondaryScreenCapture` (Windows)</li> | `startScreenCaptureBySourceType` (Windows) |
| <li>`stopPrimaryScreenCapture` (Windows)</li><li>`stopSecondaryScreenCapture` (Windows)</li> | `stopScreenCaptureBySourceType` (Windows) |

**2. Video data acquisition**

- The `onCaptureVideoFrame` and `onPreEncodeVideoFrame` callbacks are added with a new parameter called `sourceType`, which is used to indicate the specific video source type.

- The following callbacks are deleted. Get the video source type through the `sourceType` parameter in the `onPreEncodeVideoFrame` and `onCaptureVideoFrame` callbacks. (Windows)
    - `onSecondaryPreEncodeCameraVideoFrame`
    - `onScreenCaptureVideoFrame`
    - `onPreEncodeScreenVideoFrame`
    - `onSecondaryPreEncodeScreenVideoFrame`

**3. Channel media options**

- `publishCustomAudioTrackEnableAec` in `ChannelMediaOptions` is deleted. Use `publishCustomAudioTrack` instead.
- `publishTrancodedVideoTrack` in `ChannelMediaOptions` is renamed to `publishTranscodedVideoTrack`.
- `publishCustomAudioSourceId` in `ChannelMediaOptions` is renamed to `publishCustomAudioTrackId`.

**4. Local video mixing (Windows)**

- The `VideoInputStreams` in `LocalTranscoderConfiguration` is changed to `videoInputStreams`.
- The `MediaSourceType` in `TranscodingVideoStream` is changed to `VideoSourceType`.

**5. Video encoder algorithm**

As of this release, the SDK optimizes the video encoder algorithm and upgrades the default video encoding resolution from 640 × 360 to 960 × 540 to accommodate improvements in device performance and network bandwidth, providing users with a full-link HD experience in various audio and video interaction scenarios.

Call the `setVideoEncoderConfiguration` method to set the expected video encoding resolution in the video encoding parameters configuration.

The increase in the default resolution affects the aggregate resolution and thus the billing rate. See [Pricing](https://docs.agora.io/en/video-calling/reference/pricing).

**6. Miscellaneous**

- `onApiCallExecuted` is deleted. Agora recommends getting the results of the API implementation through relevant channels and media callbacks.
- The `IAudioFrameObserver` class is renamed to `IAudioPcmFrameSink`, thus the prototype of the following methods are updated accordingly:
    - `onFrame`
    - `registerAudioFrameObserver` and `unregisterAudioFrameObserver` in `MediaPlayer`
- `startChannelMediaRelay`, `updateChannelMediaRelay`, `startChannelMediaRelayEx` and `updateChannelMediaRelayEx` are deprecated. Use `startOrUpdateChannelMediaRelay` and `startOrUpdateChannelMediaRelayEx` instead.

#### New features

**1. AI noise reduction**

This release introduces the AI noise reduction function. Once enabled, the SDK automatically detects and reduces background noises. Whether in bustling public venues or real-time competitive arenas that demand lightning-fast responsiveness, this function guarantees optimal audio clarity, providing users with an elevated audio experience. You can enable this function through the newly-introduced `setAINSMode` method and set the noise reduction mode as balance, aggressive or low latency according to your scenarios.

**2. Enhanced virtual background**

To increase the fun of real-time video calls and protect user privacy, this version has enhanced the virtual background feature. You can now set custom backgrounds of various types by calling the `enableVirtualBackground` method, including:

- Process the background as alpha information without replacement, only separating the portrait and the background. This can be combined with the local video mixing feature to achieve a portrait-in-picture effect.
- Replace the background with various formats of local videos.

**3. Video scenario settings**

This release introduces `setVideoScenario` for setting the video application scene. The SDK will automatically enable the best practice strategy based on different scenes, adjusting key performance indicators to optimize video quality and improve user experience. Whether it is a formal business meeting or a casual online gathering, this feature ensures that the video quality meets the requirements.

Currently, this feature provides targeted optimizations for real-time video conferencing scenarios, including:

- Automatically activate multiple anti-weak network technologies to enhance the capability and performance of low-quality video streams in meeting scenarios where high bitrate are required, ensuring smoothness when multiple streams are subscribed by the receiving end.
- Monitor the number of subscribers for the high-quality and low-quality video streams in real time, dynamically adjusting the configuration of the high-quality stream and dynamically enabling or disabling the low-quality stream, to save uplink bandwidth and consumption.

**4. Local video mixing (macOS, iOS, Android)**

This release adds the local video mixing feature. You can use the `startLocalVideoTranscoder` method to mix and render multiple video streams locally, such as camera-captured video, screen sharing streams, video files, images, etc. This allows you to achieve custom layouts and effects, making it easy to create personalized video display effects to meet various scenario requirements, such as remote meetings, live streaming, online education, while also supporting features like portrait-in-picture effect.

Additionally, the SDK provides the `updateLocalTranscoderConfiguration` method and the `onLocalVideoTranscoderError` callback. After enabling local video mixing, you can use the `updateLocalTranscoderConfiguration` method to update the video mixing configuration. Where an error occurs in starting the local video mixing or updating the configuration, you can get the reason for the failure through the `onLocalVideoTranscoderError` callback.

<div class="alert note">Local video mixing requires more CPU resources. Therefore, Agora recommends enabling this function on devices with higher performance.</div>

**5. Local video mixing (Windows)**

This release adds the `onLocalVideoTranscoderError` callback. When there is an error in starting or updating the local video mixing, the SDK triggers this callback to report the reason for the failure.

**6. Cross-device synchronization**

In real-time collaborative singing scenarios, network issues can cause inconsistencies in the downlinks of different client devices. To address this, this release introduces `getNtpWallTimeInMs` for obtaining the current Network Time Protocol (NTP) time. By using this method to synchronize lyrics and music across multiple client devices, users can achieve synchronized singing and lyrics progression, resulting in a better collaborative experience.

**7. Instant frame rendering**

This release adds the `enableInstantMediaRendering` method to enable instant rendering mode for audio and video frames, which can speed up the first video or audio frame rendering after the user joins the channel.

**8. Video rendering tracing**

This release adds the `startMediaRenderingTracing` and `startMediaRenderingTracingEx` methods. The SDK starts tracing the rendering status of the video frames in the channel from the moment this method is called and reports information about the event through the `onVideoRenderingTracingResult` callback.

Agora recommends that you use this method in conjunction with the UI settings, such as buttons and sliders, in your app. For example, call this method when the user clicks **Join Channel** button and then get the indicators in the video frame rendering process through the `onVideoRenderingTracingResult` callback. This enables developers to optimize the indicators and improve the user experience.

#### Improvements

**1. Voice changer**

This release introduces the `setLocalVoiceFormant` method that allows you to adjust the formant ratio to change the timbre of the voice. This method can be used together with the `setLocalVoicePitch` method to adjust the pitch and timbre of voice at the same time, enabling a wider range of voice transformation effects.

**2. Enhanced screen share (Android, iOS)**

This release adds the `queryScreenCaptureCapability` method, which is used to query the screen capture capabilities of the current device. To ensure optimal screen sharing performance, particularly in enabling high frame rates like 60 fps, Agora recommends you to query the device's maximum supported frame rate using this method beforehand.

This release also adds the `setScreenCaptureScenario` method, which is used to set the scenario type for screen sharing. The SDK automatically adjusts the smoothness and clarity of the shared screen based on the scenario type you set.

**3. Improved compatibility with audio file types (Android)**

As of this release, you can use the following methods to open files with a URI starting with `content://` : `startAudioMixing`, `playEffect`, and `openWithMediaSource`.

**4. Enhanced rendering compatibility (Windows)**

This release enhances the rendering compatibility of the SDK. Issues like black screens caused by rendering failures on certain devices are fixed.

**5. Audio and video synchronization**

For custom video and audio capture scenarios, this release introduces `getCurrentMonotonicTimeInMs` for obtaining the current Monotonic Time. By passing this value into the timestamps of audio and video frames, developers can accurately control the timing of their audio and video streams, ensuring proper synchronization.

**6. Multi-camera capture and multi-screen capture**

This release introduces `startScreenCaptureBySourceType` (PC only) and `startCameraCapture`. By calling these methods multiple times and specifying the `sourceType` parameter, developers can start capturing video streams from multiple cameras and screens for local video mixing or multi-channel publishing. This is particularly useful for scenarios such as remote medical care and online education, where multiple cameras and displays need to be connected.

**7. Channel media relay**

This release introduces `startOrUpdateChannelMediaRelay` and `startOrUpdateChannelMediaRelayEx`, allowing for a simpler and smoother way to start and update media relay across channels. With these methods, developers can easily start the media relay across channels and update the target channels for media relay with a single method. Additionally, the internal interaction frequency has been optimized, effectively reducing latency in function calls.

**8. Custom audio tracks**

To better meet the needs of custom audio capture scenarios, this release adds `createCustomAudioTrack` and `destroyCustomAudioTrack` for creating and destroying custom audio tracks. Two types of audio tracks are also provided for users to choose from, further improving the flexibility of capturing external audio source:

- Mixable audio track: Supports mixing multiple external audio sources and publishing them to the same channel, suitable for multi-channel audio capture scenarios.
- Direct audio track: Only supports publishing one external audio source to a single channel, suitable for low-latency audio capture scenarios.

**9. Video frame observer**

As of this release, the SDK optimizes the `onRenderVideoFrame` callback, and the meaning of the return value is different depending on the video processing mode:

- When the video processing mode is `processModeReadOnly`, the return value is reserved for future use.
- When the video processing mode is `processModeReadWrite`, the SDK receives the video frame when the return value is `true`. The video frame is discarded when the return value is `false`.

**Miscellaneous**

This version improves the network transmission strategy, enhancing the smoothness of audio and video interactions.
#### Issues fixed

This release fixed the following issues:

- Occasional crashes occur on Android devices when users joining or leaving a channel. (Android)
- When the host frequently switching the user role between broadcaster and audience in a short period of time, the audience members cannot hear the audio of the host.
- Occasional failure when enabling in-ear monitoring. (Android)
- Occasional loss of the `onFirstRemoteVideoFrame` callback during channel media relay. (iOS)
- The receiver actively subscribed to the high-quality stream but unexpectedly received a low-quality stream. (iOS)
- The receiver was receiving the low-quality stream originally, and automatically switched to high-quality stream after a few seconds. (macOS)
- Occasional echo. (Android)
- Occasional screen jittering during screen sharing. (macOS)
- Abnormal client status cased by an exception in the `onRemoteAudioStateChanged` callback. (Android, iOS)
- When using Agora Media Player to play RTSP video streams, the video images sometimes appeared pixelated. (Windows)
- Playing audio files with a sample rate of 48 kHz failed.
- Crashes occurred after users set the video resolution as 3840 × 2160 and started CDN streaming on Xiaomi Redmi 9A devices. (Android)
- If the rendering view of the player was set as a `UIViewController`'s view, the video was zoomed from the bottom-left corner to the middle of the screen when entering full-screen mode. (macOS)
- Adding an alpha channel to an image in PNG or GIF format failed when the local client mixed video streams. (Windows)
- In real-time chorus scenarios, remote users heard noises and echoes when an OPPO R11 device joined the channel in loudspeaker mode. (Android)
- When the playback of the local music finished, the `onAudioMixingFinished` callback was not properly triggered. (Android)
- After joining the channel, remotes users saw a watermark even though the watermark was deleted. (Windows)
- If a watermark was added after starting screen sharing, the watermark did not display the screen. (Windows)
- When joining a channel and accessing an external camera, calling `setDevice` to specify the video capture device as the external camera did not take effect. (macOS, Windows)
- When using a video frame observer, the first video frame was occasionally missed on the receiver's end. (Android)
- When sharing screens in scenarios involving multiple channels, remote users occasionally saw black screens. (Android)
- When trying to outline the shared window and put it on top, the shared window did not stay on top of other windows. (Windows)
- Switching to the rear camera with the virtual background enabled occasionally caused the background to be inverted. (Android)
- When there were multiple video streams in a channel, calling some video enhancement APIs occasionally failed.
- At the moment when a user left a channel, a request for leaving was not sent to the server and the leaving behavior was incorrectly determined by the server as timed out.

#### API changes

**Added**

- `startCameraCapture`
- `stopCameraCapture`
- `startScreenCaptureBySourceType` (Windows, macOS)
- `stopScreenCaptureBySourceType` (Windows, macOS)
- `startOrUpdateChannelMediaRelay`
- `startOrUpdateChannelMediaRelayEx`
- `getNtpWallTimeInMs`
- `setVideoScenario`
- `getCurrentMonotonicTimeInMs`
- `onLocalVideoTranscoderError`
- `startLocalVideoTranscoder` (macOS, iOS, Android)
- `updateLocalTranscoderConfiguration` (macOS, iOS, Android)
- `queryScreenCaptureCapability` (iOS, Android)
- `setScreenCaptureScenario` (iOS, Android)
- `setAINSMode`
- `createCustomAudioTrack`
- `destroyCustomAudioTrack`
- `AudioTrackConfig`
- `AudioAinsMode`
- `AudioTrackType`
- `VideoApplicationScenarioType`
- `ScreenCaptureFramerateCapability`
- The `domainLimit` and `autoRegisterAgoraExtensions` members in `RtcEngineContext`
- The `sourceType` parameter in `onCaptureVideoFrame` and `onPreEncodeVideoFrame` callbacks
- The `backgroundNone` and `backgroundVideo` enumerators in `backgroundSourceType`

**Deprecated**

- `startChannelMediaRelay`
- `startChannelMediaRelayEx`
- `updateChannelMediaRelay`
- `updateChannelMediaRelayEx`
- `onChannelMediaRelayEvent`
- `ChannelMediaRelayEvent`
- `enableInstantMediaRendering`
- `startMediaRenderingTracing`
- `startMediaRenderingTracingEx`
- `onVideoRenderingTracingResult`
- `MediaTraceEvent`
- `VideoRenderingTracingInfo`

**Deleted**

- `startPrimaryScreenCapture` (Windows)
- `startSecondaryScreenCapture` (Windows)
- `stopPrimaryScreenCapture` (Windows)
- `stopSecondaryScreenCapture` (Window)
- `startPrimaryCameraCapture` (Windows)
- `startSecondaryCameraCapture` (Windows, iOS)
- `stopPrimaryCameraCapture` (Windows)
- `stopSecondaryCameraCapture` (Windows, iOS)
- `onSecondaryPreEncodeCameraVideoFrame` (Windows)
- `onScreenCaptureVideoFrame`
- `onPreEncodeScreenVideoFrame`
- `onSecondaryPreEncodeScreenVideoFrame` (Windows)
- `onApiCallExecuted`
- `publishCustomAudioTrackEnableAec` in `ChannelMediaOptions` in `ChannelMediaOptions`
- `enableRemoteSuperResolution`
- `superResolutionType` in `RemoteVideoStats`
## v4.1.0

v4.1.0 was released on January 17, 2023.


This release is the first version of RTC Unreal SDK, including the following features:

**1. Multiple media tracks**

This release supports one `IRtcEngine` instance to collect multiple audio and video sources at the same time and publish them to the remote users by setting `RtcEngineEx` and `ChannelMediaOptions.`

- After calling `joinChannel` to join the first channel, call `joinChannelEx` multiple times to join multiple channels, and publish the specified stream to different channels through different user ID (`localUid`) and `ChannelMediaOptions` settings.
- You can simultaneously publish multiple sets of video streams captured by multiple cameras or screen sharing by setting `publishSecondaryCameraTrack` and `publishSecondaryScreenTrack` in `ChannelMediaOptions`. 

This release adds `createCustomVideoTrack` method to implement video custom capture. You can refer to the following steps to publish multiple custom captured video in the channel:

1. Create a custom video track: Call this method to create a video track, and get the video track ID.
2. Set the custom video track to be published in the channel: In each channel's `ChannelMediaOptions`, set the `customVideoTrackId` parameter to the ID of the video track you want to publish, and set `publishCustomVideoTrack` to `true`.
3. Pushing an external video source: Call `pushVideoFrame`, and specify `videoTrackId` as the ID of the custom video track in step 2 in order to publish the corresponding custom video source in multiple channels.

You can also experience the following features with the multi-channel capability:

- Publish multiple sets of audio and video streams to the remote users through different user IDs (`uid`).
- Mix multiple audio streams and publish to the remote users through a user ID (`uid`).
- Combine multiple video streams and publish them to the remote users through a user ID (`uid`).

**2. Ultra HD resolution (Beta)**

In order to improve the interactive video experience, the SDK optimizes the whole process of video capture, encoding, decoding and rendering, and now supports 4K resolution. The improved FEC (Forward Error Correction) algorithm enables adaptive switches according to the frame rate and number of video frame packets, which further reduces the video stuttering rate in 4K scenes.

Additionally, you can set the encoding resolution to 4K (3840 × 2160) and the frame rate to 60 fps when calling `SetVideoEncoderConfiguration`. The SDK supports automatic fallback to the appropriate resolution and frame rate if your device does not support 4K.

<div class="alert info">This feature has certain requirements with regards to device performance and network bandwidth, and the supported upstream and downstream frame rates vary on different platforms. To experience this feature, contact <a href="mailto:support@agora.io">technical support</a>.</div>

**3. Build-in media player**

To make it easier for users to integrate the Agora SDK and reduce the SDK's package size, this release introduces the Agora media player. After calling the `createMediaPlayer` method to create a media player object, you can then call the methods in the `IMediaPlayer` class to experience a series of functions, such as playing local and online media files, preloading a media file, changing the CDN route for playing according to your network conditions, or sharing the audio and video streams being played with remote users.


**4. Screen sharing**

This release optimizes the screen sharing function. You can enable this function in the following ways.

- Call the `StartScreenCaptureByDisplayId` method before joining a channel, and then call `JoinChannel` [2/2] to join a channel and set `publishScreenTrack` or `publishSecondaryScreenTrack` as true.
- Call the `StartScreenCaptureByDisplayId` method after joining a channel, and then call `UpdateChannelMediaOptions` to set `publishScreenTrack` or `publishSecondaryScreenTrack` as true.

 
**5. Spatial audio**

<div class="alert info">This feature is in experimental status. To enable this feature, contact <a href="mailto:sales-us@agora.io ">sales@agora.io</a>. Contact <a href="mailto:support@agora.io">technical support</a> if needed.</div>

You can set the spatial audio for the remote user as following:

- Local Cartesian Coordinate System Calculation: This solution uses the `ILocalSpatialAudioEngine` class to implement spatial audio by calculating the spatial coordinates of the remote user. You need to call `updateSelfPosition` and `updateRemotePosition` to update the spatial coordinates of the local and remote users, respectively, so that the local user can hear the spatial audio effect of the remote user.
  ![img](https://web-cdn.agora.io/docs-files/1656645542473)

You can also set the spatial audio for the media player as following:

- Local Cartesian Coordinate System Calculation: This solution uses the `ILocalSpatialAudioEngine` class to implement spatial audio. You need to call `updateSelfPosition` and `updatePlayerPositionInfo` to update the spatial coordinates of the local user and media player, respectively, so that the local user can hear the spatial audio effect of media player.
  ![img](https://web-cdn.agora.io/docs-files/1656646829637)

This release also adds the following features applicable to spatial audio effect scenarios, which can effectively enhance the user's sense of presence experience in virtual interactive scenarios.

- Sound insulation area: You can set a sound insulation area and sound attenuation parameter by calling `setZones`. When the sound source (which can be a user or the media player) and the listener belong to the inside and outside of the sound insulation area, the listner experiences an attenuation effect similar to that of the sound in the real environment when it encounters a building partition. You can also set the sound attenuation parameter for the media player and the user, respectively, by calling `setPlayerAttenuation` and `setRemoteAudioAttenuation`, and specify whether to use that setting to force an override of the sound attenuation paramter in `setZones`.
- Doppler sound: You can enable Doppler sound by setting the `enable_doppler` parameter in `SpatialAudioParams`, and the receiver experiences noticeable tonal changes in the event of a high-speed relative displacement between the source source and receiver (such as in a racing game scenario).
- Headphone equalizer: You can use a preset headphone equalization effect by calling the `setHeadphoneEQPreset` method to improve the hearing of the headphones.

**6. Media Stream Encryption**

This release adds support for media stream encryption, which encrypts your app’s audio and video streams with a unique key and salt controlled by the app developer. While not every use case requires media encryption, Agora provides the option to guarantee data confidentiality during transmission. 


 **7. Media push**

This release adds support for sending the audio and video of your channel to other RTMP servers through the CDN:<li>Starts or stops publishing at any time.<li>Adds or removes an address while continuously publishing the stream. <li>Adjusts the picture-in-picture layout. | <li>To send a live stream to WeChat or Weibo.<li>To allow more people to watch the live stream when the number of audience members in the channel reached the limit.

**8. Brand-new AI noise reduction**

The SDK supports a new version of AI noise reduction (in comparison to the basic AI noise reduction in v3.7.0). The new AI noise reduction has better vocal fidelity, cleaner noise suppression, and adds a dereverberation option. 
<div class="alert info">To experience this feature, contact <a href="mailto:sales-us@agora.io ">sales@agora.io</a>.</div>


**9. Real-time chorus**

This release gives real-time chorus the following abilities:

- Two or more choruses are supported.
- Each singer is independent of each other. If one singer fails or quits the chorus, the other singers can continue to sing.
- Very low latency experience. Each singer can hear each other in real time, and the audience can also hear each singer in real time.

This release adds the `AUDIO_SCENARIO_CHORUS` enumeration in `AUDIO_SCENARIO_TYPE`. With this enumeration, users can experience ultra-low latency in real-time chorus when the network conditions are good.

**10. Extensions from the Agora extensions marketplace**

In order to enhance the real-time audio and video interactive activities based on the Agora SDK, this release supports the one-stop solution for the extensions from the [Agora extensions marketplace](https://www.agora.io/en/marketplace):

- Easy to integrate: The integration of modular functions can be achieved simply by calling an API, and the integration efficiency is improved by nearly 95%.
- Extensibility design: The modular and extensible SDK design style endows the Agora SDK with good extensibility, which enables developers to quickly build real-time interactive apps based on the Agora extensions marketplace ecosystem.
- Build an ecosystem: A community of real-time audio and video apps has developed that can accommodate a wide range of developers, offering a variety of extension combinations. After integrating the extensions, developers can build richer real-time interactive functions.
- Become a vendor: Vendors can integrate their products with Agora SDK in the form of extensions, display and publish them in the Agora extensions marketplace, and build a real-time interactive ecosystem for developers together with Agora. For details on how to develop and publish extensions.

**11. Enhanced channel management**

To meet the channel management requirements of various business scenarios, this release adds the following functions to the `ChannelMediaOptions` structure:

- Sets or switches the publishing of multiple audio and video sources.
- Sets or switches channel profile and user role.
- Sets or switches the stream type of the subscribed video.
- Controls audio publishing delay.

Set `ChannelMediaOptions` when calling `joinChannel` or `joinChannelEx` to specify the publishing and subscription behavior of a media stream, for example, whether to publish video streams captured by cameras or screen sharing, and whether to subscribe to the audio and video streams of remote users. After joining the channel, call `updateChannelMediaOptions` to update the settings in `ChannelMediaOptions` at any time, for example, to switch the published audio and video sources.


**12. Subscription allowlists and blocklists**

This release introduces subscription allowlists and blocklists for remote audio and video streams. You can add a user ID that you want to subscribe to in your whitelist, or add a user ID for the streams you do not wish to see to your blacklists. You can experience this feature through the following APIs, and in scenarios that involve multiple channels, you can call the following methods in the `IRtcEngineEx` interface:

- `SetSubscribeAudioBlacklist`：Set the audio subscription blocklist.
- `SetSubscribeAudioWhitelist`：Set the audio subscription allowlist.
- `SetSubscribeVideoBlacklist`：Set the video subscription blocklist.
- `SetSubscribeVideoWhitelist`：Set the video subscription allowlist.

If a user is added in a blacklist and a whitelist at the same time, only the blacklist takes effect.


**13. Replace video feeds with images**

This release supports replacing video feeds with images when publishing video streams. You can call the enableVideoImageSource method to enable this function and choose your own images through the options parameter. If you disable this function, the remote users see the video feeds that you publish.

**14. Local video mixing**

This release adds a series of APIs supporting local video mixing functions. You can mix multiple video streams into one video stream locally. Common scenarios are as follows:

- In interactive live streaming scenarios with cohosts or when using the Media Push function, you can merge the screens of multiple hosts into one view locally.
- In scenarios where you capture multiple local video streams (for example, video captured by cameras, screen sharing streams, video files or pictures), you can and merge them into one video stream and then publish the mixed video stream in the channel.

You can call the `startLocalVideoTranscoder` method to start local video mixing and call the `stopLocalVideoTranscoder` method to stop local video mixing. After the local video mixing starts, you can call `updateLocalTranscoderConfiguration` to update the local video mixing configuration.

**15. Video device management**

Video capture devices can support multiple video formats, each supporting a different combination of video frame width, video frame height, and frame rate.

This release adds the `numberOfCapabilities` and `getCapability` methods for getting the number of video formats supported by the video capture device and the details of the video frames in the specified video format. When calling the `startPrimaryCameraCapture` or `startSecondaryCameraCapture` method to capture video using the camera, you can use the specified video format.

<div class="alert info">The SDK automatically selects the best video format for the video capture device based on your settings in <code>VideoEncoderConfiguration</code>, so normally you should not need to use these new methods. </div>

**16. In-ear monitoring**


This release adds support for in-ear monitoring. You can call `enableInEarMonitoring` to enable the in-ear monitoring function.

After successfully enabling the in-ear monitoring function, you can call `registerAudioFrameObserver` to register the audio observer, and the SDK triggers the `onEarMonitoringAudioFrame` callback to report the audio frame data. You can use your own audio effect processing module to pre-process the audio frame data of the in-ear monitoring to implement custom audio effects. Agora recommends that you choose one of the following two methods to set the audio data format of the in-ear monitoring:

- Call the `setEarMonitoringAudioFrameParameters` method to set the audio data format of in-ear monitoring. The SDK calculates the sampling interval based on the parameters in this method, and triggers the `onEarMonitoringAudioFrame` callback based on the sampling interval.
- Set the audio data format in the return value of the `getEarMonitoringAudioParams` callback. The SDK calculates the sampling interval based on the return value of the callback, and triggers the onEarMonitoringAudioFrame callback based on the sampling interval.

<div class="alert info"> To adjust the in-ear monitoring volume, you can call <code>setInEarMonitoringVolume</code>.</div>


**17. Audio stream filter**

This release introduces filtering audio streams based on volume. Once this function is enabled, the Agora server ranks all audio streams by volume and transports 3 audio streams with the highest volumes to the receivers by default. The number of audio streams to be transported can be adjusted; you can contact support@agora.io to adjust this number according to your scenarios.

Meanwhile, Agora supports publishers to choose whether or not the audio streams being published are to be filtered based on volume. Streams that are not filtered will bypass this filter mechanism and transported directly to the receivers. In scenarios where there are a number of publishers, enabling this function helps reducing the bandwidth and device system pressure for the receivers.


<div class="alert info">To enable this function, contact <a href="mailto:support@agora.io">technical support</a>.</div>

**18. MPUDP (MultiPath UDP) (Beta)**

As of this release, the SDK supports MPUDP protocol, which enables you to connect and use multiple paths to maximize the use of channel resources based on the UDP protocol. You can use different physical NICs on both mobile and desktop and aggregate them to effectively combat network jitter and improve transmission quality.
	
<div class="alert info">To enable this function, contact <a href="mailto:sales-us@agora.io">sales@agora.io</a>.</div>

## v4.0.0

v4.0.0 was released on September 15, 2022.

#### New features


**2. Ultra HD resolution (Beta) (Windows, macOS)**

In order to improve the interactive video experience, the SDK optimizes the whole process of video capture, encoding, decoding and rendering, and now supports 4K resolution. The improved FEC (Forward Error Correction) algorithm enables adaptive switches according to the frame rate and number of video frame packets, which further reduces the video stuttering rate in 4K scenes.

Additionally, you can set the encoding resolution to 4K (3840 × 2160) and the frame rate to 60 fps when calling `SetVideoEncoderConfiguration`. The SDK supports automatic fallback to the appropriate resolution and frame rate if your device does not support 4K.

<div class="alert info"><li>This feature has certain requirements with regards to device performance and network bandwidth, and the supported upstream and downstream frame rates vary on different platforms. To experience this feature, contact [support@agora.io](mailto:support@agora.io).
<li>The increase in the default resolution affects the aggregate resolution and thus the billing rate. See <a href="./billing_rtc_ng">Pricing</a>.</div>


**3. Full HD and Ultra HD resolution (Android, iOS)**

In order to improve the interactive video experience, the SDK optimizes the whole process of video capturing, encoding, decoding and rendering. Starting from this version, it supports Full HD (FHD) and Ultra HD (UHD) video resolutions. You can set the `dimensions` parameter to 1920 × 1080 or higher resolution when calling the `setVideoEncoderConfiguration` method. If your device does not support high resolutions, the SDK will automatically fall back to an appropriate resolution.

<div class="alert info"><li>The UHD resolution (4K, 60 fps) is currently in beta and requires certain device performance and network bandwidth. If you want to experience this feature, contact <a href="mailto:support@agora.io">technical support</a>.
<li>High resolution typically means higher performance consumption. To avoid a decrease in experience due to insufficient device performance, Agora recommends that you enable FHD and UHD video resolutions on devices with better performance.
<li>The increase in the default resolution affects the aggregate resolution and thus the billing rate. See <a href="./billing_rtc_ng">Pricing</a>.</div>