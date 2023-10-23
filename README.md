# Introduction

The Nebula by Exberry API offers a complete suite of APIs allows exberry client to build trading UI application or allow their client to trade and access Nebula via API.

It includes real time market data as well as the ability to trade and access historical private data.

<img src=".gitbook/assets/image (4).png" alt="" data-size="original">

Client Application should redirect to Auth0 login form, as a response client application will be able to retrieve:   &#x20;

* User details&#x20;
* `access_token` : Bearer token that is required for the `createSession` API to authenticate to Exberry.&#x20;

Using the `access_token` client application can now access all the relevant Exberry Gateways and start getting relevant data as well as sending trading commands.  &#x20;

### Endpoints

<table><thead><tr><th width="155.28935266629296">GW</th><th width="377.41943224298006">Endpoint</th></tr></thead><tbody><tr><td>Trading</td><td><code>wss://nebula.uat.exberry-uat.io/</code></td></tr><tr><td>History &#x26; Market Data</td><td><code>wss://api-gateway.uat.exberry-uat.io</code></td></tr><tr><td>Metadata</td><td><code>https://admin-api.uat.exberry-uat.io/trader-api/broker</code></td></tr></tbody></table>
