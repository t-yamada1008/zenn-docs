---
title: "開発環境について"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["開発環境"]
published: true
---

# 開発環境について 

自分の開発環境の忘備録です。
元々SESあがりで、なんだかんだ環境構築の機会が多かったので記しておく。

## 前提
- Linuxベース
  - Mac、もしくはWSL2経由のUbuntuなど
- zsh
  - fishもまあまあ長い期間利用した
　  - fishのabbrが便利過ぎたのでzshに類似パッケージがないか探した結果、あったのでメインで使ってる(後述)。

## エディタ
- vim
- vscode
  - Jet BrainのIDEも気になるが、無料だし慣れてるし...

## コマンドライン
- MacはGhostty
  - iTerm2に戻るかもしれないが様子見
- WinはWindows Terminal
  - 使いにくさはあるけど、悪くない

## zsh
なるペくパッケージは減らす。
メインで使うのは以下のリスト。
パッケージはbrew、もしくはnpmで管理する。

- zsh-abbr
- zsh-z

続きはまた更新する予定
