---
layout: post
title: なぜ、シェルでエイリアスの使用は控えた方がいいのか。
---

# {{page.title}}

<span class="meta">March 1 2013</span>

##独自環境の弊害
他所の端末を使った時にストレスを感じるようになってしまう。
世の中の大半は未設定か自分の設定とは異なる。

##正式なコマンドを思い出せなくなる
楽をしているようで覚えることが一段増える。

##代替
bashやzshでは、履歴を多めにしてインクリメンタル検索`C-r`で十分な使い心地を得られる。

~/.zshrc 履歴の設定例

```
# 履歴を残す http://0xcc.net/unimag/3/
HISTFILE=$HOME/.zsh-history           # 履歴をファイルに保存する
HISTSIZE=100000                       # メモリ内の履歴の数
SAVEHIST=100000                       # 保存される履歴の数
setopt extended_history               # 履歴ファイルに時刻を記録
function history-all { history -E 1 } # 全履歴の一覧を出力する
setopt share_history                  # 履歴の共有
```
