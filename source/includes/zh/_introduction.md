# 介绍

通过了解以下信息，您可以方便的使用 FMex 提供的 API 来接入 FMex 交易平台。

API域名：api.testnet.fmex.com

API访问地址：https://api.testnet.fmex.com/<path>，请注意我们仅支持https访问，不支持http访问。

API请求：仅支持GET或POST请求。

如果为GET请求，所有请求参数以queryString方式传入，例如：/v1/contracts/orders/open?symbol=BTCUSD_P

如果为POST请求，所有请求参数以JSON形式作为Request Body传入。请求类型必须为Content-Type: application/json。