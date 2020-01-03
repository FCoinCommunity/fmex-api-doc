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
transfer_from|Y|转账来源，必须为"contracts"
transfer_to|Y|转账目标，必须为"wallet"
amount|Y|转账金额，例如：1.024
### 请求示例：
```
{
    "currency":"BTC",
    "transfer_from":"CONTRACTS",
    "transfer_to":"WALLET",
    "amount":1,
}
```


### API响应：
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
## 查询一个转出操作（合约->钱包）的当前执行状态
API路径：`GET /v3/contracts/transfer/out/{transferId}/status`

API请求参数：无

### API响应：
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


## 分页查询合约每日交易量
GET /v3/broker/auth/contracts/trade_volumes

### 请求参数

|  属性  | 类型|  是否必须 |含义| 
|:------|:------|:------|:------|
|has_prev  |Boolean| N|是否包含前一页，该参数由前端控制，原样返回
|id  |String|N|最后一次分页的ID,非必传
|page_size  |Integer| N|请求数量(1-40),默认为40
|symbol  |String| N|交易对,例如 BTCUSD_P, 如果指定symbol,查询的是某symbol日交易量,如果不指定symbol则查询所有symbol日交易量
 
### 响应结果
 
```
{
    "status": "ok",
    "data": {
        "content": [
            {
                "id": "1574352000000", 
                "amount": "0.141933059938008458"
            }
        ],
        "current_elements": 1,
        "has_prev": false,
        "has_next": false,
        "next_page_id": "1574352000000"
    }
}
```