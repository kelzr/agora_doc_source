# Servers

Circle is an online chat and community solution built on the Chat SDKs. It enables users to connect with communities of mutual interest and communicate with thousands of other users worldwide in real time.

Circle provides a three-layer structure, as follows:

- **Server**: Each server represents one of the communities within Circle. Users can search for and join servers by subject/interest. They can also create and manage their own servers.
- **Channel**: A server is organized into topic-based channels, where users can connect with other interested users to discuss specific topics. A default channel is automatically created during server creation.
- **Thread**: Threads serve as sub-channels inside an existing channel, to help better organize conversations in a busy channel.

Once a server is created, a default channel containing all server members is automatically created to receive system notifications. The server owner can then create public or private channels, as desired. Note that users can join and leave a server freely without requiring anyone's approval.

This page shows how to implement servers in Circle using RESTful APIs. Before proceeding, ensure that you understand the frequency limit of Chat RESTful API calls described in [Limitations](https://docs.agora.io/en/agora-chat/reference/limitations#call-limit-of-server-sides).


## <a name="param"></a>Common parameters

### Request parameters

| Parameter | Type | Description | Required |
| :--------- | :----- | :------ | :------- |
| `host` | String | The domain name assigned by the Agora Chat service to access RESTful APIs. For how to get the domain name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `org_name` | String | The unique identifier assigned to each company (organization) by the Agora Chat service.  For how to get the org name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `app_name` | String | The unique identifier assigned to each app by the Agora Chat service. For how to get the app name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `server_id`  | String | The server ID.         | Yes   |
| `user_id`    | String | The user ID.   | Yes |


### Response parameters

| Parameter | Type | Description |
| :---------------- | :----- | :--------------- |
| `name`              | String | The server name.     |
| `owner`             | String | The server owner.     |
| `description`      | String  | The server description.    |
| `custom`            | String | The server extension.    |
| `icon_url`         | String  | The URL of the server icon.                   |
| `server_tag_id`   | String   | The ID of the server tag.             |
| `tag_name`        | String   | The name of the server tag.               |
| `tag_count`       | Number   | The number of server tags.             |
| `server_id`       | String   | The server ID.                   |
| `default_channel_id` | String | The channel ID of the default channel.             |
| `user_id`   | String |  The user ID.      |
| `role`              | Number | The role of the user in a server.<li>`0`: Owner.</li><li>`1`: Moderator.</li><li>`2`: Member.</li> <div class="alert note">The moderators of a server serve as the moderators of all channels under the server.</div>  |
| `created`          | Number  | The Unix timestamp (ms) when the server was created.     |



## Authorization

Agora Chat RESTful APIs require Bearer HTTP authentication. Every time an HTTP request is sent, the following `Authorization` field must be filled in the request header:

```http
Authorization: Bearer ${YourAppToken}
```

In order to improve the security of the project, Agora uses a token (dynamic key) to authenticate users before they log in to the chat system. The Agora Chat RESTful API only supports authenticating users using app tokens. For details, see [Authentication using App Token](./generate_app_tokens?platform=RESTful).



## Check if a user exists in Circle

Checks if a user exists in Circle.

### HTTP request

```http
GET https://{host}/{orgName}/{appName}/circle/user/{user_id}
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :-------- | :------- | :------- |
| `code`   | Number     | The HTTP status code.   |
| `result` | Boolean | Whether the user exists in Circle: <li>`true`: Yes.</li><li>`false`: No.</li> |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/user/{user_id}'
```

#### Response example

```json
{
    "code": 200,
    "result" : true
}
```


## Create a server

Creates a server, and sets its name, icon URL, description, and extension.

Each user can create a maximum of 100 servers. To raise this limit, contact [support@agora.io](support@agora.io).

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/circle/server
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Content-Type` | String | `application/json` | Yes  |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

#### Request body

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `owner` | String | The user ID of the server creator (owner). The length cannot exceed 64 characters. | Yes  |
| `name` | String | The server name. The length cannot exceed 50 characters. | Yes  |
| `icon_url` | String | The URL of the server icon. The length cannot exceed 500 characters. | No |
| `description` | String | The server description. The length cannot exceed 500 characters. | No |
| `custom` | String | The server extension. The length cannot exceed 500 characters. | No |

### HTTP response
#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :-------- | :------- | :------- |
| `code`   | Number     | The HTTP status code.   |
| `server_id` | String | The ID of the created server.   |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server' -d '{
    "owner" : "user1",
    "name" : "server",
    "icon_url" : "http://circle.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
    "description" : "community",
    "custom" : "custom"
}'
```

#### Response example

```json
{
    "code": 200,
    "server_id": "19VM9oPBasxxxxxx0tvWViEsdM"
}
```


## Modify a server

Modifies the name, icon URL, description, and extension of the specified server.

### HTTP request

```http
PUT https://{host}/{org_name}/{app_name}/circle/server/{server_id} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Content-Type` | String | `application/json` | Yes  |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

#### Request body

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `name` | String | The server name. The length cannot exceed 50 characters. | No  |
| `icon_url` | String | The URL of the server icon. The length cannot exceed 500 characters. | No |
| `description` | String | The server description. The length cannot exceed 500 characters. | No |
| `custom` | String | The server extension. The length cannot exceed 500 characters. | No |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :-------- | :------- | :------- |
| `code`   | Number     | The HTTP status code.   |
| `server` | JSON |  The detailed information of the server.  |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X PUT -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX' -d '{
 "name" : "chat",
 "icon_url" : "http://discord.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
 "description" : "community",
 "custom" : ""custom"
}'
```

#### Response example

```json
{
    "code": 200,
    "server": {
        "name": "chat",
        "owner": "u1",
        "description": "community",
        "custom": "custom",
        "tags": [
            {
                "server_tag_id": "1",
                "tag_name": "social networking"
            }
        ],
        "tag_count": 1,
        "created": 1658126097514,
        "server_id": "19SW5Q85jHxxxxx6T5kexvn",
        "icon_url": "http://discord.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
        "default_channel_id": "187xxxx09"
    }
}
```

## Add a server tag

Adds a tag to the specified server.

Each server can have a maximum of 10 tags.

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/circle/server/{server_id}/tag/add
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Content-Type` | String | `application/json` | Yes  |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

#### Request body

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `tags` | List | The array of the tags to add. Each server can have a maximum of 10 tags. The length of each tag can be a maximum of 20 characters. | Yes  |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :-------- | :------- | :------- |
| `code`   | Number     | The HTTP status code.   |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/tag/add' -d '{
 "tags": ["social networking"]
}'
```

#### Response example

```json
{
    "code": 200,
    "tags": [
        {
            "server_tag_id": "1",
            "tag_name": "social networking"
        }
    ]
}
```

## Retrieve a server tag

Retrieves tags of the specified server.

### HTTP request

```http
GET http://{host}/{orgName}/{appName}/circle/server/{server_id}/tag
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code` | Number  | The HTTP status code. |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourToken>' 'http://XXX/XXX/XXX/circle/server/XXX/tag'
```

#### Response example

```json
{
    "code": 200,
    "count": 1,
    "tags": [
        {
            "server_tag_id": "78",
            "tag_name": "uur2"
        }
    ]
}
```

## Remove a server tag

Removes the specified tags from the server.

<div class="alert note">Agora recommends that you remove tags by specifying the tag IDs rather than tag names. To retrieve the tag IDs, refer to <a href="#retrieve-a-server-tag">Retrieve a server tag</a>.</div>

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/circle/server/{server_id}/tag/remove
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Content-Type` | String | `application/json` | Yes  |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

#### Request body

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `tagIds` | List | The ID array of the tags to remove. You can remove a maximum of 10 tags per request. | Yes  |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code` | Number  | The HTTP status code. |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/tag/remove' -d '{
 "tagIds" : ["1", "2"]
}'
```

#### Response example

```json
{
    "code": 200
}
```


## Search for a server

Searches for servers by specifying the server names.

Each query can return a maximum of 15 results.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/circle/server/search?name={name} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Query parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `name` | String | The server name. The length cannot exceed 50 characters. <div class="alert note">Note that fuzzy matching is not supported. Enter a complete server name rather than keywords.</div> | Yes  |

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code` | Number  | The HTTP status code. |
| `count`   | Number  | The number of matched servers. |
| `servers` | List | The details of the matched servers. |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/search?name=XXX'
```

#### Response example

```json
{
    "code": 200,
    "count": 1,
    "servers": [
        {
          "name": "chat",
          "owner": "u1",
          "description": "community",
          "custom": "custom",
          "tags": [
              {
                  "server_tag_id": "1",
                  "tag_name": "social networking"
              }
          ],
          "tag_count": 1,
          "created": 1658126097514,
          "server_id": "19SW5Q85jHxxxxx6T5kexvn",
          "icon_url": "http://discord.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
          "default_channel_id": "187xxxx09"
        }
    ]
}
```


## Retrieve the details of a server

Retrieves the detailed information of the specified server.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/circle/server/{server_id}/by-id
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code`   | Number  | The HTTP status code. |
| `server` | JSON | The detailed information of the server.  |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/by-id'
```

#### Response example

```json
{
    "code": 200,
    "server": {
        "name": "chat",
        "owner": "u1",
        "description": "community",
        "custom": "custom",
        "tags": [
            {
                "server_tag_id": "1",
                "tag_name": "social networking"
            }
        ],
        "tag_count": 1,
        "created": 1658126097514,
        "server_id": "19SW5Q85jHxxxxx6T5kexvn",
        "icon_url": "http://discord.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
        "default_channel_id": "187xxxx09"
    }
}
```


## Retrieve the details of recommended servers

This method currently retrieves the detailed information of five servers that are last created.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/circle/server/recommend/list
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code`   | Number  | The HTTP status code. |
| `count`   | Number  | The number of recommended servers. |
| `server` | JSON | The detailed information of the recommended servers.  |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/recommend/list'
```

#### Response example

```json
{
    "code": 200,
    "count": 1,
    "servers": [
        {
            "name": "chat",
          "owner": "u1",
          "description": "community",
          "custom": "custom",
          "tags": [
              {
                  "server_tag_id": "1",
                  "tag_name": "social networking"
              }
          ],
          "tag_count": 1,
          "created": 1658126097514,
          "server_id": "19SW5Q85jHxxxxx6T5kexvn",
          "icon_url": "http://discord.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
          "default_channel_id": "187xxxx09"
        }
    ]
}
```


## Retrieve the details of joined servers

Retrieves the detailed information of the servers that the specified user that has joined (by page).

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/circle/server/list?userId={user_id}&limit={limit}&cursor={cursor} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Query parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `userId` | String    |  The user ID.  | Yes  |
| `limit` | Number    |   The number of servers to query per page. The value range is [1,20]. The default value is 20. This parameter is only required when retrieving by page.  |  No  |
| `cursor`     | String  | The start position of the next query. This parameter is only required when retrieving by page.    |  No  |

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

| Parameter | Type | Description |
| :----- | :----- | :------- |
| `code`    | Number  | The HTTP status code.    |
| `count`   | Number  | The number of servers. |
| `servers` | List | The detailed information of the servers. |
| `cursor`  | String  | The start position of the next query. |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/list?userId=XXX&limit=1&cursor=XXX'
```

#### Response example

```json
{
    "code": 200,
    "count": 1,
    "servers": [
        {
          "name": "chat",
          "owner": "u1",
          "description": "community",
          "custom": "custom",
          "tags": [
              {
                  "server_tag_id": "1",
                  "tag_name": "social networking"
              }
          ],
          "tag_count": 1,
          "created": 1658126097514,
          "server_id": "19SW5Q85jHxxxxx6T5kexvn",
          "icon_url": "http://discord.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
          "default_channel_id": "187xxxx09"
        }
    ],
    "cursor": "ZGNiMjRmNGY1YjczYjlhYTNkYjk1MDY2YmEyNzFmODQ6aXXXXXXXXXXXXXX"
}
```


## Destroy a server

Destroys the specified server.

When a server is destroyed, all channels within it are destroyed as well.

### HTTP request

```http
DELETE https://{host}/{org_name}/{app_name}/circle/server/{server_id}
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code` | Number  | The HTTP status code. |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X DELETE -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX'
```

#### Response example

```json
{
    "code": 200
}
```


## Add a user to a server

Adds the specified user to a server.

Each user can be added to a maximum of 100 servers. To raise this limit, contact [support@agora.io](support@agora.io).

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/circle/server/{server_id}/join?userId={user_id} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Query parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `userId` | String    |  The user ID.  | Yes  |

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code` | Number  | The HTTP status code. |
| `server` | JSON  | The detailed information of the server to which the user is added. |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X POST -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/join?userId=XXX'
```

#### Response example

```json
{
    "code": 200,
    "server": {
        "name": "chat",
        "owner": "u1",
        "description": "community",
        "custom": "custom",
        "tags": [
            {
                "server_tag_id": "1",
                "tag_name": "social networking"
            }
        ],
        "tag_count": 1,
        "created": 1658126097514,
        "server_id": "19SW5Q85jHxxxxx6T5kexvn",
        "icon_url": "http://discord.oss/19b1d7b0-7079-11e9-9bd8-25c5e81b42a1",
        "default_channel_id": "187xxxx09"
    }
}
```


## Retrieve the member list of a server

Retrieves the list of members in a server (by page).

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/circle/server/{server_id}/users?limit={limit}&cursor={cursor} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Query parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `limit` | Number    |  The number of members to query per page. The value range is [1,20]. The default value is 20. This parameter is only required when retrieving by page.  |  No  |
| `cursor`     | String  | The start position of the next query. This parameter is only required when retrieving by page.    |  No  |

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :---- | :--- | :------------------- |
| `code`  | Number  | The HTTP status code.  |
| `count` | Number  | The number of users. |
| `users` | List | The detailed information of the users on the member list. |
| `cursor` | String  | The start position of the next query. |

For other parameters and detailed descriptions, see [Common parameters](#param).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/users?limit=1&cursor=XXX'
```

#### Response example

```json
{
    "code": 200,
    "count": 1,
    "users": [
        {
            "user_id" : "user1",
            "role" : 0
        }
    ],
    "cursor": "ZGNiMjRmNGY1YjczYjlhYTNkYjk1MDY2YmEyNzFmODQ6aXXXXXXXXXXXXXX"
}
```


## Check whether a user exists in a server

Checks whether a user exists in the specified server.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/circle/server/{server_id}/user/{user_id} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :---- | :--- | :------------------- |
| `code`   | Number     | The HTTP status code.    |
| `result` | Boolean | Whether the user exists in the server: <li>`true`: Yes.</li><li>`false`: No.</li>  |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/user/XXX'
```

#### Response example

```json
{
    "code": 200,
    "result": true
}
```


## Check the role of a user

Checks the role of the specified user in a server.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/circle/server/{server_id}/user/role?userId={user_id} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).


#### Query parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `userId` | String  |  The user ID.  | Yes  |


#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :---- | :--- | :------------------- |
| `code` | Number  | The HTTP status code.                                          |
| `role` | Number  | The role of the user in the server.<li>`0`: Owner.</li><li>`1`: Moderator.</li><li>`2`: Member.</li> |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X POST -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/user/role?userId=XXX'
```

#### Response example

```json
{
    "code": 200,
    "role" : 1
}
```


## Modify the role of a user

Modifies the role of the specified user in a server.

### HTTP request

```http
PUT https://{host}/{org_name}/{app_name}/circle/server/{server_id}/user/role?userId={user_id}&role={role} 
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Query parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `userId` | String  |  The user ID.  | Yes  |
| `role` | Number  | The role of the user. You can pass the following values:<li>`1`: Moderator.</li><li>`2`: Member.</li> <div class="alert note">An error occurs if you pass <code>0</code> representing the role of the server owner.</div>。| Yes ｜

#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code` | Number  | The HTTP status code. |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X PUT -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/user/role?userId=XXX&role=XXX'
```

#### Response example

```json
{
    "code": 200
}
```


### Remove a user from a server

Removes the specified user from a server.

Once a user is removed from a server, they are automatically removed all of that server's channels as well.

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/circle/server/{server_id}/user/remove?userId={user_id}
```

#### Path parameter

For the parameters and detailed descriptions, see [Common parameters](#param).

#### Query parameter

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `userId` | String    |  The user ID.  | Yes  |


#### Request header

| Parameter | Type | Description | Required |
| :----- | :----- | :------- | -------- |
| `Accept` | String | `application/json` | Yes  |
| `Authorization` | String | The authentication token of the user or administrator, in the format of `Bearer ${YourAppToken}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the response body contains the following fields:

|  Parameter  |  Type  |  Description  |
| :--- | :--- | :------------------ |
| `code` | Number  | The HTTP status code. |

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#status-codes) for possible reasons.

### Example

#### Request example

```shell
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer <YourAppToken>' 'http://XXX/XXX/XXX/circle/server/XXX/user/remove?userId=XXX'
```

#### Response example

```json
{
    "code": 200
}
```


## Status codes

For details, see [HTTP Status Codes](https://docs.agora.io/en/agora-chat/reference/http-status-codes).