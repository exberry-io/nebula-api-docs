# Authentication API

## Access Token - Login Form&#x20;

Any user connected to Nebula will be provided with set of `user` and `password`, each user is assigned with the relevant permissions.

In order to access the authenticated endpoint, user needs to generate access token.

There are 2 options available:&#x20;

* Integrate with Auth0 universal login, please contact exberry for further information. This is recommended for UI applications.&#x20;
* Implement `Access Token - API` , this is recommended for API based applications. &#x20;

{% swagger method="post" path=" " baseUrl="https://admin-api-shared.staging.exberry-uat.io/trader-api/auth/token" summary="Access Token - API" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="body" name="email" type="String" required="true" %}
Trader email 
{% endswagger-parameter %}

{% swagger-parameter in="body" name="password" type="String" required="true" %}
Trader password
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success Response " %}
```json
{
    "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IkpHdW9lMFNQaTB1dXAwSTU5VmRGMiJ9.eyJodHRwczovL29tcy10cmFkaW5nL2FjY291bnRJZCI6OSwiaHR0cHM6Ly9vbXMtdHJhZGluZy91c2VySWQiOiI1IiwiaHR0cHM6Ly9vbXMtdHJhZGluZy9leGNoYW5nZUlkIjozMCwiaHR0cHM6Ly9vbXMtdHJhZGluZy9tcElkIjoyMDg3NTA1MzAzLCJpc3MiOiJodHRwczovL3RyYWRpbmctbWFzdGVyLWV4YmVycnkuZXUuYXV0aDAuY29tLyIsInN1YiI6ImF1dGgwfDYyNTg3YzExMzg0MWUzMDA2YTE4MGYxOSIsImF1ZCI6ImV4YmVycnktdHJhZGluZyIsImlhdCI6MTY1NzU1NjgyNCwiZXhwIjoxNjU3NjQzMjI0LCJhenAiOiJ1blpjOVZUcThXSkZzNHI0TmN1V01MSG8ySHNGa01NSCIsInNjb3BlIjoib21zLXRyYWRpbmc6KiBvbXMtYWNjb3VudDoqIG9tcy1oaXN0b3J5OioiLCJndHkiOiJwYXNzd29yZCJ9.X55d-ZvVgoILoIssxoSbL_fwN_COxDXkh805IVqbNKq1ZOhcnOykLgfFrjSB-ydtAB6L5jCUegtSnj8yIaDDNDRk_RmLEX5W6yepIeFMN2odhb-IVjUOlLvxvgouu6AZtYFccytKltCJTjsBfX3HURPkaSP-VBBGjlRwcHFDx9qX7HiVgtKosNiiRMryxPGiSU_5RDzmVjWmuo6NIAla23Z2e74bfT4NQHhbYyQ88-HI_D3nBuLeihdpH7brohyXnbjBBupQ89sCPBFF-a8WFDxlIAVXeBfHugcx7-9zEcXhU4FSsWNUVvfyUWVRDYLOCVIPkImCpN_3mMNqNRKhLQ",
    "expiresIn": 86400
}
```
{% endswagger-response %}

{% swagger-response status="403: Forbidden" description="Failure Response" %}
```javascript
{
    "code": 2,
    "message": "Wrong email or password"
}
```
{% endswagger-response %}
{% endswagger %}

**Response Parameters**

| Name      | Description                       |
| --------- | --------------------------------- |
| token     | Token to be used for any API call |
| expiresIn | Expiration time in seconds        |

## createSession

The `createSession` API lets you authenticate to Nebula API.

Prior to any exchange API call you must create a valid session, this session remains active as long as WebSocket connection remains open.&#x20;

{% hint style="info" %}
`qualifier:` v1/broker.oms/createSession
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
