# ws接口

签名方式：<br>
1.只对http地址进行签名<br>
举例，只对这个字符串签名：`GEThttps://api.fmex.com/v2/user/ws1579154149629`，得到签名结果，签名方法与http请求相同<br>
2.将签名结果通过ws传递给服务器<br>
例如：`wss://api.fmex.com/v2/user/ws?FC-ACCESS-KEY=f8wersd4sdfc7121cbdd81986&FC-ACCESS-TIMESTAMP=1579147466690&FC-ACCESS-SIGNATURE=aKx/DMQ6j75JwegtfDVuJaXRxoxA=`<br>
3.连接成功后，就可以通过ws发送命令，获取相应信息<br>
<br>
其中，http地址：`https://api.fmex.com/v2/user/ws`<br>
ws地址：`wss://api.fmex.com/v2/user/ws`<br>

注：此接口为增量推送，即在订阅成功后，有变化时，才会有信息推送给用户

##获取账户信息
websocket 请求的数据为:

发送 sub 指令，topic: `account` (请参考 WebSocket 订阅)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["account"],
}
```
>WebSocket 请求成功的响应结果如下：

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
    "ts": 1580802053662  # 更新时间
}
```

##获取订单
websocket 请求的数据为:

发送 sub 指令，topic: `order.$symbol` (请参考 WebSocket 订阅)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["order.btcusd_p"],
}
```
>WebSocket 请求成功的响应结果如下：

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
##获取仓位
websocket 请求的数据为:

发送 sub 指令，topic: `position.$symbol` (请参考 WebSocket 订阅)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["position.btcusd_p"],
}
```
>WebSocket 请求成功的响应结果如下：

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

