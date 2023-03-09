## v4.1.0

v4.1.0 was released on November xx, 2022.

#### New features


**1. Headphone equalization effect**

This release adds the `setHeadphoneEQParameters` method, which is used to adjust the low- and high-frequency parameters of the headphone EQ. This is mainly useful in spatial audio scenarios. If you cannot achieve the expected headphone EQ effect after calling `setHeadphoneEQPreset`, you can call `setHeadphoneEQParameters` to adjust the EQ.

**2. MPUDP (MultiPath UDP) (Beta)**

As of this release, the SDK supports MPUDP protocol, which enables you to connect and use multiple paths to maximize the use of channel resources based on the UDP protocol. You can use different physical NICs on both mobile and desktop and aggregate them to effectively combat network jitter and improve transmission quality.

<div class="alert info">To enable this feature, contact <a href="sales-us@agora.io">sales-us@agora.io</a>.</div>

**3. Register extensions**

This release adds the `registerExtension` method for registering extensions. When using a third-party extension, you need to call the extension-related APIs in the following order:

`loadExtensionProvider` -> `registerExtension` -> `setExtensionProviderProperty` -> `enableExtension`

**4. Device management**

This release adds a series of callbacks to help you better understand the status of your audio devices:

- `onAudioDeviceStateChanged`: Occurs when the status of the audio device changes.
- `onAudioDeviceVolumeChanged`: Occurs when the volume of an audio device or app changes.

**5. Multi-channel management**

This release adds a series of multi-channel related methods that you can call to manage audio streams in multi-channel scenarios.

- The `muteLocalAudioStreamEx` method is used to cancel or resume publishing a local audio streams.
- The `muteAllRemoteAudioStreamsEx` is used to cancel or resume the subscription of all remote users to audio streams.
- The `startRtmpStreamWithoutTranscodingEx`, `startRtmpStreamWithTranscodingEx`, `updateRtmpTranscodingEx`, and `stopRtmpStreamEx` methods are used to implement Media Push in multi-channel scenarios.
- The `startChannelMediaRelayEx`, `updateChannelMediaRelayEx`, `pauseAllChannelMediaRelayEx`, `resumeAllChannelMediaRelayEx`, and `stopChannelMediaRelayEx` methods are used to relay media streams across channels in multi-channel scenarios.
- Adds the `leaveChannelEx` [2/2] method. Compared with the `leaveChannelEx` [1/2] method, a new `options` parameter is added, which is used to choose whether to stop recording with the microphone when leaving a channel in a multi-channel scenario.


**6. Client role switching**

In order to enable users to know whether the switched user role is low-latency or ultra-low-latency, this release adds the `newRoleOptions` parameter to the `onClientRoleChanged` callback. The value of this parameter is as follows:

- `AUDIENCE_LATENCY_LEVEL_LOW_LATENCY` (1): Low latency.
- `AUDIENCE_LATENCY_LEVEL_ULTRA_LOW_LATENCY` (2): Ultra-low latency.

#### Improvements


**1. Relaying media streams across channels**

This release optimizes the `updateChannelMediaRelay` method as follows:

- Before v4.1.0: If the target channel update fails due to internal reasons in the server, the SDK returns the error code `RELAY_EVENT_PACKET_UPDATE_DEST_CHANNEL_REFUSED`(8), and you need to call the `updateChannelMediaRelay` method again.
- v4.1.0 and later: If the target channel update fails due to internal server reasons, the SDK retries the update until the target channel update is successful.

**2. Reconstructed AIAEC algorithm**

This release reconstructs the AEC algorithm based on the AI method. Compared with the traditional AEC algorithm, the new algorithm can preserve the complete, clear, and smooth near-end vocals under poor echo-to-signal conditions, significantly improving the system's echo cancellation and dual-talk performance. This gives users a more comfortable call and live-broadcast experience. AIAEC is suitable for conference calls, chats, karaoke, and other scenarios.


#### Other improvements

This release includes the following additional improvements:

- Reduces the latency when pushing external audio sources.
- Improves the performance of echo cancellation when using the `AUDIO_SCENARIO_MEETING` scenario.
- Enhances the ability to identify different network protocol stacks and improves the SDK's access capabilities in multiple-operator network scenarios.


<a name="bugfix"></a>

#### Issues fixed

This release fixed the following issues:

- The uplink network quality reported by the `onNetworkQuality` callback was inaccurate for the user who was sharing a screen.
- The call `getExtensionProperty` failed and returned an empty string.


#### API changes

**Added**

- `setHeadphoneEQParameters`

- `leaveChannelEx` [2/2]

- `muteLocalAudioStreamEx`

- `muteAllRemoteAudioStreamsEx`

- `startRtmpStreamWithoutTranscodingEx`

- `startRtmpStreamWithTranscodingEx`

- `updateRtmpTranscodingEx`

- `stopRtmpStreamEx`

- `startChannelMediaRelayEx`

- `updateChannelMediaRelayEx`

- `pauseAllChannelMediaRelayEx`

- `resumeAllChannelMediaRelayEx`

- `stopChannelMediaRelayEx`

- `newRoleOptions` in `onClientRoleChanged`

- `adjustUserPlaybackSignalVolumeEx`

- `onAudioDeviceStateChanged`

- `onAudioDeviceVolumeChanged`

**Deprecated**

- `onApiCallExecuted`. Use the callbacks triggered by specific methods instead.


**Deleted**

- Removes `RELAY_EVENT_PACKET_UPDATE_DEST_CHANNEL_REFUSED`(8) in `onChannelMediaRelayEvent callback`