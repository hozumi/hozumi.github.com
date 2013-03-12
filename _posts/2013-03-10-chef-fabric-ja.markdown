---
layout: post
title: Chefに挫折したあなたへ。Fabricのすすめ
---

# {{page.title}}

<div class="meta">March 10 2013</div>

サーバ設定作業は面倒で間違いを犯しやすいため、Chef/Puppetなどのツールで自動化したいと考えている方は多いと思います。
私もそのような理由からChef(-solo)を習得しようと試行錯誤していました。
その結果、ある程度は動くようになったものの次のような問題があると思いました。

##学習に時間がかかる
私は正直、今でもどのファイルに何を書くのかよく分かってないです。
幾分か簡単だと言われるchef-soloでも公式サイトのドキュメントだけではよく理解出来ませんでした。
また、バージョンによる差異なのか目的が異なるのか分かりませんが、ブログ記事を参考にしようとすると十人十色でどれが私に合った手順なのかわかりませんでした。
例え最終的に理解できたとしても、私やあなたが何日もかけて理解できないことはチームのメンバーも理解するのは難しいと思います。

##対象サーバにインストールする必要がある
Chef(-solo)を使うには対象サーバにChef自体とRubyをインストールする必要があります。
ChefのインストールをChefで自動化はできないので、どうにかしてインストールします。
[knife-solo](http://d.hatena.ne.jp/naoya/20130204/1359971408)というのを使えばワンステップでインストールできるので面倒さは気にならないのですが、
本来Rubyを使わない環境だったり、あまりいろいろ入れたくないという環境では少し使いにくいです。

参考
[開発サーバに chef を入れるときの 11の方法 - Hack like a rolling stone](http://tk0miya.hatenablog.com/entry/2013/03/07/121438)

##自動化するまでに結構な手作業をしている。
Chefのインストールや一連の作業を適当な一般ユーザで行うのであれば、ユーザとsshの設定を済ませておかなければいけません。
最初からそれらが用意されている環境では気にならないでしょうが。

##公開されているレシピがそのまま使えないことがよくある
いろいろな目的のレシピが公開されていることがChefの利点の一つです。
しかし、それらのレシピをブラックボックス的に利用しようとすると、うまくいかない時に裏で何が行われているのか把握しづらく原因が分かり難いと思います。
また他の人の作ったレシピには前提条件があり、自分の要求と合わないことがよくあります。
例えば、このレシピではファイルをhttpで取得してインストールしているが自分は手元にあるファイルをhttpに置かずに使いたいんだなど些細な事で簡単にそのまま使えなくなります。
結局、遅かれ早かれ独自に作り込む必要があるのではないかと思います。

以上の点から、私はとりあえずはChefの利用を諦める事にしました。
代替えと言えるか分からないですが、Fabricというツールがサーバ設定作業になかなか便利です。

#Fabricとは
ssh越しに各サーバでコマンドを実行したりファイルを転送したりするツールです。
シェルスクリプトに毛が生えたような印象です。

シェルスクリプトと大きく違うのは、ssh越しにコマンドを実行するので、スクリプトファイルを対象サーバに配布する必要がないことと、pythonで記述するという点。

*fabfile.py*

{% highlight python %}
# -*- coding: utf-8 -*-
from fabric.api import run

def hello():
    run("uname -n")
    run("pwd")

{% endhighlight %}

実行結果

{% highlight bash %}
% ls -l fabfile.py
-rw-r--r--  1 foo  staff  100  3 10 18:15 fabfile.py

% fab -H vagrant@192.168.56.10 hello
[vagrant@192.168.56.10] Executing task 'hello'
[vagrant@192.168.56.10] run: uname -n
[vagrant@192.168.56.10] Passphrase for private key: 
[vagrant@192.168.56.10] Login password for 'vagrant': 
[vagrant@192.168.56.10] out: localhost.localdomain
[vagrant@192.168.56.10] out: 

[vagrant@192.168.56.10] run: pwd
[vagrant@192.168.56.10] out: /home/vagrant
[vagrant@192.168.56.10] out: 

Done.
Disconnecting from vagrant@192.168.56.10... done.
{% endhighlight %}


Chefとの大きな違いは、Chefは冪等性と言って、何度実行しても結果が同じになるようにできているのに対し、
Fabricはシェルスクリプトと同じく、自分で処理を切り替えて面倒を見ない限り、複数回実行すると結果が異なることがあるという点です。

もともとChefのインストールにFabricを使うといいんじゃないかと教えてもらったのですが、Fabricだけで全てのサーバ設定作業をやってる人もいて、python界隈ではなかなか重宝されてるツールのようでした。
Rubyにも似たようなのがあるようですが、そちらは調べていません。
次の点でFabricは使い易いと思います。

##ほとんどシェルスクリプト
pythonで書くと言っても見た目はほとんどシェルスクリプトなので、python分かんないやという人でも問題ないです。
私もpythonはちょっと触っただけで大体忘れてましたが、大丈夫でした。
インデントだけ気をつければ後はいくつか例を見れば使えます。

##学習と比例した効果
基本的にコマンドを羅列するだけでもいいので、最初から有用なことができます。
必要なファイルも一つだけで、なんとも割り切った感じで簡単です。
ファイルを転送したい、エラー時の挙動を変えたいなど、必要になった時にその都度調べれば十分です。

##裏で何をやってるか把握できる
実行しているコマンドと出力結果が表示されるのでエラーが出てもどこが悪いか簡単に判断出来ます。

##タスクを分割できる
pythonの関数に分けてタスクを書き、実行時に引数で関数名を指定して実行します。
このおかげで大きなタスクを細かく分けることで部分的な検証を簡単に行えます。
これをシェルスクリプトでやろうとすると、明示的に引数をcaseにかけて処理を分けるなど少し面倒です。

{% highlight python %}
# -*- coding: utf-8 -*-
from fabric.api import run, sudo, cd

def git_yum_remove():
    sudo('yum -y remove git')

def git_dependency_install():
    sudo("yum -y install curl-devel expat-devel gettext-devel \
                         openssl-devel zlib-devel perl-ExtUtils-MakeMaker")

def git_install_from_tar():
    run("curl -O http://git-core.googlecode.com/files/git-1.8.1.3.tar.gz")
    run("tar zxvf git-1.8.1.3.tar.gz")
    with cd("git-1.8.1.3"):
        run("make prefix=/usr/local all")
        sudo("make prefix=/usr/local install")
    run("rm -rf git-1.8.1.3")

def git_update():
    git_yum_remove()
    git_dependency_install()
    git_install_from_tar()

{% endhighlight %}


##対象サーバにインストールする必要がない
作業用マシンからsshで操作するだけなので、作業用マシンにfabricをインストールしさえすれば、対象サーバに何か特別なソフトをインストールする必要がありません。
気軽に始められて、気軽に辞めれるので何気に結構大きいです。

##ファイルを設置すれば十分な事が多い
そもそもサーバ構築作業はファイルを設置すれば十分な事が多いです。
/etc/passwd, shadow, group, sshd_config, iptables
などなど、用意したファイルをパーミッションとオーナーに気をつけて設置するだけで設定のかなりがカバーできます。
そんなに凝ったことをやらないのであれば重厚なツールを使うのはオーバーキル感があります。

*設定ファイル設置例*
{% highlight python %}
# -*- coding: utf-8 -*-
from fabric.api import run, cd, put

# rootで実行
def initial_setting():
    put("setup_files/passwd", "/etc/passwd", mirror_local_mode=True)
    put("setup_files/shadow", "/etc/shadow", mirror_local_mode=True)
    put("setup_files/group", "/etc/group", mirror_local_mode=True)
    put("setup_files/myuser", "/home", mirror_local_mode=True)
    run("chmod 0700 /home/myuser")
    run("chown -R myuser:staff /home/myuser")
    put("setup_files/sshd_config", "/etc/ssh/sshd_config",
        mirror_local_mode=True)
    put("setup_files/sudoers", "/etc/sudoers", mirror_local_mode=True)
    put("setup_files/iptables", "/etc/sysconfig/iptables", mode=0600)
    run("shutdown -r now")
{% endhighlight %}

##構築と運用の垣根が低い
構築で使ったタスクを運用時に活用したり、その逆が容易です。
有機的に無理なくスクリプトを育てることができます。

FabricにおいてもChefと同じように、使い捨ての壊してもいい環境があると検証が楽にできるので、[Vagrant](http://d.hatena.ne.jp/naoya/20130205/1360062070)を使うと便利です。
