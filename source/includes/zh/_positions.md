# 仓位

## 获取仓位列表

此 API 获取仓位列表。

### HTTP Request

`GET /v3/broker/auth/contracts/positions`

### 请求参数
无

### API响应：
```
{
  'status': 'ok',
  'data': {
    'results': [
      {
        'direction': 'SHORT',  #仓位方向
        'updated_at': 1566271860000,  #仓位最近修改的毫秒时间戳
        'quantity': 0,  #持有合约数量，必须为正，若为0，表示已全部平仓
        'leverage': 0, #仓位设定杠杆率，不一定等于实际杠杆率
        'riskLevel': 0, #风险等级
        'max_quantity': 100000, #持有合约数量最大值
        'realized_pnl': -0.036307524734501204, #已实现盈亏，已实现盈亏仅作为统计展示给用户，表示自开仓以来该仓位的盈亏
        'taker_fee_rate': 0.001, #开仓时确定的仓位平仓taker费率
        'margin': 0.0, #仓位保证金，必须为正 
        'bankruptcy_price': 0.0, #破产价格，以该价格平仓，扣除taker手续费后，其权益恰好为0
        'liquidation_price': 0.0, #强平价格，以该价格平仓，扣除taker手续费后，其剩余权益恰好为仓位价值 x 维持保证金率
        'entry_price': 11017.0, #开仓均价，每次仓位增加或减少时，开仓均价都会调整
        'symbol': 'BTCUSD_P', #仓位合约代码，例如BTCUSD_P
        'closed': True, #仓位是否关闭
        'minimum_maintenance_margin_rate': 0.005 #最小维持保证金，如果仓位保证金降低到此，将立刻触发强平
      }
    ]
  }
}
```
## 设置仓位杠杆
### HTTP Request

`POST /v3/contracts/positions/$symbol/leverage`

symbol：URL参数，该仓位的Symbol名称
### 请求参数
参数 | 是否必须 | 描述
--------- | ------- | -----------
leverage| 必填| 0～最大允许值，0=全仓，1～最大允许值=逐仓杠杆倍数

### 请求示例
{
  'leverage': 0
}
### API响应：
```
{
  'status': 0,
  'data': {
    'leverage': 0
  }
}
```

##设置仓位保证金

### HTTP Request

`POST /v3/contracts/positions/$symbol/margin`

symbol：URL参数，该仓位的Symbol名称
### 请求参数
参数 | 是否必须 | 描述
--------- | ------- | -----------
margin| 必填| 设置的新的仓位保证金
### 请求示例
{
  'margin': 0
}
### API响应：
```
{
  'status': 0,
  'data': {
    'margin': 0
  }
}
```

##设置仓位风险等级

### HTTP Request

`POST /v3/contracts/positions/$symbol/riskLevel`

symbol：URL参数，该仓位的Symbol名称

### 请求参数
参数 | 是否必须 | 描述|
--------- | ------- | -----------
risk_level|必填|0～最大允许值
### 请求示例
{
  'risk_level': 0
}
### API响应：
```
{
  'status': 0,
  'data': {
    'risk_level': 0
  }
}
```