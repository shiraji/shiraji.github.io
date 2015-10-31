# shiraji.github.io

[Octopress](http://octopress.org/)製ブログ

# URL

http://shiraji.github.io/

# 操作方法

以下shiraji向けOctopress利用メモ

## プレビュー

```shell
rake preview
```

[ローカルサイト](http://localhost:4000/)

## エントリー

```shell
rake new_post[title]
```

`source/_posts/2015-01-01-title.markdown`が出来る。
このファイル名は変更して問題ない。というか日本語タイトルにすると変な英語になるので、変更推奨。

## deploy

```shell
rake gen_deploy
```
