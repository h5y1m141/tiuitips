ここまでの解説を踏まえてQiitaViewerのUIを改善していきます

まずは改善前と改善後のキャプチャを以下に示します

<table>
<th></th>
<th>iPhoneの画面キャプチャ</th>
<th>Androidの画面キャプチャ</th>
<tr>
<td>Before</td>
<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-iphone-001.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-iphone-001.jpg" alt="iPhone Simulator" style="max-width:100%;"></a>
</td>

<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-android-001.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-android-001.jpg" alt="Android Simulator" style="max-width:100%;"></a>
</td>

</tr>
<tr>
<td>After</td>
<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-iphone-002.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-iphone-002.jpg" alt="背景をグラデーション設定したiPhone Simulator" style="max-width:100%;"></a>
</td>

<td>
<a href="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-android-002.jpg" target="_blank"><img src="https://s3-ap-northeast-1.amazonaws.com/tiuitips/qiitaviewer-tableView-android-002.jpg" alt="背景をグラデーション設定したAndroid Simulator" style="max-width:100%;"></a>
</td>

</tr>
</table>

## TableViewのbackgroundGradientプロパティを活用

Qiitaの開発者向けのAPIを通じて投稿情報を取得した結果をTableViewを活用して表示するようにQiitaViewerを実装していましたが、このTableViewの背景色の設定を、color プロパティではなくbackgroundGradientプロパティを設定することで上記のような UI を実現することが出来ます

私の手元の環境ではiPhoneとAndroid とで少しだけ実装方法が異なるのでその部分に触れながら以下でソースコードの解説をしていきます

## TableViewRowのbackgroundGradientプロパティがAndroid で反映されない

[Titanium MobileのAPI リファレンス](http://docs.appcelerator.com/titanium/latest/)を見る限り TableViewRowのbackgroundGradientプロパティはiPhone・Android両対応のはずですが、なぜかAndroid だけうまく反映されません。また以下のように他の方でも似たようなことが生じているようです

- [Titanium Android の TableViewRow は backgroundGradient が効かない](http://qiita.com/cathandnya/items/176ba1c2ca699b55cdd0)

> タイトルで言いたいことを言ってしまった。
iOSは効くけどAndroidは効かない。

- [backgroundGradient in a tableViewRow](http://developer.appcelerator.com/question/153579/backgroundgradient-in-a-tableviewrow)

> i can see perfectly the gradient in the browser, but in emulator and the Android device i can see the row but i can't see the gradient.

そのため、Androidについては、TableViewRowの backgroundGradient は利用せず、TableViewRowの上にViewを配置して、そのViewに対して backgroundGradient プロパティを設定することで同じUIを実現することが出来ます

## iPhoneとAndroidとで処理を分ける方法

実行中のアプリのプラットフォームのOS名は Titanium.Platform.osname というAPIを活用することで確認できます。

具体的には
```javascript
if(Titanium.Platform.osname === "iphone"){
  // iPhoneの場合の処理
  // ソースは後述

}else{
  // Androidの場合の処理
  // ソースは後述
}
```
のようにすることで、iPhoneとAndroidで処理を分けることが出来ます

### iPhoneの場合の処理

iPhoneの場合には、TableViewRowの backgroundGradient が活用できるので以下の様にする事で、最初の画面キャプチャーのようなUIが実現できます

```javascript
if(Titanium.Platform.osname==="iphone"){
  // iPhoneの場合の処理
  row = Ti.UI.createTableViewRow({
    width: 'auto',
    height:60,
    borderWidth: 0,
	  className:'entry',
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
      colors: colorSet
    }

  });
  textLabel = Ti.UI.createLabel({
    width:250,
    height:30,
    top:5,
    left:60,
    color:'#222',
    font:{
      fontSize:16,
      fontWeight:'bold'
    },
    text:body[_i].title
  });
  imagePath = body[_i].user.profile_image_url;
  iconImage = Ti.UI.createImageView({
    width:40,
    height:40,
    top:5,
    left:5,
    borderColor:"#bbb",
    borderWidth:1,
    defaultImage:"logo.png",
    image: imagePath
  });

  row.add(textLabel);
  row.add(iconImage);
}else{
  // Androidの場合の処理

}
```


### Androidの場合の処理

Androidの場合には、TableViewRowの上にViewを配置して、Viewの backgroundGradient を活用することで最初の画面キャプチャーのようなUIが実現できます

```javascript
if(Titanium.Platform.osname==="iphone"){
  // iPhoneの場合の処理
  // 省略
}else{
  // Androidの場合の処理
  row = Ti.UI.createTableViewRow({
    width: 'auto',
    height:60,
    borderWidth: 0,
	  className:'entry'

  });
  view = Ti.UI.createView({
    width: 'auto',
    height:60,
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
      color:colorSet
    }
  });
  textLabel = Ti.UI.createLabel({
    width:250,
    height:30,
    top:5,
    left:60,
    color:'#222',
    font:{
      fontSize:16,
      fontWeight:'bold'
    },
    text:body[_i].title
  });
  imagePath = body[_i].user.profile_image_url;
  iconImage = Ti.UI.createImageView({
    width:40,
    height:40,
    top:5,
    left:5,
    borderColor:"#bbb",
    borderWidth:1,
    defaultImage:"logo.png",
    image: imagePath
  });
  view.add(textLabel);
  view.add(iconImage);
  row.add(view);
}
```

## 最終的なソースコード

```javascript
var xhr,qiitaURL,method,mainTable,win,view,colorSet;
mainTable = Ti.UI.createTableView({
  width: 320,
  height:480,
  backgroundColor:"#fff",
  separatorColor:"#ccc",
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
  var body,_i ,_len ,row ,rows,textLabel,iconImage,imagePath;
  if (this.status === 200) {
    body = JSON.parse(this.responseText);
    rows = [];
    colorSet = [
      {
        color: '#f8f8f8',
        position: 0.0
      },{
        color: '#f2f2f2',
        position: 0.7
      },{
        color: '#e8e8e8',
        position: 1.0
      }
    ];
    for (_i = 0, _len = body.length; _i < _len; _i++) {
      if(Titanium.Platform.osname==="iphone"){
        row = Ti.UI.createTableViewRow({
          width: 'auto',
          height:60,
          borderWidth: 0,
	  className:'entry',
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
            colors: colorSet
          }

        });
        textLabel = Ti.UI.createLabel({
          width:250,
          height:30,
          top:5,
          left:60,
          color:'#222',
          font:{
            fontSize:16,
            fontWeight:'bold'
          },
          text:body[_i].title
        });
        imagePath = body[_i].user.profile_image_url;
        iconImage = Ti.UI.createImageView({
          width:40,
          height:40,
          top:5,
          left:5,
          borderColor:"#bbb",
          borderWidth:1,
          defaultImage:"logo.png",
          image: imagePath
        });

        row.add(textLabel);
        row.add(iconImage);

      }else{
        row = Ti.UI.createTableViewRow({
          width: 'auto',
          height:60,
          borderWidth: 0,
	  className:'entry'

        });
        view = Ti.UI.createView({
          width: 'auto',
          height:60,
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
            color:colorSet
          }
        });
        textLabel = Ti.UI.createLabel({
          width:250,
          height:30,
          top:5,
          left:60,
          color:'#222',
          font:{
            fontSize:16,
            fontWeight:'bold'
          },
          text:body[_i].title
        });
        imagePath = body[_i].user.profile_image_url;
        iconImage = Ti.UI.createImageView({
          width:40,
          height:40,
          top:5,
          left:5,
          borderColor:"#bbb",
          borderWidth:1,
          defaultImage:"logo.png",
          image: imagePath
        });
        view.add(textLabel);
        view.add(iconImage);
        row.add(view);

      }
      rows.push(row);
    }
    mainTable.setData(rows);
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
