Part 1. How Does Developing with Git at Paperboy&co.
=============
<br />
<address>Kohei Hasegawa@banyan<br />paperboy&co.<br />20120619</address>
<!-- data-x="-18000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

最初にすごく簡単にペパボと Git の自分の考える特徴を振り返ります。
----------

ペパボたくさんの事業部がある。
----------
<!-- data-x="-9000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

* lolipop
* muumuu-domain
* heteml
* colorme
* etc...

特徴として全てのチームにデザイナがアサインされる
----------
<!-- data-x="-3000" -->
<!-- data-y="-1000" -->
<!-- data-rotate-y="100" -->

最近の某プロジェクトの最初の構成

* ディレクタ1人
* プログラマ1人
* デザイナ1人

なのでデザイナとどう協業するか考える必要がある
----------

* この会社が Webistrano をこんなに使ってるわけもそこに理由があると思う
 * Webistrano なんかだるい、Capistrano でいいじゃんという意見に対して思うこと
 * 例えば hsbt さんが作った rake-confirm とかは不便にするもの
 * 不便にすることで安心できる、デプロイ作業においてはワンクリックで全部デプロイできます！という利便性よりも本質的に慎重なものだから意図的に不便にしたほうがいい箇所がある
 * ログ見れたり、ロールバックいつしたか、とか最新のバージョンとか見れて便利

A Case of heteml
----------

Windows ユーザのデザイナには
----------

* GUI を使ってもらうよりもターミナルにログインしてもらって、CUI で操作してもらう
 * Samba 経由で Git を操作すると時間がすごいかかった (社内のファイルサーバの問題もあるだろうし、プロジェクトが長くてリポジトリ自体も巨大)
* 同じ API で覚えてもらうことができる
* Linux の簡単な操作を覚えるくらいなら学習コスト低い (1h くらい)

git-flow 使った
----------

* git-flow でも git-daily でも何かラッパーツールはあったほうがいい。
* 本質的に全てのプロジェクトに合うラッパーツールはなくて、git-flow の哲学が悪いのではなくて git-flow 自体は正しいし、最終的には自分達で作る感じになると思う
* git-flow を使うのはデザイナのためだと思う
 * 全くの0からなら git-flow のほうが説明しやすいのでは、と思う
 * API 分かりやすい
* プログラマやインフラしかいない開発の場合は、適当にルール作ってやればいいと思う

実際の導入を振り返って
----------

* Git 自体の説明 + Linux (1h)
* git-flow での複数人での開発 (1h)

で理解できてもらった気がする。問題も起きてないと思う

もっとうまくやれた部分とか
----------

* sqale で今試してる部分もあるので、heteml とかにもうまくいく部分は共有していきたい
* antipop さんが作ってる new webistrano との連携で更にペパボの開発にあった感じのものができて便利になりそう

Part2. Some Tips for Git User
----------

* あんまり Git の使い方についてみんなで話す機会ない
* Git どうですか？といってもぱっとは出てこないのでひとつずつコマンド見たりする
* それぞれについて説明・実演をしつつ、みんなが「こんなことも知らないのか」とあきれて、いろいろ教えてくれる[第1回Ruby開発環境勉強会](http://kentaro.hatenablog.com/entry/2012/05/29/230254) あんちぽさんメソッドでいきたいと思います。
* 途中でつっこみ Welcome です。（終わってからでも OK です)

1. コミットメッセージ
----------
* 何をしたかは見ればわかるけど、どうしてそうしたかを書く
* 英語でコミットメッセージ書けば適当に書いて免責されるわけじゃない
 * 社内でも英語でコミットメッセージを書くのは全然いいと思うけど、そのコミットを英語で表現できない時は素直に日本語使ったほうがいい
* コミットメッセージは未来のあなたと、誰かに対する愛

2. コミットメッセージの書き方
----------
    Gemfile がなくても open エラーにならないようにする # ←タイトル
                                                       # ←空行
    ワーキングディレクトリ (リポジトリのルート) に Gemfile という名前のファイルがないと、書き換え前に中身を読むところで例外が発生してしまう。
    いろいろやりかたはありそうだけど、空ファイルでも存在すれば ok なので touch で作ってしまう。

* 慣習的にこういう風に書く
* 1行目に何をするものか完結にタイトルを書く
* 一行空けて本文を書く
* [tpope さんのタイトルは50文字、段落は72文字までに折り返しましょうサイト](http://stopwritingramblingcommitmessages.com/)
* [Gitのコミットメッセージに関する注意点](http://keijinsonyaban.blogspot.jp/2011/01/git.html)

3. コミットの粒度
----------
* 刺身先生による [明日からできる！バージョン管理システムへのコミット粒度を最適化するトレーニング方法](http://d.hatena.ne.jp/a666666/20110910/1315761292)
* Git だとブランチを作れば後で参照できるので、ある種ネームスペースがあるというか、よりコミットの粒度意識しなくてもいいですね
* 刺身さんの粒度すごい。1時間に20個くらいコミットしてる時もある。そこまでみんながする必要はないけど、ちょっと見てみると色々参考になります。

4. git config (.gitconfig)
----------
    $ git config --global core.editor vim
    $ vi ~/.gitconfig

* alias とか設定したりすると便利
* なにかおすすめの設定ありますか?

5. git status
----------

    $ git status -sb # のほうが慣れると見やすい

    # .gitconfig
    [alias]
        st = status -sb

6. git stash
----------
現在作業中のbranchでまだコミットはしたくないけど、trunkで直さないといけないバグとかが見つかったときに、今の変更を横にどけておくコマンド。

    $ git stash
    $ git stash pop

若者の git stash 忘れが多発

 * zsh で git stash を表示する
 * http://less.carbonfairy.org/post/17714419750
 * bash の人も「bash git stash」とかで検索したりして表示する

7. git rebase
----------
* [gitで複数のコミットを1つにまとめる](http://labs.timedia.co.jp/2010/11/git-squash-commits.html)
 * 分かりやすい
* コミットの順序を変える
 * デモ
* 今したコミットと2つ前のコミットと merge する
 * デモ
* 公開リポジトリにプッシュしたコミットをリベースしてはいけない

8. git grep
----------
    $ git grep hoge
    $ git grep -i Hoge (Ignore case differences between the patterns and the files.)

* 個人的には全然使ってないです (ack 使ってる)

9. git reset
----------
ローカルリポジトリ、インデックスを元に戻す

    $ git reset HEAD^
    $ git reset --hard HEAD^^

10. git reflog
----------
* Git では一度でもコミットするか、インデックスにいれれば、歴史に入るので、データが消えることはない
     $ git reflog

11. git blame
----------
$ tig blame のほうが便利

12. git clean
----------
ワーキングツリーを掃除する

* 削除されるファイルを確認する / -n, --dry-run
* 実際にファイルを削除する / -f, --force
* ディレクトリを削除する / -d

12. git now
----------
* git-now は native なコマンドではないです。

    $ brew install git-now

13. git slave
----------
* a great alternative to git-submodules when forming superprojects out of repositories you control.
* まだ使ったことないけど、git submodule の代用として使えるケースがあるらしい
* http://gitslave.sourceforge.net/

14. tig
----------
* tig かわいいよ tig
* git log の代用として使ってます
 * git log もたまに使う
* .tigrc とかで設定できる
* vimmer は Shift-G で git gc が走らないように設定しておく

15. 必要ない記述を消したけど、そこに書かれていた記述を参照したい
----------
>コメントアウトするくらいなら消してください。 そして、これを消すコミットでなぜ消したのかを説明してください。 バージョン管理システムはそのためにあります。

って永和さんの研修でレビューされてた。個人的にはコメントアウトしてもいいケースはあるとは思うけど、必要ない箇所はばっさり消すべきだとは思う。
で消したけど、その記述がほしい場合

15. 必要ない記述を消したけど、そこに書かれていた記述を参照したいの続き
----------

    # どっかのタイミングで hoge.html を削除したとする。
    # その hoge.html がほしい
    $ git log # git log でみると、そのコミットのひとつ前の hash なので注意
    $ git checkout <hash> hoge.html # で戻せる

16. git add -p で分割できるより更にコミットの粒度を下げるには？
----------

s をタイプすると、より細かく分割できる。
s でもダメな場合は、e(edit)

17. ブランチ作ったけど、ブランチ作ったときと意味が変わってきたので、ブランチ名変えたい
----------
    $ git branch -m old-branch-name new-branch-name

18. 1つ前のコミットに含めてコミットしたい
----------
    $ git commit --amend -m "foo"

* コミットするものがなくても、コミットメッセージだけを変更する場合も同じ操作でいけます

19. 1つ前のコミットに含めてコミットするが、コミットメッセージは1つ前のコミットのものを使う
----------
    $ git commit --amend -C HEAD
    $ git amend

    [alias]
        amend = commit --amend -C HEAD

ご清聴ありがとうございました
----------

* 参考
 * 入門 Git
 * Git によるバージョン管理
