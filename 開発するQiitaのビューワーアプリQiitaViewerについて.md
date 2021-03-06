**Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*

- [Qiitaの簡易ビューワーアプリ「QiitaViewer」の機能について](#qiitaの簡易ビューワーアプリ「qiitaviewer」の機能について)
- [アプリケーションの仕様について](#アプリケーションの仕様について)

## Qiitaの簡易ビューワーアプリ「QiitaViewer」の機能について

本書での目的は
「標準のAPIを利用しつつも、少ない手間とちょっとしたアイデアで、標準APIを使ってる用に見せないためのTIPSを紹介する」
ことを主眼としているため、QiitaのWebAPIを通じて取得した投稿情報を表示するシンプルなものにとどめようと考えています。

それ以外の機能（例えばQiitaへのログイン処理、Qiita上のユーザのフォロー機能といったQiitaのAPIをフル活用するような機能）が実装されたアプリの開発は目指さないことにします

## アプリケーションの仕様について

- QiitaのWebAPIを通じてパブリックな新着投稿を取得することが出来ます
- 取得した投稿情報で任意の項目（例：投稿情報のタイトル、投稿者のアイコン画像など）をTableViewの機能を活用して画面に表示することが出来ます
- Titaniumの標準APIを活用するがネイティブモジュールは活用しません
- iPhoneとAndroidと両方のプラットフォームに対応しており、出来る限り同じUIデザインにすることを目指します
