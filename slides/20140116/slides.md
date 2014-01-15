# Inside Dokku in 5 minutes

!SLIDE

# Inside Dokku in 5 minutes
## Heroku Meetup #11 New Year Party!! - 新年会 - / 20140104
### Kohei Hasegawa @banyan, Quipper, Ltd.

!SLIDE

最近遊びのアプリケーションを Dokku で動かすようにしてみて色々仕組みが面白かったので今日はその紹介をします。

!SLIDE

### Dokku - [https://github.com/progrium/dokku](https://github.com/progrium/dokku)

>Docker powered mini-Heroku in around 100 lines of Bash. The smallest PaaS implementation you've ever seen.

Docker をベースとして 100行くらいの Bash で書かれた mini-Heroku。あなたが今まで見た中でもっとも小さな PaaS 実装。

!SLIDE left

# Dokku の概要

* Docker + Heroku の Buildpack を使って、VPS や EC2 に mini-heroku が作れる
* Buildpack を使うので自分でイメージを作る必要はない
* サーバにインストールして、クライアントから鍵を通せば git push するだけで実行環境ができてアプリケーションが起動する

!SLIDE left

# Dokku の構成要素

* [Dokku](https://github.com/progrium/dokku) - Bash script
* [Docker](https://github.com/dotcloud/docker) - Container runtime and manager
* [Buildstep](https://github.com/progrium/buildstep) - Buildpack builder
* [pluginhook](https://github.com/progrium/pluginhook) - Shell based plugins and hooks
* [sshcommand](https://github.com/progrium/sshcommand) - Fixed commands over SSH
* Nginx, SSH, Git

!SLIDE left

# Dokku のインストール

#### サーバで実行

``` zsh
$ wget -qO- https://raw.github.com/progrium/dokku/master/bootstrap.sh | sudo bash
```

#### クライアントで実行

* 鍵を通す

``` zsh
$ cat ~/.ssh/id_rsa.pub | ssh example.org "sudo sshcommand acl-add dokku banyan"
```

* デプロイしたいアプリケーションでリモートリポジトリの設定

``` zsh
$ git remote add example.org dokku@example.org:rails4-sample
```

* git push で Heroku と同じようにデプロイできる

``` zsh
$ git push example.org master
```

!SLIDE

# 実際 git push すると何が起こるのか？

!SLIDE left

# まず sshcommand の設定で

* SSH の authorized_keys にはログイン時に実行するコマンドを指定することができて、それを利用してdokku ユーザが ssh で接続すると常に `dokku` コマンドが叩かれるようになる

``` zsh
$ cat ~/.ssh/id_rsa.pub | ssh example.org "sudo sshcommand acl-add dokku banyan"
```

`/home/dokku/.ssh/authorized_keys`

``` zsh
command="FINGERPRINT=02:8d... NAME=banyan `cat /home/dokku/.sshcommand` $SSH_ORIGINAL_COMMAND",... ssh-rsa AAAAB3Nza...
```

``` zsh
$ cat /home/dokku/.sshcommand
/usr/local/bin/dokku
```

!SLIDE snazzy-slide

}}} images/dokku-flow4.png

!SLIDE snazzy-slide

}}} images/dokku-flow5.png

!SLIDE left

### これでメインの dokku スクリプトが実行される。

``` bash
  receive)
    APP="$2"; IMAGE="app/$APP"
    echo "-----> Cleaning up ..."
    dokku cleanup
    echo "-----> Building $APP ..."
    cat | dokku build $APP
    echo "-----> Releasing $APP ..."
    dokku release $APP
    echo "-----> Deploying $APP ..."
    dokku deploy $APP
    echo "=====> Application deployed:"
    echo "       $(dokku url $APP)"
    echo
    ;;
```

### dokku receive では4つのフローが実行されます。

* dokku cleanup
* dokku build
* dokku release
* dokku deploy

!SLIDE left

# dokku cleanup
<br />

使われてないイメージやコンテナを消す

!SLIDE left

# dokku build
<br />

``` bash
  build)
    APP="$2"; IMAGE="app/$APP"; CACHE_DIR="$DOKKU_ROOT/$APP/cache"
    id=$(cat | docker run -i -a stdin progrium/buildstep /bin/bash -c "mkdir -p /app && tar -xC /app")
    test $(docker wait $id) -eq 0
    docker commit $id $IMAGE > /dev/null
```

パイプで docker のコンテナの中の /app にソースを inject して docker commit します。
またイメージは `progrium/buildstep` の Docker のイメージをベースにしています。

```
    id=$(docker run -d -v $CACHE_DIR:/cache $IMAGE /build/builder)
    docker attach $id
    test $(docker wait $id) -eq 0
    docker commit $id $IMAGE > /dev/null
```

その後、buildstep の `build/builder` というスクリプトが、このアプリケーションが何の言語かを調べて、各言語の buildpack を compile してコミットします。

!SLIDE left

# dokku release
<br />

``` bash
$ dokku config:set FOO=BAR
```

とかで設定してある ENV がある場合にコンテナに inject して、<br />コンテナを docker commit します。

``` bash
id=$(cat "$DOKKU_ROOT/$APP/ENV" | docker run -i -a stdin $IMAGE /bin/bash -c "mkdir -p /app/.profile.d && cat > /app/.profile.d/app-env.sh")
test $(docker wait $id) -eq 0
docker commit $id $IMAGE > /dev/null
```

!SLIDE left

# dokku deploy
<br />

``` bash
id=$(docker run -d -p 5000 -e PORT=5000 $DOCKER_ARGS $IMAGE /bin/bash -c "/start web")
```

ここで実際にコンテナが起動されます

!SLIDE left

# Dokku にある機能

* Plugins (PostgreSQL, Memcached, Redis...)
* SSL

# Dokku 0.3.0 で予定されてる機能

(現在 2014/01/16 は v0.2.1)

* [dokku-ps](https://github.com/progrium/dokku/pull/298) dokku ps:scale APP web=4 worker=1
* [dokku-add-on](https://github.com/progrium/dokku/pull/292) heroku style addons.

!SLIDE left

# Demo

!SLIDE left

# まとめ

* Dokku の大まかな流れをみてきました。Docker と heroku の buildpack やその他のミドルウェアをシンプルに使いながら、小さな PaaS を実現していてコードを読んでいても面白いです。
* Dokku 動くけどバグはなかなか多いです。後方互換性のない変更も多いし Docker の変更も多く安定はしてない。
* Dokku よりも複雑なことがしたい場合 [flynn](https://flynn.io/) というプロダクトがあり、現在開発中のようです。

!SLIDE left

### 参照URL

* [http://progrium.com/blog/2013/06/19/dokku-the-smallest-paas-implementation-youve-ever-seen/](http://progrium.com/blog/2013/06/19/dokku-the-smallest-paas-implementation-youve-ever-seen/)
* [http://off-the-stack.moorman.nu/2013-11-23-how-dokku-works.html](http://off-the-stack.moorman.nu/2013-11-23-how-dokku-works.html)
* [http://git-scm.com/book/ch9-6.html](http://git-scm.com/book/ch9-6.html)

!SLIDE

# ご清聴ありがとうございました。

}}} images/foosball.jpg
