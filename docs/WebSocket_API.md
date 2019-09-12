# WebSocket API

## WebSocket API利用手順
websocketを利用したリアルタイム板情報と終値のAPIを配信しています。 本APIが切断され再接続する場合は、１つのIPアドレスからの接続開始が4回/秒程度におさまるようにしてください。

## リクエスト方法
wss://ws.zaif.jp/stream?currency_pair={currency_pair}

ws://ws.zaif.jp/stream?currency_pair={currency_pair}

* ストリーミングは上記2つの接続方法があります。
* currency_pairに指定できる値は currency_pairs で取得できる通貨ペア情報の内、is_tokenがfalseになっているものです。

## 戻り値
```
{
        "asks":[
            [
                30000.0,
                0.1
            ],
            [
                30010.0,
                0.2
            ],
            ...
        ],
        "bids":[
            [
                29500.0,
                0.5
            ],
            [
                29300.0,
                0.1
            ],
            ...
        ],
        "trades":[
            {
                "currenty_pair":"btc_jpy",
                "trade_type":"ask",
                "price":30001,
                "tid":123,
                "amount":0.02,
                "date":1427879761
            },
        ...,
        "timestamp":"2015-04-01 18:16:01.739990",
        "last_price:{
            "action":"ask",
            "price":30001
        },
        "currency_pair":"btc_jpy"
}
```

