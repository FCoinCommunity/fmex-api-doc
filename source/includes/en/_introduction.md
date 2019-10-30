
# introduction

You can easily access the FMex trading platform using the API provided by FMex through the following information.

API domain name of real trading: https://api.fmex.com

API domain name of testnet trading: https://api.testnet.fmex.com

API access address: https://api.fmex.com/ < path >, please note that we only support HTTPS access but not HTTP.

API requests: only GET or POST requests are supported.

For the GET request, all request parameters shall be transferred-in by queryString, for example: /v1/contracts/orders/open?symbol=btcusd_p

For the POST Request, all request parameters shall be transferred-in by JSON Request as Request Body. The request Type must be content-type: application/json.