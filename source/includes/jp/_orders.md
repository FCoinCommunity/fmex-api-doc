# 注文

## 注文モデルに関する説明

注文モデルは、次の属性で構成されています：

属性 | タイプ | 意味
---------- | ------- | -------
`id`	|long	|注文ID
`sequence_id`|	long|	シーケンスID
`margin_currency_id`	|long|	証拠金Currency ID
`direction`|	enum|	ポジションの方向LONG/SHORT
`type`|	enum	|注文タイプLIMIT/MARKET
`status`	|enum	|注文状態に関する説明をご参照ください
`features`	|long	|注文特性、各bitはぞれぞれの特性を表す：0x01=FOK、0x02=post_only、0x04=Hidden、0x08=IOC、0x8000=清算注文
`price`	|decimal|	指値注文価格（成行注文の場合、市場価格の上限または下限を示す。）
`quantity`|long	|注文数量
`fill_price`|decimal|成約の平均価格
`unfilled_quantity`|	long|	未成約の数量
`maker_fee_rate`|decimal|	Makerの手数料レート
`taker_fee_rate`|	decimal|	Takerの手数料レート
`fee`|decimal|	この注文で徴収された手数料
`trigger_on`|decimal|	Stop注文のトリガー価格です。Stop以外の注文のトリガー価格は常に0です。
`trailing_base_price`|decimal|	TrailingStop注文の基準価格です。このタイプ以外の注文は常に0です。
`trailing_distance`|	decimal|	TrailingStop注文のトリガー価格との距離です。このタイプ以外の注文は常に0です。
`created_at`|long|	注文作成のタイムスタンプ
`updated_at`|long	|注文更新のタイムスタンプ

注文状態に関する説明：

属性  | 説明
----------- | -------
`PENDING`| 成約待ち
`PARTIAL_FILLED`| 部分成約
`FULLY_FILLED`| 全部成約
`PARTIAL_CANCELLED`| 部分キャンセル
`FULLY_CANCELLED`| 全部キャンセル
`STOP_PENDING`|stop注文はトリガーを待っています
`STOP_FAILED`| stop注文はトリガーされたが、実行が失敗しました（例：証拠金凍結が失敗になりました）
`STOP_CANCELLED`| stop注文はトリガーされないため、キャンセルされました

## 新規注文

このAPIを使用して、新規注文を出すことができます。

### HTTP Request

`POST /v3/contracts/orders`

### リクエスト・パラメータ

パラメータ | 必須かどうか | 説明
--------- | ------- | -----------
`symbol`|	Y	|	契約コード、例えば"BTCUSD_P"
`type	`|	Y	|	注文タイプ、OrderTypeをご参照ください
`direction`	|	Y		|注文方向、Directionをご参照ください
`source`|			""	|注文ソースマーク、例えば"WEB"、"APP"、文字と数字の組み合わせ
`price`	|	指値注文のみ	|	指値注文価格、小数点以下1桁のみサポートする
`quantity`	|	Y	|	注文数量、整数であり、最小値は1である
`trigger_on`		|	N |	利食いと損切り注文のトリガー価格
`trigger_direction` |	N | 利食いと損切り注文のトリガー条件、"long"、"short"
`trailing_distance`	|	N	|	トレーリング損切り注文のトリガー価格までの距離
`fill_or_kill	`|		N|	タイプはstringであり、FOK注文を設定するかどうか
`immediate_or_cancel`		|	N	| タイプはstringであり、IOC注文を設定するかどうか
`post_only	`	|	N|	タイプはstringであり、post_only注文を設定するかどうか
`hidden`		|	N	| タイプはstringであり、Hidden注文を設定するかどうか
`reduce_only	`	|	N	| タイプはstringであり、reduce_only注文を設定するかどうか


ご注意：

注文タイプが「LIMIT」の場合、priceを入力する必要があります。

trigger_onとtrailing_distanceを同時に入力することができません。

fill_or_kill=trueの場合、immediate_or_cancel、post_only、hidden及びreduce_onlyを設定することができません。

immediate_or_cancel=trueの場合、fill_or_kill、post_only、hidden及びreduce_onlyを設定することができません。

post_only=trueの場合、fill_or_kill、immediate_or_cancel及びreduce_onlyを設定することができません。

hidden=trueの場合、fill_or_killとimmediate_or_cancelを設定することができません。

MARKET注文とIOC注文のみ、reduce_only=trueを設定できます。

例を挙げて、普通の利食いと損切り注文の場合：

{

type: "MARKET",

quantity: 10,

trigger_on: 8000,

trigger_direction: "LONG",

direction: "LONG",

... }

この例の中で、価格が8000及びそれ以上になる場合、10USDT相当する成行ロングの契約はマーケットに出されます。trigger_directionがshortであり、つまり、価格が8000以下である場合、10USDT相当する成行ショートの契約はマーケットに出されます。

trailing stopの場合：

{

type: "MARKET",

quantity: 10,

trailing_distance: 100,

trigger_direction: "SHORT",

direction: "SHORT",

... }

ユーザーがこのタイプの注文を出すと、10USDT相当する成行ロングの注文は最新価格がtrailing_distanceより100USDT以上下がる場合、出されます。

ただし、最新価格が上がる場合、この注文はこの価格に追いつけ、さらに、最新価格が最高価格になり、trailing_distanceより100USDT以上下がる場合、執行されます。

trigger_direction及びdirectionが同時にlongである場合、つまり：ユーザーがこのタイプの注文を出すと、10USDT相当する成行ロングの注文は、最新価格がtrailing_distanceより100USDT以上上がる場合、出されます。


### APIレスポンス：
```
{
  "status": 0,
  "data": {
    "id": 167624805711936,  #APIによって計算された分散型のID
    "sequenceId": 260087,  #sequenceによって計算されたsequenceId
    "type": "LIMIT",  #注文タイプ LIMIT/MARKET
    "status": "PENDING",  #注文状態
    "direction": "SHORT", #注文方向LONG/SHORT
    "features": 0,        #注文特性、bitにより組み合わせ：FOK/IOC/HIDDEN/post_only
    "price": 100.0,     #指値価格、LIMIT注文に限定されています
    "quantity": 200,    #数量
    "unfilled_quantity": 200, #未成約数量
    "maker_fee_rate": -0.00025,#maker取引手数料レート
    "taker_fee_rate": 0.001,   #taker取引手数料レート
    "fee": 0,                #注文で徴収されたfee
    "trigger_direction": "LONG",#トリガーの方向
    "trigger_on": 0,
    "trailing_base_price": 0,#トリガーの基準価格
    "trailing_distance": 0,#トリガー価格までの距離
    "created_at": 1566283234499, #注文作成のタイムスタンプ
    "updated_at": 1566283234499, #注文更新のタイムスタンプ
    "frozen_margin": 0.034188034188034184,#凍結されたmargin
    "frozen_quantity": 20000,#凍結数量
    "hidden": false #Hidden注文にするかどうか
  }
}
```

## 注文のキャンセル

このAPIを使用して、注文をキャンセルできます。

### HTTP Request
`POST /v3/contracts/orders/<order_id>/cancel`

说明：
order_idは注文のidです。

### リクエスト・パラメータ
無し

### APIレスポンス：
```
{
  "status": 0,
  "data": {
    "id": 167624805711936,  #APIによって計算された分散型のID
    "sequenceId": 260087,  #sequenceによって計算されたsequenceId
    "type": "LIMIT",  #注文タイプLIMIT/MARKET
    "status": "PENDING",  #注文状態
    "direction": "SHORT", #注文方向LONG/SHORT
    "features": 0,        #注文属性、bitにより組み合わせ：FOK/IOC/HIDDEN/post_only
    "price": 100.0,     #指値価格、LIMIT注文だけ
    "quantity": 200,    #数量
    "unfilled_quantity": 200, #未成約数量
    "maker_fee_rate": -0.00025,#maker手数料レート
    "taker_fee_rate": 0.001,   #taker手数料レート
    "fee": 0,                #注文で徴収されたfee
    "trigger_direction": "LONG",#トリガーの方向
    "trigger_on": 0,
    "trailing_base_price": 0,#トリガーの基準価格
    "trailing_distance": 0,#トリガー価格までの距離
    "created_at": 1566283234499, #注文作成のタイムスタンプ
    "updated_at": 1566283234499, #注文更新のタイムスタンプ
    "frozen_margin": 0.034188034188034184,#凍結されたmargin
    "frozen_quantity": 20000,#凍結数量
    "hidden": false #Hidden注文にするかどうか
  }
}
```

## Activity Ordersの照会

このAPIを使用して、Activity Ordersを照会できます。

### HTTP Request
`GET /v3/contracts/orders/open`

### リクエスト・パラメータ
無し

### APIレスポンス：
```
{
  'status': 0,
  'data': {
    'results': [
      {
        'id': 167560221818945,
        'sequenceId': 259092,
        'type': 'LIMIT',
        'status': 'PENDING',
        'direction': 'SHORT',
        'features': 0,
        'price': 15677.0,
        'quantity': 265,
        'unfilled_quantity': 265,
        'maker_fee_rate': -0.00025,
        'taker_fee_rate': 0.001,
        'fee': 0,
        'trigger_direction': 'LONG',
        'trigger_on': 0,
        'trailing_base_price': 0,
        'trailing_distance': 0,
        'created_at': 1566275535810,
        'updated_at': 1566275535810,
        'frozen_margin': 0.000169037443388403,
        'frozen_quantity': 265,
        'hidden': False
      }
    ]
  }
}
```
## Activity Ordersの詳細の照会

このAPIを使用し、Activity Ordersの詳細を照会できます。

### HTTP リクエスト
`GET /v3/contracts/orders/open/<order_id>`

説明：
order_idは注文idです。

### パリクエスト・パラメータ
無し

### APIレスポンス：
```
{
  'status': 0,
  'data': {
        'id': 167560221818945,
        'sequenceId': 259092,
        'type': 'LIMIT',
        'status': 'PENDING',
        'direction': 'SHORT',
        'features': 0,
        'price': 15677.0,
        'quantity': 265,
        'unfilled_quantity': 265,
        'maker_fee_rate': -0.00025,
        'taker_fee_rate': 0.001,
        'fee': 0,
        'trigger_direction': 'LONG',
        'trigger_on': 0,
        'trailing_base_price': 0,
        'trailing_distance': 0,
        'created_at': 1566275535810,
        'updated_at': 1566275535810,
        'frozen_margin': 0.000169037443388403,
        'frozen_quantity': 265,
        'hidden': False
  }
}
```

## 注文IDで注文の詳細を取得する

このAPIを使用し、すべての注文の詳細を照会できます

### HTTP リクエスト
`GET /v3/contracts/orders/<order_id>`

説明：
order_idは注文idです。

### リクエスト・パラメータ
無し

### APIレスポンス：
```
{
  'status': 0,
  'data': {
        'id': 167560221818945,
        'sequenceId': 259092,
        'type': 'LIMIT',
        'status': 'PENDING',
        'direction': 'SHORT',
        'features': 0,
        ‘fill_price':0   #成約の平均価格
        'price': 15677.0, #注文価格 
        'quantity': 265,
        'unfilled_quantity': 265,
        'maker_fee_rate': -0.00025,
        'taker_fee_rate': 0.001,
        'fee': 0,
        'trigger_direction': 'LONG',
        'trigger_on': 0,
        'trailing_base_price': 0,
        'trailing_distance': 0,
        'created_at': 1566275535810,
        'updated_at': 1566275535810,
        'frozen_margin': 0.000169037443388403,
        'frozen_quantity': 265,
        'hidden': False
  }
}
```

## 注文履歴の照会

このAPIを使用し、注文履歴を照会できます。

### HTTP リクエスト
`GET /v3/contracts/orders/closed`

### リクエスト・パラメータ

パラメータ | 必須かどうか | 説明
--------- | ------- | -----------
range | N | 月を照会する。形式はYYYYMMです。例、「201907」。デフォルトは空で、現在の月を示します。
symbol | N | 照会条件のフィルタリングに使用され、指定されたsymbolの注文のみをフィルタリングします。デフォルトは空で、すべてのsymbolを示します。
offsetId | N | 現在のページの開始idを入力します。デフォルトは0で、最初のページを示します。
limit | N | 1〜100の範囲で結果集合内の履歴の最大値を返信します。デフォルトは100です。

### APIレスポンス：
```
{
  'status': 0,
  'data': {
    'range': '201908',
    'hasMore': True,
    'nextOffsetId': 163466438508609,
    'results': [
      {
        'id': 163466455285825,
        'sequenceId': 79260,
        'type': 'LIMIT',
        'status': 'FULLY_FILLED',
        'direction': 'SHORT',
        'features': 0,
        'price': 5286.0,
        'quantity': 332,
        'unfilled_quantity': 0,
        'maker_fee_rate': -0.00025,
        'taker_fee_rate': 0.001,
        'fee': -1.570185395384e-05,
        'trigger_direction': 'LONG',
        'trigger_on': 0.0,
        'trailing_base_price': 0.0,
        'trailing_distance': 0.0,
        'created_at': 1565787520849,
        'updated_at': 1565787520849
      },
      {...}
    ]
  }
}
```

## 成約注文履歴の照会

このAPIを使用して、成約注文の履歴を照会できます。

### HTTP リクエスト
`GET /v3/contracts/orders/<order_id>/matches`

説明：
order_idは注文idです。

### リクエスト・パラメータ
無し

### APIレスポンス：
```
{
  'status': 0,
  'data': {
    'results': [
      {
        'taker': False,
        'price': 13403.0,
        'quantity': 994.0,
        'fee': -1.8540625233156e-05,
        'created_at': 1566275534853
      }
    ]
  }
}
```

