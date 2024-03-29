---
description: Allows to send trading command and track your orders and balances
---

# Trading API

To properly connect Nebula Trading API, you need to authenticate via Auth0, once done, you should use the `createSession` method using the provided access token, now you are connected to Nebula Trading API and you are able to send command and get real time events on your orders changes.&#x20;

![](<../.gitbook/assets/image (1).png>)

## placeOrder

The `placeOrder` API lets you place a new order into Nebula.

If you send a valid order, you will receive ack response, this does not mean that order will be accepted by system, it only means that request is valid and was accepted,  to track the exact state of the order you need to subscribe upfront to `orders` stream where you'll get real time event with your order details and with its state, it can be "Rejected" if order was not accepted or one of the order states ("Added", Executed" or "Cancelled") that means that order was accepted by system.  &#x20;

Non-valid order will be responded with error message.&#x20;

Order types:

* **Limit**: Order is being sent with a specific price. A buy order will be executed with the requested price or lower price a sell order will be executed with the requested price or higher price.&#x20;
* **Market**: Order is attempted filled at the best price in the market. Partial filled is allowed. In case not all the amount can be filled, the residual amount will be cancelled.&#x20;

Time in force:

* **GTC** - Good Till Cancel (resting on book till cancellation)
* **GTD** - Good Till Date (at given time (expiryDate) order will be automatically cancelled).
* **FOK** - Fill Or Kill (all or nothing)
* **IOC** - Immediate Or Cancelled (allows partial fills)
* <mark style="color:blue;">NEW</mark> **DAY** - Order will be automatically cancelled when trading day is closed. &#x20;

<table><thead><tr><th width="117">Type</th><th width="97" align="center">GTC</th><th width="90" align="center">GTD</th><th width="85" align="center">FOK</th><th width="90" align="center">IOC</th><th>NEW DAY</th></tr></thead><tbody><tr><td>Limit</td><td align="center">✔️</td><td align="center">✔️</td><td align="center">✔️</td><td align="center">✔️</td><td>✔️</td></tr><tr><td>Market</td><td align="center">❌</td><td align="center">❌</td><td align="center">✔️</td><td align="center">✔️</td><td>❌</td></tr></tbody></table>

{% hint style="info" %}
`qualifier:` v1/broker.oms/placeOrder
{% endhint %}

### **Request Parameters**&#x20;

<table><thead><tr><th width="144.20631067961165">Parameter</th><th width="105">Type</th><th width="492.2">Description</th></tr></thead><tbody><tr><td>orderType</td><td>Enum</td><td>Order type Limit or Market</td></tr><tr><td>side</td><td>Enum</td><td>Order side Buy or Sell</td></tr><tr><td>instrument</td><td>String</td><td>Instrument identifier</td></tr><tr><td>quantity</td><td>Decimal</td><td>Order quantity.<br>Allowed precision: baseAsset.quantityPrecision  </td></tr><tr><td>price <code>optional</code></td><td>Decimal</td><td>The price of the Limit order. For Market order this will not be sent.<br>Allowed precision: quoteAsset.quantityPrecision </td></tr><tr><td>timeInForce</td><td>Enum</td><td><p>GTC, GTD, FOK, IOC, <mark style="color:blue;">NEW</mark> DAY for Limit order</p><p>FOK, IOC - for Market order</p></td></tr><tr><td><p>expiryDate</p><p><code>optional</code></p></td><td>UTC Time</td><td>Required only for GTD orders - expiration date and time in seconds, in GMT. <br>In case that trading is halted or market is closed at expiry date - the order will be cancelled (even that cancelOrder requests are not allowed).</td></tr></tbody></table>

### **Ack Response**

Empty response&#x20;

### **Error Codes**

<table><thead><tr><th width="110.89757058437291">Code</th><th width="568.7142857142858">Message</th></tr></thead><tbody><tr><td>1</td><td><code>System is unavailable</code></td></tr><tr><td>100</td><td><code>Missing or invalid parameter: [FieldName]</code></td></tr><tr><td>101</td><td><p><code>Instrument no found</code> </p><p><code>Account is not verified</code> </p><p><code>Instrument is not active</code><br><code>Missing fee setup</code></p></td></tr><tr><td>104</td><td><code>[ledgerSystem] account does not opt in to [AssetName]</code></td></tr><tr><td>105</td><td><code>Insufficient balance, [ order locked balance - account free balance] in [AssetName] are missing</code></td></tr><tr><td>200</td><td><p><code>Invalid session</code> </p><p><code>You don’t have the right permission</code></p></td></tr><tr><td>99</td><td><code>[ErrorMassage]</code></td></tr></tbody></table>

### **Samples**

{% tabs %}
{% tab title="Request" %}
```javascript
{
  "q": "v1/broker.oms/placeOrder",
  "sid": 5,
  "d": {
    "orderType": "Limit",
    "side": "Buy",
    "instrument": "Apple1-USD",
    "quantity": 1.9,
    "price": 0.9,
    "timeInForce": "GTC"
  }
}
```
{% endtab %}

{% tab title="Ack" %}
```javascript
{
  "sig": 1,
  "q": "v1/broker.oms/placeOrder",
  "sid": 5
}
```
{% endtab %}

{% tab title="Nack" %}
```javascript
{
  "sig": 2,
  "q": "v1/broker.oms/placeOrder",
  "errorType": "400",
  "sid": 5,
  "d": {
    "errorCode": 100,
    "errorMessage": "Missing or invalid parameter: orderType"
  }
}
```
{% endtab %}
{% endtabs %}

## cancelOrder

The `cancelOrder` API is used to request cancelation of active order, in case order was already partially executed only the remaining active account will be cancelled.&#x20;

If you send a valid cancel order request, you will receive ack response, this does not mean that order will be cancelled by system, it only means that request is valid and was accepted,  to track the exact state of the order you need to subscribe upfront to `orders` stream where you'll get real time event with your cancel order request details and with its state, it can be "Rejected" if cancel order request was not accepted or "Cancelled" in case that order was cancelled by system.  &#x20;

Non-valid request will be responded with error message.&#x20;

{% hint style="info" %}
`qualifier:` v1/broker.oms/cancelOrder
{% endhint %}

### **Request Parameters**&#x20;

<table><thead><tr><th width="146.7242848447961">Parameter</th><th width="90">Type</th><th width="357.2">Description</th></tr></thead><tbody><tr><td>orderId</td><td>Long</td><td>Nebula Order Id</td></tr><tr><td>instrument</td><td>String</td><td>Instrument identifier</td></tr></tbody></table>

### **Ack Response**

Empty response&#x20;

### **Error Codes**

<table><thead><tr><th width="100.57212713936428">Code</th><th width="492.7142857142857">Message</th></tr></thead><tbody><tr><td>1</td><td><code>System is unavailable</code></td></tr><tr><td>100</td><td><code>Missing or invalid parameter: [FieldName]</code></td></tr><tr><td>101</td><td><p><code>Instrument not found</code> </p><p><code>Instrument is not active</code> </p><p><code>orderId not found</code></p></td></tr><tr><td>200</td><td><code>Invalid session</code><br><code>You don’t have the right permission</code></td></tr><tr><td>99</td><td><code>[ErrorMessage]</code></td></tr></tbody></table>

### **Samples**

{% tabs %}
{% tab title="Request" %}
```javascript
{
  "q": "v1/broker.oms/cancelOrder",
  "sid": 31,
  "d": {
    "instrument": "Apple1-USD",
    "orderId": 368
  }
}
```
{% endtab %}

{% tab title="Ack" %}
```javascript
{
  "sig": 1,
  "q": "v1/broker.oms/cancelOrder",
  "sid": 31
}
```
{% endtab %}

{% tab title="Nack" %}
```javascript
{
  "sig": 2,
  "q": "v1/broker.oms/cancelOrder",
  "errorType": "400",
  "sid": 31,
  "d": {
    "errorCode": 100,
    "errorMessage": "Missing or invalid parameter: instrument"
  }
}
```
{% endtab %}
{% endtabs %}

## Orders Stream

`orders` stream allows you to track your own orders, its built from 3 parts:

* Get your current active orders&#x20;
* Snapshot end&#x20;
* Real time orders events&#x20;

When subscribing to this stream you will get list of all your active orders, when snapshot is done you will get "SanpshotEnd" message, from that moment you will get real time events for any trading activity in your account, this stream is the only way to track your trading command (place/ cancel order) and understand your orders state .

{% hint style="info" %}
`qualifier:` v1/broker.account/orders
{% endhint %}

### **Request Parameters**&#x20;

<table><thead><tr><th width="150">Parameter</th><th width="103.7242848447961">Type</th><th width="357.2">Description</th></tr></thead><tbody><tr><td>instrument <code>optional</code></td><td>String</td><td>Instrument identifier </td></tr></tbody></table>

### **Error Codes**

<table><thead><tr><th width="104.57212713936428">Code</th><th width="546.7142857142858">Message</th></tr></thead><tbody><tr><td>1</td><td><code>System is unavailable</code></td></tr><tr><td>100</td><td><code>Missing or invalid parameter: [FieldName]</code></td></tr><tr><td>200</td><td><code>Invalid session</code><br><code>You don’t have the right permission</code></td></tr></tbody></table>

### **Samples**

{% tabs %}
{% tab title="Request" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
        "instrument": "Apple1-USD"
  }
}
```
{% endtab %}

{% tab title="ActiveOrder" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
    "messageType": "ActiveOrder",
    "orderId": 355,
    "orderType": "Limit",
    "side": "Buy",
    "instrument": "Apple1-USD",
    "quantity": 3.2,
    "price": 5.8,
    "timeInForce": "GTC",
    "createdAt": 1648324604049207800,
    "filledQuantity": 2.5,
    "remainingOpenQuantity": 0.7,
    "removedQuantity": 0,
    "filledPrice": 5.8,
    "user": "34",
    "account": "Account1",
    "accountId": 4
  }
}
```
{% endtab %}

{% tab title="SnapshotEnd" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
    "messageType": "SnapshotEnd"
  }
}
```
{% endtab %}

{% tab title="Added" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
    "messageType": "Added",
    "orderId": 385,
    "orderType": "Limit",
    "side": "Buy",
    "instrument": "Apple1-USD",
    "quantity": 1.9,
    "price": 0.9,
    "timeInForce": "GTC",
    "createdAt": 1648394325457,
    "filledQuantity": 0,
    "remainingOpenQuantity": 1.9,
    "removedQuantity": 0,
    "filledPrice": 5.8,
    "user": "34",
    "account": "Account1",
    "accountId": 4
  }
}
```
{% endtab %}

{% tab title="Executed" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
    "messageType": "Executed",
    "orderId": 387,
    "orderType": "Limit",
    "side": "Sell",
    "instrument": "Apple1-USD",
    "quantity": 1.9,
    "price": 0.9,
    "timeInForce": "GTC",
    "createdAt": 1648394741368,
    "filledQuantity": 1.9,
    "remainingOpenQuantity": 0,
    "removedQuantity": 0,
    "user": "34",
    "account": "Account1",
    "accountId": 4,
    "filledPrice": 5.8,
    "lastFilledQuantity": 1.9,
    "lastFilledPrice": 9.77,
    "matchId": 69
  }
}
```
{% endtab %}

{% tab title="Cancelled" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
    "messageType": "Cancelled",
    "orderId": 385,
    "orderType": "Limit",
    "side": "Buy",
    "instrument": "Apple1-USD",
    "quantity": 1.9,
    "price": 0.9,
    "timeInForce": "GTC",
    "createdAt": 1648394325457,
    "filledQuantity": 0,
    "remainingOpenQuantity": 0,
    "removedQuantity": 0,
    "user": "34",
    "account": "Account1",
    "accountId": 4,
    "filledPrice": 0,
    "cancelledQuantity": 1.9
  }
}
```
{% endtab %}

{% tab title="Rejected" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
    "messageType": "Rejected",
    "orderType": "Limit",
    "side": "Buy",
    "instrument": "Apple1-USD",
    "quantity": 1.9,
    "price": 0.9,
    "timeInForce": "GTC",
    "user": "34",
    "account": "Account1",
    "accountId": 4,
    "errorCode": 99,
    "errorMessage": "Instrument trading is halted"
  }
}
```
{% endtab %}

{% tab title="Rejected(Cancellation)" %}
```json
{
  "q": "v1/broker.account/orders",
  "sid": 44,
  "d": {
    "messageType": "Rejected",
    "orderId": 368,
    "instrument": "Apple1-USD",
    "user": "34",
    "account": "Account1",
    "accountId": 4,
    "errorCode": 101,
    "errorMessage": "orderId not found"
  }
}
```
{% endtab %}
{% endtabs %}

## Balances

`balance` stream allows you to track your own balances, its built from 3 parts:

* Get your current balance&#x20;
* Snapshot end&#x20;
* Real time balance changed events&#x20;

When subscribing to this stream you will get list of all your balances, when snapshot is done you will get "SanpshotEnd" message, from that moment you will get real time events for any balance changes in your account.\
\
No request parameter for this API.

{% hint style="info" %}
`qualifier:` v1/broker.account/balance
{% endhint %}

### **Error Codes**

<table><thead><tr><th width="119.57212713936428">Code</th><th width="492.7142857142857">Message</th></tr></thead><tbody><tr><td>1</td><td><code>System is unavailable</code></td></tr><tr><td>100</td><td><code>Missing or invalid parameter: [FieldName]</code></td></tr><tr><td>200</td><td><code>Invalid session</code><br><code>You don’t have the right permission</code></td></tr></tbody></table>

### **Samples**

{% tabs %}
{% tab title="Request" %}
```json
{
  "q": "v1/broker.account/balance",
  "sid": 55,
  "d": {}
}
```
{% endtab %}

{% tab title="Balance" %}
```json
{
  "q": "v1/broker.account/balance",
  "sid": 55,
  "d": {
    "messageType": "Balance",
    "asset": "USD",
    "free": 1001.58,
    "locked": 8998.42
  }
}
```
{% endtab %}

{% tab title="SnapshotEnd" %}
```json
{
  "q": "v1/broker.account/balance",
  "sid": 55,
  "d": {
    "messageType": "SnapshotEnd"
  }
}
```
{% endtab %}
{% endtabs %}
