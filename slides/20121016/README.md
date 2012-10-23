A Brief Introduction About Sqale
=============
<br />
<address>Kohei Hasegawa@banyan<br />paperboy&co.<br />20121016</address>
<!-- data-x="-18000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

paperboy&co. が新しく始めたホスティングサービス - Sqale
----------
* 2012年5月23日にクローズドβリリース
* 2012年8月23日に正式リリース
* 意図的に PaaS という言葉は省いた。
  * PaaS という言葉を分かる人だけが対象にするのではなく、もっと間口を広げたい

対応している言語、フレームワーク
----------
<!-- data-x="-9000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

* 現状では言語としては Ruby のみ
* Rack アプリケーションに対応しています
* PHP は恐らく次に取り組む予定です

利用されている数
----------
<!-- data-x="-3000" -->
<!-- data-y="-1000" -->
<!-- data-rotate-y="100" -->

* ユーザ登録をされているものはビジネスとしてはまだまだ厳しい段階
* 社内的には新卒の研修で使われたり、あとは新規事業などでも Sqale を利用する予定などがあります。

どこのデータセンター or IaaSを使ってる？
----------

* AWS (東京リージョン)

稼働保証はあるか？
----------

* SLA を明示的に謳うかどうかは検討中
* AWS 自体に SLA があるとはいえ、冗長化を考える必要があり、リージョンを変えたバックアッププランなどの提供も検討している。

初めて使う時の流れ - お試し or 無料アカウント
----------

* お試し(or 無料)アカウントの作り方、カードは必要？
  * カードは必要
  * お試しはプロジェクト単位で利用可能 (15日間を予定)
  * アカウント自体は無料アカウント

初めて使う時の流れ - 必要なツール
----------

* 特に必要なツールをインストールする必要はないですが、Ruby 環境と Git (or SFTP) が必要です

初めて使う時の流れ - デプロイまでの大まかな流れ
----------

* [https://github.com/paperboy-sqale/sqale-support](https://github.com/paperboy-sqale/sqale-support) に Mac or Windows でのはじめ方があります
  * [GettingStartedForWindows](https://github.com/paperboy-sqale/sqale-support/wiki/GettingStartedForWindows)
  * [GettingStartedForMac](https://github.com/paperboy-sqale/sqale-support/wiki/GettingStartedForMac)
* デモは後で行います

想定しているユーザ
----------

* 個人ユーザを対象としています
* 個人ユーザの中でもビギナーも意識しています
* paperboy&co. ではロリポップ、ヘテムルなど長くレンタルサーバを提供してきました。
* 法人ユーザも多くいますが、ロリポップなどは基本的に個人がメインで、その中で親しみやすい、使いやすい、低価格ということで市場に受け入れていただきました
* 前から声のあった Rails を使いたいというニーズに応える形もあります。
* VPS を使うのに抵抗があるユーザ

今後の展開
----------

* Web アプリケーションとして必要なミドルウェアなどひと通り提供できるようにする
* Ruby 以外の言語に対応
* 国際化等

Archtecture
----------
* [Archtecture](images/archtecture.png)
* Talked at Sapporo Ruby Kaigi 2012 and YAPC::Asia Tokyo 2012
* [Inside Sqale](http://yapcasia.org/2012/talk/show/048d468c-ab9e-11e1-a3b5-2a656aeab6a4) by mizzy.

Any question or request?
----------

ありがとうございました。
