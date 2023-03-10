Security compliance is crucial for instant messaging technology. To guarantee safe and reliable cloud services, Agora follows the compliance regulations of different countries, regions, and industries, and has achieved ISO/IEC 27001 certification.

To this end, Chat has built-in security measures to prevent common attacks in instant messaging scenarios. This page describes the security best practices for Chat recommended by Agora, as summarized in the following table:

| Protection measure   | Enabled by default | Recommended scenarios               |
| :------------------- | :------- | :--------------------- |
| Channel separation | Yes  | All instant messaging scenarios.                                  |
| Token authentication | Yes  | All instant messaging applications. |
| Encryption | Yes  | All instant messaging scenarios.                                  |
| Network geofencing | Yes  | All instant messaging scenarios.                                 |

## Level 1 - Channel separation
The channels architect is the first built-in layer of protection. Chat's communication models contain several types of chat including one-to-one chat, group chat, and chat room. Chat creates an independent and isolated channel for data transmission for each chat of every type. All channels are logically separated, and only authenticated users from the same App ID can join the same channel.

## Level 2 - Token authentication
The second layer of protection is identity authentication achieved by a dynamic token authentication system. A token is a short-lived access key, which is generated by the app's backend server and allows a user to access the Chat service after they are properly validated by the app.

Chat provides tokens with different privileges:
- Token with user privileges: Used to allow users to access the Chat service.
- Token with app privileges: Used to allow app developers to perform administrator-level operations on the app with RESTful APIs.

To generate a token with user privileges, you need to provide unique information, including the App ID, App Certificate, `uuid`, and token expiration time.
![](https://web-cdn.agora.io/docs-files/1665213460331)

Developers can enable the token authentication system (achieved by App Certificate) on Agora Console. Once enabled, valid tokens are required for all users who want to log in to Chat.
![](https://web-cdn.agora.io/docs-files/1665213588546)

Please note that developers need to set a token expiration time (the default value is 24 hours) to ensure security. A token contains the timestamp of the expiration time prior to which a user is allowed users are allowed to access the Chat service.                                               

## Level 3 - Encryption
The third layer is encryption. Chat supports encryption during data transmission and data storage.

The communication between users and the Chat server is encrypted using transmission protocols, such as Chat's private transmission protocol, Transport Layer Security (TLS), and Web Socket Secure (WSS). User data and messages generated by Chat are encrypted using AES256 and then stored in the designated data center. Chat servers retain user information only for as long as the information is needed to fulfill the purposes for which it was collected, as shown in the following table:

| Data Type                  | Data Classification | Retention Time                                                              |
|:-----------------------------------------|:---------|:--------------------------------------------------------------------------------------|
| Console account data | Customer data | Until the account is deleted or is not used for 180 consecutive days. |
| Messages (Message history, roaming messages, offline messages, and so on) | User data | Depends on the cloud storage time associated with your pricing plan:<ul><li>Free: 3 days</li><li>Starter: 7 days</li><li>Pro: 90 days</li><li>Enterprise: 180 days</li></ul> |
| Message attachments          | User data | 7 days                                                                           |
| Message callbacks               | User data | 3 days                                                                            |
| User information hosting    | User data | Until the account is deleted or is not used for 180 consecutive days. |
| Monitoring data                 | Operational data | 7 days                                                                                |


## Data center geofencing

In order to meet the laws and regulations of different countries and regions, Chat supports service area geofencing, which prevents the cross-border data transfer of user privacy data in designated service areas.

Chat's data center locations and corresponding service areas are listed as follows:

| Data Center    | Location           | Service Area   |
| :------------- | :----------------- | :------------- |
| Singapore      | Singapore          | Southeast Asia |
| Mainland China | Beijing            | Mainland China |
| Europe         | Frankfurt, Germany | Europe         |
| North America  | Virginia, US       | North America  |

To use Chat, you need to specify a data center. After you select a data center, both the RESTful requests and the SDK API requests to the message server are directed to the data center accordingly.
Once selected, the data center cannot be changed. Chat does not support data migration across service areas. All data is stored in the designated data centers.

## Security best practice checklist
Use this list to quickly check what protection measures you need to take to ensure the security of your app and users:

1. Enable token authentication on [Agora Console](https://console.agora.io/).
2. Disable **No certificate** on your project management page so that your app authenticates users with tokens only.
3. [Deploy a token server](https://docs.agora.io/en/agora-chat/develop/generate-user-tokens#deploy-a-token-server) in your backend services.
4. Protect the token server by only allowing the app's backend server to connect to the token server.
5. Set the token expiration time to a reasonable value. 
6. To strengthen security, do not use real user IDs in your app as `username` in Chat. Instead, use randomly generated IDs as `username`.


