This page shows how to call Agora Chat RESTful APIs to send different types of messages, upload and download files, and retrieve historical messages.

Before calling the following methods, make sure you understand the call frequency limit of the Agora Chat RESTful APIs as described in [Limitations](./agora_chat_limitation?platform=RESTful#call-limit-of-server-side).

## <a name="param"></a>Common parameters

The following table lists common request and response parameters of the Agora Chat RESTful APIs:

### Request parameters

| Parameter | Type | Description | Required |
| :--------- | :----- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- |
| `host` | String | The domain name assigned by the Chat service to access RESTful APIs. For how to get the domain name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `org_name` | String | The unique identifier assigned to each company (organization) by the Chat service. For how to get org name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `app_name` | String | The unique identifier assigned to each app by the Chat service. For how to get the app name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `username` | String | The unique login account of the user. The user ID must be 64 characters or less and cannot be empty.  The following character sets are supported:<ul><li>26 lowercase English letters (a-z)</li><li>26 uppercase English letters (A-Z)</li><li>10 numbers (0-9)</li><li>"\_", "-", "."</li></ul><div class="alert note"><ul><li>The username is case insensitive, so `Aa` and `aa` are the same username.</li><li>Ensure that each `username` under the same app is unique.</li><li>Do not set this parameter as a UUID, email address, phone number, or other sensitive information.</li></ul></div> | Yes |

### Response parameters

| Parameter | Type | Description |
| :---------------- | :----- | :---------------------------------------------------------------- |
| `action` | String | The request method. |
| `organization` | String | The unique identifier assigned to each company (organization) by the Chat service. This is the same as `org_name`. |
| `application` | String | A unique internal ID assigned to each app by the Chat service. You can safely ignore this parameter. |
| `applicationName` | String | The unique identifier assigned to each app by the Chat service. This is the same as `app_name`. |
| `uri` | String | The request URL. |
| `path` | String | The request path, which is part of the request URL. You can safely ignore this parameter. |
| `entities ` | JSON | The response entity. |
| `timestamp` | Number | The Unix timestamp (ms) of the HTTP response. |
| `duration` | Number | The duration (ms) from when the HTTP request is sent to the time the response is received. |

## Authorization

Chat RESTful APIs require Bearer HTTP authentication. Every time an HTTP request is sent, the following `Authorization` field must be filled in the request header:

```http
Authorization: Bearer ${YourAppToken}
```

In order to improve the security of the project, Agora uses a token (dynamic key) to authenticate users before they log in to the chat system. Chat RESTful APIs only support authenticating users using app tokens. For details, see [Authentication using App Token](./generate_app_tokens?platform=RESTful).

<a name="sendmessage"></a>

## Send a message

This group of methods enable you to send and receive peer-to-peer and group messages. Message types include text, image, voice, video, command, extension, file, and custom messages.

For each App Key, the call frequency limit of this method is 100 per second.

Follow the instructions below to implement sending messages:

- For text, command, and custom messages: Call the send-message method, and pass in the message content in the request body.
- For image, voice, video, and file messages:
   1. Call the [upload-file](#upload) method to upload images, voice messages, videos, or other types of files, and get the file `uuid` from the response body.
   2. Call the send-message method, and pass the `uuid` in the request body.

<div class="alert note"><ul>
<li>When calling the RESTful API to send a message, you can use the <code>from</code> field to specify the message sender.</li>
<li>If the data length of the request body exceeds 5 KB, error code 413 will be returned. The maximum data length of the request body and extension fields is 3 KB.</li>
</ul></div>

### Send a one-to-one message

This method sends a message to a peer user.

#### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/messages/users
```

##### Path parameter

For the descriptions of the path parameter, see [Common Parameters](#param).

##### Request header

| Parameter | Type | Description | Required |
|:---------------| :------ | :------- |:------------------|
| `Content-Type` | String | The content type. Set it to `application/json`.  | Yes |
| `Accept`       | String  | The content type. Set it to `application/json`.  | Yes |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${token}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |


<a name="request"></a>

##### Request body

The request body is a JSON object, which contains the following parameters:

| Parameter | Type | Description | Required |
| --- | --- | --- | --- |
| `from` | String | The username of the message sender. If you do not set this field, the Chat server takes the `admin` as the sender. If you set it as the empty string "", this request fails.  | No |
| `to` | Array | An array of the usernames of the message recipient. For each request, you can send a message to a maximum of 600 users. Within one minute, you can send messages to a maximum of 6,000 users. | Yes |
| `type` | String | The message type: <ul><li>`txt`: Text message</li><li>`img`: Image message</li><li>`audio`: Audio message</li><li>`video`: Video message</li><li>`file`: File message</li><li>`loc`: Location message</li><li>`cmd`: Command message</li><li>`custom`: Custom message</li></ul> | Yes |
| `body` | JSON | The message content. For different message types, this parameter contains different fields. For details, see [Body of different message types](#body). | Yes |
| `sync_device` | Bool | Whether to synchronize the message to the message sender.<ul><li>`true`: Yes.</li><li>`false`: No.</li></ul> | No |
| `routetype` | String | The route type when the message is not online. <ul><li>To send the message only when the user is online, set this parameter as `ROUTE_TYPE`.</li><li>To send the message regardless of whether the user is online or not, do not set this parameter.</li></ul> | No |
| `ext`  | JSON  | The extension filed of the message. | No |

<a name="body"></a>

**Body of different message types**

- Text message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `msg` | String | The message content. | Yes |

- Image message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `filename` | String | The name of the image file. | Yes |
  | `secret` | String | The secret for accessing the image file. You can obtain the value of `secret` from the `share-secret` parameter in the response body of the [upload](#upload) method. If you set `resctrict-access` as `true` in the request header of `upload` when uploading the image file, ensure that you set this parameter. | No |
  | `size` | JSON | The size of the image (in pixels). This parameter contains two fields:<ul><li>height: The image height.</li><li>width: The image width.</li></ul> | Yes |
  | `url` | String | The URL address of the image file, in the format of `https://{host}/{org_name}/{app_name}/chatfiles/{file_uuid}`, in which `file_uuid` can be obtained from the response body of `upload` after you upload the file to the server. | Yes |

- Voice message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `filename` | String | The name of the audio file. | Yes |
  | `secret` | String | The secret for accessing the audio file. You can obtain the value of `secret` from the `share-secret` parameter in the response body of the [upload](#upload) method. If you set `resctrict-access` as `true` in the request header of `upload` when uploading the audio file, ensure that you set this parameter. | No |
  | `length` | Int | The length of the audio file (in seconds). | Yes |
  | `url` | String | The URL address of the audio file, in the format of `https://{host}/{org_name}/{app_name}/chatfiles/{file_uuid}`, in which `file_uuid` can be obtained from the response body of `upload` after you upload the file to the server. | Yes |

- Video message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `thumbnail` | String | The URL address of the video thumbnail, in the format of `https://{host}/{org_name}/{app_name}/chatfiles/{file_uuid}`, in which `file_uuid` can be obtained from the response body of `upload` after you upload the file to the server. | Yes |
  | `length` | Int | The length of the video file (in seconds). | Yes |
  | `secret` | String | The secret for accessing the video file. You can obtain the value of `secret` from the `share-secret` parameter in the response body of the [upload](#upload) method. If you set `resctrict-access` as `true` in the request header of `upload` when uploading the video file, ensure that you set this parameter. | No |
  | `file_length` | Long | The data length of the video file (in bytes). | Yes |
  | `thumb_secret` | String | The secret for accessing the video thumbnail. You can obtain the value of `thumb_secret` from the `share-secret` parameter in the response body of the [upload](#upload) method. If you set `restrict-access` as `true` in the request header of `upload` when uploading the thumbnail, ensure that you set this parameter. | No |
  | `url` | String | The URL address of the video file, in the format of `https://{host}/{org_name}/{app_name}/chatfiles/{file_uuid}`, in which `file_uuid` can be obtained from the response body of `upload` after you upload the file to the server. | Yes |

- File message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `filename` | String | The name of the file. | Yes |
  | `secret` | String | The secret for accessing the file. You can obtain the value of `secret` from the `share-secret` parameter in the response body of the [upload](#upload) method. If you set `restrict-access` as `true` in the request header of `upload` when uploading file, ensure that you set this parameter. | No |
  | `url` | String | The URL address of the file, in the format of `https://{host}/{org_name}/{app_name}/chatfiles/{file_uuid}`, in which `file_uuid` can be obtained from the response body of `upload` after you upload the file to the server. | Yes |

- Location message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `lat` | String | The latitude of the location (in degrees). | Yes |
  | `lng` | String | The longitude of the location (in degrees). | Yes |
  | `addr` | String | The address of the location. | Yes |

- CMD message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `action` | String | The content of the command. | Yes |

- Custom message

  | Parameter | Type | Description | Required |
  | --- | --- | --- | --- |
  | `customEvent` | String | The event type customized by the user. The value of this parameter should be a regular expression, for example, `[a-zA-Z0-9-_/\.]{1,32}`. | No |
  | `customExts` | JSON | The event attribute customized by the user. The data type is `Map<String,String>`. You can set a maximum of 16 elements. | No |
  | `from`       | String | The username of the message sender. If you do not set this field, the Chat server takes the `admin` as the sender. If you set it as the empty string "", this request fails. | No |
  | `ext` | JSON | The extension property, which supports customized settings of the app. Do not set it as `ext:null`. | No |

#### HTTP response

##### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following parameters:

| Parameter      | Type   | Description |
| :------- |:-------------|:-------------|
| `data` | JSON | The detailed content of the response. The value of this parameter includes a key-value pair where key represents the username of the message recipient and value the message ID. For example, if the returned data is `"user2":"1029457500870543736"`, it means that user2 has sent a message with the ID of 1029457500870543736.  | 

For the other parameters and descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible causes.

#### Example

##### Request example

- Send a text message to the specified user without synchronizing the message with the sender

    ```shell
    # Replace {YourToken} with the app token generated on your server         
    curl -X POST -i 'http://XXXX/XXXX/XXXX/messages/users' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["user2"],"type": "txt","body": {"msg": "testmessages"}}'
    ```

- Send a text message to the online user while synchronizing the message with the sender

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'http://XXXX/XXXX/XXXX/messages/users' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["user2"],"type": "txt","body": {"msg": "testmessages"},"routetype":"ROUTE_ONLINE", "sync_device":true}'
    ```

- Send an image message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/users' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["user2"],"type": "img","body": {"filename":"testimg.jpg","secret":"VfXXXXNb_","url":"https://XXXX/XXXX/XXXX/chatfiles/55f12940-XXXX-XXXX-8a5b-ff2336f03252","size":{"width":480,"height":720}}}'
    ```

- Send a voice message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/users' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["user2"],"type": "audio","body": {"url": "https://XXXX/XXXX/XXXX/chatfiles/1dfc7f50-XXXX-XXXX-8a07-7d75b8fb3d42","filename": "testaudio.amr","length": 10,"secret": "HfXXXXCjM"}}'
    ```

- Send a video message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/users' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d  '{"from": "user1","to": ["user2"],"type": "video","body": {"thumb" : "https://XXXX/XXXX/XXXX/chatfiles/67279b20-7f69-11e4-8eee-21d3334b3a97","length" : 0,"secret":"VfXXXXNb_","file_length" : 58103,"thumb_secret" : "ZyXXXX2I","url" : "https://XXXX/XXXX/XXXX/chatfiles/671dfe30-XXXX-XXXX-ba67-8fef0d502f46"}}'
    ```

- Send a file message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/users' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["user2"],"type": "file","body": {"filename":"test.txt","secret":"1-g0XXXXua","url":"https://XXXX/XXXX/XXXX/chatfiles/d7eXXXX7444"}}'
    ```

- Send a location message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i "https://XXXX/XXXX/XXXX/messages/users"  -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["user2"],"type": "loc","body":{"lat": "39.966","lng":"116.322","addr":"North America"}}'
    ```

- Send a CMD message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i "https://XXXX/XXXX/XXXX/messages/users" -H 'Content-Type: application/json' -H 'Accept: application/json'  -H "Authorization:Bearer {YourToken}" -d '{"from": "user1","to": ["user2"],"type": "cmd","body":{"action":"action1"}}'
    ```

##### Response example

- Send a text message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send an image message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a voice message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a video message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a file message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a location message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a CMD message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a custom message

    ```json
    {
        "path": "/messages/users",
        "uri": "https://XXXX/XXXX/XXXX/messages/users",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "user2": "1029457500870543736"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

### Send a group message

#### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/messages/chatgroups
```

##### Path parameter

For the descriptions of the path parameter, see [Common Parameters](#param).

##### Request header

| Parameter | Type | Description | Required |
|:---------------| :------ | :------- |:------------------|
| `Content-Type` | String | The content type. Set it to `application/json`.  | Yes |
| `Accept`       | String  | The content type. Set it to `application/json`.  | Yes |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${token}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

##### Request body

The request body is a JSON object, which contains the following parameters:

| Parameter | Type | Description | Required |
| --- | --- | --- | --- |
| `to` | Array | An array of the group IDs that receives the message. Within one second, you can send a maximum of 20 messages to a chat group, and for each request, you can send messages to a maximum of 3 chat groups. | Yes |

The other parameters and descriptions are the same with those of [Sending a one-to-one message method](#request).

#### HTTP response

##### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following parameters:

| Parameter      | Type   | Description |
| :------- |:-------------|:-------------|
| `data` | JSON | The detailed content of the response. The value of this parameter includes a key-value pair where key represents the group ID that receives the message and value the message ID. For example, if the returned data is `"184524748161025": "1029544257947437432"`, it means that a message with the ID of 1029544257947437432 is sent in chat group 184524748161025.  | 

For the other parameters and descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible causes.


#### Example

##### Request example

- Send a text message to the specified chat group without synchronizing the message with the sender

    ```shell
    # Replace {YourToken} with the app token generated on your server        
    curl -X POST -i 'http://XXXX/XXXX/XXXX/messages/chatgroups' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["184524748161025"],"type": "txt","body": {"msg": "testmessages"}}'
    ```

- Send a text message to online users in the specified chat group while synchronizing the message with the sender

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'http://XXXX/XXXX/XXXX/messages/chatgroups' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["184524748161025"],"type": "txt","body": {"msg": "testmessages"},"routetype":"ROUTE_ONLINE", "sync_device":true}'
    ```

- Send an image message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatgroups' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["184524748161025"],"type": "img","body": {"filename":"testimg.jpg","secret":"VfXXXXNb_","url":"https://XXXX/XXXX/XXXX/chatfiles/55f12940-XXXX-XXXX-8a5b-ff2336f03252","size":{"width":480,"height":720}}}'
    ```
    
- Send a voice message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatgroups' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["184524748161025"],"type": "audio","body": {"url": "https://XXXX/XXXX/XXXX/chatfiles/1dfc7f50-XXXX-XXXX-8a07-7d75b8fb3d42","filename": "testaudio.amr","length": 10,"secret": "HfXXXXCjM"}}'
    ```

- Send a video message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatgroups' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d  '{"from": "user1","to": ["184524748161025"],"type": "video","body": {"thumb" : "https://XXXX/XXXX/XXXX/chatfiles/67279b20-7f69-11e4-8eee-21d3334b3a97","length" : 0,"secret":"VfXXXXNb_","file_length" : 58103,"thumb_secret" : "ZyXXXX2I","url" : "https://XXXX/XXXX/XXXX/chatfiles/671dfe30-XXXX-XXXX-ba67-8fef0d502f46"}}'
    ```

- Send a file message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatgroups' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["184524748161025"],"type": "file","body": {"filename":"test.txt","secret":"1-g0XXXXua","url":"https://XXXX/XXXX/XXXX/chatfiles/d7eXXXX7444"}}'
    ```

- Send a location message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i "https://XXXX/XXXX/XXXX/messages/chatgroups"  -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["184524748161025"],"type": "loc","body":{"lat": "39.966","lng":"116.322","addr":"North America"}}'
    ```

- Send a CMD message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i "https://XXXX/XXXX/XXXX/messages/chatgroups" -H 'Content-Type: application/json' -H 'Accept: application/json'  -H "Authorization:Bearer {YourToken}" -d '{"from": "user1","to": ["184524748161025"],"type": "cmd","body":{"action":"action1"}}'
    ```

##### Response example

- Send a text message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send an image message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a voice message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a video message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a file message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a location message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a CMD message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a custom message

    ```json
    {
        "path": "/messages/chatgroups",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
        "184524748161025": "1029544257947437432"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

### Send a chat room message

For each app, you can send a maximum of 100 messages to chat rooms per second using this API and can send messages to at most 10 chat rooms at each call of this API. If you send a message to 10 chat rooms, the server records that 10 messages are sent. 

For chat room messages, Agora Chat provides three messages priorities: high, normal, and low. High-priority messages will be delivered first. When you create a message, you can assign a high priority for a certain type of messages in a chat room or of the specified member to ensure that these messages are delivered first. This method ensures that when messages are sent concurrently in large quantities or at a high rate, important ones can be delivered first, thereby increasing the delivery reliability of important messages. When the load on the server is high, low-priority messages will be discarded first to reserve resources for high-priority messages. However, the message priority function only ensures that high-priority messages arrive first, but not ensure that they are bound to arrive. Even high-priority messages will still be dropped when the server load is too high.
#### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/messages/chatrooms
```

##### Path parameter

For the descriptions of the path parameter, see [Common Parameters](#param).

##### Request header

| Parameter | Type | Description | Required |
|:---------------| :------ | :------- |:------------------|
| `Content-Type` | String | The content type. Set it to `application/json`.  | Yes |
| `Accept`       | String  | The content type. Set it to `application/json`.  | Yes |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${token}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

##### Request body

The request body is a JSON object, which contains the following parameters:

| Parameter | Type | Description | Required |
| --- | --- | --- | --- |
| `to` | Array | An array of the chat room IDs that receives the message. Within one second, you can send messages to a maximum of 100 chat rooms, and for each request, you can send messages to a maximum of 10 chat rooms. | Yes |
| `chatroom_msg_level` | String | The chat room message priority: <ul><li>`high`</li><li>(Default) `normal`</li><li>`low`</li></ul> | No       | 

The other parameters and descriptions are the same with those of [Sending a one-to-one message method](#request).

#### HTTP response

##### Reponse body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following parameters:

| Parameter      | Type   | Description |
| :------- |:-------------|:-------------|
| `data` | JSON | The detailed content of the response. The value of this parameter includes a key-value pair where key represents the chat room ID that receives the message and value the message ID. For example, if the returned data is `"185145305923585": "1029545553039460728"`, it means that a message with the ID of 1029545553039460728 is sent in chat room 185145305923585.  | 

For the other parameters and descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible causes.

#### Example

##### Request example

- Send a text message to the specified chat room without synchronizing the message with the sender

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'http://XXXX/XXXX/XXXX/messages/chatrooms' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["185145305923585"],"type": "txt","body": {"msg": "testmessages"}}'
    ```

- Send a text message to online users in the specified chat room while synchronizing the message with the sender

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'http://XXXX/XXXX/XXXX/messages/chatrooms' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["185145305923585"],"type": "txt","body": {"msg": "testmessages"},"routetype":"ROUTE_ONLINE", "sync_device":true}'
    ```

- Send an image message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatrooms' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["185145305923585"],"type": "img","body": {"filename":"testimg.jpg","secret":"VfXXXXNb_","url":"https://XXXX/XXXX/XXXX/chatfiles/55f12940-XXXX-XXXX-8a5b-ff2336f03252","size":{"width":480,"height":720}}}'
    ```

- Send a voice message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatrooms' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["185145305923585"],"type": "audio","body": {"url": "https://XXXX/XXXX/XXXX/chatfiles/1dfc7f50-XXXX-XXXX-8a07-7d75b8fb3d42","filename": "testaudio.amr","length": 10,"secret": "HfXXXXCjM"}}'
    ```

- Send a video message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatrooms' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d  '{"from": "user1","to": ["185145305923585"],"type": "video","body": {"thumb" : "https://XXXX/XXXX/XXXX/chatfiles/67279b20-7f69-11e4-8eee-21d3334b3a97","length" : 0,"secret":"VfXXXXNb_","file_length" : 58103,"thumb_secret" : "ZyXXXX2I","url" : "https://XXXX/XXXX/XXXX/chatfiles/671dfe30-XXXX-XXXX-ba67-8fef0d502f46"}}'
    ```

- Send a file message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i 'https://XXXX/XXXX/XXXX/messages/chatrooms' -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["185145305923585"],"type": "file","body": {"filename":"test.txt","secret":"1-g0XXXXua","url":"https://XXXX/XXXX/XXXX/chatfiles/d7eXXXX7444"}}'
    ```

- Send a location message

    ```shell
    # Replace {YourToken} with the app token generated on your server 
    curl -X POST -i "https://XXXX/XXXX/XXXX/messages/chatrooms"  -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' -d '{"from": "user1","to": ["185145305923585"],"type": "loc","body":{"lat": "39.966","lng":"116.322","addr":"North America"}}'
    ```

- Send a CMD message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -i "https://XXXX/XXXX/XXXX/messages/chatrooms" -H 'Content-Type: application/json' -H 'Accept: application/json'  -H "Authorization:Bearer {YourToken}" -d '{"from": "user1","to": ["185145305923585"],"type": "cmd","body":{"action":"action1"}}'
    ```

##### Response example

- Send a text message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Sned an image message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a voice message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a video message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a file message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a location message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a CMD message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
            "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

- Send a custom message

    ```json
    {
        "path": "/messages/chatrooms",
        "uri": "https://XXXX/XXXX/XXXX/messages/chatrooms",
        "timestamp": 1657254052191,
        "organization": "XXXX",
        "application": "e82bcc5f-XXXX-XXXX-a7c1-92de917ea2b0",
        "action": "post",
        "data": {
        "185145305923585": "1029545553039460728"
        },
        "duration": 0,
        "applicationName": "XXXX"
    }
    ```

<a name="upload"></a>

## Upload a file

This method enables you to upload images, audios, videos, or other types of files. For images and videos that have thumbnails, note the following:

- Images: After uploading an image, the Agora server automatically generates the thumbnail of the image.
- Videos: The Agora server does not generate thumbnails for videos automatically. After uploading a video, you must recall this method to upload the thumbnail for the video yourself.

<table>
    <thead>
        <tr>
            <th>File size</th><th>Thumbnail size</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">≤10</th><td>The size of the thumbnail remains the same as that of the original file.</td>
        </tr>
        <tr>
            <th rowspan="2" scope="rowgroup">>10</th><td>A thumbnail is generated based on the specified <code>thumbnail-height</code> and <code>thumbnail-width</code> parameters.</td>
        </tr>
        <tr>
            <td>If you leave <code>thumbnail-height</code> and <code>thumbnail-width</code> empty, the height and width of the thumbnail is 170 × 170 pixels by default.</td>
        </tr>
    </tbody>
</table>

Take note of the following considerations before calling this method:

- You cannot upload a file larger than 10 MB.
- You can restrict access to the uploaded file by requiring users to provide an access key before they can download the file. The format of the key is `{{url}}?share-secret={{secret}}`.

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/chatfiles
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :---------------- | :----- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------- |
| `Content-Type` | String | The content type. Pass `multipart/form-data` | Yes |
| `Authorization` | String | The authentication token of the user or admin, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |
| `restrict-access` | Bool | Whether to restrict access to this file.<ul><li>`true`: Restrict access to the file. The user needs to provide a file access key (`share-secret)` to download the file. You can obtain the access key from the response body.</li><li>`false`: The access is not restricted. Users can download the file directly.</li></ul> | No |

#### Request body

The request body is in the form-data format and contains the following fields:

| Field | Type | Description | Required |
| :---- | :----- | :--------------------------------------------------------------------------------------------- | :------- |
| `file` | String | The local path of the file to be uploaded. | Yes |
| `thumbnail-height` | Number | The height of the image thumbnail, in pixels. This parameter is valid only if the size of the uploaded image exceeds 10 KB. If you leave this parameter empty, the height is 170 pixels by default. | No     |
| `thumbnail-width` | Number| The width of the image thumbnail, in pixels. This parameter is valid only if the size of the uploaded image exceeds 10 KB. If you leave this parameter empty, the width is 170 pixels by default. | No    |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

| Field | Type | Description |
| :---------------------- | :----- | :------------------------------------------------------------------------------------------------------------------- |
| `entities.uuid` | String | The file ID, a unique ID assigned to the file by the Chat service. <br>You need to save this `uuid` yourself, and provide it when calling the [Send-file-messages](#sendmessage) method. |
| `entities.type` | String | File type: `chatfile`. |
| `entities.share-secret` | String | The file access key. <br>You need to save the `share-secret` yourself for use when [downloading the file](#download). |

For other fields and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

```shell
# Replace {YourToken} with the app token generated on your server, and the path of file with the local full path where the file to be uploaded is located
curl -X POST https://XXXX/XXXX/XXXX/chatfiles -H 'Authorization: Bearer {YourToken}' -H 'content-type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW' -H 'restrict-access: true' -F file=@/Users/test/9.2/chat/image/IMG_2953.JPG
```

#### Response example

```json
{
    "action": "post",
    "application": "8be024f0-XXXX-XXXX-b697-5d598d5f8402",
    "path": "/chatfiles",
    "uri": "https://XXXX/XXXX/XXXX/chatfiles",
    "entities": [
        {
            "uuid": "5fd74830-XXXX-XXXX-822a-81ea50bb049d",
            "type": "chatfile",
            "share-secret": "X9dXXXX7Yc"
        }
    ],
    "timestamp": 1554371126338,
    "duration": 0,
    "organization": "XXXX",
    "applicationName": "XXXX"
}
```

<a name="download"></a>

## Download a file

This method downloads images, audio, video, or other types of files. 

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/chatfiles/{file_uuid}
```

#### Path parameter


| Parameter      | Type   | Required | Description        |
| :---------- | :----- | :------- | :------------------------------ |
| `file_uuid` | String | Yes      | The UUID of the file to be downloaded. |

For the other parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :-------------- | :----- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------- |
| `Accept` | String | The content type. Set it to`application/octet-stream`, which means to download files in binary data stream format. | Yes |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${token}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |
| `share-secret` | String | The file access key for downloading the file. After the file is uploaded successfully using the [Upload the file](#upload) method, you can obtain the access key from the response body of `upload`. | This field is mandatory if you set `restrict-access` to `true` when uploading the file. |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds. You can refer to [Common parameters](#param) for the parameters and detailed description.

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

```shell
# Replace {YourToken} with the app token generated on your server, and the path of file with the local full path where the file to be downloaded is located
curl -X GET -H 'Accept: application/octet-stream' -H 'Authorization: Bearer {YourToken}' -H 'share-secret: f0Vr-uyyEeiHpHmsu53XXXXXXXXZYgyLkdfsZ4xo2Z0cSBnB' 'http://XXXX/XXXX/XXXX/chatfiles/7f456bf0-XXXX-XXXX-b630-777db304f26c'-o /Users/test/chat/image/image.JPG
```

#### Response example

```json
{
    // The content of the voice/image file
}
```

## Download a thumbnail

When uploading an image or video file, the Chat server can create a thumbnail for the file. This method has an extra `thumbnail:true` field in the request header compared with [downloading a file](#download).

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/chatfiles/{file_uuid}
```

#### Path parameter


| Parameter  | Type   | Required | Description   |
| :---------- | :----- | :------- | :------------------------------ |
| `file_uuid` | String | Yes   | The UUID that the server generates for the thumbnail. |


For the other parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :-------------- | :----- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------- |
| `Accept` | String | `application/octet-stream`, which means to download files in binary data stream format. | Yes |
| `Authorization` | String | Bearer ${YourAppToken} | Yes |
| `thumbnail` | Bool | Whether to download the thumbnail of the image or video file.<ul><li>`true`: Yes.</li><li>`false`: (Default) No. Download the original file instead.</ul> | No. |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds. You can refer to [Common parameters](#param) for the parameters and detailed description.

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

```shell
# Replace {YourToken} with the app token generated on your server
curl -X GET -H 'Accept: application/octet-stream' -H 'Authorization: Bearer {YourToken}' -H 'share-secret: f0Vr-uyyEeiHpHmsu53XXXXXXXXZYgyLkdfsZ4xo2Z0cSBnB' -H 'thumbnail: true' 'http://XXXX/XXXX/XXXX/chatfiles/7f456bf0-ecb2-11e8-b630-777db304f26c'
```

#### Response example

```json
{
    // The content of the thumbnail
}
```

## Retrieve historical messages

This method retrieves historical messages sent and received by the user. 

- For each request, you can retrieve all the historical messages sent and received within one hour from the specified time.
- The response of this method is not returned in real time.
- The default storage time of historical messages differs by plan version. For details, see [package details](./agora_chat_plan?platform=RESTful).

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/chatmessages/${time}
```

#### Path parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------- |
| `time` | String | The start time of the historical messages to query. UTC time, using the ISO8601 standard, in the format of `yyyyMMddHH`. <br>For example, if `time` is` 2018112717`, it means to query historical messages from 17:00 on November 27, 2018 to 18:00 on November 27, 2018. | Yes |

For other parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Description | Required |
| :-------------- | :--------------------- | :------- |
| `Accept`       | String  | The content type. Set it to `application/json`.  | Yes |
| `Authorization` | The authentication token of the user or admin, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

| Parameter | Type | Description |
| :--------- | :----- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `url` | String | The download address of the historical message file. This parameter is valid within a limited time duration. The `Expires` field indicates when the returned URL is valid. Once the URL expires, you need to call this method to get the URL again. |

For other fields and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

```shell
# Replace {YourToken} with the app token generated on your server.
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer {YourToken}' 'http://XXXX/XXXX/XXXX/chatmessages/2018112717'
```

#### Response example

```json
{  
    "action": "get",  
    "application": "8be024f0-XXXX-XXXX-b697-5d598d5f8402",  
    "uri": "'http://XXXX/XXXX/XXXX/chatmessages/2018112717",  
    "data": [    
        {      
            "url": "http://XXXX?Expires=1543316122&OSSAccessKeyId=XXXX&Signature=XXXX"    
        }
    ],  
    "timestamp": 1543314322601,  
    "duration": 0,  
    "organization": "XXXX",  
    "applicationName": "testapp"
}
```

### Content of historical messages

After successfully querying historical messages, you can visit the URL to download the historical message file and view the specific content of the historical message.

The data format for historical messages are JSON, which contains the following fields:

| Parameter | Type | Description |
| --- | --- | --- |
| `msg_id` | String | The message ID. |
| `timestamp` | Long | The UTC Unix timestamp when the message is sent, in miliseconds. |
| `from` | String | The username that sends the message. |
| `to` | String | The message recipient.<ul><li>For a one-to-one chat, this parameter indicates the peer user that receives the message.</li><li>For a group chat, this parameter indicates the chat group ID.</ul>|
| `chat_type` | String | The chat type:<ul><li>`chat`: One-to-one chat.</li><li>`groupchat`: Group chat.</li><li>`chatroom`: Chat room.</li></ul> |
| `payload` | JSON | The content of the message, including message extensions and customzied message attributes. |
  
```json
{    
    "msg_id": "5I02W-XX-8278a",     
    "timestamp": 1403099033211,     
    "direction":"outgoing",    
    "to": "XXXX",    
    "from": "XXXX",     
    "chat_type": "chat",     
    "payload": 
    {        
        "bodies": [
            {             
            // For different message types, the parameters differ         
            }        
        ],        
        "ext": 
            {            
                "key1": "value1",              ...        
            },         
        "from":"XXXX",         
        "to":"XXXX"    
    }
}
```

The fields of `bodies` for different message types vary:

- Text messages

    | Field | Type | Description |
    | :----- | :----- | :------------------------------- |
    | `msg` | String | The message content. |
    | `type` | String | The message type. For text messages, set it as `txt`. |

    Example:

    ```json
    {
        "bodies": [
            {
                "msg": "welcome to Agora!",
                "type": "txt"
            }
        ]
    }
    ```

- Image messages

    | Field | Type | Description |
    | :------------ | :----- | :-------------------------------------------------------------------------------- |
    | `file_length` | Number | The size of the image attachment, in bytes. |
    | `file_name` | String | The name of the image file. |
    | `secret` | String | The image file access key. <br>This field exists if you set the access restriction when calling the [upload-file](#upload) method. |
    | `size` | Number | The size of the image, in pixels.<li>`height`: The image height<li>`width`: The image width |
    | `type` | String | The message type. For image messages, set it as `img`. |
    | `url` | String | The URL address of the image. |

    Example:

    ```json
    {
        "bodies": [
            {
                "file_length": 128827,
                "filename": "test1.jpg",
                "secret": "DRGM8OZrEeO1vaXXXXXXXXHBeKlIhDp0GCnFu54xOF3M6KLr",
                "size": {
                    "height": 1325,
                    "width": 746
                },
                "type": "img",
                "url": "https://a1.agora.com/agora-demo/chatdemoui/chatfiles/65e54a4a-XXXX-XXXX-b821-ebde7b50cc4b"
            }
        ]
    }
    ```

- Location messages

    | Field | Type | Description |
    | :----- | :----- | :--------------------------- |
    | `addr` | String | The descriptions of the location. |
    | `lat` | Number | The latitude of the location. |
    | `lng` | Number | The longitude of the location. |
    | `type` | String | The message type. For location messages, set it as `loc`. |

    Example:

    ```json
    {
        "bodies": [
            {
                "addr": "test",
                "lat": 39.9053,
                "lng": 116.36302,
                "type": "loc"
            }
        ]
    }
    ```

- Voice messages

    | Field | Type | Description |
    | :------------ | :----- | :-------------------------------------------------------------------------------- |
    | `file_length` | Number | The size of the audio file, in bytes. |
    | `filename` | String | The audio file name, including a suffix that indicates the the audio file format. |
    | `secret` | String | The audio file access key. <br>This field exists if you set the access restriction when calling the [upload-file](#upload) method. |
    | `length` | Number | The duration of the audio file, in seconds. |
    | `type` | String | The message type. For voice messages, set it as `audio`. |
    | `url` | String | The URL address of the audio file. |

    Example:

    ```json
    {
        "bodies": [
            {
                "file_length": 6630,
                "filename": "test1.amr",
                "length": 10,
                "secret": "DRGM8OZrEeO1vafuJSo2IjHBeKlIhDp0GCnFu54xOF3M6KLr",
                "type": "audio",
                "url": "https://a1.agora.com/agora-demo/chatdemoui/chatfiles/0637e55a-XXXX-XXXX-ba23-51f25fd1215b"
            }
        ]
    }
    ```

- Video messages

    | Field | Type | Description |
    | :------------- | :----- | :---------------------------------------------------------------------------------- |
    | `file_length` | Number | The size of the video file, in bytes. |
    | `filename` | String | The video file name, including a suffix that indicates the video file format. |
    | `secret` | String | The video file access key. <br>This field exists if you set the access restriction when calling the [upload-file](#upload) method. |
    | `length` | Number | The video duration, in seconds. |
    | `size` | Number | The video thumbnail size, in pixels.<li>`width`: The width of the video thumbnail<li>`height`: The height of the video thumbnail |
    | `thumb` | String | The URL address of the video thumbnail. |
    | `thumb_secret` | String | The thumbnail file access key. <br>This field exists if you set the access restriction when calling the [upload-file](#upload) method. |
    | `type` | String | The message type. For video messages, set it as `video`. |
    | `url` | String | The URL address of the video file. You can visit this URL to download video files. |

    Example:

    ```json
    {
        "bodies": [
            {
                "file_length": 58103,
                "filename": "1418105136313.mp4",
                "length": 10,
                "secret": "VfEpSmSvEeS7yU8dwa9rAQc-DIL2HhmpujTNfSTsrDt6eNb_",
                "size": {
                    "height": 480,
                    "width": 360
                },
                "thumb": "https://a1.agora.com/agora-demo/chatdemoui/chatfiles/67279b20-XXXX-XXXX-8eee-21d3334b3a97",
                "thumb_secret": "ZyebKn9pEeSSfY03ROk7ND24zUf74s7HpPN1oMV-1JxN2O2I",
                "type": "video",
                "url": "https://a1.agora.com/agora-demo/chatdemoui/chatfiles/671dfe30-XXXX-XXXX-ba67-8fef0d502f46"
            }
        ]
    }
    ```

- File messages

    | Field | Type | Descriptions |
    | :------------ | :----- | :---------------------------------------------------------------------------- |
    | `file_length` | Number | The file size, in bytes. |
    | `filename` | String | The file name, including a suffix that indicates the file format. |
    | `secret` | String | The file access key. <br>This field exists if you set the access restriction when calling the [upload-file](#upload) method. |
    | `type` | String | The message type. For file messages, set it as `file`. |
    | `url` | String | The URL address of the file. You can visit this URL to download video files. |

    Example:

    ```json
    {
        "bodies": [
            {
                "file_length": 3279,
                "filename": "record.md",
                "secret": "2RNXCgeeEeeXXXX-XXXXbtZXJH4cgr2admVXn560He2PD3RX",
                "type": "file",
                "url": "https://XXXX/XXXX/XXXX/chatfiles/d9135700-XXXX-XXXX-b000-a7039876610f"
            }
        ]
    }
    ```

- CMD messages

    | Field | Type | Description |
    | :------- | :----- | :--------------------------- |
    | `action` | String | The request method. |
    | `type` | String | The message type. For command messages, set it as `cmd`. |

    Example:

    ```json
    {
        "bodies": [
            {
                "action": "run",
                "type": "cmd"
            }
        ]
    }
    ```

- Custom messages

    | Field | Type | Description |
    | :------------ | :----- | :----------------------------------------------- |
    | `customExts` | JSON | The custom extension properties. You can set the fields in the extension properties yourself. |
    | `customEvent` | String | The custom event type. |
    | `type` | String | The message type. For custom messages, set it as `custom`. |

    Example:

    ```json
    {
        "bodies": [
            {
                "customExts": {
                    "name": "flower",
                    "size": "16",
                    "price": "100"
                },
                "customEvent": "gift_1",
                "type": "custom"
            }
        ]
    }
    ```
    
## Recall a message

Once a message is sent, you can call this method to recall the message. The default time limit for recalling a message is two minutes. To adjust this limit, contact support@agora.io.

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/messages/msg_recall
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter            | Type | Required | Description                                                         |
| :-------------- | :------- | :------- | :----------------------------------------------------------- |
| `Content-Type`  | String   | Yes     | `application/json`                             |
| `Accept`       | String  | The content type. Set it to `application/json`.  | Yes |
| `Authorization` | String   | Yes     | The authentication token of the user or admin, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. |

#### Request body

| Parameter        | Type | Required | Description                                                         |
| :---------- | :------- | :------- | :----------------------------------------------------------- |
| `msg_id`    | String   | Yes     | The ID of the recalled message.                                      |
| `to`        | String   | No   | The user, chat group, or chat room that receives the recalled message. You can specify a username, a chat group ID, or a chat room ID.</br>**Note**: If the recalled message exceeds the message storage duration and no longer exists in the server, this message cannot be recalled on the server side. You can only recall the message on the receiver client instead. |
| `chat_type` | String   | Yes     | The type of the chat where the recalled message is located. <ul><li>`chat`: An one-on-one chat.</li><li>`group_chat`: A chat group.</li><li>`chatroom`: A chat room.</li></ul> |
| `from`      | String   | No   | The user who recalls the message. By default, the recaller is the app admin. You can also specify another user as the recaller. |
| `force`     | bool     | Yes     | Whether to force a recall if the recalled message exceeds the message storage duration and no longer exists in the server. <ul><li>`true`: Force the recall on the client that receives the message.</li><li>`false`: The recall fails.</li></ul>The maximum message storage duration varies based on different pricing plans. For details, see [Message storage duration](./agora_chat_limitation?platform=RESTful#message-storage-duration). |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

| Parameter       | Description                                                         |
| :--------- | :----------------------------------------------------------- |
| `msg_id`   | The ID of the recalled message.                                          |
| `recalled` | Returns `yes` if the request is successful.                         |
| `from`     | The user who recalls the message. By default, the recaller is the app admin.          |
| `to`       | The user, chat group, or chat room that receives the recalled message.                      |
| `chattype` | The type of the chat where the recalled message is located. <ul><li>`chat`: An one-on-one chat.</li><li>`group_chat`: A chat group.</li><li>`chatroom`: A chat room.</li></ul> |

For other fields and detailed descriptions, see [Common parameters](#param).

If the request fails, refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

```shell
# Replace {YourToken} with the app token generated on your server
curl -i -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H "Authorization: Bearer {YourToken}" 
"http://XXXX/XXXX/XXXX/messages/msg_recall" 
-d '{
    "msg_id": "1028442084794698104",
    "to": "user2",
    "from": "user1",
    "chat_type": "chat",
    "force": true
}'  
```

#### Response example

- If the message is recalled:

    ```json
    {
        "path": "/messages/msg_recall",
        "uri": "https://XXXX/XXXX/XXXX/messages/msg_recall",
        "timestamp": 1657529588473,
        "organization": "XXXX",
        "application": "09ebbf8b-XXXX-XXXX-XXXX-d47c3b38e434",
        "action": "post",
        "data": {
            "recalled": "yes",
            "chattype": "chat",
            "from": "XXXX",
            "to": "XXXX",
            "msg_id": "1028442084794698104"
        },
        "duration": 8,
        "applicationName": "XXXX"
    }
    ```

- If the message fails to be recalled:

    ```json
    {
        "msgs": 
        [
            {   "msg_id":"673296835082717140",
                "recalled":"not_found msg" 
            }
        ] 
    }
    ```
  Possible causes for failing to recall the message include the following:

  - `"can't find message to"`: Fails to find the recipient of the message.
  - `"exceed call time limit"`: Exceeds the time limit for recalling a message.
  - `"not_found msg"`: The message is recalled.
  - `"internal error"`: An internal error occurs with the back-end service.



## Delete conversations from the server

This method enables the chat user to delete conversations one way from the server. Once the conversation is deleted, this chat user can longer retrieve the conversation from the server. Other chat users can still get the conversation from the server.

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
DELETE https://{host}/{orgName}/{appName}/users/{userName}/user_channel
```

#### Path parameter

| Parameter | Type | Description | Required |
| --- | --- | --- | --- |
| `userName` | String | The username whosr conversation is to be deleted. |

For the other parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter            | Type | Required | Description                                                         |
| :-------------- | :------- | :------- | :----------------------------------------------------------- |
| `Authorization` | String   | Yes     | The authentication token of the user or admin, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. |

#### Request body

| Parameter         | Type | Required | Description                                                         |
| :------------ | :------- | :------- | :----------------------------------------------------------- |
| `channel`     | String   | Yes     | The ID of the conversation that you want to delete.                                  |
| `type`        | String   | Yes     | The type of the chat.<ul><li>`chat`: An one-on-one chat.</li><li>`groupchat`: A group chat.</li></ul> |
| `delete_roam` | Bool     | Yes     | Whether to delete the chat from the server:<ul><li>`true`: Yes. </li><li>`false`: No. </li></ul> |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds. The response body contains the following fields:

| Parameter     | Description                                                  |
| :------- | :---------------------------------------------------- |
| `result` | Returns `ok` if the request is successful. |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

```shell
# Replace {YourToken} with the app token generated on your server
curl -X DELETE -H "Authorization: Bearer {YourToken}" "https://XXXX/XXXX/XXXX/users/u1/user_channel" -d '{"channel":"u2", "type":"chat"，"delete_roam": true}'
```

#### Response example

```json
{
    "path": "/users/user_channel",
    "uri": "https://a1.agora.com/agora-demo/test-app/users/u1/user_channel",
    "timestamp": 1638440544078,
    "organization": "agora-demo",
    "application": "c3624975-3d51-4b0a-9da2-ee91ed4c5a76",
    "entities": [],
    "action": "delete",
    "data": {
        "result": "ok"
    },
    "duration": 3,
    "applicationName": "test-app"
}
```

## Import one-to-one chat messages

This method imports one-to-one messages.

### HTTP request

```http
POST https://{host}/{orgName}/{appName}/messages/users/import
```

#### Request header

| Parameter            | Type | Required | Description                                                         |
| :-------------- | :------- | :------- | :----------------------------------------------------------- |
| `Authorization` | String   | Yes     | The authentication token of the user or admin, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. |


#### Request body

| Parameter | Type | Description | Required |
| --- | --- | --- | --- |
| `from` | String | The username of the message sender.   | Yes |
| `target` | String | The username of the message recipient. | Yes |
| `type` | String | The message type: <ul><li>`txt`: Text message</li><li>`img`: Image message</li><li>`audio`: Audio message</li><li>`video`: Video message</li><li>`file`: File message</li><li>`loc`: Location message</li><li>`cmd`: Command message</li><li>`custom`: Custom message</li></ul> | Yes |
| `body` | JSON | The message content. For different message types, this parameter contains different fields. For details, see [Body of different message types](#body). | Yes |
| `is_ack_read` | Bool | Whether to set the message as read. <ul><li>`true`: Yes.</li><li>`false`: No.</li></ul> | No |
| `msg_timestamp` | Long | The timestamp for importing the messages, in milliseconds. If you leave this parameter empty, the server automatically sets it as the current time. | No |
| `need_download` | Bool | Whether to download the attachment and upload it to the server:<ul><li>`true`: Yes.</li><li>`false`: (Default) No.</li></ul> | No |

### HTTP Response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

| Parameter       | Description                                                         |
| :--------- | :----------------------------------------------------------- |
| `msg_id`   | The ID of the imported messages.                                  |

For other fields and detailed descriptions, see [Common parameters](#param).

If the request fails, refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

- Import a text message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -H "Authorization: Bearer {YourToken}" "https://XXXX/XXXX/XXXX/messages/users/import" -d '{
        "target": "username2",
        "type": "txt",
        "body": {
            "msg": "import message."
        },
        "from": "username1",
        "is_ack_read": true,
        "msg_timestamp": 1656906628428
    }'
    ```

- Import an image message

    ```shell
    # Replace {YourToken} with token generated on your server
    curl -X POST -H "Authorization: Bearer {YourToken}" "https://XXXX/XXXX/XXXX/messages/users/import" -d '{
        "target": "username2",
        "type": "img",
        "body": {
            "url": "<YourImageUrl>",
            "filename": "<ImageFileName>",
            "size": {
                "width": 1080,
                "height": 1920
            }
        },
        "from": "username1",
        "is_ack_read": true,
        "msg_timestamp": 1656906628428,
        "need_download": true
    }'
    ```

#### Response example

```json
{
    "path": "/messages/users/import",
    "uri": "https://XXXX/XXXX/XXXX/messages/users/import",
    "timestamp": 1638440544078,
    "organization": "XXXX",
    "application": "c3624975-XXXX-XXXX-9da2-ee91ed4c5a76",
    "entities": [],
    "action": "post",
    "data": {
        "msg_id": "10212123848595"
    },
    "duration": 3,
    "applicationName": "XXXX"
}
```

## Import chat group messages

### HTTP request

```http
POST https://{host}/{orgName}/{appName}/messages/chatgroups/import
```

#### Request header

| Parameter            | Type | Required | Description                                                         |
| :-------------- | :------- | :------- | :----------------------------------------------------------- |
| `Authorization` | String   | Yes     | The authentication token of the user or admin, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. |

#### Request body

| Parameter | Type | Description | Required |
| --- | --- | --- | --- |
| `from` | String | The username of the message sender.   | Yes |
| `target` | String | The chat group ID that receives the message. | Yes |
| `type` | String | The message type: <ul><li>`txt`: Text message</li><li>`img`: Image message</li><li>`audio`: Audio message</li><li>`video`: Video message</li><li>`file`: File message</li><li>`loc`: Location message</li><li>`cmd`: Command message</li><li>`custom`: Custom message</li></ul> | Yes |
| `body` | JSON | The message content. For different message types, this parameter contains different fields. For details, see [Body of different message types](#body). | Yes |
| `is_ack_read` | Bool | Whether to set the message as read. <ul><li>`true`: Yes.</li><li>`false`: No.</li></ul> | No |
| `msg_timestamp` | Long | The timestamp for importing the messages, in milliseconds. If you leave this parameter empty, the server automatically sets it as the current time. | No |
| `need_download` | Bool | Whether to download the attachment and upload it to the server:<ul><li>`true`: Yes.</li><li>`false`: (Default) No.</li></ul> | No |

### HTTP Response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

| Parameter       | Description                                                         |
| :--------- | :----------------------------------------------------------- |
| `msg_id`   | The ID of the imported messages.   |

For other fields and detailed descriptions, see [Common parameters](#param).

If the request fails, refer to [Status codes](./agora_chat_status_code?platform=RESTful) for possible reasons.

### Example

#### Request example

- Import a text message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -H "Authorization: Bearer {YourToken}" "https://XXXX/XXXX/XXXX/messages/chatgroups/import" -d '{
        "target": "username2",
        "type": "txt",
        "body": {
            "msg": "import message."
        },
        "from": "username1",
        "is_ack_read": true,
        "msg_timestamp": 1656906628428
    }'
    ```

- Import an image message

    ```shell
    # Replace {YourToken} with the app token generated on your server
    curl -X POST -H "Authorization: Bearer {YourToken}" "https://XXXX/XXXX/XXXX/messages/chatgroups/import" -d '{
        "target": "username2",
        "type": "img",
        "body": {
            "url": "<YourImageUrl>",
            "filename": "<ImageFileName>",
            "size": {
                "width": 1080,
                "height": 1920
            }
        },
        "from": "username1",
        "is_ack_read": true,
        "msg_timestamp": 1656906628428,
        "need_download": true
    }'
    ```

#### Response example

```json
{
    "path": "/messages/users/import",
    "uri": "https://XXXX/XXXX/XXXX/messages/chatgroups/import",
    "timestamp": 1638440544078,
    "organization": "XXXX",
    "application": "c3624975-XXXX-XXXX-9da2-ee91ed4c5a76",
    "entities": [],
    "action": "post",
    "data": {
        "msg_id": "10212123848595"
    },
    "duration": 3,
    "applicationName": "XXXX"
}
```

## Status codes

For details, see [HTTP Status Codes](./agora_chat_status_code?platform=RESTful).