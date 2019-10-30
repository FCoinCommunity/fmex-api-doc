# Market

## Market overview

Market is a fully public API, HTTP and WebSocket API are available for now.
WebSocket is recommended to ensure more timely access to the market.
The current public part can only obtain the market of the latest period to ensure the real-time performance of the market as far as possible. If you need to obtain the full volume or historical market, please consult `support@fmex.com`

All HTTP request URLs base is: `https://api.fmex.com`

All WebSocket request URL is: `wss://api.fmex.com/v2/ws`

Controlled terminology in following text:

- `topic` is the topic subscribed
- `symbol` means the currency of the transaction, the topic distinguish the currencies is at the end of the topic.  
- `ticker` market tick information include the latest closed price, the latest volume, BUY 1 & SELL 1 and the volume of the latest 24 hours.
- `depth` means the market depth, orderbook and order book entrance.
- `level` represents the type of market depth, such as' L20 ', 'L100'.
- `trade` means the latest fulfilled orders and transaction.
- `candle` means a diagram, a candle stick, a K line.
- `resolution`  means the type of candle chart, such as' M1 ', 'M15'. 
- `base volume` represents the volume of the base currency, such as  btc in btcusdt。
- `quote volume` refers to the volume of the quota currency, such as the volume of usdt in btcusdt
- `ts` represents the time of server push, which is numeric type in millisecond, unix epoch in millisecond.

## WebSocket connection established firstly

The server sends a welcome message when the connection for the first time is successful

The server returns information after the connection is successful:

```json
{
  "type":"hello",
  "ts":1523693784042
}
```

> * `ts`: represents the current time of server push.

## WebSocket connection remains - heartbeat

```python
# WebSocket send ping - heartbeat to server end 
import time
import fcoin

api = fcoin.authorize('key', 'secret', timestamp)
now_ms = int(time.time())
api.market.ping(now_ms)
```


After the connection is established between the WebSocket  Client and the WebSocket server, it is recommended that the WebSocket Client make a ping request to the server every *15s* (10 ~ 20s are recommended). If the server does not receive the ping request from the Client for a long time, it will actively disconnect.

### WebSocket request

Send **ping commend**: `{"cmd":"ping","args":[$client_ts],"id":"$client_id"}`

* `client_id`:  The server returns it as is if the client is  a custom id specified by the current request 
* `client_ts`: current time on client end

> ping command request example：

```json
{"cmd":"ping","args":[1540557696867],"id":"sample.client.id"}
```

> server return successfully ping commend：

```json
{
  "id":"sample.client.id",
  "type":"ping",
  "ts":1523693784042,
  "gap":112
}
```

> * `gap`: The time gap between the push server to process this statement and the client to transmit it.
> * `ts`:  The current time of the push server

<aside class="notice">
tip: Can obtain the time gap  between push server time and data transmission by the ts and gap the server returned by ping request 
</aside>

## WebSocket subscription

Send **sub commend**: `{"cmd":"sub","args":["$topic", ...],"id":"$client_id"}`

* `client_id`: The custom id specified by the client for the current request will be returned as is by the server
* `topic`: multiple topics to be subscribed, please separate them with English comma ', 'The maximum  can be subscribed is 20 

> sub commend request example (single topic)：

```json
{"cmd":"sub","args":["ticker.ethbtc"]}
```

> sub commend request example （multiple topics）：

```json
{"cmd":"sub","args":["ticker.ethbtc", "ticker.btcusdt"]}
```

> The responding result of the successful subscription are as follows：

```json
{
  "type": "topics",
  "topics": ["ticker.ethbtc", "ticker.btcusdt"]
}
```

> The responding result of the failed subscription are as follows：

```json
{
  "id":"invalid_topics_sample",
  "status":41002,
  "msg":"invalid sub topic, xxx.M1.xxx"
}
```

## Obtain ticker data

To make the ticker information group small and fast enough, we forced the use of a list format.

> Corresponding meaning description of the ticker list:

```json
[
  "Latest closed price",
  "Amount of last transaction",
  "Maximum price of buy 1",
  "Maximum amount of buy 1",
  "Minimum price of buy 1"",
  "Minimum amount of buy 1",
  "The closed price of 24 hours ago",
  "The highest price within 24 hours",
  "The lowest price within 24 hours",
  "Volume of base currency in 24 hours such as the btc btcusdt",
  "Volume of quota currency in 24 hours such as the used in btcusdt "
]
```


### HTTP request

`GET https://api.fmex.com/v2/market/ticker/$symbol`


>The result of HTTP request is noticed as follow：

```json
{
  "status": 0,
  "data": {
    "type": "ticker.btcusdt",
    "seq": 680035,
    "ticker": [
      7140.890000000000000000,
      1.000000000000000000,
      7131.330000000,
      233.524600000,
      7140.890000000,
      225.495049866,
      7140.890000000,
      7140.890000000,
      7140.890000000,
      1.000000000,
      7140.890000000000000000
    ]
  }
}
```

### WebSocket subscription

Send **sub commend**，topic: `ticker.$symbol`  (Please refer to `WebSocket subscription`)

> The result of WebSocket subscription is noticed as follow:

```json
{
  "type": "ticker.btcusdt",
  "seq": 680035,
  "ticker": [
    7140.890000000000000000,
    1.000000000000000000,
    7131.330000000,
    233.524600000,
    7140.890000000,
    225.495049866,
    7140.890000000,
    7140.890000000,
    7140.890000000,
    1.000000000,
    7140.890000000000000000
  ]
}
```

## Obtain the latest depth details

### HTTP request

`GET https://api.fmex.com/v2/market/depth/$level/$symbol`

`$level` included types (case sensitive)：

Type | Description
-------- | --------
`L20` | 20-level market depth 
`L150` | 150-level market depth. 

Among the push time of `L20` will be a bit earlier than `L150`, and the push frequency will be a bit more than `L150`, please use as appropriate for specific stress and situation.

> The result of WebSocket subscription is noticed as follow：

```json
{
  "status":0,
  "data":{
    "type": "depth.L20.ethbtc",
    "ts": 1523619211000,
    "seq": 120,
    "bids": [0.000100000, 1.000000000, 0.000010000, 1.000000000],
    "asks": [1.000000000, 1.000000000]
  }
}
```

### WebSocket subscription

Send **sub commend**，topic: `depth.$level.$symbol`   (please refer to `WebSocket subscription`)

> The result of WebSocket subscription is noticed as follow:

```json
{
  "type": "depth.L20.ethbtc",
  "ts": 1523619211000,
  "seq": 120,
  "bids": [0.000100000, 1.000000000, 0.000010000, 1.000000000],
  "asks": [1.000000000, 1.000000000]
}
```

> The corresponding array between bids and asks must be even number Buy (Sell)1 price,Buy (Sell)1 quantity, in order.



## Obtain the latest transaction details 

By comparing the size of the transaction id  to determine whether it is an updated transaction.
It should be noted that due to the trade to transaction process, the transaction id of the public market does not actually correspond to the transaction id in the clearing system.
Even if the transaction is a record, there is no guarantee that the id of the latest transaction will always be the same when retrieved. 


PS: It's guaranteed that the transaction id is kept constant in the historical market. {transaction id} is only used as a market update notification and should not be relied on for filing.


### HTTP request

`GET https://api.fmex.com/v2/market/trades/$symbol`

#### Request parameters(HTTP request)

Parameters | Defaults | description
--------- | ------- | -----------
before |  | Query the history trade of a certain id 
limit |  | 20  by default

### WebSocket request

Send **req commend **: `{"cmd":"req", "args":["$topic", limit],"id":"$client_id"}`

* `client_id`: The custom id specified by the client for the current request will be returned as is by the server
* `topic`: `trade.$symbol`
* `limit`: the closed transaction number need to be obtained.

> The result of WebSocket subscription is noticed as follow：

```json
{
  "id":null,
  "ts":1523693400329,
  "data":[
    {
      "amount":1.000000000,
      "ts":1523419946174,
      "id":76000,
      "side":"sell",
      "price":4.000000000
    },
    {
      "amount":1.000000000,
      "ts":1523419114272,
      "id":74000,
      "side":"sell",
      "price":4.000000000
    },
    {
      "amount":1.000000000,
      "ts":1523415182356,
      "id":71000,
      "side":"sell",
      "price":3.000000000
    }
  ]
}
```

### WebSocket subscription

send **sub commend**，topic: `trade.$symbol`   (please refer to `WebSocket subsription`)

* `symbol`: corresponding trading pairs

> The result of WebSocket subscription is noticed as follow:

```json
{
  "type":"trade.ethbtc",
  "id":76000,
  "amount":1.000000000,
  "ts":1523419946174,
  "side":"sell",
  "price":4.000000000
}
```

## Obtain Candle information

### HTTP request

`GET https://api.fmex.com/v2/market/candles/$resolution/$symbol`

#### Request parameters(HTTP request)

Parameters | Defaults | Description
--------- | ------- | -----------
before |  | Query the history Candle of a certain id 
limit |  | 20 by defaults

$resolution including types(case sensitive)：

Type     | description
-------- | --------
 `M1`    | 1 Minute
 `M3`    | 3 Minutes
 `M5`    | 5 Minutes
 `M15`   | 15 Minutes
 `M30`   | 30 Minutes
 `H1`    | 1 Hour
 `H4`    | 4 Hours
 `H6`    | 6 Hours
 `D1`    | 1 Day
 `W1`    | 1 Week
 `MN`    | 1 Month

### WebSocket request

Send **req commend**: `{"cmd":"req","args":["$topic",limit,before],"id":"$client_id"}`

- `client_id`: The custom id specified by the client for the current request will be returned as is by the server
- `topic`: `candle.$resolution.$symbol`
- `limit`: The candle number needs to be obtained
- `before`: Query the history Candle of a certain id 

>The responding result of the successful WebSocket request is as follow：

```json
{
  "id":"candle.M15.btcusdt",
  "data":[
    {
      "id":1540809840,
      "seq":24793830600000,
      "high":6491.74,
      "low":6489.24,
      "open":6491.24,
      "close":6490.07,
      "count":26,
      "base_vol":8.2221,
      "quote_vol":53371.531286
    },
    {
      "id":1540809900,
      "seq":24793879800000,
      "high":6490.47,
      "low":6487.62,
      "open":6490.09,
      "close":6487.62,
      "count":23,
      "base_vol":10.8527,
      "quote_vol":70430.840624
    }
  ]
}
```

### WebSokcet subscription

Send **sub commend**，topic: `candle.$resolution.$symbol`   (please refer to `WebSocket subsription`)

* `resolution`： Same as HTTP request resolution parameter

>The notice of the WebSocket subscription result is as follow:

```json
{
  "type":"candle.M1.ethbtc",
  "id":1523691480,
  "seq":11400000,
  "open":2.000000000,
  "close":2.000000000,
  "high":2.000000000,
  "low":2.000000000,
  "count":0,
  "base_vol":0,
  "quote_vol":0
}
```


## Obtain the current system index

### HTTP request
`GET https://api.fmex.com/v2/market/indexes`



## Obtain the latest value of a certain index
### HTTP request
`GET https://api.fmex.com/v2/market/indexes/$indexname`


## Obtain fee rate
### HTTP request
`GET https://api.fmex.com/v2/market/fex`
