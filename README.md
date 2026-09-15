# Orca ハンズオン 第3弾（ジュニアエンジニア向け・自習用）

[第1弾](https://github.com/misshii3/orca-handson) では同じバグ修正を Claude Code と Codex に競わせて PR を作り、
[第2弾](https://github.com/misshii3/orca-handson-2) では Issue を起点に 3 本のタスクを並列で進めてマージとコンフリクト解消まで通しました。

第3弾のテーマは **画面を見ながら直す** です。Orca の **ワークツリーごとのブラウザ** で注文確認ページを開き、
**Design Mode**（崩れている要素をクリックしてメモを付け、エージェントに送る）で Claude Code に見た目を直させます。
並行して別のワークツリーで Codex に機能を追加させ、どちらもブラウザで確かめてから PR → マージ → 後片付けまでを一人で通します。

## 前提

- [第1弾](https://github.com/misshii3/orca-handson) と [第2弾](https://github.com/misshii3/orca-handson-2) を終えていること（Orca のインストール、GitHub 連携、Issue からのワークツリー作成、PR パネルでのマージを知っている前提で書いています）
- macOS、Orca、Claude Code、Codex CLI、`gh`、Node.js 20 以上

## 何を体験するか

![Design Mode のループ。ブラウザで崩れた要素をクリックしてメモを付け、Claude Code に送る。Claude Code がファイルを直すと dev サーバーが検知してブラウザが自動で再読み込みされ、もう一度クリックして確認する。最後に差分を読んで PR にする](images/diagrams/14_design-mode-loop.svg)

| | 第1弾 | 第2弾 | 第3弾 |
|---|---|---|---|
| タスク | 同じバグ修正を 2 つのエージェントで競争 | 別々の機能追加を 3 本並列 | **見た目の崩れ 4 か所の修正**（Claude Code）と **クーポン入力欄の追加**（Codex）を 2 本並列 |
| エージェントへの指示 | 指示文を貼る | Issue の URL が入力欄に入っている | **ブラウザで要素をクリックしてメモを付け、送る** |
| 確認の仕方 | テストと差分 | テスト・差分・PR のチェック | **ブラウザで見た目を確認**（自動で再読み込み）+ 差分 |
| 権限 | 手動 | Yolo（練習リポなので解禁） | Yolo（同じ条件。終わったら戻す） |
| ゴール | PR を作るまで | マージ → コンフリクト解消 → Issue クローズ | **画面が直った状態で 2 本ともマージ** → Issue クローズ |

## 進め方（合計 約 100 分）

[01_handson.md](./01_handson.md) を上から順に進めてください。

| 区分 | 章 | 内容 | 目安 |
|---|---|---|---|
| 準備 | 1〜3 | ツールの確認、テンプレートから複製して Issue を作成、Orca に追加して Yolo に切り替え | 20 分 |
| 本編 | 4〜10 | ワークツリー 2 本と dev サーバー、Design Mode で崩れ 4 か所を修正、Codex の機能追加を確認、PR 2 本をマージ | 75 分 |
| 後片付け | 11 | dev サーバー停止、ワークツリー削除、Yolo を手動に戻す | 5 分 |
| 付録 | A〜E | Codex との比較、エージェントにブラウザを操作させる、トラブルシューティング、業務での判断基準 | 任意 |

章ごとの作業場所（#1 のワークツリー、#2 のワークツリー、プライマリ）と、先に知っておきたい「つまずきやすいところ」は `01_handson.md` の 0 章にまとめてあります。
Orca の概念（worktree、ADE、権限の 3 層）は第1弾の [01_overview.md](https://github.com/misshii3/orca-handson/blob/main/01_overview.md) を、
Issue からのワークツリー作成・PR パネル・マージの操作は第2弾の [01_handson.md](https://github.com/misshii3/orca-handson-2/blob/main/01_handson.md) を参照します。

## このリポジトリの使い方（参加者向け）

このリポジトリは **テンプレートリポジトリ** です。自分のアカウントに複製し、同梱のスクリプトでタスク用の Issue を作ってから使います
（テンプレートから複製しても Issue はコピーされないため、スクリプトで作ります）。
詳しい手順は `01_handson.md` の 2 章にありますが、要点だけ書くと次のコマンドです。

```bash
gh repo create orca-handson-3 --template misshii3/orca-handson-3 --private --clone
cd orca-handson-3
npm test                      # 26 件すべて成功するのが正常です
npm run dev                   # http://localhost:3000 に崩れた注文確認ページが出ます。見たら Ctrl+C で止めてから次へ
bash scripts/seed-issues.sh   # 自分のリポジトリに Issue #1〜#2 を作ります
```

## ファイル構成

```
orca-handson-3/
├── README.md                    # このファイル
├── 01_handson.md                # ハンズオン手順（約 100 分）
├── images/                      # 実機のスクリーンショット（01 から参照）
│   └── diagrams/                # 図解（SVG）
├── issues/                      # タスクの Issue 本文（seed-issues.sh が読む）
│   ├── 01-fix-order-page-layout.md   # 見た目の崩れを直す（Claude Code + Design Mode）
│   └── 02-add-coupon-input.md        # クーポン入力欄を追加する（Codex）
├── scripts/
│   ├── seed-issues.sh           # gh issue create で Issue を 2 件作る（何度実行しても増えない）
│   └── dev-server.js            # 依存なしの dev サーバー（public/ と src/ を配信。ファイルを保存するとブラウザを自動で再読み込み）
├── public/                      # 注文確認ページ（HTML / CSS / JS。ビルドなし）
│   ├── index.html
│   ├── style.css
│   └── app.js                   # ../src/price.js と ../src/date.js をブラウザから直接 import する
├── .github/workflows/test.yml   # PR と main への push で npm test を実行（GitHub Actions）
├── package.json                 # npm test と npm run dev の定義。依存パッケージなし
├── src/
│   ├── price.js                 # 税込・割引・送料・クーポン（第2弾で追加した関数が入った状態）
│   └── date.js                  # 日付フォーマット（formatDate、formatDateJa）
└── test/
    ├── price.test.js
    ├── date.test.js
    └── dev-server.test.js       # dev サーバーが起動して配信できることの確認
```

## サンプルアプリについて

第2弾の 3 つの Issue（`formatDateJa`、`calcShipping`、`applyCoupon`）が **すべてマージされた状態** が初期状態です。`npm test` は 26 件すべて成功します。

第3弾では、この関数を使う **注文確認ページ**（`public/`）を追加しています。商品 3 品の小計 3,300 円にクーポン `WELCOME10` を適用し、送料と税込合計、お届け予定日を表示する 1 ページです。
このページには **見た目の崩れが 4 か所** 仕込まれています（一覧は Issue #1 = `issues/01-fix-order-page-layout.md`）。Design Mode で要素を指して Claude Code に直させるのが本編です。

- ページは `npm run dev` で起動する dev サーバー経由（`http://localhost:3000`）で開いてください。`public/index.html` を直接開く（`file://`）と、`src/` の ES module を読み込めずに動きません
- `public/` や `src/` のファイルを保存すると、開いているブラウザが自動で再読み込みされます（dev サーバーが変更を監視しています）
- 2 本目のワークツリーで同時に起動するときは `PORT=3001 npm run dev` のようにポートを分けます
- 割引の関数 `applyDiscount`（5,000 円以上で 10% オフ）は、このページの小計では条件を満たさないため使っていません

依存パッケージはありません。Node.js 20 以上で `npm test` と `npm run dev` が動きます。

## 動作確認環境

- macOS（Apple Silicon）
- Orca 1.4.200（日本語 UI）
- Claude Code 2.1.x / Codex CLI 0.15x
- gh 2.8x / Node.js 22

Orca は更新頻度が高く、画面の表記が変わることがあります。手順と画面が合わないときは
[公式ドキュメント](https://www.onorca.dev/docs) を確認してください。

## ライセンス

MIT
