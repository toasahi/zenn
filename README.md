# zenn


### 記事の作成
以下のコマンドによりmarkdownファイルを簡単に作成できます。
```shell
bunx zenn new:article
```

markdownファイル作成時にFront Matterを指定できる
```shell
bunx zenn new:article --slug 記事のスラッグ --title タイトル --type idea --emoji ✨
```

作成されたファイルの中身は次のようになっています。
```
---
title: ""
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
ここから本文を書く
```

### プレビューする
本文の執筆は、ブラウザでプレビューしながら確認できます。ブラウザでプレビューするためには次のコマンドを実行します。

```shell
bunx zenn preview --port 3000 # プレビュー開始
```

