**Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*

- [QiitaViewer用にプロジェクトを作成する](#qiitaviewer用にプロジェクトを作成する)
- [Qiitaの投稿情報を取得する](#qiitaの投稿情報を取得する)
	- [QiitaのWebAPIについて](#qiitaのwebapiについて)
	- [Qiitaの投稿情報を取得する機能を実装する](#qiitaの投稿情報を取得する機能を実装する)
		- [プロジェクト作成時に自動的に生成されたapp.jsの中身を全て削除します。](#プロジェクト作成時に自動的に生成されたappjsの中身を全て削除します。)
		- [その後に以下を記述します](#その後に以下を記述します)
		- [動作確認するために、buildします。](#動作確認するために、buildします。)
	- [Qiitaの投稿情報を取得する機能の解説](#qiitaの投稿情報を取得する機能の解説)
- [Qiitaの投稿情報を取得した後に画面に表示する](#qiitaの投稿情報を取得した後に画面に表示する)
	- [取得した結果をTableViewを活用して画面に表示する(1)](#取得した結果をtableviewを活用して画面に表示する1)
	- [取得した結果をTableViewを活用して画面に表示する(2)](#取得した結果をtableviewを活用して画面に表示する2)

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

<table>
<th>iPhone起動時の画面キャプチャ</th>
<th>Android起動時の画面キャプチャ</th>
<tr>
<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-006.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-006.jpg" alt="iPhone Simulator"></a>
</td>
<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-007.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-project-007.jpg" alt="iPhone Simulator"></a>
</td>
</tr>
</table>


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


#### プロジェクト作成時に自動的に生成されたapp.jsの中身を全て削除します。

#### その後に以下を記述します

```javascript
var xhr,qiitaURL,method;
qiitaURL = "https://qiita.com/api/v1/items";
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

#### 動作確認するために、buildします。

iPhone Simulatorを選択した場合：以下の様な画面になります

![iPhone起動時の画面キャプチャ](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-iphone-001.jpg)

Android Emulatorを選択した場合：以下の様な画面になります

![Android起動時の画面キャプチャ](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-android-001.jpg)

シミュレーターの画面には何も標示されずコンソール上に複数の文字が標示されるかと思いますので、その点が確認できたらOKなので、次で画面に表示する方法について解説します

### Qiitaの投稿情報を取得する機能の解説

ご存じの方が多いかもしれませんが、念のためTitanium MobileのhttpClientについて解説します

```javascript
var xhr,qiitaURL,method;
qiitaURL = "https://qiita.com/api/v1/items";
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

(2)open()メソッドを使ってQiitaのWebAPIにアクセスします。

最初の引数にHTTPメソッドを指定しますが、Qiitaの投稿の取得をする場合には、GETメソッドを指定する必要があります（詳しくは[Qiitaのドキュメント](http://qiita.com/docs#13)を参照してください)

次の引数で投稿情報を取得するQiitaのエンドポイントとなるURLを指定します。

(3)QiitaのWebAPIにアクセスして、接続成功したかどうかを判定して、その後の処理を実施します。具体的にはthis.statusの値を確認して、値が200の場合には接続成功しているため該当する処理を実施します

(4)this.responseTextの値を確認することで、サーバから取得できた値をテキスト形式で取得できます。this.responseTextは見た目はJSON形式になっていますが、そのまま変数に代入すると文字列としてその後処理されてしまうため、JSON.parse()を使って、JSON化した状態で変数に格納します

(5)例えば、QiitaのWebAPIにアクセスして、150リクエスト/1時間というAPIの利用制限に引っかかってしまう場合などはエラーになり、その時にはonerrorイベントが呼び出されます。

イメージとしては以下のような対応関係になります

![httpClientのonloadとonerrorの対応関係](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-overview-001.jpg)

## Qiitaの投稿情報を取得した後に画面に表示する

Titanium Mobileの標準APIであるhttpCLientを活用して、Qiitaの投稿情報を取得する処理までは実装出来ましたので、今度は取得した情報を画面に表示する部分について解説します

### 取得した結果をTableViewを活用して画面に表示する(1)

Qiitaの開発者向けのAPIを通じて投稿情報を取得した結果をTableViewを活用して表示する方法について解説します。

図にすると以下の様な処理になります。

![概念図１](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-overview-001.jpg)

まずは取得した投稿情報のタイトルのみを表示する以下のソースコードをサンプルに順次解説していきます。

```javascript
var xhr,qiitaURL,method,mainTable,win;
mainTable = Ti.UI.createTableView({
  width: 320,
  height:480,
  backgroundColor:"#fff",
  left: 0,
  top: 0
});
win = Ti.UI.createWindow({
  title:'QiitaViewer'
});

qiitaURL = "https://qiita.com/api/v1/items";
method = "GET";
xhr = Ti.Network.createHTTPClient();
xhr.open(method,qiitaURL);
xhr.onload = function(){
  var body,_i ,_len ,row ,rows,textLabel;
  if (this.status === 200) {
    body = JSON.parse(this.responseText);
    rows = [];
    for (_i = 0, _len = body.length; _i < _len; _i++) { // (1)
      Ti.API.info(body[_i].title);
      row = Ti.UI.createTableViewRow({	// (2)
        width: 'auto',
        height:40,
        borderWidth: 0,
		className:'entry',
        color:"#222"
      });
      textLabel = Ti.UI.createLabel({	// (3)
        width:'auto',
        height:30,
        top:5,
        left:5,
        color:'#222',
        font:{
          fontSize:16,
          fontWeight:'bold'
        },
        text:body[_i].title
      });
      row.add(textLabel);		// (4)
      rows.push(row);			// (5)
    }
    mainTable.setData(rows);    // (6)
    win.add(mainTable);
    win.open();

  } else {
    Ti.API.info("error:status code is " + this.status);
  }
};
xhr.onerror = function(e) {
  var error;
  error = JSON.parse(this.responseText);
  Ti.API.info(error.error);
};

xhr.send();
```

上記をbuildして、iPhone、AndroidのEmulatorで表示した場合以下の様になります

<table>
<th>iPhone起動時の画面キャプチャ</th>
<th>Android起動時の画面キャプチャ</th>
<tr>
<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-iphone-002.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-iphone-002.jpg" alt="iPhone Simulator"></a>
</td>
<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-android-002.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-httpClient-android-002.jpg" alt="iPhone Simulator"></a>
</tr>
</table>




### 取得した結果をTableViewを活用して画面に表示する(2)

先程は、タイトルのみ表示する方法について解説しましたが、タイトルだけではなく投稿したユーザのアイコンと本文の一部をTableViewを活用して表示する方法について解説します

図にすると以下の様な処理になります。

![概念図２](https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-overview-002.jpg)
