---
title: "写経タイピングCLI開発RTA"
emoji: "⛳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [rta]
published: true
---

アドベントカレンダーをやりますとノリで言ってしまったのでやる。
個人開発は何年ぶりだ？2,3年ぶりかも。
レギュレーションはZenn記事+プロダクト, No Article AI Any%。
前説はおいておいて、タイマースタート！グッドラック！！
12:21 20:44:22

## コンセプト
アルゴリズムを学ぶための写経ソフト

目的は3つ。

1. 最近アルゴリズム系をやってないのでやりたい
2. キーボードをHHKBに変えたのでブラインドタッチの練習をしたい
3. たまにはオーガニックコーディングしよう

## やらないこと

- vim使うこと
  - 写経が目標
- GUIの実装
  - 自分が使えればオールオッケー

## MVP
- 問題を選ぶ
- お手本コードを表示
- ユーザー入力を受け取る

## typer
typerはpythonでCLIを作るツール。
argparseも選択肢だが、とりあえず今回はtyperにする。

### メリット
- argparseより短くて可読性が高いらしい
- 関数の引数がCLIのオプションになる
- --helpが自動生成
- 型ヒントが効く

### sample

GPTで出したサンプルを動かす。

```python
import typer
app = typer.Typer()

@app.command()
def hello(name: str):
    print(f"Hello {name}")

app()

# % python main.py Taka
# Hello Taka
```

なお、venvを切るタイミングでPythonを更新した。
.venvを入れて、pythonの仮想化環境を作る。
.gitignoreで.venvの環境を弾く必要がある。
ここはdocker cliにしてもいいかも。

## PyYAML

YAMLをPythonで読み書きするためのライブラリ。
問題はYAMLで管理する。
変える余地はあり。

### そもそもYAMLって？

JSONより人間が読みやすい(らしい)ファイル形式。
改行やインデントを保ったコードを書けるとか。設定ファイルや教材に向いてるらしい。

### sample

```yaml
import yaml

with open("binary_search.yml") as f:
    data = yaml.safe_load(f)
```

## 実践

というわけでコードを生成してもらおう。
なお、今回はGPTを使っている。

```py
% cat main.py
import sys
import time
import difflib
import typer

app = typer.Typer()

TEMPLATE = """\
def binary_search(arr, x):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == x:
            return mid
        elif arr[mid] < x:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
"""

def score(expected: str, typed: str):
    sm = difflib.SequenceMatcher(a=expected, b=typed)
    accuracy = sm.ratio() * 100.0

    mistakes = 0
    for tag, i1, i2, j1, j2 in sm.get_opcodes():
        if tag == "equal":
            continue
        mistakes += max(i2 - i1, j2 - j1)
    return accuracy, mistakes

def human_time(seconds: float) -> str:
    sec = int(round(seconds))
    m, s = divmod(sec, 60)
    h, m = divmod(m, 60)
    if h:
        return f"{h:02d}:{m:02d}:{s:02d}"
    return f"{m:02d}:{s:02d}"

@app.command()
def hello(name: str):
    typer.echo(f"Hello {name}")

@app.command()
def start(
    show_hint: bool = typer.Option(True, "--show-hint/--hide-hint"),
    normalize_tabs: bool = typer.Option(True, "--normalize-tabs/--keep-tabs"),
):
    expected = TEMPLATE
    if normalize_tabs:
        expected = expected.replace("\t", "    ")

    typer.echo("[Binary Search]")
    typer.echo("-" * 40)
    if show_hint:
        typer.echo(expected, nl=False)
        typer.echo("-" * 40)
    else:
        typer.echo("(Hint hidden)")
        typer.echo("-" * 40)

    typer.echo("Start typing. Finish with Ctrl-D (macOS/Linux) or Ctrl-Z then Enter (Windows).")
    typer.echo("")

    t0 = time.perf_counter()
    typed = sys.stdin.read()
    t1 = time.perf_counter()

    if normalize_tabs:
        typed = typed.replace("\t", "    ")

    elapsed = max(0.001, t1 - t0)
    accuracy, mistakes = score(expected, typed)
    cpm = (len(typed) / elapsed) * 60.0

    typer.echo("\nResult:")
    typer.echo(f"  Accuracy: {accuracy:.2f}%")
    typer.echo(f"  Time: {human_time(elapsed)}")
    typer.echo(f"  Speed: {cpm:.0f} chars/min")
    typer.echo(f"  Mistakes: {mistakes}")

if __name__ == "__main__":
    app()
```

### sample
実行してみた。

```sh
% python main.py start

[Binary Search]
----------------------------------------
def binary_search(arr, x):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == x:
            return mid
        elif arr[mid] < x:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
----------------------------------------
Start typing. Finish with Ctrl-D (macOS/Linux) or Ctrl-Z then Enter (Windows).

def binary_search(arr, x):
        lo, hi = 0, len(arr) - 1
        while lo <= hi:
                mid = (lo + hi) // 2
                if arr[mid] == x:
                        return mid
                elif arr[mid] < x:
                        lo = mid + 1
                else:
                        hi = mid -1
        return -1

^C%
```
よし、動いたな。

## 完走した感想

というわけで、CLIのタイピングソフトを作ってみた。
とりあえずGPTでゴニョゴニョしただけだけど、まあいいでしょう。
というかAIやばすぎる。個人開発爆速すぎる。ハードルめちゃ低すぎて引く。

今年は本当に生成AIの年だったなあ。
プライベートはGPTを使ってるけど、Claude CodeとGeminiのほうが筋は良いと思う。
Geminiはやばい。会社で無料で使ってるけどマジでやばい。

とりあえずこのアプリはドッグフーディング感覚でそのうちもっとよくしようかなと思う。

案としては、--hide-hintを実装したり、ミスの箇所をdiffにしたり。
あとは問題増やして正答率とか、時間とか、そういうのを入れるのもいいかも
あと履歴保存ね。

というわけで、ここまで！
タイマーストップ、お疲れ様でした。

リポジトリ:
https://github.com/t-yamada1008/algotype

21:57:29

