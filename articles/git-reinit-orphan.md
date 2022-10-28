---
title: "mainブランチをまっさらな状態にしてやりなおす"
emoji: "🍽️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git"]
published: true
---

mainブランチを一旦まっさらな状態に戻してやりなおしたい。

## リモートブランチにバックアップをつくる

いままでのものは残しておきたいのでバックアップ用ブランチをつくる。
`backup-<日付>`のようなリモートブランチにバックアップしておく。

```sh
$ git push origin main:backup-<日付>
```

## 空ブランチをつくる

まっさらな状態の空ブランチをつくる。ここでは`empty`という名前にした。

```sh
$ git checkout --orphan empty
$ git rm -rf .
$ git commit --allow-empty -m "init"
```

## mainブランチを空にする

```sh
$ git checkout main
$ git reset --hard empty
$ git push origin --force
```

## あとがき

ちゃんと履歴を残しながら作業するのがよいんだろうけど、いろいろ面倒になってまっさらにしたいことがある。