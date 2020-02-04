# wsプッシュインターフェース

署名方式：<br>
1.httpアドレスのみに署名する<br>
例えば、この文字列のみに署名します：`GEThttps://api.fmex.com/v2/user/ws1579154149629`、署名結果を取得できます。署名方法はhttpリクエストとは同じです。<br>
2.署名結果はwsを介してサーバーに転送します。<br>
例：`wss://api.fmex.com/v2/user/ws?FC-ACCESS-KEY=f8wersd4sdfc7121cbdd81986&FC-ACCESS-TIMESTAMP=1579147466690&FC-ACCESS-SIGNATURE=aKx/DMQ6j75JwegtfDVuJaXRxoxA=`<br>
3.接続が成功すると、wsを介してコマンドを送信し、対応する情報を取得できます。<br>
<br>
その中、http地址：`https://api.fmex.com/v2/user/ws`<br>
wsアドレス：`wss://api.fmex.com/v2/user/ws`<br>

補足説明：このインターフェイスはインクリメントプッシュです。つまり、サブスクリプションが成功すると、変更があった場合のみ情報がユーザーにプッシュされます。

##アカウント情報の取得
websocketにリクエストされる情報は:

sub コマンドを送信する、topic: `account`  ( WebSocket サブスクリプションを参考してください)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["account"],
}
```
>WebSocket リクエストが成功したレスポンスは次のとおりです：

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
    "ts": 1580802053662  # 更新時間
}
```

##注文の取得
websocketにリクエストされる情報は:

sub コマンドを送信する、topic: `order.$symbol`  ( WebSocket サブスクリプションを参考してください)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["order.btcusd_p"],
}
```
>WebSocket リクエストが成功したレスポンスは次のとおりです：

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
##ポジションの取得
websocketにリクエストされる情報は:

sub コマンドを送信する、topic: `position.$symbol` ( WebSocket サブスクリプションを参考してください)

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["position.btcusd_p"],
}
```
>WebSocket リクエストが成功したレスポンスは次のとおりです：

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

