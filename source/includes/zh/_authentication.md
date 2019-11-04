# 认证


FMex 使用 FCoin testnet 的API key 和 API secret 进行验证，请访问 [设置中心](https://exchange.fcoin.com/settings/api)，获取 API key 和 API secret。

FMex 使用 FCoin 的 API 请求，除公开的 API 外都需要携带 API key 以及签名




## 访问限制

目前访问频率为每个用户 100次 / 10秒，未来会按照业务区分访问频率限制。




## API 签名

签名前准备的数据如下：

`HTTP_METHOD` + `HTTP_REQUEST_URI` + `TIMESTAMP` + `POST_BODY`

连接完成后，进行 `Base64` 编码，对编码后的数据进行 `HMAC-SHA1` 签名，并对签名进行二次 `Base64` 编码，各部分解释如下：

<aside class="warning">
请注意需要进行两次 `Base64` 编码！
</aside>

### HTTP_METHOD

`GET`, `POST`, 需要大写

### HTTP_REQUEST_URI
真实交易地址：
`https://api.fmex.com/` 为 API 的请求前缀
模拟交易地址
`https://api.testnet.fmex.com/` 为 API 的请求前缀


后面再加上真正要访问的资源路径，如 `orders?param1=value1`，最终即 `https://api.fmex.com/orders?param1=value1`

对于请求的 URI 中的参数，需要按照字母表排序！

即如果请求的 URI 为 `https://api.fmex.com/orders?c=value1&b=value2&a=value3`，则进行签名时，应先将请求参数按照字母表排序，最终进行签名的 URI 为 `https://XXX/orders?a=value3&b=value2&c=value1`，
请注意，原请求 URI 中的三个参数顺序为 `c, b, a`，排序后为 `a, b, c`。

### TIMESTAMP

访问 API 时的 UNIX EPOCH 时间戳，需要和服务器之间的时间差少于 30 秒

### POST_BODY

如果是 `POST` 请求，`POST` 请求数据也需要被签名，签名规则如下：

所有请求的 key 按照字母顺序排序，然后进行 url 参数化，并使用 `&` 连接。

<aside class="warning">
请注意 POST_BODY 的键值需要按照字母表排序！
</aside>

> 如果请求数据为：

```json
{
  "username": "username",
  "password": "passowrd"
}
```

> 则先将 key 按照字母排序，然后进行 url 参数化，即：

```
password=password
username=username
```

> 因为 `p` 在字母表中的排序在 `u` 之前，所以 `password` 要放在 `username` 之前，然后使用 `&` 进行连接，即：

```
password=password&username=username
```

## 完整示例

> 对于如下的请求：

```
POST https://api.fmex.com/orders

{
  "symbol":"btcusd_p",
  "type":"limit",
  "direction":"short",
  "source":"web",
  "price":5500,
  "quantity":100
}

timestamp: 1523069544359
```

> 签名前的准备数据如下：

```
POSTPOSThttps://api.fmex.com/v3/contracts/orders1571109222426direction=short&price=5500&quantity=100&source=WEB&symbol=btcusd_p&type=limit
```

> 进行 Base64 编码，得到：

```
UE9TVGh0dHBzOi8vYXBpLmZtZXguY29tL3YzL2NvbnRyYWN0cy9vcmRlcnMxNTcxMTA5MjIyNDI2ZGlyZWN0aW9uPXNob3J0JnByaWNlPTU1MDAmcXVhbnRpdHk9MTAwJnNvdXJjZT1XRUImc3ltYm9sPWJ0Y3VzZF9wJnR5cGU9bGltaXQ=
```

> 拷贝在申请 API Key 时获得的秘钥（API SECRET），下面的签名结果采用 `ebfaeef06e2e49e1bc7e535c2766bbe6` 作为示例，

> 对得到的结果使用秘钥进行 `HMAC-SHA1` 签名，并对二进制结果进行 `Base64` 编码，得到：

```
s37ML6NwRLqMco75wCqsXFVz5iw=
```

> 即生成了用于向 API 服务器进行验证的最终签名

## header 参数名称

* `FC-ACCESS-KEY`
* `FC-ACCESS-SIGNATURE`
* `FC-ACCESS-TIMESTAMP`

