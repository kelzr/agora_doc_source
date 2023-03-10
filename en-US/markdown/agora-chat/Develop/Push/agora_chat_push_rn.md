# Offline Push

Agora Chat supports the integration of Google Firebase Cloud Messaging (FCM). This enables React Native developers to use an offline push notification service that features low latency, high delivery, high concurrency, and no violation of the users' personal data.


## Understand the tech

![](https://web-cdn.agora.io/docs-files/1655713515869)

Assume that User A sends a message to User B, but User B goes offline before receiving it. The Agora Chat server pushes a notification to the device of User B via FCM and temporarily stores this message. Once User B comes back online, the Agora Chat SDK pulls the message from the server and pushes the message to User B using persistent connections.


## Prerequisites

Before proceeding, ensure that you meet the following requirements:
- You have initialized the Agora Chat SDK. For details, see [Get Started with React Native](./agora_chat_get_started_rn).
- You understand the call frequency limit of the Agora Chat APIs supported by different pricing plans as described in [Limitations](./agora_chat_limitation).

## Integrate FCM with Agora Chat

This section guides you through how to integrate FCM with Agora Chat.

### 1. Create a project in Firebase

1. Log in to [Firebase console](https://console.firebase.google.com/), and click **Add project**.

2. On the **Create a project** page, enter a project name, and proceed with prompts. The `Sender Id` and `Server Key` are required for Cloud Messaging.

After the project is created, add an `iOS` application or an `Android` application according to the following steps:

- Add an `iOS` app
   1. The package name is the same as that of the `iOS` app;
   2. Once created, move the downloaded `GoogleService-Info.plist` file to the root directory of the `Xcode` project, and add it to all targets;
   3. Upload the `APNs` certificate on the Cloud Messaging page.

- Add an `Android` app
   1. The package name is the same as that of the `Android` app;
   2. Once created, move the downloaded `google-services.json` file to your module (application level) root directory.


### 2. Upload FCM certificate to Agora Console

1. Log in to [Agora Console](https://console.agora.io/), and click **Project Management** in the left navigation bar.

2. On the **Project Management** page, locate the project that has Chat enabled and click **Config**.

3. On the project edit page, click **Config** next to **Chat**.

4. On the project config page, select **Features** > **Push Certificate** and click **Add Push Certificate**.

5. In the pop-up window, select the **Google** tab, and configure the following fields:
  - **Certificate Name**: Fill in the [Sender ID](#token).
  - **Push Key**: Fill in the [Server Key](#token).

<img src="https://web-cdn.agora.io/docs-files/1658462250450">


### 3. Enable FCM in Agora Chat

1. Initialize and enable FCM in the Agora Chat SDK.

```typescript
// For FCM, the settings are as follows:
// senderId: The FCM Sender ID.
// fcmToken: The FCM Device Token.
const pushConfig = new ChatPushConfig({
  deviceId: senderId,
  deviceToken: fcmToken,
});
let o = new ChatOptions({
  appKey: "<#Your AppKey#>",
  pushConfig: pushConfig,
  autoLogin: false,
});
ChatClient.getInstance()
  .init(o)
  .then(() => {
    console.log("init success");
  })
  .catch((error) => {
    console.log("init fail", error);
  });
```

2. Pass the device token of FCM to the Agora Chat SDK.

```typescript
import messaging from '@react-native-firebase/messaging';
messaging()
  .getToken()
  .then((deviceToken) => {
    console.log("get token success:", deviceToken);
    ChatClient.getInstance()
      .updatePushConfig(
        new ChatPushConfig({ deviceId: deviceId, deviceToken: deviceToken })
      )
      .then(() => {
        console.log("updatePush success");
      })
      .catch((reason) => {
        console.log(`updatePush fail: ${JSON.stringify(reason)}`);
      });
  })
  .catch((error) => {
    console.log("get token fail", error);
  });
```


## Set up push notifications

To optimize user experience when dealing with an influx of push notifications, Agora Chat provides fine-grained options for the push notification and do-not-disturb (DND) modes at both the app and conversation levels, as shown in the following table:

<table class="" cellspacing=0 border=1>
<tbody>
    <tr>
        <th style="font-family:Helvetica Neue">
            <nobr>Mode</nobr>
        </th>
        <th style="font-family:Helvetica Neue">
            <nobr>Option</nobr>
        </th>
        <th style="font-family:Helvetica Neue">
            <nobr>App</nobr>
        </th>
        <th style="font-family:Helvetica Neue">
            <nobr>Conversation</nobr>
        </th>
    </tr>
    <tr>
        <td rowspan=3>
            <nobr>Push notification mode</nobr>
        </td>
        <td>
            <nobr><code>ALL</code>: Receives push notifications for all offline messages.</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
    </tr>
    <tr>
        <td>
            <nobr><code>MENTION_ONLY</code>: Only receives push notifications for mentioned messages.</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
    </tr>
    <tr>
        <td>
            <nobr><code>NONE</code>: Do not receive push notifications for offline messages.</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
    </tr>
    <tr>
        <td rowspan=2>
            <nobr>Do-not-disturb mode</nobr>
        </td>
        <td>
            <nobr><code>SILENT_MODE_DURATION</code>: Do not receive push notifications for the specified duration.</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
    </tr>
    <tr>
        <td>
            <nobr><code>SILENT_MODE_INTERVAL</code>: Do not receive push notifications in the specified time frame.</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✓</nobr>
        </td>
        <td style="text-align:center">
            <nobr>✗</nobr>
        </td>
    </tr>
</tbody>
</table>


**Push notification mode**

The setting of the push notification mode at the conversation level takes precedence over that at the app level, and those conversations that do not have specific settings for the push notification mode inherit the app setting by default.

For example, assume that the push notification mode of the app is set to `MENTION_ONLY`, while that of the specified conversation is set to `ALL`. You receive all the push notifications from this conversation, while you only receive the push notifications for mentioned messages from all the other conversations.

**Do-not-disturb mode**

<div class="alert note"><ol><li>You can specify both the DND duration and DND time frame at the app level. During the specified DND time periods, you do not receive any push notifications.<li>Conversations only support the DND duration setting; the setting of the DND time frame does not take effect.</ol></div>

For both the app and all the conversations in the app, the setting of the DND mode takes precedence over the setting of the push notification mode.

For example, assume that a DND time period is specified at the app level and the push notification mode of the specified conversation is set to `ALL`. The DND mode takes effect regardless of the setting of the push notification mode, that is, you do not receive any push notifications during the specified DND time period.

Alternatively, assume that a DND time period is specified for a conversation, while the app does not have any DND settings and its push notification mode is set to `ALL`. You do not receive any push notifications from this conversation during the specified DND time period, while the push of all the other conversations remains the same.


### Set the push notifications of an app

You can call `setSilentModeForAll` to set the push notifications at the app level and set the push notification mode and DND mode by specifying the `ChatSilentModeParam` field, as shown in the following code sample:

```typescript
// Sets the push notification mode to `MENTION_ONLY`。
const option = ChatSilentModeParam.constructorWithNotification(
  ChatPushRemindType.MENTION_ONLY
);
// Sets the DND duration to 10 minutes.
const option = ChatSilentModeParam.constructorWithDuration(10);
// Sets the DND period from 10:10 to 11:00.
const option = ChatSilentModeParam.constructorWithPeriod({
  startTime: new ChatSilentModeTime({ hour: 10, minute: 10 }),
  endTime: new ChatSilentModeTime({ hour: 11, minute: 10 }),
});
// Sets offline app push.
ChatClient.getInstance()
  .pushManager.setSilentModeForAll(option)
  .then(() => {
    console.log("set notify success");
  })
  .catch((reason) => {
    console.log("set notify fail.", reason);
  });
```


### Retrieve the push notification setting of an app

You can call `fetchSilentModeForAll` to retrieve the push notification settings at the app level, as shown in the following code sample:

```typescript
ChatClient.getInstance()
  .pushManager.fetchSilentModeForAll()
  .then((result) => {
    console.log("get notify success:", result);
  })
  .catch((reason) => {
    console.log("get notify fail.", reason);
  });
```


### Set the push notifications of a conversation

You can call `setSilentModeForConversation` to set the push notifications for the conversation specified by the `conversationId` and `ConversationType` fields, as shown in the following code sample:

```typescript
// convId：The conversation ID.
// convType：The conversation type.
// Sets the push notification mode to `MENTION_ONLY`。
const option = ChatSilentModeParam.constructorWithNotification(
  ChatPushRemindType.MENTION_ONLY
);
// Sets the DND duration to 10 minutes.
const option = ChatSilentModeParam.constructorWithDuration(10);
// Sets the DND period from 10:10 to 11:00.
const option = ChatSilentModeParam.constructorWithPeriod({
  startTime: new ChatSilentModeTime({ hour: 10, minute: 10 }),
  endTime: new ChatSilentModeTime({ hour: 11, minute: 10 }),
});
// Sets push notifications for a specified session.
ChatClient.getInstance()
  .pushManager.setSilentModeForConversation({
    convId,
    convType,
    option,
  })
  .then(() => {
    console.log("set conversions notify success");
  })
  .catch((reason) => {
    console.log("set conversions notify fail.", reason);
  });
```

### Retrieve the push notification setting of a conversation

You can call `fetchSilentModeForConversation` to retrieve the push notification settings of the specified conversation, as shown in the following code sample:

```typescript
// convId：The conversation ID.
// convType：The conversation type.
ChatClient.getInstance()
  .pushManager.fetchSilentModeForConversation({
    convId,
    convType,
  })
  .then(() => {
    console.log("get conversions notify success");
  })
  .catch((reason) => {
    console.log("get conversions notify fail.", reason);
  });
```

### Retrieve the push notification settings of multiple conversations

<div class="alert info"><ol><li>You can retrieve the push notification settings of up to 20 conversations at each call.<li>If a conversation inherits the app setting or its push notification setting has expired, the returned dictionary does not include this conversation.</ol></div>

You can call `fetchSilentModeForConversations` to retrieve the push notification settings of multiple conversations, as shown in the following code sample:

```typescript
// conversations: The conversation list.
ChatClient.getInstance()
  .pushManager.fetchSilentModeForConversations(conversations)
  .then(() => {
    console.log("get conversions list success");
  })
  .catch((reason) => {
    console.log("get conversions list fail.", reason);
  });
```

### Clear the push notification mode of a conversation

You can call `removeSilentModeForConversation` to clear the push notification mode of the specified conversation. Once the specific setting of a conversation is cleared, this conversation inherits the app setting by default.

The following code sample shows how to clear the push notification mode of a conversation:

```typescript
// convId：The conversation ID.
// convType：The conversation type.
ChatClient.getInstance()
  .pushManager.removeSilentModeForConversation({
    convId,
    convType,
  })
  .then(() => {
    console.log("delete conversions success");
  })
  .catch((reason) => {
    console.log("delete conversions fail.", reason);
  });
```


## Set up push translations

If a user enables the [automatic translation](./agora_chat_translation_rn#automatic-translation) feature and sends a message, the SDK sends both the original message and the translated message.

Push notifications work in tandem with the translation feature. As a receiver, you can set the preferred language of push notifications that you are willing to receive when you are offline. If the language of the translated message meets your setting, the translated message displays in push notifications; otherwise, the original message displays instead.

The following code sample shows how to set and retrieve the preferred language of push notifications:

```typescript
// languageCode: See the Microsoft documentations for details: https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support.
ChatClient.getInstance()
  .pushManager.setPreferredNotificationLanguage(languageCode)
  .then(() => {
    console.log("set notify language success");
  })
  .catch((reason) => {
    console.log("set notify language fail.", reason);
  });
// Retrieves the preferred language of push notifications.
ChatClient.getInstance()
  .pushManager.fetchPreferredNotificationLanguage()
  .then(() => {
    console.log("get notify language success");
  })
  .catch((reason) => {
    console.log("get notify language fail.", reason);
  });
```


## Set up push templates

Agora Chat allows users to use ready-made templates for push notifications.

You can create and provide push templates for users by referring to the following steps:

1. Log in to [Agora Console](https://console.agora.io/), and click **Project Management** in the left navigation bar.

2. On the **Project Management** page, locate the project that has Chat enable and click **Config**.

3. On the project edit page, click **Config** next to **Chat**.

4. On the project config page, select **Features** > **Push Template** and click **Add Push Template**, and configure the fields in the pop-up window, as shown in the following figure:

![](https://web-cdn.agora.io/docs-files/1655445229699)

Once the template creation is complete in Agora Console, users can choose this push template as their default layout when sending a message, as shown in the following code sample:

```typescript
// content: The text type.
// targetId: The message recipient.
// chatType: The message recipient type, individual or group.
const msg = ChatMessage.createTextMessage(targetId, content, chatType);
// Sets the push template created in Agora Console as their default layout.
msg.attributes = {
  // Adds the push template to the message.
  "em_push_template": {
    // Sets the template name.
    "name": "test7",
    // Sets the template title by specifying the variable.
    "title_args": ["value1"],
    // Sets the template content by specifying the variable.
    "content_args": ["value1"],
  }
};
// Sends the message.
ChatClient.getInstance()
  .chatManager.sendMessage(msg！, new ChatMessageCallback())
  .then(() => {
    console.log("send message operation success.");
  })
  .catch((reason) => {
    console.log("send message operation fail.", reason);
  });
```