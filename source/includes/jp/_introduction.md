# 紹介

以下の情報を理解することにより、FMexが提供するAPIを使用してFMex取引プラットフォームに簡単にアクセスできます。

実際取引APIドメイン名：api.fmex.com

テストネットAPIドメイン名：api.testnet.fmex.com

APIアクセスアドレス：https://api.fmex.com/<path> 。また、httpsアクセスのみをサポートし、httpアクセスはサポートしていないので、ご注意ください。

APIリクエスト：GETまたはPOSTリクエストのみがサポートされています。

GETリクエストの場合、すべてのリクエスト・パラメータはqueryStringで送信してください。たとえば：/v1/contracts/orders/open?symbol=BTCUSD_P

POSTリクエストの場合、すべてのリクエスト・パラメータはRequest BodyとしてJSONで送信されます。 リクエストタイプはContent-Type: application/json でなければなりません。