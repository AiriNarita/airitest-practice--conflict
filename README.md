# git練習用レポジトリ

## 🚩 目次

- [pullの方向性](#pullの方向性)
- [rebase](#rebase)
- [コンフリクト解消法](#コンフリクト解消法)
- [参考文献](#参考文献)

## pullの方向性

pullは簡単に言えば、”引っ張ってくる”の意味だが、その引っ張ってきた後で、
**方向性(つながり方)によって、以下のように３通りに分かれる。**
![](https://storage.googleapis.com/zenn-user-upload/dee6fa8b1b17-20230224.jpg)

そして、mergeとrebaseのつながり方はこのような感じだ
![](https://storage.googleapis.com/zenn-user-upload/c04110126a0f-20230224.jpg)

上記したA,B,C…はブランチで、mergeとrebaseのつながり方の図だ。
**margeも、rebaseも、合わせる(ブランチを統合する)の意味では一緒**だ。
違いは、その繋がり方だ。

### merge

自分のブランチへ、**他のブランチの更新を取り込んで最新のブランチとする**。

## rebase

上記の絵で言うと、
元々DはBから切ったわけだが、このDブランチがrebaseコマンドを行うことで、
B->Cの**変更分を取り込んで、(分岐開始場所を変更して)最新ブランチとする**こと。

要するに、
**ブランチの基点(切った場所)となるブランチを、別のブランチに移動する**こと。
そして、**その間の他のブランチでの変更分も自分に取り込み**最新ブランチにする。
＝　コミットの**履歴もきれいに一直線になる(複雑にならない)**
= git logも確認しやすくなる。

> 一言メモ：rebaseという名前の意味は？！<br>
> re: 再び 　/  base:基点　　
> 親のコミット(ブランチ)=base　を、新しくするの意味。

ここまでの違いを理解したら、rebaseの使用場面がわかってくる.

**【主要な使い方】**

- 不要なマージコミットのないすっきりした、直線的な履歴にする
( 開発コミットを繋げ直す.別々のブランチで伸ばしていた開発コミットを繋げ直す )
- **複数コミットを1コミットにまとめる**

## コンフリクト解消法

### リモートでコンフリクトの発生を確認

![](https://storage.googleapis.com/zenn-user-upload/281c786d6228-20230225.jpg)

(少しみずらいかもですが、zoomお願いします…！)
このように、**PRのページでみると、コンフリクトが確認**でき、
マージしようとしてもできないことが確認できます。

このページの**resolve conflictsボタンで、修正箇所を見ることができる**し、
修正もできます。しかし！！！！**！ここでは基本しないこと！**
=> リモートのみでの修正になり、ローカルにプルした際に、
　　　　　　またそっちでも修正しないといけなく、二度手間だから。

### ローカルに戻りそのままpullし、手動で修正

![](https://storage.googleapis.com/zenn-user-upload/da9438d0af7e-20230225.jpg)

このように、**pullするとhintがたくさん出てきます**。**設定してね**の記述です。
いかに日本語訳も載せます。

[**merge,rebase,fast-fowardの３つのどれかで設定してください**]と、言っています。
この３つは前回話した３つですね！

今回は、**rebaseでの修正**方法を行なっていきます。

### pullの方向をrebaseに設定

今回はrebaseで行なっていきます。
以下のコマンドを打ち設定します。

`git config --global pull.rebase true`

このコマンドで設定完了です。
そしてこのように、コンフリクトの内容を明記してくれます。

![](https://storage.googleapis.com/zenn-user-upload/8f45d4c3f273-20230225.png)
今回は、top.htmlで発生しているので開きます。

![](https://storage.googleapis.com/zenn-user-upload/2c1f3f3d9117-20230225.png)

選択肢が表記されているので、残すものを選択しましょう。
両方も可能だし、片方も可能です。

### 修正後

**修正したら、`git add .`を**行い、変更をステージに上げましょう。
そして**コミットはせず、以下のコマンド実行**しましょう。

`git rebase --continue`

これはもう一度rebaseを行うと言うことです。
(=　修正を踏まえて、統合実行！)

### コミットメッセージの変更可能

このように、前回のコミットメッセージが出てきます。
ここのコミットに、**rebaseした内容も含むようになるので、変更できるようになっています**。

![](https://storage.googleapis.com/zenn-user-upload/d8e4a4175268-20230225.jpg)

ビームモードで表記されているので、コミットメッセージを変更しようとしてもできません。
=>**キーボードの i を押すことでinsertモードに代わり入力可能に！**

変更し終えたら、出ます。
(エンターできるようにするため）　　**esc　を押して、　`:wq!`　を入力。**
> w: wright　書く
> q: quit 出る

**これも終わったら、pushしましょう。**

するとこのようにでます。

![](https://storage.googleapis.com/zenn-user-upload/db42b39f1fd5-20230225.png)

これが出るのは、リモートと差があるからです.
(rebaseして修正してるので当たり前です。)

なので、次は…

### rebaseの時のみ、ーーforce使用

同じ開発チームの人とも確認した上で、

**`git push origin HEAD --force` で強制pushです。**

**コンフリクト解消です！！！**

これで、リモートに戻ると、マージも可能になっています！

![](https://storage.googleapis.com/zenn-user-upload/030678a957d3-20230225.png)

## 参考文献

- [`git style guide`](<https://github.com/objectx/git-style-guide#%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81>)
