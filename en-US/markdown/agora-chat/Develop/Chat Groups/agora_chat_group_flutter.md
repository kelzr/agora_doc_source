Chat groups enable real-time messaging among multiple users.

This page shows how to use the Agora Chat SDK for Flutter to create and manage a chat group in your app.


## Understand the tech

The Agora Chat SDK provides the `ChatGroup`, `ChatGroupManager`, and `ChatGroupEventHandler` classes for chat group management, which allows you to implement the following features:

- Create and destroy a chat group
- Join and leave a chat group
- Retrieve the chat group attributes
- Retrieve the chat group member list
- Retrieve the chat group list
- Block and unblock a chat group
- Listen for chat group events


## Prerequisites

Before proceeding, ensure that you meet the following requirements:

- You have initialized the Agora Chat SDK. For details, see [Get Started with Flutter](./agora_chat_get_started_flutter).
- You understand the call frequency limit of the Agora Chat APIs supported by different pricing plans as described in [Limitations](./agora_chat_limitation).
- You understand the number of chat groups and chat group members supported by different pricing plans as described in [Pricing Plan Details](./agora_chat_plan).


## Implementation

This section describes how to call the APIs provided by the Agora Chat SDK for Flutter to implement chat group features.

### Create a chat group

Set `ChatGroupStyle` and `inviteNeedConfirm` before creating a chat group.

1. Is the group public or private, and who can invite members (`ChatGroupStyle`):
- `PrivateOnlyOwnerInvite`: A private group. Only the chat group owner and admins can add users to the chat group.
- `PrivateMemberCanInvite`: A private group. All chat group members can add users to the chat group.
- `PublicJoinNeedApproval`: A public group. The chat group owner and admins can add users, and users can send join requests to the chat group.
- `PublicOpenJoin`: A public group. All users can join the chat group automatically without any need for approval from the chat group owner and admins.

2. Does a group invitation require consent from an invitee to add them to the group (`inviteNeedConfirm`):

- Yes (`ChatGroupOptions#inviteNeedConfirm` is set to `true`). After creating a group and sending group invitations, the subsequent logic varies based on whether an invitee automatically consents to the group invitation (`autoAcceptGroupInvitation`):
  - Yes (`autoAcceptGroupInvitation` is set to `true`). The invitee automatically joins the chat group and receives the `ChatGroupEventHandler#onAutoAcceptInvitationFromGroup` callback, the chat group owner receives the `ChatGroupEventHandler#onInvitationAcceptedFromGroup` and `ChatGroupEventHandler#onMemberJoinedFromGroup` callbacks, and the other chat group members receives the `ChatGroupEventHandler#onMemberJoinedFromGroup` callback.
  - No (`autoAcceptGroupInvitation` is set to `false`). The invitee receives the `ChatGroupEventHandler#onInvitationReceivedFromGroup` callback and chooses whether to join the chat group:
    - If the invitee accepts the group invitation, the chat group owner receives the `ChatGroupEventHandler#onInvitationAcceptedFromGroup` and `ChatGroupEventHandler#onMemberJoinedFromGroup` callbacks, and the other chat group members receive the `ChatGroupEventHandler#onMemberJoinedFromGroup` callback;
    - If the invitee declines the group invitation, the chat group owner receives the `ChatGroupEventHandler#onInvitationDeclinedFromGroup` callback.

![](https://web-cdn.agora.io/docs-files/1653385689954)

- No (`ChatGroupOptions#inviteNeedConfirm` is set to `false`). After creating a chat group and sending group invitations, an invitee is added to the chat group regardless of their `autoAcceptGroupInvitation` setting. The invitee receives the `ChatGroupEventHandler#onAutoAcceptInvitationFromGroup` callback, the chat group owner receives the `ChatGroupEventHandler#onInvitationAcceptedFromGroup` and `ChatGroupEventHandler#onMemberJoinedFromGroup` callbacks, and the other chat group members receive the `ChatGroupEventHandler#onMemberJoinedFromGroup` callback.

Users can call `createGroup` to create a chat group and set the chat group attributes such as the chat group name, description, maximum number of members, and reason for creating the group, by specifying `ChatGroupOptions`.

The following code sample shows how to create a chat group:

```dart
ChatGroupOptions groupOptions = ChatGroupOptions(
  // The permission style of the chat group.
  style: ChatGroupStyle.PrivateMemberCanInvite,
  inviteNeedConfirm: true,
);
// The name of the chat group can be a maximum of 128 characters.
String groupName = "newGroup";
// The description of the chat group can be a maximum of 512 characters.
String groupDesc = "group desc";
try {
  await ChatClient.getInstance.groupManager.createGroup(
    groupName: groupName,
    desc: groupDesc,
    options: groupOptions,
  );
} on ChatError catch (e) {
}
```

### Destroy a chat group

Only the chat group owner can call `destroyGroup` to disband a chat group. Once a chat group is disbanded, all chat group members receive the `onGroupDestroyed` callback and are immediately removed from the chat group.

<div class="alert note"">Once a chat group is destroyed, all chat group data is deleted from the local database and memory.</div>

The following code sample shows how to destroy a chat group:

```dart
try {
  await ChatClient.getInstance.groupManager.destroyGroup(groupId);
} on ChatError catch (e) {
}
```

### Join a chat group

The logic of joining a chat group varies according to the `GroupStyle` setting you choose when [creating the chat group](#create-a-chat-group):

- If the `GroupStyle` is set to `PublicOpenJoin`, all users can join the chat group without the consent from the chat group owner and admins. Once a user joins a chat group, all chat group members receive the `ChatGroupEventHandler#onMemberJoinedFromGroup` callback;
- If the `GroupStyle` is set to `PublicJoinNeedApproval`, users can send join requests to the chat group. The chat group owner and chat group admins receive the `ChatGroupEventHandler#onRequestToJoinReceivedFromGroup` callback and choose whether to accept the join request:
  - If the request is accepted, the user joins the chat group and receives the `ChatGroupEventHandler#onRequestToJoinAcceptedFromGroup` callback, while all the other chat group members receive the `ChatGroupEventHandler#onMemberJoinedFromGroup` callback.
  - If the request is declined, the user receives the `ChatGroupEventHandler#onRequestToJoinDeclinedFromGroup` callback.

<div class="alert info">Users can only request to join public groups; private groups do not allow join requests.</div>

Users can refer to the following steps to join a chat group:

1. Call `fetchPublicGroupsFromServer` to retrieve the list of public groups from the server, and locate the ID of the chat group that you want to join.

2. Call `joinPublicGroup` to pass in the chat group ID and request to join the specified chat group.

The following code sample shows how to join a chat group:

```dart
// Retrieve the list of public groups with pagination.
try {
  ChatCursorResult<ChatGroupInfo> result =
      await ChatClient.getInstance.groupManager.fetchPublicGroupsFromServer();
} on ChatError catch (e) {
}
// Request to join the specified chat group.
try {
  await ChatClient.getInstance.groupManager.joinPublicGroup(groupId);
} on ChatError catch (e) {
}
```

### Leave a chat group

Chat group members can call `leave` to leave the specified chat group, whereas the chat group owner cannot perform this operation. Once a member leaves a chat group, all the other chat group members receive the `ChatGroupEventHandler#onMemberExitedFromGroup` callback.

The following code sample shows how to leave a chat group:

```dart
try {
  await ChatClient.getInstance.groupManager.leaveGroup(groupId);
} on ChatError catch (e) {
}
```

### Retrieve the attributes of a chat group

All chat group members can call `getGroupWithId` to retrieve the chat group attributes from memory. The attributes contain the chat group ID, name, description, owner, announcements, number of members, admin list, and whether to mute all members.

All chat group members can also call `fetchGroupInfoFromServer` to retrieve the chat group attributes from the server. The attributes contain the chat group ID, name, description, owner, announcements, number of members, admin list, and whether to mute all members.

The following code sample shows how to retrieve the chat group attributes:

```dart
// Retrieve the chat group attributes from memory.
try {
  ChatGroup? group = await ChatClient.getInstance.groupManager.getGroupWithId(groupId);
} on ChatError catch (e) {
}
// Retrieve the chat group attributes from the server.
try {
  ChatGroup group = await ChatClient.getInstance.groupManager.fetchGroupInfoFromServer(groupId);
} on ChatError catch (e) {
}
```

### Retrieve the chat group member list

All chat group members can call `fetchMemberListFromServer` to retrieve the chat group member list from the server with pagination.

The following code sample shows how to retrieve the chat group member list:

```dart
// The ID of the chat group.
try {
  ChatCursorResult<String> result =
      await ChatClient.getInstance.groupManager.fetchMemberListFromServer(
    groupId,
  );
} on ChatError catch (e) {
}
```

### Retrieve the chat group list

Users can call `fetchJoinedGroupsFromServer` to retrieve the joined chat group list from the server with pagination, as shown in the following code sample:

```dart
try {
  List<ChatGroup> list =
      await ChatClient.getInstance.groupManager.fetchJoinedGroupsFromServer();
} on ChatError catch (e) {
}
```

Users can call `getJoinedGroups` to retrieve the joined chat group list from the local database. To ensure the accuracy of results, retrieve the joined chat group list from the server first. The code sample is as follows:

```dart
try {
  List<ChatGroup> list =
      await ChatClient.getInstance.groupManager.getJoinedGroups();
} on ChatError catch (e) {
}
```

Users can also call `fetchPublicGroupsFromServer` to retrieve public chat group list from the server with pagination, as shown in the following code sample:

```dart
try {
  ChatCursorResult<ChatGroupInfo> result =
      await ChatClient.getInstance.groupManager.fetchPublicGroupsFromServer(
    // The maximum number of chat groups to retrieve with pagination.
    pageSize: pageSize,
    // The page number from which to start getting data.
    cursor: cursor,
  );
} on ChatError catch (e) {
}
```

### Block and unblock a chat group

#### Block a chat group

All chat group members can call `blockGroup` to block a chat group. Once a member blocks a chat group, this member can no longer receive messages from the chat group.

The following code sample shows how to block a chat group:

```dart
try {
  await ChatClient.getInstance.groupManager.blockGroup(groupId);
} on ChatError catch (e) {
}
```

#### Unblock a chat group

All chat group members can call `unblockGroup` to unblock a chat group.

The following code sample shows how to unblock a chat group:

```dart
try {
  await ChatClient.getInstance.groupManager.unblockGroup(groupId);
} on ChatError catch (e) {
}
```

#### Check whether a user blocks a chat group

All chat group members can call `fetchGroupInfoFromServer` to check whether they block a chat group according to the `ChatGroup#messageBlocked` field.

The following code sample shows how to check whether a user blocks a chat group:

```dart
try {
  ChatGroup group = await ChatClient.getInstance.groupManager
      .fetchGroupInfoFromServer(groupId);
  // Check whether a user blocks a chat group.
  if (group.messageBlocked == true) {
  }
} on ChatError catch (e) {
}
```

### Listen for chat group events

To monitor the chat group events, users can listen for the callbacks in the `ChatGroupEventHandler` class and add app logics accordingly. If a user wants to stop listening for the callbacks, make sure that the user removes the listener to prevent memory leakage.

Refer to the following code sample to listen for chat group events:

```dart
    // Add the group event handler.
    ChatClient.getInstance.groupManager.addEventHandler(
      "UNIQUE_HANDLER_ID",
      ChatGroupEventHandler(),
    );

    ...

    // Remove the group event handler.
    ChatClient.getInstance.groupManager.removeEventHandler("UNIQUE_HANDLER_ID");
```