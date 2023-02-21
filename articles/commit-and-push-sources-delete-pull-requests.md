---
title: "公開予定でないソースをリモートにプッシュ・プルリクエストしてしまった時の対処法"
emoji: "😇"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git"]
published: true
published_at: 2023-02-23 00:00
---

公開予定ではないコミットしたソースをリモートにプッシュ・プルリクエストした際の対処方法をご紹介します。

# 前提条件

1. 公開予定でないソースをコミット、リモートに push している
2. 1 の状況でプルリクエストをしている

# 対処手順

## 【手順 1】git-filter-repo をインストール

```
brew install git-filter-repo
```
[git filter-repo](https://github.com/newren/git-filter-repo)は履歴を書き換えるための汎用ツールです。

## 【手順 2】対象リポジトリをクローン

履歴の修正を行う前に、新しく clone した状態の物を利用しましょう。

```
git clone https://github.com/your-username/hoge
```

## 【手順 3】対象リポジトリの作業ディレクトリに移動

```
cd your-repository
```

## 【手順 4】以下のコマンドに削除対象ファイル指定

※ --path 以降に削除対象ファイルパスを指定
```
git filter-repo --invert-paths --path hoge/hogehge/hoge.txt
```

## 【手順 5】削除対象ファイルを.gitignore に追加
機密情報を含むソースを再度誤ってコミットしないように.gitignoreに追加します。

```
echo hoge/hogehoge/hoge.txt >> .gitignore
```

## 【手順 6】削除されているかの確認

以下のコマンド等を使用して、削除対象ファイルのコミット履歴が削除されているか確認します。

```
git log [削除対象ファイルを含んだコミットID]
```

```
git show [削除対象ファイルを含んだコミットID]
```

## 【手順 7】ローカル変更をリモートに強制的にプッシュ
プッシュしたブランチを全て上書きするためには、強制プッシュが必要です。
```
 git push origin --force --all
```

もし以下のエラーメッセージが表示された場合は、リモートリポジトリの設定がされていません。
```
fatal: 'origin' does not appear to be a git repository
fatal: Could not read from remote repository.
```

リモートリポジトリの設定には以下のコマンド実行
※origin 以降には設定したい自身のリモートリポジトリのアクセス先を記載
```
git remote add orign https://github.com/hoge/hoge.git
```

## 【手順 8】GitHub からデータを完全に削除する

:::message
API KEYや個人情報等の機密情報をコミット・プッシュしてしまった場合は、
  以下のGithub 個人情報ポリシーを参考にしてください
:::

[GitHub 個人情報削除ポリシー](https://docs.github.com/ja/site-policy/content-removal-policies/github-private-information-removal-policy)に従って対象リポジトリのプルリクエストの削除依頼を行います。

https://docs.github.com/ja/site-policy/content-removal-policies/github-private-information-removal-policy

#### 以上で意図しないソースのコミット・プッシュ、プルリクエストを削除できます。
# 再発防止に向けて

業務だけでなく、個人開発等行っている人で、様々な外部APIを使用する機会があると思います。
その際は外部APIの利用状況確認やAPI KEYの適切な管理が必要になります。
管理する上でどれだけ確認して防ごうとしても、ツール等を使用しない状態だとヒューマンエラーのリスクは必ず伴います。
##### そういったときに有効なのが、次に紹介する「GitGuardian」です。

- ### [GitGuardian](https://www.gitguardian.com/)の導入

GitGuardianとは、機密情報をリアルタイムに検知するサービスです。

詳しい内容は以下のサイトを参照してください。
https://www.twx-threatintel.com/security-service/gitguardian_lp/

# 参考資料・サイト
- ### [GitHubから流出した機密情報をリアルタイムに検知](https://www.twx-threatintel.com/security-service/gitguardian_lp/)
- ### [GitGuardian](https://www.gitguardian.com/)