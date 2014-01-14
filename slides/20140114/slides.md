# dokku-slide

!SLIDE

# Dokku, 世界で最も小さい PaaS を5分で説明する LT (仮)
## Heroku Meetup #11 New Year Party!! - 新年会 - / 20140104
### Kohei Hasegawa @banyan, Quipper, Ltd.

!SLIDE

最近遊びのアプリケーションを Dokku で動かすようにしてみたので今日はその紹介をします。

!SLIDE left

# アジェンダ

1. Dokku とは?
1. デプロイの流れ
1. Dokku のプラグインと今後
1. まとめ

!SLIDE top-left

# 1. Dokku とは?

}}} images/dokku.png

!SLIDE

>Docker powered mini-Heroku in around 100 lines of Bash.

>The smallest PaaS implementation you've ever seen.

Docker をベースとして 100行くらいの Bash で書かれた mini-Heroku

あなたが今まで見た中でもっとも小さな PaaS 実装

!SLIDE left

# Dokku の概要

* Docker + Heroku の Buildpack を使って、VPS や EC2 に mini-heroku が作れる
* heroku の Buildpack を使うので自分でイメージを作る必要はない
* クリーンインストールで使うことが想定されている
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

* これでデプロイできる、Heroku と同じ。

``` zsh
$ git push example.org master
```

!SLIDE

# 実際 git push すると何が起こるのか？

!SLIDE left

# dokku ユーザが ssh で接続すると

常に `dokku` コマンドが叩かれるようになる。

``` zsh
$ cat ~/.ssh/id_rsa.pub | ssh example.org "sudo sshcommand acl-add dokku banyan"
```

`/home/dokku/.ssh/authorized_keys`

``` zsh
command="FINGERPRINT=02:8d... NAME=banyan `cat /home/dokku/.sshcommand` $SSH_ORIGINAL_COMMAND",no-agent-forwarding,no-user-rc,no-X11-forwarding,no-port-forwarding ssh-rsa AAAAB3Nza...
```

``` zsh
# cat /home/dokku/.sshcommand
/usr/local/bin/dokku
```

!SLIDE

}}} images/dokku-flow.png

!SLIDE left

これでメインの dokku スクリプトが実行される

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

!SLIDE left

# dokku cleanup

Exit Status のコンテナと使われてないイメージを消す

!SLIDE left

# dokku build

``` bash
  build)
    APP="$2"; IMAGE="app/$APP"; CACHE_DIR="$DOKKU_ROOT/$APP/cache"
    id=$(cat | docker run -i -a stdin progrium/buildstep /bin/bash -c "mkdir -p /app && tar -xC /app")
    test $(docker wait $id) -eq 0
    docker commit $id $IMAGE > /dev/null
```

パイプで docker のコンテナの中の /app にソースを inject して docker commit します。

!SLIDE left

# dokku release

``` bash
$ dokku config:set FOO=BAR
```

とかで設定してある ENV がある場合にコンテナに inject して、コンテナを docker commit します。

``` bash
      id=$(cat "$DOKKU_ROOT/$APP/ENV" | docker run -i -a stdin $IMAGE /bin/bash -c "mkdir -p /app/.profile.d && cat > /app/.profile.d/app-env.sh")
      test $(docker wait $id) -eq 0
      docker commit $id $IMAGE > /dev/null
```


!SLIDE left

# dokku deploy

``` bash
id=$(docker run -d -p 5000 -e PORT=5000 $DOCKER_ARGS $IMAGE /bin/bash -c "/start web")
```

ここで実際にコンテナが起動されます

!SLIDE

# Demo

!SLIDE left

# まとめ

* Dokku の大まかな流れをみてきました。Docker と heroku の buildpack やその他のミドルウェアをシンプルに使いながら、小さな PaaS を実現していてコードを読んでいても面白いです。
* Dokku には PostgreSQL, Memcached, Redis などプラグイン機構があります。
* Dokku 動くけどバグはなかなか多いです。後方互換性壊す変更も多い :(
* Dokku よりも複雑なことがしたい場合 flynn というプロダクトがあり、現在開発中です。

!SLIDE left

## 参照URL

* http://progrium.com/blog/2013/06/19/dokku-the-smallest-paas-implementation-youve-ever-seen/
* http://off-the-stack.moorman.nu/2013-11-23-how-dokku-works.html
* http://git-scm.com/book/ch9-6.html

!SLIDE

# ご清聴ありがとうございました。

}}} images/foosball.jpg
