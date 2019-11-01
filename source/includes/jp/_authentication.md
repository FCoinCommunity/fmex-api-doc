# 認証


FMex は FCoin testnet のAPIキーとAPIシークレットを使用して、認証を行います。 [設定センター](https://exchange.fcoin.com/settings/api)にアクセスし、APIキーとAPIシークレットを取得できます。

FMex は FCoin のAPIリクエストを使用しています。公開API以外に、APIキー及び署名が必要となります。




## アクセス制限

現在、アクセスの頻度はユーザーごとに100回 / 10秒，将来はサービスごと、アクセスの頻度を制限する予定です。



## API署名

署名を行うため、あらかじめ下記データをご用意してください：

`HTTP_METHOD` + `HTTP_REQUEST_URI` + `TIMESTAMP` + `POST_BODY`

接続が確立後、`Base64`エンコーディングが必要です。エンコーディング後のデータに対して、 `HMAC-SHA1`署名を行います。また、署名に対して、 `Base64`エンコーディングを2回実施します。各ステップの詳細内容について、ご説明します：

<aside class="warning">
ご注意： `Base64`エンコーディングは2回実施する必要があります！
</aside>

### HTTP_METHOD

`GET`, `POST`, `DELETE`, `PUT` は必ず大文字にしてください。

### HTTP_REQUEST_URI

`https://api.testnet.fmex.com/` はAPIリクエストの接頭辞です。

その後に、アクセスしたいリソースパスを記入してください。例： `orders?param1=value1`の場合は，最終形は`https://api.testnet.fmex.com/orders?param1=value1`となります。

リクエストされたURIのパラメータについては、アルファベット順に並べ替える必要があります！

すなわち、リクエストされたURIは`https://api.testnet.fmex.com/orders?c=value1&b=value2&a=value3`の場合，デジタル署名する際に、リクエスト・パラメータをアルファベット順にソートさせ、デジタル署名の最終URIは`https://XXX/orders?a=value3&b=value2&c=value1`となります。
元のリクエスト中には、URI中の三つのパラメータの順番は`c, b, a`ですが、ソート後、順番は`a, b, c`になっております。ご注意ください。

### TIMESTAMP

APIを利用してアクセスする時のUNIX EPOCHタイムスタンプは、サーバーとの時間差は30秒以内でなければなりません。

### POST_BODY

`POST` リクエストの場合は、`POST` リクエストデータへのデジタル署名も必要です。デジタル署名のルールはご説明します：

リクエストされたすべてのキーはアルファベット順に並べ替えられた後、urlパラメータ化されます。 `&`を使用して、繋ぎます。 

<aside class="warning">
POST_BODYのキーの値は、アルファベット順にソートする必要があります。ご注意ください！
</aside>

> 下記リクエストデータの場合は：

```json
{
  "username": "username",
  "password": "passowrd"
}
```

> 先に、キーをアルファベット順にソートし、urlパラメータ化します。すなわち：

```
password=password
username=username
```

> アルファベットの順番には、`p`が`u`の前にあるため、`password`は`username` の前に置かれるべき、その次、`&`を使用して接続します。すなわち：

```
password=password&username=username
```

## デモンストレーション

> 下記リクエストデータの場合は：

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

> デジタル署名を行うため、あらかじめ下記データをご用意してください：

```
POSThttps://api.testnet.fmex.com/v3/contracts/orders1571109222426direction=short&price=5500&quantity=100&source=WEB&symbol=btcusd_p&type=limit
```

> Base64 エンコーディングし、下記データになります：

```
UE9TVGh0dHBzOi8vYXBpLnRlc3RuZXQuZm1leC5jb20vdjMvY29udHJhY3RzL29yZGVyczE1NzExMDkyMjI0MjZkaXJlY3Rpb249c2hvcnQmcHJpY2U9NTUwMCZxdWFudGl0eT0xMDAmc291cmNlPVdFQiZzeW1ib2w9YnRjdXNkX3AmdHlwZT1saW1pdA==
```

> API キーを申請した際に取得したキー（API シークレット）をコピーし，以下のデジタル署名結果は、例として `ebfaeef06e2e49e1bc7e535c2766bbe6` を使用しています。

> 結果に対して、秘密鍵を使用して、`HMAC-SHA1`デジタル署名し、バイナリ結果に対して`Base64` エンコーディング後、下記になります：

```
g6vFomL3T3pOhCugUNo/UcaLxTw=
```

> すなわち、APIサーバーへ検証するための最終的なデジタル署名が生成されます。

## パラメータの名称

* `FC-ACCESS-KEY`
* `FC-ACCESS-SIGNATURE`
* `FC-ACCESS-TIMESTAMP`

## 説明

デベロッパーツール（準備中）を使用して、オンライン検証を行えます。
