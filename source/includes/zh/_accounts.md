# 账户


## 查询当前用户账户余额

此api用于查询用户资产
### HTTP Request

API路径：`GET /v3/contracts/accounts`

API请求参数：无

### API响应：
```
{
  "status":0,
  "data":
        {币种: [可用余额, 订单冻结金额, 仓位保证金金额]
          "BTC":[2.796888254907565438,0E-18,0E-18]
          }
        }
}
```

## 保证金转出至钱包

### HTTP Request
API路径：`POST /v3/contracts/transfer/out/request`

### 请求参数
|字段名称|是否必须|描述|
|:------|:------|:------|
currency|Y|币种名称，例如"BTC"
transferFrom|Y|转账来源，必须为"CONTRACTS"
transferTo|Y|转账目标，必须为"WALLET"
amount|Y|转账金额，例如：1.024
### 请求示例：
```
{
    "currency":"BTC",
    "transferFrom":"CONTRACTS",
    "transferTo":"WALLET",
    "amount":1,
}
```


### API响应：
```
{
  "status":0,
  "data":{
        "transferId":"167600369696833",  // 由系统生成的唯一转账ID
        "transferFrom":"CONTRACTS",
        "transferTo":"WALLET",
        "amount":0.01,
        "done":false,  // 是否已完成
        "createdAt":1566280321419,
        "updatedAt":1566280321419,
        "error":false,  // 是否有错误
        "currency":"BTC"
        }
}

```
## 查询一个转出操作（合约->钱包）的当前执行状态
API路径：`GET /v3/contracts/transfer/out/{transferId}/status`

API请求参数：无

### API响应：
```
{
  "status":0,
  "data":{
        "transferId":"167600369696833",  // 由系统生成的唯一转账ID
        "transferFrom":"CONTRACTS",
        "transferTo":"WALLET",
        "amount":0.01,
        "done":false,  // 是否已完成
        "createdAt":1566280321419,
        "updatedAt":1566280321419,
        "error":false,  // 是否有错误
        "currency":"BTC"
        }
}
```


## 查询转账日志
API路径：`GET /v3/contracts/transfer/logs`

API请求参数：无

### API响应：
```
{
  'status': 0,
  'data': {
    'hasMore': False,
    'nextOffsetId': 0,
    'results': [
      {
        'transferId': '167600369696833',
        'transferFrom': 'CONTRACTS',
        'transferTo': 'WALLET',
        'amount': 0.01,
        'done': True,
        'createdAt': 1566280321419,
        'updatedAt': 1566280321851,
        'error': False,
        'currency': 'BTC'
      }
    ]
  }
}
```