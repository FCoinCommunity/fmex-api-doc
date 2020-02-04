# ws Push portal 

Signing method ：<br>
1.Only sign to http address <br>
For example，only sign to this character string：`GEThttps://api.fmex.com/v2/user/ws1579154149629`，and get the same signing result, the signing method is same with http request<br>
2. Pass the signing result to the server by ws <br>
For example：`wss://api.fmex.com/v2/user/ws?FC-ACCESS-KEY=f8wersd4sdfc7121cbdd81986&FC-ACCESS-TIMESTAMP=1579147466690&FC-ACCESS-SIGNATURE=aKx/DMQ6j75JwegtfDVuJaXRxoxA=`<br>
3.Commands can be sent through ws to get the information once the connection is successful <br>
<br>
Among，http address：`https://api.fmex.com/v2/user/ws`<br>
ws address：`wss://api.fmex.com/v2/user/ws`<br>

Note: this portal is the Incremental push, which means the information will be pushed to users only changes happened after success subscription.

##Obtain the account information
websocket the requested data is:

send sub commend，topic: `account` (please refer to WebSocket subsription)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["account"],
}
```
>The responding result of the successful WebSocket request is as follow：

```json

{
    "type": "account", 
    "account": 
    {
        "currency": "BTC", 
        "available": 1.34242880408925, 
        "frozen": 0.0, 
        "position": 9.747280117178e-06, 
        "updated_at": 1580802053643
    }, 
    "ts": 1580802053662  # Update time
}
```

##Obtain the order
websocket the requested data is:

send sub commend, topic: `order.$symbol` (please refer to WebSocket subsription)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["order.btcusd_p"],
}
```
>The responding result of the successful WebSocket request is as follow：

```json
{
    "type": "order.btcusd_p", 
    "order": 
    {
        "id": 849758102002, 
        "quantity": 1, 
        "direction": "LONG", 
        "features": 8, 
        "price": 10210, 
        "fill_price": 9288.0, 
        "unfilled_quantity": 0, 
        "symbol": "BTCUSD_P", 
        "margin_currency": "BTC", 
        "fee": 4.8449612403e-08, 
        "type": "MARKET", 
        "status": "FULLY_FILLED", 
        "trigger_direction": "LONG", 
        "trigger_on": 0, 
        "trailing_base_price": 0, 
        "trailing_distance": 0, 
        "created_at": 1580802204215
        }, 
    "ts": 1580802204232
}
```
##Obtain the position
websocket the requested data is:

send sub commend, topic: `position.$symbol`(please refer to WebSocket subsription)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["position.btcusd_p"],
}
```
>The responding result of the successful WebSocket request is as follow：

```json
{
    "type": "position.btcusd_p", 
    "position": 
    {
         "symbol": "BTCUSD_P", 
         "quantity": 1, 
         "direction": "LONG", 
         "leverage": 50, 
         "risk_level": 0, 
         "margin": 9.749658727683e-06,
          "taker_fee_rate": 0.00045, 
          "realized_pnl": -4.8499218623e-08, 
          "entry_price": 9278.5, 
          "liquidation_price": 9363.483892761602, 
          "bankruptcy_price": 8512.606170669584, 
          "minimum_maintenance_margin_rate": 0.1, 
          "updated_at": 1580802507384
    },
     "ts": 1580802507401
}
```

