# アカウント


## 現在アカウントの残高を照会する

このapiは、ユーザーの資産リストを照会するために使用されます。
### HTTP リクエスト

APIパス：`GET /v3/contracts/accounts`

APIリクエスト・パラメータ：無し

### APIレスポンス：
```
{
  "status":0,
  "data":
        {通貨: [利用可能な残高, 注文に利用した金額, ポジションの証拠金金額]
          "BTC":[2.796888254907565438,0E-18,0E-18]
          }
        }
}
```

## 証拠金をウォレットに振替する

### HTTP リクエスト
APIパス：`POST /v3/contracts/transfer/out/request`

### リクエスト・パラメータ
|フィールド名称|必要性|内容説明|
|:------|:------|:------|
currency|Y|通貨名称、例えば「BTC」
transfer_from|Y|送金側、「contracts」にしなければなりません。
transfer_to|Y|送金先、「wallet」にしなければなりません。
amount|Y|送金金額、例えば：1.024
### デモンストレーション：
```
{
    "currency":"BTC",
    "transfer_from":"CONTRACTS",
    "transfer_to":"WALLET",
    "amount":1,
}
```


### APIレスポンス：
```
{
  "status": 0,
  "data": {
    "transfer_from": "contracts",
    "transfer_to": "wallet",
    "amount": 0.1,
    "transfer_id": "179363219767363",
    "currency": "BTC"
  }
}


```
## 現在、1つの振替動作（コントラクト->ウォレット）の実行状態を照会する
APIパス：`GET /v3/contracts/transfer/out/{transferId}/status`

APIリクエスト・パラメータ：無し

### APIレスポンス：
```
{
  'status': 0,
  'data': {
    'transfer_id': '179364285120578',
    'transfer_from': 'contracts',
    'transfer_to': 'wallet',
    'amount': 0.1,
    'done': True,
    'created_at': 1567682689265,
    'updated_at': 1567682689473,
    'error': False,
    'currency': 'BTC'
  }
}
```


## 振替履歴の照会
APIパス：`GET /v3/contracts/transfer/logs`

APIリクエスト・パラメータ：無し

### APIレスポンス：
```
{
  'status': 0,
  'data': {
    'hasMore': False,
    'nextOffsetId': 0,
    'results': [
      {
        'transfer_id': '167600369696833',
        'transfer_from': 'CONTRACTS',
        'transfer_to': 'WALLET',
        'amount': 0.01,
        'done': True,
        'created_at': 1566280321419,
        'updated_at': 1566280321851,
        'error': False,
        'currency': 'BTC'
      }
    ]
  }
}
```