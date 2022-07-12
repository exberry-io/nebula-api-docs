# Meta Data API

The meta data API allows you to get nebula meta data and markets setup details.\
This is a REST API authenticated via the Bearer Token mentioned in the authentication API

### Instruments API

Instruments API allows you to get list of tradable instruments with all relevant information.&#x20;

Instrument fields:&#x20;

{% swagger method="get" path="" baseUrl="https://admin-api-shared.staging.exberry-uat.io/trader-api/broker/instruments" summary="Get Instruments" %}
{% swagger-description %}
Get instruments list with their details 
{% endswagger-description %}

{% swagger-parameter in="header" required="true" name="Authorization" %}
"Bearer " + access_token
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success Response " %}
```javascript
[
  {
    "id": "e12abef8-2e16-4b84-b542-e1ff4fd4ab4a",
    "symbol": "Asset1-USD",
    "status": "Active",
    "baseAsset": "Asset1",
    "quoteAsset": "USD",
    "orderFlow": "None",
    "tradeFlow": "None",
    "description": "Asset1 vs USD",
    "imageUrls": [
      "https://exberry.io/wp-content/uploads/elementor/thumbs/logo-black-pkso2isujdormloa3oce24vz38sqamtcbq29sn5yko.png",
      "https://exberry.io/wp-content/uploads/2022/02/nebula-768x413.jpg"
    ],
    "quantityPrecision": 2
  },
  {
    "id": "4ecb5184-ae0f-42e4-9f20-654ae342c71d",
    "symbol": "Asset2-USD",
    "status": "Active",
    "baseAsset": "Asset2",
    "quoteAsset": "USD",
    "orderFlow": "None",
    "tradeFlow": "None",
    "description": "Asset2-USD is dummy instrument ",
    "imageUrls": [
      "data:image/jpeg;base64,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"
    ],
    "quantityPrecision": 5
  }
]
```
{% endswagger-response %}

{% swagger-response status="401: Unauthorized" description="" %}
```javascript
{
    "code": 10000,
    "message": "Invalid token"
}
```
{% endswagger-response %}
{% endswagger %}

| Field                  | Type   | Description                                |
| ---------------------- | ------ | ------------------------------------------ |
| id                     | int    | Instrument Id                              |
| symbol                 | String | Instrument symbol                          |
| status                 | eNum   | “Active”/ “Disabled”                       |
| baseAsset              | String | Base asset name                            |
| quoteAsset             | String | Quote asset name                           |
| orderFlow              | eNum   | Not relevant, ignore                       |
| tradeFlow              | eNum   | Not relevant, ignore                       |
| description `optional` | String | Instrument description                     |
| imageUrls `optional`   | \[]Url | List of instrument images urls             |
| quantityPrecision      | Int    | Allowed decimal places for orders quantity |

&#x20;
