# zenn

zenn-cliの使い方

## 新規記事作成
`npx zenn new:article`

```md
articles/your-article-slug.md

---
title: "Zenn CLIの使い方"
emoji: "🧭"
type: "tech" # tech or idea
topics: ["zenn", "markdown", "cli"]
published: false
---
```

git commitしてpushすると更新が反映される。

## local preview
npx zenn preview

なお、エラー吐く時はnvmとかのバージョンが古い場合がある。
```sh
ReferenceError: File is not defined
    at 11705 (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:129:75682)
    at __webpack_require__ (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:138:394392)
    at 45014 (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:129:58232)
    at __webpack_require__ (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:138:394392)
    at 88150 (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:129:16238)
    at __webpack_require__ (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:138:394392)
    at 84925 (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:129:51552)
    at __webpack_require__ (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:138:394392)
    at 30676 (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:129:22779)
    at __webpack_require__ (/Users/takatoshi/Documents/workspace/zenn-docs/node_modules/zenn-cli/dist/server/zenn.js:138:394392)

Node.js v18.17.0
takatoshi@yamadatakatoshinoMacBook-Pro-2 zenn-docs %`
```
こういう時は

```sh
nvm install --lts
```
でアップデート。

確認は
```sh
nnod -v

takatoshi@yamadatakatoshinoMacBook-Pro-2 zenn-docs % which node
/Users/takatoshi/.nvm/versions/node/v22.21.0/bin/node
takatoshi@yamadatakatoshinoMacBook-Pro-2 zenn-docs % nvm --version
0.40.3
takatoshi@yamadatakatoshinoMacBook-Pro-2 zenn-docs % node -v
v22.21.0
```

## ブックを作成する場合
そのうちやるかも
`npx zenn new:book`

```md
books/your-book-slug/
├── config.yaml
└── chapters/
    ├── chapter1.md
    └── chapter2.md
```
