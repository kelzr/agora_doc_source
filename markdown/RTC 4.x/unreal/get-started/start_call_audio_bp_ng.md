$$ 3265bf10-5be5-11ec-af4b-2b38abdb1c68
{
  "feature": "语音通话",
  "product": "Agora 音频 SDK"
}
$$


## 技术原理

~4c205950-acd6-11ec-83d9-39fb20031be5~


## 前提条件

- [Unreal Engine](https://www.unrealengine.com/zh-CN/download) 4.27 及以上版本
- 两台设备，不可使用虚拟机。参考下方列出的 Unreal Engine 官方文档，根据你的目标平台和引擎版本准备开发环境：
  | 开发平台 | 参考文档  | 备注    |
  | :--- | :------ | :------------------------------ |
  | Android  | [Android 开发环境要求](https://docs.unrealengine.com/4.27/zh-CN/SharingAndReleasing/Mobile/Android/AndroidSDKRequirements/) | 无    |
  | iOS | [iOS 开发环境要求](https://docs.unrealengine.com/4.27/zh-CN/SharingAndReleasing/Mobile/iOS/SDKRequirements/) | 有效的 Apple 开发者签名。  |
  | macOS | [macOS 开发环境要求](https://docs.unrealengine.com/4.27/zh-CN/Basics/InstallingUnrealEngine/RecommendedSpecifications/) | 有效的 Apple 开发者签名。  |
  | Windows | [Windows 开发环境要求](https://docs.unrealengine.com/4.27/zh-CN/Basics/InstallingUnrealEngine/RecommendedSpecifications/) | 32 位 Windows 仅支持 Unreal Engine 4 及以下版本，你需要在 `AgoraPluginLibrary.Build.cs` 文件中将 Windows 32 相关的代码取消注释。 |
- 计算机可以访问互联网。请确保你的网络环境正确部署防火墙，能正常支持声网服务所需的权限和行为。
- 一个有效的[声网账号](https://docs.agora.io/cn/Agora%20Platform/sign_in_and_sign_up)以及声网项目。请参考[开始使用声网平台](https://docs.agora.io/cn/Agora%20Platform/get_appid_token?platform=All%20Platforms)从声网控制台获得以下信息：
  - App ID：声网随机生成的字符串，用于识别你的项目。
  - 临时 token：token 也称为动态密钥，在客户端加入频道时对用户鉴权。临时 token 的有效期为 24 小时。
  - 频道名：用于标识频道的字符串。


## 创建项目

参考以下步骤或 [Unreal 官方操作指南](https://docs.unrealengine.com/4.27/zh-CN/Basics/Projects/Browser/)创建一个 **Unreal** 项目。如果已有 **Unreal** 项目，可进行下一步[集成 SDK](#集成-sdk)。
1. 打开 **Unreal Engine**，在 **New Project Categories** 下选择 **Games**，点击 **Next**。
2. 在 **SelectTemplate** 页面，选择 **Blank**，点击 **Next**。
3. 在 **Project Settings** 页面，进行如下设置：
    1. 选择 **Blueprint** 或 **C++**。
    2. 根据你开发的目标平台选择 **Desktop/Console** 或 **Mobile/Tablet**。
    3. 选择项目存储路径，并为项目命名。
    4. 点击 **Create Project** 完成创建。
   ![](https://web-cdn.agora.io/docs-files/1689850102339)


## 集成 SDK

1. 前往 [SDK 下载页面](https://docs.agora.io/cn/All/downloads?platform=Blueprint)，下载最新版本的 Unreal 音频 SDK，并解压缩。
2. 在你的项目根目录文件夹下，创建名为 `Plugins` 的文件夹。
3. 将 Unreal SDK 文件夹中的 `AgoraPlugin` 拷贝到 `Plugins` 中。

![](https://web-cdn.agora.io/docs-files/1689830211462)


## 在客户端实现语音通话

本节介绍如何使用声网音频 SDK 在你的项目中实现语音通话功能。

### 创建 UMG

为直观地体验语音通话，需根据应用场景创建以下控件：
   - 加入频道按钮
   - 离开频道按钮

![](https://web-cdn.agora.io/docs-files/1689850125716)

参考以下步骤创建上图所示 UI 界面。如果你的项目中已有用户界面，可进行下一步[创建关卡](#创建关卡)。

1. 创建 **Widget Blueprint**。
    1. 在 **Content Browser** 的 **Content** 文件夹中，右击选择 **User Interface** > **Widget Blueprint** 创建 **Widget Blueprint**，并将其命名为 **BP_AudioWidget**。
    2. 双击打开 **BP_AudioWidget**。此时可以在 **Hierarchy** 面板中看到 **BP_AudioWidget** > **Canvas Panel**。
    3. 创建视图背景。在 **Palette** 面板中，选择 **Common** > **Image**，将其拖至 **Canvas Panel** 中，命名为 **background**。通过拖拽调整至画布大小，在 **Details** 面板中调整背景颜色。
2. 在 **Widget Blueprint** 中创建加入和离开频道的按钮。
    1. 创建加入频道按钮。在 **Palette** 面板中，选择 **Common** > **Button**，将其拖至 **Canvas Panel** 中，重命名为 **Btn_JoinChannel**，并调整其在画布中的位置和大小：
        - **Position X**：-400
        - **Position Y**：0
        - **Size X**：300
        - **Size Y** ：50
    2. 选择 **Common** > **Text**，将其拖至 **Btn_JoinChannel** 中，并在 **Details** 面板中将 **Text** 文本内容修改为 **JoinChannel**。
    3. 重复上述步骤创建离开频道按钮，将其命名为 **Btn_LeaveChannel**，并将 **Text** 文本内容修改为 **LeaveChannel**。调整按钮在画布中的位置和大小：
        - **Position X**：100
        - **Position Y**：0
        - **Size X**：300
        - **Size Y** ：50
3. 保存上述更改。

### 创建关卡

1. 在 **Content Browser** 的 **Content** 文件夹中，右击选择 **Level** 创建 **Level Blueprint**，并将其命名为 **BasicAudioCallScene**。
2. 双击 **BasicAudioCallScene**，点击编辑器上方的 **Blueprints** > **Open Level Blueprint** 打开关卡蓝图。

![](https://web-cdn.agora.io/docs-files/1689850139388)

### 实现基础流程

在 **My Blueprint** 面板中，双击 **Graphs** > **EventGraph** 打开事件图表，其中已有 **Event BeginPlay** (游戏开始) 和 **Event End Play** (游戏结束) 两个事件节点。接下来，创建语音通话相关的事件节点以及对应的函数和变量，并将它们连接起来以实现语音通话的逻辑和交互，如下图所示：

![](https://web-cdn.agora.io/docs-files/1689863064982)

其中主要节点如下：

   | 序号 | 节点  | 类型  | 描述     |
   | :--- | :------ | :------- | :-------- |
   | 1    | **Set Show Mouse Cursor**    | 原生<sup>*</sup>   | (可选) 设置是否显示鼠标光标，勾选代表显示。<div class="alert note"><ul><li>该节点仅适用于 Windows 和 macOS。</li><li>如果在创建时检索不到该节点，可以取消勾选 <b>Context Sensitive</b>。 <img src="https://web-cdn.agora.io/docs-files/1689863102745"/></li></ul></div> |
   | 2    | **Load Agora Config**   | 自定义<sup>**</sup> | 加载声网配置。用于后续在创建和加入频道时验证用户身份。       |
   | 3    | **Create BP Audio Widget Widget** | 原生     | 创建用户界面。步骤如下：<ol><li>创建 **Create Widget** 节点。</li><li>选择该节点的 **Class** 为 **BP_AudioWidget**，将该节点关联至已经创建好的用户界面。</li></ol> |
   | 4    | **Set Basic Audio Call Widget**   | 自定义   | 设置用户界面。步骤如下：<ol><li>创建 **BasicAudioCallWidget** 变量，选择变量的 **Variable Type** 为 **BP_AudioWidget**，即已经创建好的用户界面，用于在蓝图中存储对用户界面的引用。</li><li>拖拽创建好的变量到 **EventGraph** 后，会出现 **Set BasicAudioCallWidget** 和 **Get BasicAudioCallWidget** 两个选项，选择 **Set BasicAudioCallWidget** 创建节点，用于访问并设置用户界面。</li></ol> |
   | 5    | **Bind UIEvent**   | 自定义   | 绑定 UI 事件，用于处理点击 **JoinChannel** 和 **LeaveChannel** 按钮后的事件逻辑。 |
   | 6    | **Add to Viewport**   | 原生  | 将用户界面添加到视口。   |
   | 7    | **Check Permission**   | 自定义   | (可选) 检查是否已获取语音通话所需的系统权限，如访问麦克风等。<div class="alert note">如果你的目标平台是 Android，需要创建该节点用于检查系统权限。</div> |
   | 8    | **Init Rtc Engine**     | 自定义   | 创建并初始化 RTC 引擎。  |
   | 9    | **Un Init Rtc Engine**  | 自定义   | 离开频道并释放资源。     |

<sup>*</sup>：原生节点为蓝图内置的节点，无需额外配置即可直接添加调用。
<sup>**</sup>：自定义节点非蓝图内置，需要在创建自定义函数后才能添加对应节点。

### 加入频道相关变量

1. 创建 **Token**、**ChannelId** 和 **AppId** 三个变量，选择 **Variable Type** 为 **String**。
2. 在 **Load Agora Config** 函数中，添加 **Sequence** 节点，然后分别连接 **Set Token**、**Set Channel Id** 和 **Set App Id**，在其中填入从声网控制台获取的 Token、频道名和 App ID，用于后续创建和加入频道时验证用户身份。
![](https://web-cdn.agora.io/docs-files/1689863340147)

### 初始化 RTC 引擎

1. (可选) 如果你的目标平台是 Android，在初始化 RTC 引擎前，需要检查是否已获取 Android 系统权限。在 **CheckPermission** 函数中，参照下图创建节点，用于添加 Android 系统中访问麦克风等权限。
![](https://web-cdn.agora.io/docs-files/1689862339398)

2. 在 **InitRtcEngine** 函数中，参照下图创建并连接节点以初始化 RTC 引擎。
![](https://web-cdn.agora.io/docs-files/1689862357748)
主要步骤如下：
   1. 创建 `IRtcEngine` 和 `IRtcEngineEventHandler`。
      i. 创建 **RtcEngine** 和 **EventHandler** 变量，将 **Variable Type** 分别设置为 **Agora Rtc Engine** 和 **IRtc Engine Event Handler**，即声网 SDK 的基础功能类和事件回调类，用于在蓝图中存储对这两个接口类的引用。
      ii. 添加两个 **Construct Object From Class** 节点，将 **Class** 分别设置为 **Agora Rtc Engine** 和 **IRtc Engine Event Handler**，并分别连接 **Set Rtc Engine** 和 **Set Event Handler**。
   2. 绑定 `IRtcEngineEventHandler` 类相关的回调函数。
      i. 创建 **onJoinChannelSuccess**、**onLeaveChannel**、**onUserJoined** 和 **onUserOffline** 回调函数。参考下表配置回调的输入参数：
        | 回调函数  | 描述 | 输入参数 |
        | :------- | :--- | :----- |
        | `onJoinChannelSuccess` | 成功加入频道回调。 |  <ul><li>`channel`: (String) 频道名。</li><li>`uid`: (Integer64) 加入频道的本地用户的用户 ID。</li><li>`elapsed`: (Integer) 从本地调用 `joinChannel` 开始到发生此事件过去的时间（毫秒）。</li></ul>|
        | `onLeaveChannel` | 离开频道回调。 | `stats`: 通话的统计数据。详见 [RtcStats](https://docportal.shengwang.cn/cn/video-call-4.x/API%20Reference/unreal_ng/API/class_rtcstats.html)。|
        | `onUserJoined` | 远端用户加入当前频道回调。 | <ul><li>`uid`: (Integer64) 新加入频道的远端用户的用户 ID。</li><li>`elapsed`: (Integer) 从本地调用 `joinChannel` 开始到发生此事件过去的时间（毫秒）。</li></ul> |
        | `onUserOffline` | 远端用户离开当前频道回调。 | <ul><li>`uid`: (Integer64) 离线用户的用户 ID。</li><li>`reason`: 离线原因。详见 [USER_OFFLINE_REASON_TYPE](https://docportal.shengwang.cn/cn/video-call-4.x/API%20Reference/unreal_ng/API/enum_userofflinereasontype.html)。</li></ul> |
    
      ii. 创建 **Bind Event** 函数，在该函数中，添加 **Sequence** 节点，然后分别绑定 **onJoinChannelSuccess**、**onLeaveChannel**、**onUserJoined** 和 **onUserOffline** 回调事件。
      ![](https://web-cdn.agora.io/docs-files/1689862369064)

   3. 初始化 `IRtcEngine`。
      i. 调用 **initialize** 初始化 RTC 引擎。
      ii. 连接 **RtcEngineContext** 配置 `IRtcEngine` 实例，注意将 **Channel Profile** 选为 **CHANNEL_PROFILE_LIVE_BROADCASTING** (直播场景)。


### 绑定 UI 事件

<div class="alert info">你也可以在 UMG 中绑定 UI 事件，本文仅展示使用 <b>Bind UIEvent</b> 函数进行绑定。</div>

1. 创建并实现 **OnJoinChannelClicked** 事件回调。
![](https://web-cdn.agora.io/docs-files/1689861266481)
主要步骤如下：
   1. 调用 **Enable Audio** 启用音频模块。
   2. 调用 **Join Channel** 加入频道。
   3. 在 **Make ChannelMediaOptions** 中设置以下参数：
      - 设置 **Publish Microphone Track** 为 **AGORA TRUE VALUE**，发布麦克风采集的音频流。
      - 设置 **Auto Subscribe Audio** 为 **AGORA TRUE VALUE**，自动订阅所有音频流。
      - 勾选 **Client Role Type Set Value** 并设置 **Client Role Type** 为 **CLIENT_ROLE_BROADCASTER**，设置用户角色为主播。
      - 勾选 **Channel Profile Set Value** 并设置 **Channel Profile** 为 **CHANNEL_PROFILE_LIVE_BROADCASTING**，设置频道场景为直播场景。

2. 创建并实现 **OnLeaveChannelClicked** 事件回调。当事件触发时，调用 **Leave Channel** 离开频道。
![](https://web-cdn.agora.io/docs-files/1689861284734)

3. 在 **Bind UIEvent** 函数中，参考下图将 **OnJoinChannelClicked** 和 **OnLeaveChannelClicked** 回调函数分别绑定 **JoinChannel** 和 **LeaveChannel** 按钮。点击按钮时，会触发对应的事件回调。
![](https://web-cdn.agora.io/docs-files/1689861304018)


### 实现回调函数

将之前创建的 **onJoinChannelSuccess**、**onLeaveChannel**、**onUserJoined** 和 **onUserOffline** 回调函数按照以下实现步骤进行配置：
1. 本地用户成功加入频道后，触发 `onJoinChannelSuccess` 回调。
![](https://web-cdn.agora.io/docs-files/1689860677797)

2. 本地用户离开频道后，触发 `onLeaveChannel` 回调。
![](https://web-cdn.agora.io/docs-files/1689860690896)

3. 远端用户加入频道时，触发 `onUserJoined` 回调。
![](https://web-cdn.agora.io/docs-files/1689860704441)

4. 远端用户离开频道时，触发 `onUserOffline` 回调。
![](https://web-cdn.agora.io/docs-files/1689860714410)


### 离开频道并释放资源

参考下图实现 **UnInitRtcEngine** 函数。
![](https://web-cdn.agora.io/docs-files/1689860658599)
主要步骤如下：
   1. 离开频道。
   2. 取消注册 RTC 事件回调。
   3. 销毁 `IRtcEngine` 对象，释放声网 SDK 使用的所有资源。


## 测试你的项目

按照以下步骤来测试你的语音通话项目：
   1. 在 **Load Agora Config** 函数中，分别填入将你的项目的 App ID、频道名以及临时 Token。
   2. 在 **Unreal Editor** 中，点击 <img src="https://web-cdn.agora.io/docs-files/1690867946685" width="25"/> 来运行你的项目，然后点击 **JoinChannel** 加入语音通话。
   3. 邀请一位朋友通过另一台设备来使用相同的 App ID、频道名、Token 加入频道。你们可以听见对方。