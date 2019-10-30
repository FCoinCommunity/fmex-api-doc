# Autennication


FMex uses API key and API secret of FCoin testnet for authentication，please access "settings"(https://exchange.fcoin.com/settings/api) to obtain API key and API secret.

FMex uses API request of  FCoin, all other API require API key and signature expect public API.




## Access limitations

The access frequency is 100 times / 10 seconds per user at present. And access frequency will be restricted according to the business in the future.





## API signature

The data prepared before signature is as follows:

`HTTP_METHOD` + `HTTP_REQUEST_URI` + `TIMESTAMP` + `POST_BODY`


After the connection is completed, conduct 'Base64' encoding, sign 'hmac-sha1' for the encoded data, and conduct 'Base64' encoding twice for the signature. Each part is explained as follows:

<aside class="warning">
Please be noted that 'Base64' encoding needs to be done twice!
</aside>

### HTTP_METHOD

`GET`, `POST`, `DELETE`, `PUT` must be in capital letters

### HTTP_REQUEST_URI

`https://api.testnet.fmex.com/` is the prefix of API request

The actual resource path to access needs to be added, such as `orders?param1=value1`，this final shall be `https://api.testnet.fmex.com/orders?param1=value1`

Please alphabetize the parameters in the requested URI.

If the request URI is `https://api.testnet.fmex.com/orders?c=value1&b=value2&a=value3`，then the request parameters should be alphabetized first when signing, and the final URI for signing is `https://XXX/orders?
a=value3&b=value2&c=value1`，
Please be noted that the three parameters in the original request URI are in order 'c, b, a' and  'a, b, c' after alphabetizing.

### TIMESTAMP

The UNIX EPOCH timestamp for accessing API needs to at less than 30 seconds from the server

### POST_BODY

If it is a 'POST' request, the 'POST' request data also needs to be signed. The signature rules are as follows:
The keys of all requests are sorted alphabetically, then the url is parameterized and connected by using '&'.


<aside class="warning">
Please alphabetize the key assignments of POST_BODY!
</aside>

> If the request data is:

```json
{
  "username": "username",
  "password": "passowrd"
}
```

> therefore, first to alphabetize Key and then parameterized the url  which is：

```
password=password
username=username
```

> Because 'p' is sorted before 'u' in the alphabet, 'password' should be placed before 'username' and then connected with '&', that is:

```
password=password&username=username
```

## Complete example

> for the following request:


```
POST https://api.testnet.fmex.com/orders

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

> The prepared data before signing are as follows: 

```
POSThttps://api.testnet.fmex.com/v3/contracts/orders1571109222426direction=short&price=5500&quantity=100&source=WEB&symbol=btcusd_p&type=limit
```

> Conduct Base64 code，then you will get：

```
UE9TVGh0dHBzOi8vYXBpLnRlc3RuZXQuZm1leC5jb20vdjMvY29udHJhY3RzL29yZGVyczE1NzExMDkyMjI0MjZkaXJlY3Rpb249c2hvcnQmcHJpY2U9NTUwMCZxdWFudGl0eT0xMDAmc291cmNlPVdFQiZzeW1ib2w9YnRjdXNkX3AmdHlwZT1saW1pdA==
```

>copy SECRET (API SECRET)when applying for API Key, using ` ebfaeef06e2e49e1bc7e535c2766bbe6 ` as an example for the signature results ，

> Use private key for `HMAC-SHA1` signature  for the obtained result, conduct Base64 encode to the binary result then you will get:
```
g6vFomL3T3pOhCugUNo/UcaLxTw=
```

> Then generates the final signature used for API server authentication
## Parameter name

* `FC-ACCESS-KEY`
* `FC-ACCESS-SIGNATURE`
* `FC-ACCESS-TIMESTAMP`

##  instructions

You can use [developer tool]() (not available yet) for online debugging,