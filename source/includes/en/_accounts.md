# Account


## Query the balance of the current account

This api is used for query user's asset
### HTTP Request

API Path：`GET /v3/contracts/accounts`

API Request Parameter：NON

### API Responding：
```
{
  "status":0,
  "data":
        {Currency: [Available balance, frozen amount for orders, position margin]
          "BTC":[2.796888254907565438,0E-18,0E-18]
          }
        }
}
```

## Transfer Margin to wallet 

### HTTP Request
API Path：`POST /v3/contracts/transfer/out/request`

### Request Parameter
|Fieldname|Required or not|Description|
|:------|:------|:------|
currency|Y|Currency name，such as"BTC"
transfer_from|Y|Transfer from，must be"contracts"
transfer_to|Y|Transfer to，must be"wallet"
amount|Y|Transfer amount，such as：1.024
### Request example：
```
{
    "currency":"BTC",
    "transfer_from":"CONTRACTS",
    "transfer_to":"WALLET",
    "amount":1,
}
```


### API responding：
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
## Query the current executing state of a transfer-out operation (Contract->wallet) 
API path：`GET /v3/contracts/transfer/out/{transferId}/status`

API Request Parameter：NON

### API responding：
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


## Query transfer journal 
API path：`GET /v3/contracts/transfer/logs`

API Request Parameter：NON

### API responding：
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