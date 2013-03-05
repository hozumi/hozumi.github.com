---
layout: post
title: なぜ、シェルでエイリアスの使用は控えた方がいいのか。
---

# {{page.title}}

<div class="meta">March 1 2013</div>

##エイリアスの解決しようとした問題
「長いコマンドをいちいち入力するのは面倒だ」

##エイリアスの解決方法
事前にコマンドの短縮名を登録することで、元の長いコマンドの入力を避ける。

##エイリアスの引き起こした別の問題
###独自環境化
他所の設定されていない端末を使った時にストレスを感じるようになってしまう。
世の中の大半は未設定か自分の設定とは異なる。

###正式なコマンドを思い出せなくなる
楽をしているようで覚えることが一段増える。

##他の解決策
bashやzshでは、履歴を多めにしてインクリメンタル検索`C-r`で十分な使い心地を得られる。

~/.zshrc 履歴の設定例

{% highlight bash%}
# 履歴を残す http://0xcc.net/unimag/3/
HISTFILE=$HOME/.zsh-history           # 履歴をファイルに保存する
HISTSIZE=100000                       # メモリ内の履歴の数
SAVEHIST=100000                       # 保存される履歴の数
setopt extended_history               # 履歴ファイルに時刻を記録
function history-all { history -E 1 } # 全履歴の一覧を出力する
setopt share_history                  # 履歴の共有
{% endhighlight %}
