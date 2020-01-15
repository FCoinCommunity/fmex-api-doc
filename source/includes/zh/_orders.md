# 合约交易接口

## 订单模型说明

订单模型由以下属性构成：

属性 | 类型 | 含义解释
---------- | ------- | -------
`id`	|long	|订单ID
`sequence_id`|	long|	定序ID
`margin_currency_id`	|long|	保证金Currency ID
`direction`|	enum|	仓位方向LONG/SHORT
`type`|	enum	|订单类型LIMIT/MARKET
`status`	|enum	|参见订单状态说明
`features`	|long	|订单特性，每个bit表示一种特性：0x01=FOK，0x02=post_only，0x04=Hidden，0x08=IOC，0x8000=爆仓单
`price`	|decimal|	限价单价格（若为市价单，此处为市价单价格上限或下限）
`quantity`|long	|订单数量
`fill_price`|decimal|成交均价
`unfilled_quantity`|	long|	未成交数量
`maker_fee_rate`|decimal|	作为Maker的费率
`taker_fee_rate`|	decimal|	作为Taker的费率
`fee`|decimal|	该订单累计已收取的手续费
`trigger_on`|decimal|	Stop订单的触发价格，非Stop订单触发价格始终为0
`trailing_base_price`|decimal|	TrailingStop订单的基准价格，非此类型订单则始终为0
`trailing_distance`|	decimal|	TrailingStop订单的触发价格距离，非此类型订单则始终为0
`created_at`|long|	订单创建时间
`updated_at`|long	|订单最后修改时间

订单状态说明：

属性  | 含义解释
----------- | -------
`PENDING`| 等待成交
`PARTIAL_FILLED`| 部分成交
`FULLY_FILLED`| 完全成交
`PARTIAL_CANCELLED`| 部分取消
`FULLY_CANCELLED`| 全部取消
`STOP_PENDING`|stop订单正在等待触发
`STOP_FAILED`| stop订单被触发，但执行失败（例如：冻结失败）
`STOP_CANCELLED`| stop订单未被触发而取消

## 创建新的订单

此 API 用于创建新的订单。

### HTTP Request

`POST /v3/contracts/orders`

### 请求参数

参数 | 是否必须 | 描述
--------- | ------- | -----------
`symbol`|	Y	|	合约代码，例如"BTCUSD_P"
`type	`|	Y	|	订单类型，参见OrderType
`direction`	|	Y		|订单方向，参见Direction
`source`|			""	|订单来源标识，例如"WEB", "APP"，字母和数字组合
`price`	|	仅限价单	|	限价单报价,只支持1位小数
`quantity`	|	Y	|	订单数量，整数，最小为1
`trigger_on`		|	N |	止盈止损订单触发价格
`trigger_direction` |	N | 止盈止损订单触发条件,"long","short"
`trailing_distance`	|	N	|	追踪止损订单触发距离
`fill_or_kill	`|		N|	类型为string，是否设置FOK订单，全部成交或全部取消
`immediate_or_cancel`		|	N	| 类型为string，是否设置IOC订单，立即成交或全部取消
`post_only	`	|	N|	类型为string，是否设置post_only订单
`hidden`		|	N	| 类型为string，是否设置隐藏订单
`reduce_only	`	|	N	| 类型为string，是否设置只减仓订单


请注意：

订单类型如果为LIMIT，则必须填写price；

trigger_on与trailing_distance不能同时填写

若fill_or_kill=true，则无法设置immediate_or_cancel、post_only、hidden和reduce_only；

若immediate_or_cancel=true，则无法设置fill_or_kill、post_only、hidden和reduce_only；

若post_only=true，则无法设置fill_or_kill、immediate_or_cancel和reduce_only；

若hidden=true，则无法设置fill_or_kill和immediate_or_cancel；

只有MARKET订单与IOC订单可以设置reduce_only=true。

举例说明
普通止盈止损订单：

{

    type: "MARKET",

    quantity: 10,

    trigger_on: 8000,

    trigger_direction: "LONG",

    direction: "LONG",

    ...
}

在此示例中，当价格大于等于8000，一个10张合约的市价多单委托将被提交至市场。
如果trigger_direction为short,则表示价格小于等于8000时，一个10张合约的市价空单委托将被提交至市场。

追踪止损：

{

    type: "MARKET",

    quantity: 10,

    trailing_distance: 100,

    trigger_direction: "SHORT",

    direction: "SHORT",

    ...
}

一旦用户提交此类型的委托，10张合约的市价买委托将在当前市场价格下跌超过追踪价距100时被提交。

然而，如果市场价格上涨，那么此委托将会追踪此价格，并在市场价格至最高点下跌超过追踪价距100时被执行。

如果trigger_direction和direction同时为long，则表示：一旦用户提交此类型的委托，10张合约的市价买委托将在当前市场价格上涨超过追踪价距100时被提交。
### API响应：
```
{
  "status": 0,
  "data": {
    "id": 167624805711936,  #由API计算的分布式ID
    "sequenceId": 260087,  #由sequence计算的sequenceId
    "type": "LIMIT",  #订单类型LIMIT/MARKET
    "status": "PENDING",  #订单状态
    "direction": "SHORT", #订单方向LONG/SHORT
    "features": 0,        #订单属性，按bit组合：FOK/IOC/HIDDEN/post_only
    "price": 100.0,     #限价价格，仅限LIMIT单
    "quantity": 200,    #数量
    "unfilled_quantity": 200, #未成交数量
    "maker_fee_rate": -0.00025,#maker费率
    "taker_fee_rate": 0.001,   #taker费率
    "fee": 0,                #订单已累积收取的fee
    "trigger_direction": "LONG",#触发方向
    "trigger_on": 0,
    "trailing_base_price": 0,#触发基础价格
    "trailing_distance": 0,#触发距离
    "created_at": 1566283234499, #订单创建时间戳
    "updated_at": 1566283234499, #更新时间戳
    "frozen_margin": 0.034188034188034184,#冻结margin
    "frozen_quantity": 20000,#冻结数量
    "hidden": false #是否隐藏
  }
}
```

## 取消订单

此 API 用于取消订单

### HTTP Request
`POST /v3/contracts/orders/<order_id>/cancel`

说明：
order_id为订单id

### 请求参数
无
### API响应：
```
{
  "status": 0,
  "data": {
    "id": 167624805711936,  #由API计算的分布式ID
    "sequenceId": 260087,  #由sequence计算的sequenceId
    "type": "LIMIT",  #订单类型LIMIT/MARKET
    "status": "PENDING",  #订单状态
    "direction": "SHORT", #订单方向LONG/SHORT
    "features": 0,        #订单属性，按bit组合：FOK/IOC/HIDDEN/post_only
    "price": 100.0,     #限价价格，仅限LIMIT单
    "quantity": 200,    #数量
    "unfilled_quantity": 200, #未成交数量
    "maker_fee_rate": -0.00025,#maker费率
    "taker_fee_rate": 0.001,   #taker费率
    "fee": 0,                #订单已累积收取的fee
    "trigger_direction": "LONG",#触发方向
    "trigger_on": 0,
    "trailing_base_price": 0,#触发基础价格
    "trailing_distance": 0,#触发距离
    "created_at": 1566283234499, #订单创建时间戳
    "updated_at": 1566283234499, #更新时间戳
    "frozen_margin": 0.034188034188034184,#冻结margin
    "frozen_quantity": 20000,#冻结数量
    "hidden": false, #是否隐藏
    "trailing": false
  }
}
```

## 查询活动订单

此 API 用于查询活动订单

### HTTP Request
`GET /v3/contracts/orders/open`
### 请求参数
无
### API响应：
```
{
  'status': 0,
  'data': {
    'results': [
      {
        "id": 203090171912,
        "features": 0,
        "price": 1127.00,
        "fee": -0.000196850393700787,
        "fill_price": 1127.0,
        "quantity": 200,
        "unfilled_quantity": 100,
        "maker_fee_rate": -0.000250000000000000,
        "taker_fee_rate": 0.000650000000000000,
        "type": "limit",
        "status": "partial_filled",
        "direction": "long",
        "trigger_direction": "long",
        "trigger_on": 0,
        "trailing_base_price": 0,
        "trailing_distance": 0,
        "created_at": 1577158837098,
        "updated_at": 1577167476836,
        "symbol": "BTCUSD_P",
        "trailing": false
      }
    ]
  }
}
```
## 查询活动订单详情

此 API 用于查询订单详情

### HTTP Request
`GET /v3/contracts/orders/open/<order_id>`

说明：
order_id为订单id
### 请求参数
无
### API响应：
```
{
  'status': 0,
  'data': {
    'id': 20309017941912,
    'features': 0,
    'price': 126.0,
    'fee': 0,
    'fill_price': 0.0,
    'quantity': 200,
    'unfilled_quantity': 200,
    'maker_fee_rate': -0.00025,
    'taker_fee_rate': 0.00065,
    'type': 'limit',
    'status': 'pending',
    'direction': 'long',
    'trigger_direction': 'long',
    'trigger_on': 0,
    'trailing_base_price': 0,
    'trailing_distance': 0,
    'created_at': 1577158837767,
    'updated_at': 1577158837767,
    'symbol': 'BTCUSD_P',
    'trailing': False
  }
}
```

## 通过订单id获取订单详情

此 API 用于查询所有订单的详情

### HTTP Request
`GET /v3/contracts/orders/<order_id>`

说明：
order_id为订单id
### 请求参数
无
### API响应：
```
{
  'status': 0,
  'data': {
    'id': 20309017941912,
    'features': 0,
    'price': 126.0,
    'fee': 0,
    'fill_price': 0.0,
    'quantity': 200,
    'unfilled_quantity': 200,
    'maker_fee_rate': -0.00025,
    'taker_fee_rate': 0.00065,
    'type': 'limit',
    'status': 'pending',
    'direction': 'long',
    'trigger_direction': 'long',
    'trigger_on': 0,
    'trailing_base_price': 0,
    'trailing_distance': 0,
    'created_at': 1577158837767,
    'updated_at': 1577158837767,
    'symbol': 'BTCUSD_P',
    'trailing': False
  }
}
```

## 查询历史订单

此 API 用于查询历史订单

### HTTP Request
`GET /v3/contracts/orders/closed`

### 请求参数

参数 | 是否必须 | 描述
--------- | ------- | -----------
range | N | 查询月份，格式为YYYYMM，例如"201907"，默认为空，表示当前月份。
symbol | N | 用于过滤查询条件，仅筛选指定symbol的订单，默认为空，表示所有symbol。
offsetId | N | 传入当前页的起始id，默认为0，表示第一页。
limit | N | 返回结果集的最大记录数量，范围1～100，默认为100。

### API响应：
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
## 全部撤单
`POST /v3/contracts/orders/cancel`

此 API 用于撤销全部交易对的委托单
如果需要撤销某个交易对的委托单，则在url中加入symbol
举例：https://api.fmex.com/v3/contracts/orders/cancel?symbol=btcusd_p

### API响应：
```
{
  "status": 0,
  "data": {
    "cancelled": 0
  }
}
```




## 查询订单成交历史

此 API 用于查询订单成交历史

### HTTP Request
`GET /v3/contracts/orders/<order_id>/matches`

说明：
order_id为订单id
### 请求参数 
无

### API响应：
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

## 分页查询合约每日交易量
`GET /v3/broker/auth/contracts/trade_volumes`

### 请求参数

|  属性  | 类型|  是否必须 |含义| 
|:------|:------|:------|:------|
|has_prev  |Boolean| N|是否包含前一页，该参数由前端控制，原样返回
|id  |String|N|最后一次分页的ID,非必传
|page_size  |Integer| N|请求数量(1-40),默认为40
|symbol  |String| N|交易对,例如 BTCUSD_P, 如果指定symbol,查询的是某symbol日交易量,如果不指定symbol则查询所有symbol日交易量
 
### 响应结果
 
```
请求参数示例:
 1. 指定symbol
{
    "status": "ok",
    "data": {
        "content": [
            {		
                "id": "1574352000000", # 分页id, 也代表时间
                "base_vol":"2.4853453948753947", # 交易量,指定symbol时返回
                "quote_vol":"30", # 合约张数,指定symbol时返回
                "amount": "0.141933059938008458" # 交易量折合(单位btc)
            }
        ],
        "current_elements": 1,
        "has_prev": false,
        "has_next": false,
        "next_page_id": "1574352000000" # 下一页id
    }
}

2.未指定symbol
{
    "status": "ok",
    "data": {
        "content": [
            {		
                "id": "1574352000000", # 分页id, 也代表时间
                "amount": "0.141933059938008458" # 交易量折合(单位btc)
            }
        ],
        "current_elements": 1,
        "has_prev": false,
        "has_next": false,
        "next_page_id": "1574352000000" # 下一页id
    }
}
```

## 分页查询合约每日手续费
`GET /v3/broker/auth/contracts/user_fees`

### 请求参数

|属性 | 是否必须|  含义|
|:------|:------|:------|
|has_prev |N| 是否包含前一页，该参数由调用方提供，原样返回
|id |N|最后一次分页的ID
|page_size |N| 请求数量(1-40),默认为40
|symbol |N| 交易对,例如 BTCUSD_P, 如果指定symbol,查询的是某symbol日手续费（指定symbol时还会返回手续费详情）,如果不指定symbol则查询所有symbol日手续费
 
### 响应结果
 
```
返回参数示例:
 1. 指定symbol
	{
	    "status": "ok",
	    "data": {
	        "content": [
	            {
	             "id": 1574352000000 # 分页id, 也代表时间
	             "currency": btc,      # 币种
	             "in_fees": 0.00000059938,         # maker 手续费
	             "in_fees_amount": 0.00000059938,  #  maker 手续费折合(单位btc)
	             "out_fees": 0.000060059938,       # taker 手续费
	             "out_fees_amount": 0.000060059938 # taker 手续费折合(单位btc)
	            }
	        ],
	        "current_elements": 1,
	        "has_prev": false,
	        "has_next": false,
	        "next_page_id": "1574352000000"
	    }
	}

2.未指定symbol
   {
	    "status": "ok",
	    "data": {
	        "content": [
	            {
	             "id": 1574352000000 # 分页id, 也代表时间
	             "in_fees_amount": 0.00000059938,  # maker 手续费折合(单位btc)
	             "out_fees_amount": 0.000060059938 # taker 手续费折合(单位btc)
	            }
	        ],
	        "current_elements": 1,
	        "has_prev": false,
	        "has_next": false,
	        "next_page_id": "1574352000000"
	    }
	}
```

## 根据时间查询手续费详情
`GET /v3/broker/auth/contracts/user_fees/{timestamp}`

  URL 参数  timestamp :  每日开始时间戳,单位毫秒
  
### 响应结果
 
```
	{
	  "status": "ok"
	  "data": [
	    {
	      "currency": btc,     # 币种
		   "in_fees": 0.00000059938,         # maker 手续费
         "in_fees_amount": 0.00000059938,  #  maker 手续费折合(单位btc)
         "out_fees": 0.000060059938,       # taker 手续费
         "out_fees_amount": 0.000060059938 # taker 手续费折合(单位btc)
	      "symbol": btcusd_p   # symbol
	    }
	  ]
	}
```