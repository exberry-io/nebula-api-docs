---
description: Allows to retrieve historical data from system such as orders and trades
---

# Reporting API

Reporting API enables Nebula account to easily retrieve orders and executions data.\
This API is close to real time API, allows to access historical data as well up to date data.&#x20;

Note: This API is accessible via a separated websocket endpoint, not the one used for Trading  API.

History API include pagination mechanism, so every call will return only the relevant subset of data per the request parameters.&#x20;

Similarly to the Trading API, same authentication approach is applicable to the Reporting API to retrieve historical data. Note that due to the fact this is a separated end point you must use the `createSession` method  separately from the one used for Trading API.   &#x20;

![](<../.gitbook/assets/image (3).png>)

## Orders

Any account can use the `orders` API  to retrieve the full list of all its own orders . &#x20;

{% hint style="info" %}
`endpoint:` v1/broker.account/ordersHistory
{% endhint %}

### Request

| Parameter  | Type       | Description                                                                                                                                                                      |
| ---------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| instrument | String     | <p>Optional <br>Instrument symbol</p>                                                                                                                                            |
| dateFrom   | Unix time  | <p>Optional, In seconds<br>Search for orders where createdAt ≥ dateFrom</p>                                                                                                      |
| dateTo     | Unix time  | <p>Optional, In seconds <br>Search for orders where createdAt &#x3C; dateTo</p>                                                                                                  |
| status     | Enum       | <p>Optional <br><strong></strong>Search for orders with that status:<br><strong>Inactive</strong> = Cancelled or Executed Empty <strong>Status</strong> = All statuses</p>       |
| orderBy    | Object     | <p>Optional <br>object with 2 parameters: field (String) direction (Asc, Desc) direction</p><p>If nothing or invalid field was sent the default is [timestamp, Desc]</p>         |
| limit      | Int        | <p>Optional <br>How many records to include in each page If nothing was sent default is 100 <br>Max value = 100</p>                                                              |
| offset     | Int        | <p>Optional <br>Which record to start send from (if you want page 3 and there are 50 records per page offset should be 150) If nothing was sent default is 0 (=first record)</p> |

### **Response**

`orders` response provides close to real time list of all current orders for the requested period with the entire details of those orders.

| Field                 | Description                                                                                                                                  |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| orderId               | Order ID                                                                                                                                     |
| instrument            | Same as in `placeOrder` request                                                                                                              |
| orderType             | Same as in `placeOrder` request                                                                                                              |
| side                  | Same as in `placeOrder` request                                                                                                              |
| price                 | Same as in `placeOrder` request                                                                                                              |
| quantity              | Same as in `placeOrder` request                                                                                                              |
| timeInForce           | Same as in `placeOrder` request                                                                                                              |
| expiryDate            | <p>Optional<br>Order expiry date (GMT)<br>Format: YYYY-MM-DDThh:mm:ss</p>                                                                    |
| createdAt             | Order creation timestamp (GMT), Unix timestamp in milliseconds                                                                               |
| filledPrice           | Weighted average filled price for all fills on that order $$Sum(event.executedQuantity * event.executedPrice)/Sum(event.executedQuantity)$$  |
| filledQuantity        | Total filled quantity                                                                                                                        |
| remainingOpenQuantity | <p>Remaining open quantity. </p><p><em></em><span class="math">quantity - filledQuantity - removedQuantity</span></p>                        |
| removedQuantity       | Quantity that was removed with modifyOrder request                                                                                           |
| cancelledQuantity     | Quantity that was cancelled                                                                                                                  |
| account               | Account name                                                                                                                                 |

### **Error Codes**

| Code | Message                                    |
| ---- | ------------------------------------------ |
| 1    | System is unavailable                      |
| 100  | Missing or invalid parameter: \[FieldName] |
| 101  | dateTo must be greater than dateFrom       |
| 10   | Permission denied                          |

### **Samples**

{% tabs %}
{% tab title="Request" %}
```json
{
  "q": "v1/broker.account/ordersHistory",
  "sid": 1,
  "d": {
  "status":"Active",
  "istrument":"Apple1-MDL",
  "dateFrom":1647532880,
    "dateTo":1647532889,
    "limit": 10,
    "offset": 0,
     "orderBy": {
      "field": "timestamp",
      "direction": "Asc"
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "q": "v1/broker.account/ordersHistory",
  "sid": 15,
  "d": {
    "orders": [
      {
        "account": "Dudi",
        "orderId": 21,
        "instrument": "Gold-USDT",
        "orderType": "Limit",
        "side": "Buy",
        "price": 3,
        "quantity": 3,
        "timeInForce": "GTC",
        "createdAt": 1649850511776,
        "filledPrice": 0,
        "filledQuantity": 0,
        "remainingOpenQuantity": 3,
        "removedQuantity": 0
      },
      {
        "account": "Dudi",
        "orderId": 20,
        "instrument": "Gold-USDT",
        "orderType": "Limit",
        "side": "Buy",
        "price": 44,
        "quantity": 1,
        "timeInForce": "GTC",
        "createdAt": 1649844830208,
        "filledPrice": 0,
        "filledQuantity": 0,
        "remainingOpenQuantity": 1,
        "removedQuantity": 0
      }
    ],
    "count": 7
  }
}
```
{% endtab %}
{% endtabs %}

## Trades

`trades` API  allows to retrieve the full list of all its own trades.  &#x20;

{% hint style="info" %}
`endpoint: v`1/broker.account/tradesHistory
{% endhint %}

### Request

| Parameter  | Type       | Description                                                                                                                                                                      |
| ---------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| instrument | String     | <p>Optional <br>Instrument symbol</p>                                                                                                                                            |
| dateFrom   | Unix time  | <p>Optional, In seconds<br>Search for orders where createdAt ≥ dateFrom</p>                                                                                                      |
| dateTo     | Unix time  | <p>Optional, In seconds<br>Search for orders where createdAt &#x3C; dateTo</p>                                                                                                   |
| orderBy    | Object     | <p>Optional <br>object with 2 parameters: field (String) direction (Asc, Desc) direction</p><p>If nothing or invalid field was sent the default is [timestamp, Desc]</p>         |
| limit      | Int        | <p>Optional <br>How many records to include in each page If nothing was sent default is 100 <br>Max value = 100</p>                                                              |
| offset     | Int        | <p>Optional <br>Which record to start send from (if you want page 3 and there are 50 records per page offset should be 150) If nothing was sent default is 0 (=first record)</p> |

### **Response**

`trades` response provides close to real time list of all trades for the requested period with the entire details of those trades.

| Field      | Description                                                                                                                                                    |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| account    | Account name                                                                                                                                                   |
| timestamp  | Trade timestamp (in microseconds)                                                                                                                              |
| tradeId    | Trade identifier                                                                                                                                               |
| instrument | Instrument symbol                                                                                                                                              |
| side       | Buy/ Sell                                                                                                                                                      |
| quantity   | Trade amount                                                                                                                                                   |
| price      | Trade price                                                                                                                                                    |
| orderId    | Order ID                                                                                                                                                       |
| makerTaker | <p><strong>Taker</strong> if order was never resting on the book for that trade <br><strong>Maker</strong> if order was resting on the book for that trade</p> |

### **Error Codes**

| Code | Message                                    |
| ---- | ------------------------------------------ |
| 1    | System is unavailable                      |
| 100  | Missing or invalid parameter: \[FieldName] |
| 101  | dateTo must be greater than dateFrom       |
| 10   | Permission denied                          |

### **Samples**

{% tabs %}
{% tab title="Request" %}
```jsonp
{
  "q": "v1/broker.account/tradesHistory",
  "sid": 1,
  "d": {
    "istrument": "Apple1-MDL",
    "dateFrom": 1647532880,
    "dateTo": 1647532889,
    "limit": 10,
    "offset": 0,
    "orderBy": {
      "field": "timestamp",
      "direction": "Asc"
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "q": "v1/broker.account/tradesHistory",
  "sid": 1,
  "d": {
    "trades": [
      {
        "account": "Dudi",
        "timestamp": 1648973531006,
        "tradeId": 6,
        "instrument": "Gold-USD",
        "side": "Buy",
        "quantity": 0.2,
        "price": 1410,
        "orderId": 1,
        "makerTaker": "Maker"
      },
      {
        "account": "Dudi",
        "timestamp": 1648973470264,
        "tradeId": 5,
        "instrument": "Gold-USD",
        "side": "Buy",
        "quantity": 0.1,
        "price": 1410,
        "orderId": 1,
        "makerTaker": "Maker"
      }
    ],
    "count": 2
  }
}
```
{% endtab %}
{% endtabs %}

##
