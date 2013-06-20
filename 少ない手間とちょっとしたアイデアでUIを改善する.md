前の章でQiitaViewerの基本機能の実装が完成したので、この章では、少ない手間とちょっとしたアイデアでUIを改善する方法について解説します

## アプリのUIデザインは「ズルいデザインテクニック」を活用する

少ない手間とちょっとしたアイデアでUIを改善する際のベースとなる考えについて紹介したい記事が、@ITというニュースサイトにあったのでまずはそれについて触れて行きたいと思います

@ITの[HTML5 + UX : HTML5とUXの総合情報フォーラム](http://www.atmarkit.co.jp/ait/subtop/ux/)というカテゴリの中に[少ない手間と知識でそれなりに見せる、ズルいデザインテクニック](http://www.atmarkit.co.jp/ait/articles/1212/06/news004.html)という記事があります。

上記記事で

> Webプログラマ、Webエンジニアの皆さんが、個人で作るWebサービスやハッカソンなどで、短期間に集中してサービス開発してローンチしたいときに、もうちょっと自分でイイ感じにデザインできるといいなあという声をよく聞きます。
>
>この企画は、そんなプログラマが、少ない手間で簡単に、ちょっといい感じのデザインに見せるための、ちょっとした小ズルいTipsを紹介します。

ということが書かれていましたが、この考えを取り入れることで、QiitaViewerのUIを改善していくことを目指していきます

ズルいデザインテクニックとしていくつか紹介されていますが、Titaniumでの開発で一番使えそうだと個人的に感じたズルいグラデーションを取り上げることにします

## ズルいグラデーションとは?

[SassやCompassを使って、ズルいデザインテクニック (1/2)](http://www.atmarkit.co.jp/ait/articles/1302/26/news059.html)という記事の中でズルいグラデーションという内容が取り上げられているので、そこから一部引用します

>ベタ塗り一歩手前くらいの、微妙な明度差のグラデーションで本物っぽい質感を出そう
>
>一見、1色でベタ塗りにしたように見えるけど、よく見ると微妙に上下で色の明るさが異なるグラデーションを、「ズルいグラデーション」と呼んでいます。


このような考え方を取り入れることで、Titanium標準のAPIだけで作ったとは思えないようなUIが比較的簡単に作ることができるので、これ以降で具体的な実装方法について説明します

## Titanium MobileにおけるUIの背景色の設定

View、ButtonといったUI の背景色に単色の塗りを設定したい場合には、backgroundColor プロパティに値を設定します。

背景色に単色の色（例：黒）を設定したボタンを生成するサンプルコードは以下のようなものになります。

```javascript
var addUserButton = Titanium.UI.createButton({
    top: 5,
    left: 5,
    width: 50,
    height: 25,
    title: 'Add user',
    backgroundColor: '#000000',
    color: '#ffffff',
});
```
背景色にグラデーションを設定したい場合には、backgroundGradient というプロパティに値を設定しますが、backgroundGradientプロパティは設定できる項目があるので以下で掘り下げて解説します

### backgroundGradientのtypeについて
ここで指定できる値は、’linear’か’radial’のどちらかになります。

直線でグラデーションという場合にはlinearを指定しますが、radial使うと複雑なグラデーションを指定出来るようになります

### backgroundGradientのstartPointとかendPoint

グラデーションがかかる方向（縦方向 or 横方向）をstartPointとかendPointの値により決めることが出来ます。

縦方向のグラデーションを設定したい場合には、startPoint、endPointのxの値は同じに設定して、yの値を0→100%のようにすることで可能になります。

サンプルコードとしては

```javascript
var addUserButton = Titanium.UI.createButton({
    top: 5,
    left: 5,
    width: 50,
    height: 25,
    title: 'Add user',
    backgroundGradient: {
        type: 'linear',
        startPoint: {
          x: '0%',
          y: '0%'
        },
        endPoint: {
          x: '0%',
          y: '100%'
        }
      },
    color: '#ffffff',
});

```
のようになります。


横方向のグラデーションを設定したい場合には、startPoint、endPointのxの値を0→100%のにして、yの値は同じものに設定することで可能になります。

サンプルコードとしては

```javascript
var addUserButton = Titanium.UI.createButton({
    top: 5,
    left: 5,
    width: 50,
    height: 25,
    title: 'Add user',
    backgroundGradient: {
        type: 'linear',
        startPoint: {
          x: '0%',
          y: '0%'
        },
        endPoint: {
          x: '100%',
          y: '0%'
        }
      },
    color: '#ffffff',
});
```
のようになります。

### backgroundGradientのcolorsプロパティのcolorとpositionについて
グラデーションの配色具合を調整しますが、実際のコードをサンプルにしながら順番に解説します。
```javascript
backgroundGradient: {
    type: 'linear',
    startPoint: {
      x: '0%',
      y: '0%'
    },
    endPoint: {
      x: '0%',
      y: '100%'
    },
    colors: [
      {
        color: '#222',
        position: 0.0
      }, {
        color: '#363636',
        position: 0.3
      }, {
        color: '#dfed33',
        position: 1.0
      }
    ]
  }
```
typeをlinearにしてstartPointのx,yを同じ値にして、yの値を0→100%にしているので、直線で縦方向にグラデーションがかかります。

さて、メインとなるcolorsの所ですが
```javascript
    colors: [
      {
        color: '#222',
        position: 0.0
      }, {
        color: '#363636',
        position: 0.3
      }, {
        color: '#dfed33',
        position: 1.0
      }
    ]

```
このように、colorと positionの２つのプロパティを持つオブジェクトを1つ以上含んだ配列を指定します

この例の場合ですと

- 塗りつぶしの開始位置から30%まで:#222→#363636のグラデーション

- 30%から塗りつぶしの終了位置:#363636→#dfed33のグラデーション

となり、画面上ではこのような表示になります

![実際のグラデーションの画面キャプチャ](http://titableview.info/img/tableViewGradientVerticalYellow.png)

黒から黄色という感じで通常はあまり見かけないような配色をしてみましたが、これでグラデーションがどのように設定されるのか理解しやすいのではと思ってます

