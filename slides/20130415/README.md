vagrant-lxc を触ってみた
=============
<br />
<address>Kohei Hasegawa / @banyan / paperboy&co.</address>
Chef Casual Talks Vol.1 / 20130415
<!-- data-x="-18000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

普段はアプリケーション寄りの仕事がメインでインフラは専門の人がたくさんいるのであまり触ってません。
----------
<!-- data-x="-9000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

今日はアプリケーション寄りの視点からお話できればと思います。

Vagrant + LXC
----------

* [fgrehm/vagrant-lxc](https://github.com/fgrehm/vagrant-lxc)
 * Vagrant の plugin として利用可能、API が Vagrant のままなので使いやすい
* [chrisroberts/vagabond](https://github.com/chrisroberts/vagabond)
 * vagabond は yet another vagrant for lxc みたいなもの。試したがうまく動かなかった...

vagabond の README の中に
----------

* Issue: VMs are slow.
* Discovery: Linux has LXC, which is pretty cool.
* Helpful: LXCs can run different distributions.
* Implementation: Vagabond

LXC とは?
----------

* LXC (Linux Container) は Linux カーネルの Control Group や Namespace という機能を使って，ホストOSとは隔離された環境を作るツール
* KVM や VirtualBox と比べてオーバーヘッドが小さく軽い仮想化ソフトウェアの一種が LXC
* [参照: 仮想化ソフトウェアとchrootの“いいとこ取り”─LXCで実現するVPS](http://gihyo.jp/admin/column/01/vm/2011/lxc_container)

vagrant-lxc で vagrant/VB と速さを比較してみたい
----------

* 普段 Rails のアプリケーションを書いていて、テストにとても時間がかかる
* 最近の Rails のオープンソースのアプリケーションだと、プロジェクトに Vagrantfile が用意されているものも多い
 * [rails/rails-dev-box](https://github.com/rails/rails-dev-box)
  * rails 自体を開発する環境 (Puppet + Vagrant)
 * [discourse/discourse](https://github.com/discourse/discourse)
  * 掲示板みたいなサービス (Vagrant ファイル)
 * [banyan/chef-rails-dev-box](https://github.com/banyan/chef-rails-dev-box)
  * rails-dev-box を chef で車輪の再発明してみました
* VirtualBox は windows/mac をカバーしていて lxc に比べれば便利だけど、lxc がどれくらい速いかどうかは試したい
* 今回は rails-dev-box を使います

setup vagrant-lxc
----------

    $ sudo apt-get -y install lxc redir
    $ wget "http://files.vagrantup.com/packages/64e360814c3ad960d810456add977fd4c7d47ce6/vagrant_`uname -m`.deb" -O /tmp/vagrant.deb
    $ sudo dpkg -i /tmp/vagrant.deb
    $ vagrant plugin install vagrant-lxc
    $ vagrant box add lxc-quantal64 http://dl.dropbox.com/u/13510779/lxc-quantal64-2013-04-10.box
    $ vagrant up --provider=lxc

Vagrant/VB on Mac
----------

    real    134m21.753s
    user    24m7.230s
    sys     66m6.972s

vagrant-lxc on Ubuntu (m1.small)
----------

    real    68m20.202s
    user    50m21.161s
    sys     11m42.844s

* [used Vagrantfile](https://gist.github.com/banyan/5383379)

Vagrant/VB on Mac with NFS
----------


結論
----------

* lxc のパフォーマンスがよいことを期待したけど、NFS を有効にした VirtualBox とそんなに差がでなかった。
* vagrant-lxc の cgroups の settings を有効にすると vagrant up で止まってしまった。
 * まだ vagrant-lxc もバギーな挙動が多かった (一度 halt で止めると起動がうまくいかなかったりとか)
