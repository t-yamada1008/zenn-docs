---
title: "開発環境について"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["開発環境"]
published: true
---

開発環境構築の忘備録。
時々機会があるので記しておく。

# 前提
なるべく固有設定やインストールするパッケージやプラグインを絞ること。
煩雑になりやすく、導入してもあまり利用していないといったケースが発生するため。

## OS
LinuxベースのOSを扱う。
MacOS、Windowsの場合はWSL2経由のUbuntuなど。
Ubuntuは24.04などその時々で新しめのLTSを利用する。

## キーボード
US規格のHHKBを持ち込むこと。
持ち込めない場合、aの隣にctrlを設定すること。
DIPスイッチはOSに合わせて設定。また、3のbackspaceの入れ替えをON。
共通でctrl(left) + backspace で delele になるようにショートカットを再マップ。

### Windowsの場合
Power ToysのKeyboard Managerを利用すること。
キー入力はMac準拠で行う。Windowsキーに右ctrlをバインドすること。

# ターミナル
Macの場合はGhostty。iTerm2に戻るかもしれないが現状は様子見。
Windowsの場合はWindows TerminalをUbuntsuデフォルトで起動。案外悪くない。

## Shell
原則zshを採用する。
fishも長い期間利用したが、どの環境でも使えるというのは大きい。
ただ、abbrは便利だったのでzshの代替プラグインを引き続き使っている。口述。

### bashからzshへの切り替え

```sh
$ echo $SHELL
/bin/bash
$ sudo apt update # パッケージ更新
...
64 packages can be upgraded. Run 'apt list --upgradable' to see them.
$ sudo apt install zsh -y
...
Setting up zsh (5.9-6ubuntu2) ...
Processing triggers for debianutils (5.17build1) ...
Processing triggers for man-db (2.12.0-4build2) ...
$ zsh --version
zsh 5.9 (x86_64-ubuntu-linux-gnu)
$ which zsh
/usr/bin/zsh
$ chsh -s $(which zsh) # ログインシェルをzshに切り替え
Password:
```

ターミナルを再起動すると以下の初回起動メッセージが出力される。

```sh
This is the Z Shell configuration function for new users,
zsh-newuser-install.
You are seeing this message because you have no zsh startup files
(the files .zshenv, .zprofile, .zshrc, .zlogin in the directory
~).  This function can help you with a few settings that should
make your use of the shell easier.

You can:

(q)  Quit and do nothing.  The function will be run again next time.

(0)  Exit, creating the file ~/.zshrc containing just a comment.
     That will prevent this function being run again.

(1)  Continue to the main menu.

(2)  Populate your ~/.zshrc with the configuration recommended
     by the system administrator and exit (you will need to edit
     the file by hand, if so desired).

--- Type one of the keys in parentheses ---
```

0を選択して空の`~/.zshrc`を作成して終了。

## git
zsh設定用にdotfilesが必要なのでgitをインストールする。
デフォルトで入っているかも。

```sh
% sudo apt install -y git
...
git is already the newest version (1:2.43.0-1ubuntu7.3).
git set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 64 not upgraded.
% git --version
git version 2.43.0
```

### GitHub
sshでGitHubと連携させる。

公式の手順を参照。
https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

```sh
% ssh-keygen -t ed25519 -C "メールアドレス" -f ~/.ssh/id_ed25519_<任意の名前> # 鍵生成
% eval "$(ssh-agent -s)" # ssh-agent 呼び出し
% ssh-add ~/.ssh/id_ed25519_<任意の名前> # ssh-agent登録
% cat ~/.ssh/id_ed25519_<任意の名前>.pub # 公開鍵をGitHubに登録
```

`~/.ssh/config`を作成しておく。

```sh
% cat config
# ~/.ssh/config
Host tyamada
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_tyamada
  AddKeysToAgent yes
  IdentitiesOnly yes

% ssh -T git@github.com # 疎通確認
```

### WSLのssh-agentについて
WSLでは各ターミナルセッションごとにssh-agentが独立しているらしい。
その結果、sshする際に常に下記のコマンドを叩く必要が出てしまう。

```sh
% eval "$(ssh-agent -s)"
% ssh-add ~/.ssh/id_ed25519
```

対策としてはkeychainを利用する。

```sh
sudo apt install keychain
```

`.zshrc`に追加

## zshとpluginsについて
zsh関連の設定はdotfiles、 シンボリックリンクの管理はstowで行う。

```sh
git clone https://github.com/<yourname>/dotfiles.git ~/workspace/dotfiles
cd ~/workspace/dotfiles

# Stow で設定反映
stow -v -t ~ zsh git fzf

# 更新
git pull
stow -Rvt ~ zsh git fzf
```

詳しくは別記事参照。

## Visual Studio Code
IDEはVisual Studio Codeを利用する。
Jet Brain社製のIDEも気になるが、どの環境でも使えるというのは大きい。

### プラグイン
プラグインは最小限とすること。
使っていないものは適宜アンインストールし、本ドキュメントを更新すること。

- Japanese Language Pack for Visual Studio Code
- WSL
- Vim
- markdownlint
- vscode-pdf
- Dev Container
- Container Tools

## Git
WSL側でgitconfigを設定した際、VSCode側でcommitする際にエラーになるケースがある。
これはWSLでなくWindows側を見ているせい。
先にWSLプラグインを入れて、WSLで起動されているか確認するのがいい。
VSCodeでcommitする時に最初ハマった。

なお、git cloneにはgitconfigはいらないみたい。
 
## ロケール設定

以下の警告が出る場合、、WSLのロケールを設定する。
manpath: can't set the locale; make sure $LC_* and $LANG are correct


WSL では `ja_JP.UTF-8` が未生成の場合があるため、  
以下のコマンドを実行してロケールを有効化する。

```bash
sudo locale-gen ja_JP.UTF-8
sudo update-locale LANG=ja_JP.UTF-8
```

## Node.js

Node.jsは`nvm`(Node Version Manager)を用いる。
nvmなのは暫定。

```bash
# nvm install
# 終了後にsource ~/.zshrc
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# LTS版Nodeを導入
nvm install --lts

# バージョンを確認
% node -v
v24.11.0
% npm -v
11.6.1
% nvm -v
0.39.7
```

### node.js系ツール

```sh
# md → pdf変換
npm install -g md-to-pdf
```
