---
description: Allows to get market data from system
---

# Market Data API

Market data API is using the same websocket endpoint as the Reporting API.

## partialOrderBook

This API allows easy refresh of the book state without the need to calculate the entire book, it allows consumers to subscribe to some price levels in the book.

{% hint style="info" %}
`qualifier:` v1/exchange.marketdata/partialOrderBook
{% endhint %}

### **Request**

| Parameter | Type   | Description                                                      |
| --------- | ------ | ---------------------------------------------------------------- |
| symbol    | String | Symbol to retrieve the light tickers for                         |
| levels    | eNum   | Price level to be returned , values can be : 1,5,10,20,100       |
| interval  | eNum   | Response interval in milliseconds, allowed values: 100,1000,2000 |
| decimals  | Int    | Define the grouping decimal places (empty means no grouping)     |

### **Response**

| Parameter                 | Type           | Description                                      |
| ------------------------- | -------------- | ------------------------------------------------ |
| symbol                    | String         | Instrument symbol                                |
| timeStamp                 | Unix timestamp | Timestamp where message was sent                 |
| bids                      | \[]priceLevel  | Array of bids, sorted by price level descending  |
| asks                      | \[]priceLevel  | Array of asks, sorted by price level ascending   |
| priceLevel.price          | Decimal        | Price level according to the decimal in request  |
| priceLevel.quantity       | Decimal        | Aggregated quantity for that price level         |
| priceLevel.numberOfOrders | Int            | Aggregated number of orders for that price level |

### **Error Codes**

| Code | Message                                                                                |
| ---- | -------------------------------------------------------------------------------------- |
| 1    | System is unavailable                                                                  |
| 2    | Missing fields: \[Fieldname]                                                           |
| 3    | <p>Wrong levels |<br>Wrong interval |<br>Wrong symbol [symbol] |<br>Wrong decimals</p> |
| 100  | Your connection is slow, please reduce data consumed                                   |

****

### **Samples**

{% tabs %}
{% tab title="Subscription" %}
```javascript
{
  "q": "v1/exchange.marketdata/partialOrderBook",
  "sid": 10,
  "d": {
    "symbol": "INS1",
    "levels": 5,
    "interval": 1000,
    "decimals": 2
  }
}
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
  "q": "v1/exchange.marketdata/partialOrderBook",
  "sid": 10,
  "d": {
    "symbol": "AMZ",
    "timeStamp": 1646549579452,
    "bids": [
      [
        100.5,  //Price level
        10.25,  //Total quantity 
        1       //number of orders 
      ],
      [
        100.33,
        6.5,
        5
      ],
      [
        100,
        93.0049,
        1
      ],
      [
        97,
        99.0038,
        2
      ],
      [
        96,
        123.0193,
        3
      ]
    ],
    "asks": [
      [
        100.6,
        1.3,
        1
      ],
      [
        101,
        1.3,
        1
      ]
    ]
  }
}
```
{% endtab %}

{% tab title="Empty Response" %}
```javascript
{
  "q": "v1/exchange.marketdata/partialOrderBook",
  "sid": 13,
  "d": {
    "symbol": "INS3",
    "timeStamp": 1646667453613,
    "bids": [],
    "asks": []
  }
}
```
{% endtab %}
{% endtabs %}

## lightTickers

This API allows easy refresh of the instruments market data.

{% hint style="info" %}
`qualifier:` v1/exchange.marketdata/lightTickers
{% endhint %}

### **Request**

| Parameter | Type      | Description                                                      |
| --------- | --------- | ---------------------------------------------------------------- |
| symbols   | \[]String | List of symbols to retrieve the light tickers for                |
| interval  | eNum      | Response interval in milliseconds, allowed values: 100,1000,2000 |

### **Response**

| Parameter   | Type           | Description                                                 |
| ----------- | -------------- | ----------------------------------------------------------- |
| symbol      | String         | Instrument symbol                                           |
| lastPrice   | Decimal        | last execution price                                        |
| bidPrice    | Decimal        | Highest bid price                                           |
| bidQuantity | Decimal        | Sum of quantity of all orders with `bidPrice`               |
| askPrice    | Decimal        | Lowest ask price                                            |
| askQuantity | Decimal        | Sum of quantity of all orders with the `askPrice`           |
| timeStamp   | Unix timestamp | Latest timestamp where one of the above values was changed  |

### **Error Codes**

| Code | Message                                          |
| ---- | ------------------------------------------------ |
| 1    | System is unavailable                            |
| 2    | Missing fields: \[Fieldname]                     |
| 3    | <p>Wrong interval |<br>Wrong symbol [symbol]</p> |

### **Samples**

{% tabs %}
{% tab title="Subscription" %}
```javascript
{
  "q": "v1/exchange.marketdata/lightTickers",
  "sid": 10,
  "d": {
    "symbols": ["AMZN","INS1"],
    "interval": 2000
  }
}
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
  "q": "v1/exchange.marketdata/lightTickers",
  "sid": 10,
  "d": {
    "symbol": "AMZ",
    "lastPrice": 100.5,
    "bidPrice": 100.5,
    "bidQuantity": 10.25,
    "askPrice": 100.6,
    "askQuantity": 1.3,
    "timeStamp": 1646549579452
  }
}
```
{% endtab %}

{% tab title="Empty Response" %}
```javascript
{
  "q": "v1/exchange.marketdata/lightTickers",
  "sid": 10,
  "d": {
    "symbol": "INS10",
    "bidQuantity": 0,
    "askQuantity": 0,
    "timeStamp": 1646661691612
  }
}
```
{% endtab %}
{% endtabs %}

Market data API is using the same websocket endpoint as the Reporting API.

## liveTrades

This API allows to get close to real time trades details happen on the market, you can retrieve limited amount last trades the were already happen and right after you will get real time event for new trades.\
Trade will come sorted by time, old trades will come first. \
When snapshot is completed, the last trade will be sent again, but quantity field will be set to 0.&#x20;

{% hint style="info" %}
`qualifier:` v1/exchange.marketdata/liveTrades
{% endhint %}

### **Request**

| Parameter | Type   | Description                                                                          |
| --------- | ------ | ------------------------------------------------------------------------------------ |
| symbol    | String | Symbol to retrieve the light tickers for                                             |
| limit     | eNum   | <p>Number of past trades (sorted by time descending). </p><p>0 ≤ limit ≤ 10,000 </p> |

### **Response**

<table><thead><tr><th data-type="number">Index</th><th>Parameter</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td>1</td><td>price</td><td>Decimal</td><td>Trade price</td></tr><tr><td>2</td><td>quantity</td><td>Decimal</td><td>Trade quantity, for snapshot end message this will be set to 0.  </td></tr><tr><td>3</td><td>makerSide</td><td>Decimal</td><td>If resting order is Buy→ 1 else 0</td></tr><tr><td>4</td><td>timeStamp</td><td>Unix timestamp</td><td>Trade timestamp (milliseconds)</td></tr></tbody></table>

### **Error Codes**

| Code | Message                                         |
| ---- | ----------------------------------------------- |
| 1    | System is unavailable                           |
| 2    | Missing fields: \[Fieldname]                    |
| 3    | <p>Wrong limit |<br>Wrong symbol [symbol] |</p> |

****

### **Samples**

{% tabs %}
{% tab title="Subscription" %}
```json
{
  "q": "v1/exchange.marketdata/liveTrades",
  "sid": 153,
  "d": {
    "symbol": "AMZ",
    "limit": 100
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "q": "v1/exchange.marketdata/liveTrades",
  "sid": 153,
  "d": [
    3.2,           //price
    1.21,          //quantity
    1,             //makerSide
    1648969398501  //timestamp
  ]
}
```
{% endtab %}

{% tab title="End Of Snapshot" %}
```javascript
{
  "q": "v1/exchange.marketdata/liveTrades",
  "sid": 153,
  "d": [
    3.2,
    0, //This means snapshot is completed
    1,
    1648969398501
  ]
}
```
{% endtab %}

{% tab title="Empty Instrument" %}
```json
{
  "q": "v1/exchange.marketdata/liveTrades",
  "sid": 153,
  "d": [
    0,
    0, //This means snapshot is completed
    0,
    0
  ]
}
```
{% endtab %}
{% endtabs %}

