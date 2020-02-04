# ws推送接口

签名方式：<br>
1.只对http地址进行签名<br>
举例，只对这个字符串签名：`GEThttps://api.fmex.com/v2/user/ws1579154149629`，得到签名结果，签名方法与http请求相同<br>
2.将签名结果通过ws传递给服务器<br>
例如：`wss://api.fmex.com/v2/user/ws?FC-ACCESS-KEY=f8wersd4sdfc7121cbdd81986&FC-ACCESS-TIMESTAMP=1579147466690&FC-ACCESS-SIGNATURE=aKx/DMQ6j75JwegtfDVuJaXRxoxA=`<br>
3.连接成功后，就可以通过ws发送命令，获取相应信息<br>
<br>
其中，http地址：`https://api.fmex.com/v2/user/ws`<br>
ws地址：`wss://api.fmex.com/v2/user/ws`<br>

##获取账户信息：
websocket 请求的数据为:

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["account"],
}
```
##获取订单：
websocket 请求的数据为:

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["order.btcusd_p"],
}
```
##获取仓位：
websocket 请求的数据为:

```json
{
    "id":"11111",
    "cmd":"sub",
    "args":["position.btcusd_p"],
}
```
