# Qiitaの簡易ビューワーアプリのQiitaViewerの実装

この章ではQiitaの簡易ビューワーアプリのQiitaViewerの実装について解説していきます。これから実装するアプリの機能についてまずは簡単にまとめておこうと思います

## QiitaViewの機能について

本書での目的は「標準のAPIを利用しつつも、少ない手間とちょっとしたアイデアで、標準APIを使ってる用に見せないためのTIPSを紹介する」ことを主眼としているため、ビューワーアプリとしての機能はシンプルなものにとどめようと考えています。

そのためQiitaの投稿情報を取得して、取得した投稿情報をTableViewを利用して配置する程度にとどめて、それ以外の機能、例えば、Qiitaへのログイン処理や、Qiita上のユーザのフォロー機能といったQiitaのAPIをふる活用することは目指さないことにします。


## Titanium Studioを起動してプロジェクトを作成する

- プロジェクト名:QiitaViewer
- AppID:info.h5y1m141.ti.qiitaviewer
- ターゲットOS:iPhone、Androidのみチェック。iPad、MobileWebはオフにする

## Hello World的なアプリが起動したら、ひとまずbuildをしてみる

## Qiitaの投稿情報を取得する時のWebAPIについて

まずは、Qiitaの開発者向けのAPIを通じて、投稿情報を取得する機能を実装しますが、その前にQiitaの投稿情報を取得する時のWebAPIについて簡単に説明します

以下URLにアクセスすることでパブリックな新着投稿を取得することができます。
https://qiita.com/api/v1/items

投稿情報は以下の様なJSON形式になります

```javascript
[{"id": 1,
 "uuid": "1a43e55e7209c8f3c565",
 "user":
  {"name": "Hiroshige Umino",
   "url_name": "yaotti",
   "profile_image_url": "https://si0.twimg.com/profile_images/2309761038/1ijg13pfs0dg84sk2y0h_normal" },
 "title": "てすと",
 "body": "<p>foooooooooooooooo</p>\n",
 "created_at": "2012-10-03 22:12:36 +0900",
 "updated_at": "2012-10-03 22:12:36 +0900",
 "created_at_in_words": "18 hours ago",
 "updated_at_in_words": "18 hours ago",
 "tags":
  [{"name": "FOOBAR",
    "url_name": "FOOBAR",
    "icon_url": "http://qiita.com/icons/thumb/missing.png",
    "versions": ['1.2', '1.3']}],
 "stock_count": 0,
 "stock_users": [],
 "comment_count": 0,
 "url": "http://qiita.com/items/1a43e55e7209c8f3c565",
 "gist_url": null,
 "tweet": false,
 "private": false,
 "stocked": false
},
 ...
]
```

詳しい情報を知りたい方は、[Qiitaのサイトをご覧ください](http://qiita.com/docs#13)


## Qiitaの投稿情報を取得する機能を実装する

QiitaのようなWebAPIと連携するアプリを開発する場合にTitanium Mobileでは、標準APIのhttpCLientを活用します。

プロジェクト作成時に自動的に生成されたapp.jsの中身を以下のように書き換えます

```javascript
var xhr,url,httpVerb;
qiitaURL = "https://qiita.com/api/v1/items"
httpVerb = "GET";
xhr = Ti.Network.createHTTPClient();
xhr.open(httpVerb,qiitaURL); // (1)
xhr.onload = function(){
  var body;
  if (this.status === 200) {
    body = JSON.parse(this.responseText); // (2)
	Ti.API.info(body);
  } else {
    Ti.API.info("error:status code is " + this.status);
  }
}
xhr.onerror = function(e) {
  var error;
  error = JSON.parse(this.responseText);
  Ti.API.info(error.error);
}
xhr.timeout = 5000;
xhr.send()
```

書き換えた後に、run を実行します。シミュレーターの画面には何も標示されず、コンソール上に複数の文字が標示されるかと思いますので、まずはその点を確認してください。

