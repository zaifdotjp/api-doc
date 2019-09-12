# その他

## ライブラリへのリンク
Zaifの公開API/取引APIを各種プログラミング言語から簡単に使用するためのライブラリをご紹介させていただきます。

``` note::
  ここで紹介させていただいているライブラリは、Zaifと直接関係のない一般の方々が作成して公開しておられるもの、いわゆる「非公式ライブラリ」となります。 
  これ以外にもご存知の場合や作成して頂いた場合で、ここにリンクを希望されます方は、お手数ですがzaifdotjpのTwitterアカウントや関係者などにご一報いただければ幸いです。
  また、もしここへの掲載を希望されない場合も、お手数おかけしますがご連絡頂ければ対処いたしますので、よろしくおねがいします。
```

### Java
* zaif.jp API for Java. - JavaからZaif.jpのAPIを使うためのライブラリです。
[https://github.com/techbureau/JZaif](https://github.com/techbureau/JZaif)

### JavaScript
* node.js zaif.jp module

	[https://www.npmjs.com/package/zaif.jp](https://www.npmjs.com/package/zaif.jp)

	[https://github.com/you21979/node-zaif](https://github.com/you21979/node-zaif)

* Qiita : Node.js - zaif（取引所）でAPIを使って取引する
[http://qiita.com/you21979@github/items/32da1d0e1782e9e31938](http://qiita.com/you21979@github/items/32da1d0e1782e9e31938)

### Ruby
* RubyGems zaif
	
	[https://github.com/techbureau/zaif-ruby](https://github.com/techbureau/zaif-ruby)

### PHP
* Zaif4PHP

	[https://github.com/tk1024/Zaif4PHP/](https://github.com/tk1024/Zaif4PHP/)

### Golang
* Zaif取引所(Bitcoin/Monacoin)のAPIをGolangから使う

	[http://qiita.com/yanakend/items/e516f250800c4b876e65](http://qiita.com/yanakend/items/e516f250800c4b876e65)

### Python
* zaifのAPIを簡単にコール出来るようにしました

	[https://github.com/techbureau/zaifapi](https://github.com/techbureau/zaifapi)

* [Zaif]Pythonで簡単に仮想通貨の取引が出来るようにしてみた

	[http://qiita.com/Akira-Taniguchi/items/e52930c881adc6ecfe07](http://qiita.com/Akira-Taniguchi/items/e52930c881adc6ecfe07)

-------------------------------------------------------------------------------------------------------------------------------------------------------------

## サンプルコード
サンプルコードをご紹介させていただきます。

### Python
requestsは外部ライブラリとなります。利用するためには任意の環境にpipなどを使ってインストールする必要があります。

現物公開API
```
import requests
import json


response = requests.get('https://api.zaif.jp/api/1/last_price/btc_jpy')
if response.status_code != 200:
    raise Exception('return status code is {}'.format(response.status_code))
json.loads(response.text)
>>>{"last_price": 130065.0}
```
現物取引API
```
import json
import hmac
import hashlib
import requests
from future.moves.urllib.parse import urlencode


secret = 'your secret key'
key = 'your key'
params = {
    'method': 'actiuve_orders',
    'nonce': 123,
    'currency_pairs': 'btc_jpy'
}
encoded_params = urlencode(params)
signature = hmac.new(bytearray(secret.encode('utf-8')), digestmod=hashlib.sha512)
signature.update(encoded_params.encode('utf-8'))
headers = {
    'key': key,
    'sign': signature.hexdigest()
}
response = requests.post('https://api.zaif.jp/tapi', data=encoded_params, headers=headers)
if response.status_code != 200:
    raise Exception('return status code is {}'.format(response.status_code))
print(json.loads(response.text))
>>>{
>>>    "success": 1,
>>>    "return": {
>>>        "184": {
>>>            "currency_pair": "btc_jpy",
>>>            "action": "ask",
>>>            "amount": 0.03,
>>>            "price": 56000,
>>>            "timestamp": 1402021125
>>>        }
>>>    }
>>>}
```
### Go

現物公開API
```
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    uri := "https://api.zaif.jp/api/1/last_price/btc_jpy"
    req, _ := http.NewRequest("GET", uri, nil)

    client := new(http.Client)
    resp, _ := client.Do(req)
    defer resp.Body.Close()

    byteArray, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(byteArray))
}
>>>{"last_price": 130065.0}
```
現物取引API
```
package main

import (
    "fmt"
    "time"
    "strconv"
    "crypto/hmac"
    "crypto/sha512"
    "io/ioutil"
    "net/http"
    "encoding/hex"
    "net/url"
    "strings"
)

var key = "<your_key>"
var secret = "<your_secret>"

func main() {

    uri := "https://api.zaif.jp/tapi"
    values := url.Values{}
    values.Add("method", "get_info")
    values.Add("nonce", strconv.FormatInt(time.Now().Unix(), 10))

    encodedParams := values.Encode()
    req, _ := http.NewRequest("POST", uri, strings.NewReader(encodedParams))

    hash := hmac.New(sha512.New, []byte(secret))
    hash.Write([]byte(encodedParams))
    signature := hex.EncodeToString(hash.Sum(nil))

    req.Header.Add("Key", key)
    req.Header.Add("Sign", signature)
    client := new(http.Client)
    resp, _ := client.Do(req)
    defer resp.Body.Close()

    byteArray, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(byteArray))
}
```
### Swift

現物公開API
```
let apiUrl = URL(string: "https://api.zaif.jp/api/1/last_price/btc_jpy")!
var request = URLRequest(url: apiUrl)
request.addValue("application/json", forHTTPHeaderField: "Content-type")
request.addValue("application/json", forHTTPHeaderField: "Accept")
request.httpMethod = "GET"
URLSession.shared.dataTask(with: request) {data, response, err in
    if let data = data {
        do {
            let json = try JSONSerialization.jsonObject(with: data, options: JSONSerialization.ReadingOptions.allowFragments)
            print(json)
        } catch {
            print("Serialize Error")
        }
    } else {
        print("Error")
    }
}.resume()
>>>{"last_price": 130065.0}
```
現物取引API

```
import Foundation
import CryptoSwift

let key = "<your_key>"
let secret = "<your_secret>"

let url = URL(string: "https://api.zaif.jp/tapi")
let postStr = "method=get_info&nonce=" + String(Date().timeIntervalSince1970)
var request = URLRequest(url: url!)
request.httpMethod = "POST"
request.httpBody = postStr.data(using: .utf8)!

let signature = try HMAC(key: Array(secret.utf8), variant: .sha512).authenticate(Array(postStr.utf8))
request.addValue(key, forHTTPHeaderField: "Key")
request.addValue(signature.toHexString(), forHTTPHeaderField: "Sign")

URLSession.shared.dataTask(with: request) { data, response, err in
    if let data = data {
        do {
           let json = try JSONSerialization.jsonObject(with: data, options: JSONSerialization.ReadingOptions.allowFragments)
            print(json)
        } catch {
            print("Serialize Error")
        }
    } else {
        print("Error")
    }
}.resume()
```
