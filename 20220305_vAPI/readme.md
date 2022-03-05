# vAPI Writeup

## これはなに
OWASP API Security Top10の10項目に沿って公開されているセキュリティ学習用コンテンツ「vAPI」のWriteUp。  
Writeupは「vAPI version.1.1」のもので、2022年3月5日にGithubから取得した時点の内容に準ずる。

最初にPortSwiggerのブログで知ったため勘違いしたが、PortSwiggerのコンテンツでは無かった。
- vAPI  
https://github.com/roottusk/vapi
- Introducing vAPI – an open source lab environment to learn about API security  
https://portswigger.net/daily-swig/introducing-vapi-an-open-source-lab-environment-to-learn-about-api-security
- OWASP API Security Top 10 2019  
https://owasp.org/www-project-api-security/

## 導入と注意
Githubから落としてきてコマンド実行すれば環境が立ち上がる。
```sh
docker-compose up -d
```

何も変えていなければ以下のURLでアクセス可能
> http://localhost/vapi

アクセスすると画面上部からOpenAPIのswagger.jsonをダウンロードできるボタンがある。  
なお、Postmanを使う場合は、Githubから入手した`postman`フォルダの下にJSONが2つあるのでこっちを使用する。
- vAPI.postman_collection.json
- vAPI_ENV.postman_environment.json  

また、`Resources`フォルダにもAPI2とAPI3で使う資材が入っている。
