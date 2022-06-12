# Best Practices

## API Flows Best Practice

There are streams that need to be permanently kept open and some need to be unsubscribed. \
There are 2 possible ways to manage the streams:&#x20;

* Disconnect the websocket connection and reconnect&#x20;
* Unsubscribe to the not required streams&#x20;

Usually it is more common to use the second approach the sections below describe all the required action for the second approach (unsubscribe the not required streams)&#x20;

To unsubscribe to a stream you need to send request with (samples can be found on the below sections):&#x20;

* q= original q sent on subscription
* sid= original sid sent on subscription
* sig= 3

### Successful login&#x20;

After successful login to auth0, using the retrieved access\_token need to call the below APIs that are required to load the main Screen:

| Id  | Pre-Condition          | End Point       | Description & Sample                                                                                                                                                                                                                                                                              |
| --- | ---------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.1 | Auth0 successful login | MetaData (Rest) | Get list of instruments: `GET  /instruments`                                                                                                                                                                                                                                                      |
| 1.2 | Auth0 successful login | Trading (WS)    | <p>Create session for Trading WS:<br><code>{  "q": "v1/broker.oms/createSession",  "sid": 1,  "d": {    "token": "auth0Token"  }}</code></p>                                                                                                                                                      |
| 2   | 1.1 Completed          | MarketData (WS) | <p>Using the instrument list retrieved in point 1, need to permanently subscribe to light ticker stream to get prices:</p><p><code>{  "q": "v1/exchange.marketdata/lightTickers",  "sid": 11,  "d": {    "symbols": [      "Gold-USD",      "Silver-USD"    ],    "interval": 1000  }}</code></p> |
| 3   | 1.2 Completed          | Trading (WS)    | <p>Get balances per asset:</p><p><code>{  "q": "v1/broker.account/balance",  "sid": 10,  "d": {}}</code></p>                                                                                                                                                                                      |

### Entering Trading Screen&#x20;

When entering trading screen need to call the below APIs



| End Point       | Description & Sample                                                                                                                                                                                        |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MarketData (WS) | <p>Subscribe to trades stream: <br><code>{  "q": "v1/exchange.marketdata/liveTrades",  "sid": 50,  "d": {    "symbol": "Gold-USD",    "limit": 1000  }}</code></p>                                          |
| MarketData (WS) | <p>Subscribe to order book stream </p><p><code>{  "q": "v1/exchange.marketdata/partialOrderBook",  "sid": 20,  "d": {    "symbol": "Demo1-USD",    "levels": 5,    "interval": 1000  }}</code></p>          |
| Trading (WS)    | <p>Get list of active orders Get orders event (for place order and cancel order confirmations):</p><p><code>{  "q": "v1/broker.account/orders",  "sid": 30,  "d": {    "symbol": "Demo1-USD"  }}</code></p> |

### Leaving Trading Screen

When leaving trading screen need to call the below APIs



| End Point       | Description & Sample                                                                                                              |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| MarketData (WS) | <p>Unsubscribe from trades stream:<br> <code>{  "q": "v1/exchange.marketdata/liveTrades",  "sid": 50,  "sig":3}</code></p>        |
| MarketData (WS) | <p>Unsubscribe from order book stream:<br> <code>{ "q": "v1/exchange.marketdata/partialOrderBook", "sid": 20, "sig":3}</code></p> |
| Trading (WS)    | <p>Unsubscribe from orders stream:<br> <code>{  "q": "v1/broker.account/orders",  "sid": 30,   "sig":3}</code></p>                |

