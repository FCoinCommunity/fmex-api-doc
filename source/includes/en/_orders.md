# Order

##Order mode instruction 

Order mode are composed by the following characters:

Character | Type | Meaning
---------- | ------- | -------
`id`	|long	|Order ID
`sequence_id`|	long|	 SequencingID
`margin_currency_id`	|long|	 Margin Currency ID
`direction`|	enum|	Position Direction LONG/SHORT
`type`|	enum	|Order type LIMIT/MARKET
`status`	|enum	|See order status description
`features`	|long	|Order features, each bit represents a feature:0x01=FOK，0x02=post_only，0x04=Hidden，0x08=IOC，0x8000=liquidated orders
`price`	|decimal|	Limit order price (if it's market-price order, here means the upper or lower limit of market-price order)
`quantity`|long	|Order quantity
`fill_price`|decimal|Average closing price
`unfilled_quantity`|	long|	Unfulfilled quantity 
`maker_fee_rate`|decimal|	Fee rate for Maker
`taker_fee_rate`|	decimal|	Fee rate for Taker
`fee`|decimal|	The accumulated transaction fee charged for this order
`trigger_on`|decimal|	The trigger price for Stop orders, and the trigger price for non-stop orders is always 0 
`trailing_base_price`|decimal|	The base price for TrailingStop orders other than this type is always 0 
`trailing_distance`|	decimal|	The distance between trigger price for TrailingStop orders other than this type is always 0
`created_at`|long|	Order creation time
`updated_at`|long| Last modified time of this order

Description of order status:

Attribute  | Meaning
----------- | -------
`PENDING`| PENDING transaction
`PARTIAL_FILLED`|Partially fulfilled
`FULLY_FILLED`| Fully filled
`PARTIAL_CANCELLED`| Partially canceled 
`FULLY_CANCELLED`| Fully canceled
`STOP_PENDING`|stop order is waiting to be triggered
`STOP_FAILED`| stop order was triggered but execution fails (for example: freeze failure)  
`STOP_CANCELLED`| stop order has been canceled for not been triggered

## Create new orders

This API is used to creating new orders.

### HTTP Request

`POST /v3/contracts/orders`

### Request parameters

Parameters | Required or not| Description
--------- | ------- | -----------
`symbol`|	Y	|	Contract code, such as"BTCUSD_P"
`type	`|	Y	|	order type, see OrderType
`direction`	|	Y		|Order direction，see Direction
`source`|			""	|Order source identification, such as "WEB", "APP", letter and number combination
`price`	|	only limit orders 	|only limit orders and only support 1 decimal place
`quantity`	|	Y	|	order quantity, integral number no less than 1 `trigger_on`		|	N |	Triggered price of Stop-win & stop-loss orders  
`trigger_direction` |	N | Triggered conditions of Stop-win & stop-loss orders,"long","short"
`trailing_distance`	|	N	|The distance for being triggered of the Stop-win & stop-loss orders	
`fill_or_kill	`|		N|	Type is string，set FoK order or not
`immediate_or_cancel`		|	N	| Type is string，set IOC order or not
`post_only	`	|	N|	Type is string，set post_only order or not
`hidden`		|	N	|  Type is string，set Hidden order or not
`reduce_only	`	|	N	| Type is string，set reduce_only order or not


Please note:

If the order type is LIMIT, price must be filled in ;；

Can not both fill in trigger_on and trailing_distance；

If fill_or_kill=true，immediate_or_cancel, post_only、hidden or reduce_only will not be able to set；

If immediate_or_cancel=true，fill_or_kill, post_only、hidden or reduce_only will not be able to set；

If post_only=true，then fill_or_kill, immediate_or_cancel or reduce_only will not be able to set；

If hidden=true，then fill_or_kill or immediate_or_cancel will not be able to set；

Only MARKET orders and IOC order can set reduce_only=true。

[回复 Peggy]
Examples of the general stop-profit & stop-loss orders:

{

type: "MARKET",

quantity: 10,

trigger_on: 8000,

trigger_direction: "LONG",

direction: "LONG",

... }


In this example, when the price is greater than or equal to 8000, a 10-contract market price long order will be submitted to the market. If trigger_direction is short, it indicates that if the price is less than or equal to 8000, a 10-contract market price short order will be submitted to the market.

Trailing stop-loss:
：

{

type: "MARKET",

quantity: 10,

trailing_distance: 100,

trigger_direction: "SHORT",

direction: "SHORT",

... }


Once the user submits this typed order, the 10-contracts market price buy order will be submitted when the current market price falls beyond the trailing distance of 100.


However, if the market price rises, the order will track the price and be executed when the market price reaches its peak and falls beyond the trailing distance of 100.

If trigger_direction and direction are both long, it means that once the user submits this type of orders, the 10-contracts market price buy order will be submitted when the current market price increases beyond the trailing distance of 100.

### API responding ：
```
{
  "status": 0,
  "data": {
    "id": 167624805711936,  #Distributed id calculated by API
    "sequenceId": 260087,  #SequenceId calculated by sequence 
    "type": "LIMIT",  #Order type LIMIT/MARKET
    "status": "PENDING",  #Order status
    "direction": "SHORT", #Order direction LONG/SHORT
    "features": 0,        #Order features，combine by bit：FOK/IOC/HIDDEN/post_only
    "price": 100.0,     #Limit price, only for LIMIT orders
    "quantity": 200,    #Quantity
    "unfilled_quantity": 200, #Unfilled quantity 
    "maker_fee_rate": -0.00025,#maker fee rate
    "taker_fee_rate": 0.001,   #taker fee rate
    "fee": 0,                #Accumulated fee charged for the order 
    "trigger_direction": "LONG",# Trigger direction
    "trigger_on": 0,
    "trailing_base_price": 0,#Trigger base price
    "trailing_distance": 0,#Triggered distance
    "created_at": 1566283234499, #Order creation timestamp
    "updated_at": 1566283234499, #Update timestamp
    "frozen_margin": 0.034188034188034184,#freeze margin
    "frozen_quantity": 20000,#frozen quantity
    "hidden": false #Hidden or not 
  }
}
```

## Cancel order

This API is used for canceling orders

### HTTP Request
`POST /v3/contracts/orders/<order_id>/cancel`

Description：
order_id is order id

###  Request Parameter
NON
### API responding：
```
{
  "status": 0,
  "data": {
    "id": 167624805711936,  #Distributed ID calculated by API
    "sequenceId": 260087,  #SequenceId calculated by sequence 
    "type": "LIMIT",  #Order type is LIMIT/MARKET
    "status": "PENDING",  #Order status
    "direction": "SHORT", #Order direction LONG/SHORT
    "features": 0,        #Order features，combine by bit：FOK/IOC/HIDDEN/post_only
    "price": 100.0,     #Limit price, only for LIMIT orders
    "quantity": 200,    #Quantity
    "unfilled_quantity": 200, #Unfilled quantity 
    "maker_fee_rate": -0.00025,#maker fee rate
    "taker_fee_rate": 0.001,   #taker fee rate
    "fee": 0,                #Accumulated fee charged for the order 
    "trigger_direction": "LONG",#Trigger direction
    "trigger_on": 0,
    "trailing_base_price": 0,#Trigger base price
    "trailing_distance": 0,#Triggered distance
    "created_at": 1566283234499, #Order creation timestamp
    "updated_at": 1566283234499, #Update timestamp
    "frozen_margin": 0.034188034188034184,#Freeze margin
    "frozen_quantity": 20000,#Frozen quantity
    "hidden": false #Hidden or not
  }
}
```

## Query activity orders

This API is used to querying activity orders

### HTTP Request
`GET /v3/contracts/orders/open`
### Request Parameter
NON
### API responding：
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
## Query activity orders details

This API is used to querying activity orders details

### HTTP Request
`GET /v3/contracts/orders/open/<order_id>`

Description：
order_id is order id
### Request Parameter
NON
### API responding：
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

## Obtain order details by order id

This API is used to query all order details


### HTTP Request
`GET /v3/contracts/orders/<order_id>`

Description：
order_id is order id
### Request parameters
NON
### API responding：
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
        ‘fill_price':0   #Average filled price
        'price': 15677.0, #order placed price 
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

## Query history order

This API is used to query history orders



### HTTP Request
`GET /v3/contracts/orders/closed`

### Request parameters

Parameters | Required or not | Description
--------- | ------- | -----------
range | N |Query the month in the form of YYYYMM, for example "201907", which by default is null to indicate the current month.
symbol | N | is used to filter query conditions, filtering only orders that specify symbol, which by default is null, representing all symbols. 
offsetId | N | Transfer-in the starting id of the current page, which by default is  0, indicating the first page.
limit | N | The maximum number of returning ResultSets, ranging from 1 to 100, which by default is 100.

### API responding：
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

## Query order fulfillment history

This API is used to query order fulfillment history


### HTTP Request
`GET /v3/contracts/orders/<order_id>/matches`

Description：
order_id is the order id
### Request parameters
NON

### API responding：
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

