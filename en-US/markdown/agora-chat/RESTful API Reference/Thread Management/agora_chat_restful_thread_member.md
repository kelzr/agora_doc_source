## Manage Thread Members

This page shows how to manage thread members by calling the Agora Chat RESTful APIs. Before calling the following methods, ensure that you understand the call frequency limit described in [Limitations](./agora_chat_limitation?platform=RESTful#call-limit-of-server-side).

## Common parameters

### Request parameters <a name="request"></a>

| Parameter | Type | Description | Required |
|:----------|:-----|:------------|:---------|
| `host` | String | The domain name assigned by the Agora Chat service to access RESTful APIs. For how to get the domain name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `org_name` | String | The unique identifier assigned to each company (organization) by the Agora Chat service. For how to get the organization name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |
| `app_name` | String | The unique identifier assigned to each app by the Agora Chat service. For how to get the app name, see [Get the information of your project](./enable_agora_chat?platform=RESTful#get-the-information-of-the-agora-chat-project). | Yes |

### Response parameters <a name="response"></a>

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `action` | String | The HTTP request method. |
| `organization` | String | The unique identifier assigned to each company (organization) by the Agora Chat service. This is the same as `org_name`. |
| `applicationName` | String | The unique identifier assigned to each app by the Agora Chat service. This is the same as `app_name`. |
| `data` | String | The details of the response. |
| `duration` | String | The duration (ms) from when the HTTP request is sent to the time the response is received. |
| `timestamp` | String | The Unix timestamp (ms) of the HTTP response. |
| `uri` | String | The request URI, which is part of the request URL. You can safely ignore this parameter. |
| `entities` | String | The request entity. |
| `properties` | String | The request property. |


## Authorization <a name="auth"></a>

Agora Chat RESTful APIs require Bearer HTTP authentication. Every time an HTTP request is sent, the following Authorization field must be filled in the request header:

```
Authorization: Bearer ${YourAppToken}
```

The authentication token of the user or administrator, in the format of `Bearer ${token}`, where `Bearer` is a fixed character, followed by an English space, and then the obtained token value. In order to improve the security of the project, Agora uses a token (dynamic key) to authenticate users before they log in to the chat system. The Agora Chat RESTful API only supports authenticating users using app tokens. For details, see [Authentication using App Token](./generate_app_tokens).


## Retrieving thread members

Retrieves all the members in the specified thread.

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
GET https://{host}/{org_name}/{app_name}/thread/{thread_id}/users?limit={N}&cursor={cursor}
```

#### Path parameter

| Parameter | Type | Description | Required |
|:------------|:-------|:-----|:-----------|
| `thread_id` | String | The ID of the thread. | Yes |

For the descriptions of the other path parameters, see [Common Parameters](#request).


#### Query parameter

| `limit` | String | The maximum number of threads to retrieve per page. The range is [1, 50]. The default value is 50. | No |
| `cursor` | String | The page from which to start retrieving threads. Pass in `null` or an empty string at the first query. | No |


#### Request header

For the descriptions of the request headers, see [Authorization](#auth).

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the data field in the response body contains the following parameters:

| Parameter      | Type           | Description |
| :------- |:-------------|:-------------|
| `affiliations` | List | The usernames of the members in the thread. | 
| `properties.cursor` | String | The cursor that indicates the starting position of the next query. |

For other fields and descriptions, see [Common parameters](#response).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#Status-codes) for possible causes.


### Example

#### Request example

```json
curl -X GET http://XXXX.com/XXXX/testapp/thread/177916702949377/users -H 'Authorization: Bearer <YourAppToken>'
```

#### Response example

```json
{
    "action": "get",
    "data": {
        "affiliations": [
            "test4"
        ]
    },
    "duration": 4,
    "properties": {
        "cursor": "ZGNiMjRmNGY1YjczYjlhYTNkYjk1MDY2YmEyNzFmODQ6aW06dGhyZWFkOmVhc2Vtb2ItZGVtbyN0ZXN0eToyMA"
    },
    "timestamp": 1650872048366,
    "uri": "http://XXXX.com/XXXX/testy/thread/179786360094768/users"
}
```

## Adding multiple users to a thread

Adds multiple users to the specified thread. You can add a maximum of 10 users to a thread at each call.

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
POST https://{host}/{org_name}/{app_name}/thread/{thread_id}/users
```

#### Path parameter

| Parameter | Type | Description | Required |
|:------------|:-------|:-----|:-----------|
| `thread_id` | String | The ID of the thread. | Yes |

For the descriptions of the other path parameters, see [Common Parameters](#request).

#### Request header

For the descriptions of the request headers, see [Authorization](#auth).

#### Request body

| Parameter | Type | Description | Required |
|:------------|:-------|:-----|:-----------|
| `usernames` | List | The usernames of the members in the thread. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the `data` field in the response body contains the following parameter:

| Parameter      | Type           | Description |
| :------- |:-------------|:-------------|
| `status` | String | Whether the users are successfully added to the thread. `ok` indicates that the addition succeeds; otherwise, you can troubleshoot according to the returned reasons. | 

For other fields and descriptions, see [Common parameters](#response).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#Status-codes) for possible causes.

### Example

#### Request example

```json
curl -X POST http://XXXX.com/XXXX/testapp/thread/177916702949377/users -d '{
"usernames": [
"test2",
"test3"
]
}' -H 'Authorization: Bearer <YourAppToken>'
```

#### Response example

```json
{
    "action": "post",
    "applicationName": "testapp",
    "data": {
        "status": "ok"
    },
    "duration": 1069,
    "organization": "XXXX",
    "timestamp": 1650872649160,
    "uri": "http://XXXX.com/XXXX/testy/thread/179786360094768/joined_thread"
}
```

## Removing multiple thread members

Removes multiple users from the specified thread. You can remove a maximum of 10 users from a thread at each call.

For each App Key, the call frequency limit of this method is 100 per second.

### HTTP request

```http
DELETE https://{host}/{org_name}/{app_name}/threads/{thread_id}/users
```

#### Path parameter

| Parameter | Type | Description | Required |
|:------------|:-------|:-----|:-----------|
| `thread_id` | String | The ID of the thread. | Yes |

For the descriptions of the other path parameters, see [Common Parameters](#request).

#### Request header

For the descriptions of the request headers, see [Authorization](#auth).

#### Request body

| Parameter | Type | Description | Required |
|:------------|:-------|:-----|:-----------|
| `usernames` | List | The usernames of the members in the thread. | Yes |

### HTTP response

#### Response body

If the returned HTTP status code is `200`, the request succeeds, and the data field in the response body contains the following parameters:

| Parameter      | Type           | Description |
| :------- |:-------------|:-------------|
| `result` | Bool | Whether the specified thread member is removed from the thread:<li>`true`: Yes.<li>`false`: No.| 
| `user` | List | The username of the removed member in the thread. | 

For other fields and descriptions, see [Common parameters](#response).

If the returned HTTP status code is not `200`, the request fails. You can refer to [Status codes](#Status-codes) for possible causes.

### Example

#### Request example

```json
curl -X DELETE http://XXXX.com/XXXX/testapp/thread/177916702949377/users -H 'Authorization: Bearer <YourAppToken>'
```

#### Response example

```json
{
    "action": "delete",
    "applicationName": "testy",
    "duration": 12412,
    "entities": [
        {
            "result": false,
            "user": "test2"
        },
        {
            "result": false,
            "user": "test6"
        }
    ],
    "organization": "XXXX",
    "timestamp": 1650874050419,
    "uri": "http://XXXX.com/XXXX/testy/thread/179786360094768/users"
}
```

## Status codes

For details, see [HTTP Status Codes](./agora_chat_status_code?platform=RESTful).