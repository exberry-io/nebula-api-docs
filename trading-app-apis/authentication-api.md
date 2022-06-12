# Authentication API

## Access Token&#x20;

Any user connected to Nebula will be provided with set of `user` and `password`, each user is assigned with the relevant permissions.

In order to access the authenticated endpointד user needs to integrate with Auth0 universal login, please contact exberry for further information.&#x20;

## createSession

The `createSession` API lets you authenticate to Nebula API.

Prior to any exchange API call you must create a valid session, this session remains active as long as WebSocket connection remains open.&#x20;

{% hint style="info" %}
`endpoint:` v1/broker.oms/createSession
{% endhint %}

### **Request**

| Parameter | Type      | Description                 |
| --------- | --------- | --------------------------- |
| token     | JWT Token | Access token received Auth0 |

### **Error Codes**

| Code | Message                        |
| ---- | ------------------------------ |
| 1    | System is unavailable          |
| 6002 | Missing fields: ‘\[fieldName]’ |
| 6000 | Authentication failed          |

### **Samples**

{% tabs %}
{% tab title="Request" %}
```javascript
{
        "q":"v1/broker.oms/createSession",
        "sid":15,
        "d":{
                    "token":"Auth0Token"
            }
}
```
{% endtab %}

{% tab title="Success Response" %}
```javascript
{
  "sig": 1,
  "q": "v1/broker.oms/createSession",
  "sid": 1
}
```
{% endtab %}

{% tab title="Failure Response" %}
```javascript
{
  "sig": 2,
  "q": "v1/broker.oms/createSession",
  "errorType": "401",
  "sid": 1,
  "d": {
    "errorCode": 6000,
    "errorMessage": "Authentication failed"
  }
}
```
{% endtab %}
{% endtabs %}
