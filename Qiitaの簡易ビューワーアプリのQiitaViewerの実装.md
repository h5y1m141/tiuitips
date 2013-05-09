**Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*

- [Qiitaの簡易ビューワーアプリ「QiitaViewer」の機能について](#qiitaの簡易ビューワーアプリ「qiitaviewer」の機能について)
- [QiitaViewer用にプロジェクトを作成する](#qiitaviewer用にプロジェクトを作成する)
- [Qiitaの投稿情報を取得する](#qiitaの投稿情報を取得する)
	- [QiitaのWebAPIについて](#qiitaのwebapiについて)
	- [Qiitaの投稿情報を取得する機能を実装する](#qiitaの投稿情報を取得する機能を実装する)
- [Qiitaの投稿情報を取得した後に画面に表示する](#qiitaの投稿情報を取得した後に画面に表示する)
	- [取得した結果をTableViewを活用して画面に表示する(1)](#取得した結果をtableviewを活用して画面に表示する1)
	- [取得した結果をTableViewを活用して画面に表示する(2)](#取得した結果をtableviewを活用して画面に表示する2)

## Qiitaの簡易ビューワーアプリ「QiitaViewer」の機能について

この章ではQiitaの簡易ビューワーアプリのQiitaViewerの実装について解説していきます。これから実装するアプリの機能についてまずは簡単にまとめておこうと思います

本書での目的は「標準のAPIを利用しつつも、少ない手間とちょっとしたアイデアで、標準APIを使ってる用に見せないためのTIPSを紹介する」ことを主眼としているため、ビューワーアプリとしての機能はシンプルなものにとどめようと考えています。

そのためQiitaの投稿情報を取得して、取得した投稿情報をTableViewを利用して配置する程度にとどめます。

それ以外の機能、例えば、Qiitaへのログイン処理や、Qiita上のユーザのフォロー機能といったQiitaのAPIをフル活用したアプリの開発は目指さないことにします

## QiitaViewer用にプロジェクトを作成する

Titanium Studioを起動して、QiitaViewer用にプロジェクトを作成するためにFile → New → Mobile Project と進みます

![選択メニュー](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-001.jpg)

Projectのテンプレートを選択する画面が標示されたら、「Default Project」を選択して次に進みます

![Projectのテンプレート選択画面](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-002.jpg)

Projectの詳細情報を入力する画面が標示されるので以下のように入力/選択して、finishボタンをクリックします

- プロジェクト名:QiitaViewer
- AppID:info.ti.qiitaviewer
- ターゲットOS:iPhone、Androidのみチェック。iPad、MobileWebはオフにする

![Projectの詳細情報入力画面](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-003.jpg)

しばらくすると、以下の様な画面が標示されて、Projectの初期設定が完了します

![Project設定完了後の画面](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-004.jpg)

念のためこの状態でbuildが出来るかどうか確認するため、Titanium StudioのApp Explorerのrunのアイコンを選択します

![App Explorerの画面キャプチャ](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-005.jpg)

iPhone Simulatorを選択した場合：以下の様な画面になります

![iPhone Simulator](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-006.jpg)

Android Emulatorを選択した場合：以下の様な画面になります

![Android Emulator](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-007.jpg)

Projectの設定が完了したので、次から具体的にQiitaViewerの機能を実装していきます


## Qiitaの投稿情報を取得する

Qiitaの開発者向けのAPIを通じて投稿情報を取得する機能を実装します

Titanium Mobileで実装をはじめる前に、Qiitaの投稿情報を取得する時のWebAPIについて簡単に説明します

### QiitaのWebAPIについて

ブラウザで以下URLにアクセスすることでパブリックな新着投稿を取得することができます。

[https://qiita.com/api/v1/items](https://qiita.com/api/v1/items)

※Google Chromeでアクセスした時の例
![ブラウザでアクセスした時のキャプチャ](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-webapi-001.jpg)

投稿情報は以下の様なJSON形式になりますが、詳しい情報を知りたい方は、[Qiitaのサイトをご覧ください](http://qiita.com/docs#13)

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

### Qiitaの投稿情報を取得する機能を実装する

Titanium MobileでQiitaのようなWebAPIと連携するアプリを開発する場合に、標準機能のhttpCLientを活用することで簡単に実現できます。まずはhttpCLientの使い方について解説をします。

プロジェクト作成時に自動的に生成されたapp.jsの中身を全て削除します。

その後に以下を記述します

```javascript
var xhr,url,method;
qiitaURL = "https://qiita.com/api/v1/items"
method = "GET";
xhr = Ti.Network.createHTTPClient();
xhr.open(method,qiitaURL);
xhr.onload = function(){
  var body;
  if (this.status === 200) {
    body = JSON.parse(this.responseText);
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
xhr.send();
```

動作確認するために、buildします。

iPhone Simulatorを選択した場合：以下の様な画面になります

![iPhone起動時の画面キャプチャ](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-iphone-001.jpg)

Android Emulatorを選択した場合：以下の様な画面になります

![Android起動時の画面キャプチャ](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-android-001.jpg)

シミュレーターの画面には何も標示されずコンソール上に複数の文字が標示されるかと思いますので、その点が確認できたらOKなので、次で画面に表示する方法について解説します

### Qiitaの投稿情報を取得する機能の解説

ご存じの方が多いかもしれませんが、念のためTitanium MobileのhttpClientについて解説します

```javascript
var xhr,url,method;
qiitaURL = "https://qiita.com/api/v1/items"
method = "GET";
xhr = Ti.Network.createHTTPClient(); // (1)
xhr.open(method,qiitaURL);  // (2)
xhr.onload = function(){
  var body;
  if (this.status === 200) { // (3)
    body = JSON.parse(this.responseText); // (4)
	Ti.API.info(body);
  } else {
    Ti.API.info("error:status code is " + this.status);
  }
}
xhr.onerror = function(e) { // (5)
  var error;
  error = JSON.parse(this.responseText);
  Ti.API.info(error.error);
}
xhr.timeout = 5000;
xhr.send();
```
(1)httpClientを利用するためのオブジェクトを生成します
(2)open()メソッドをQiitaの投稿の取得をする場合には、GETメソッドにて

詳しくは[Qiitaのドキュメント](http://qiita.com/docs#13)を参照してください
(3)
(4)
(5)

## Qiitaの投稿情報を取得した後に画面に表示する

QiitaのようなWebAPIを通じて取得した情報をiPhone/Android上で表示する際にTableViewを利用するかと思います

### 取得した結果をTableViewを活用して画面に表示する(1)

Qiitaの開発者向けのAPIを通じて投稿情報を取得した結果をTableViewを活用して表示する方法について解説します。



### 取得した結果をTableViewを活用して画面に表示する(2)

先程は、タイトルのみ表示する方法について解説しましたが、タイトルだけではなく投稿したユーザのアイコンと本文の一部をTableViewを活用して表示する方法について解説します
