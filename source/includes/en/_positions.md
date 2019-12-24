# Positions

## Obtain position lists 

This API is used for obtaining position list.

### HTTP Request

`GET /v3/broker/auth/contracts/positions`

### Request Parameter
NON

### API responding：
```
{
  'status': 'ok',
  'data': {
    'results': [
      {
        'direction': 'SHORT',  #Position Direction
        'updated_at': 1566271860000,  #Millisecond timestamp of the latest modified Position 
        'quantity': 0,  #The number of contracts held must be positive. If it is 0, means the position is fully closed 
        'leverage': 0, #leverage ratio set for position which is not necessarily equal to actual leverage ratio
        'riskLevel': 0, #Risk level
        'max_quantity': 100000, #The maximum contract quantity held
        'realized_pnl': -0.036307524734501204, #Realized PNL, Realized PNL is only as a statistical display to the user which is the profit and loss since the opening of the position  
        'taker_fee_rate': 0.001, #Taker position closing rate determined at position opening 
        'margin': 0.0, #Position margin, must be positive 
        'bankruptcy_price': 0.0, #Bankruptcy price, the profit shall be 0 if the position has been closed at the price after deducting taker fee
        'liquidation_price': 0.0, #Liquidation price, the profit shall be position value x margin rate if the position has been closed at this price after deducting taker fee        'entry_price': 11017.0, #Average opening price, the average opening price will be adjusted  each time a position is increased or decreased
        'symbol': 'BTCUSD_P', #Position contract code, such asBTCUSD_P
        'closed': True, #Close the position or not
        'minimum_maintenance_margin_rate': 0.005 #The minimum maintenance margin. If the margin is lowered to this level, the liquidation will be triggered immediately.      }
    ]
  }
}
```
## Set position leverage
### HTTP Request

`POST /v3/contracts/positions/$symbol/leverage`

symbol：URL Parameter, Symbol name of the position
### Request Parameters
Parameters | Required or not |Description
--------- | ------- | -----------
leverage| Required| 0～the maximum allowable value，0 = full position，1～the maximum allowable value = Isolated margin leverage

### Request example
{
  'leverage': 0
}
### API responding：
```
{
  'status': 0,
  'data': {
    'leverage': 0
  }
}
```

##Set position margin

### HTTP Request

`POST /v3/contracts/positions/$symbol/margin`

symbol：URL Parameter, Symbol name of the position
### Request Parameters
Parameters | Required or not  | Description
--------- | ------- | -----------
margin| Required| Set new position margin
### Request example
{
  'margin': 0
}
### API responding：
```
{
  'status': 0,
  'data': {
    'margin': 0
  }
}
```

##Set risk level of the position

### HTTP Request

`POST /v3/contracts/positions/$symbol/riskLevel`

symbol：URL Parameter, Symbol name of the position

### Request Parameters
Parameters | Required or not  | Description| 
--------- | ------- | -----------
risk_level|Required|0～the maximum allowable value
### Request example
{
  'risk_level': 0
}
### API responding：
```
{
  'status': 0,
  'data': {
    'risk_level': 0
  }
}
```