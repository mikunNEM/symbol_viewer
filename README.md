# Symbol Live Tx Viewer

Symbol ブロックチェーンのリアルタイム取引ビューアです。  
指定した Symbol アドレスへの未承認（unconfirmed）および承認済み（confirmed）トランザクションを WebSocket でライブ表示します。

Identicon（ブロック風アイコン）を生成し、送信者の publicKey をビジュアル化します。

---

## 🚀 機能

- 未承認 / 承認済みトランザクションのリアルタイム表示
- WebSocket による自動更新 & 再接続
- 送信者の Identicon（5x5 グリッドアイコン）表示
- 最新30件の履歴を自動取得
- スマホでも見やすい UI

---

## 📁 ファイル構成

- **index.html**  
  ビューア本体（JavaScript 内蔵）
- **README.md**  
  説明書

---

## 🧰 使い方

### 1. index.html をブラウザで開く  
そのまま開くだけで動作します。  
ローカルファイルでも利用可能です。

---

## 🔧 設定

### 監視する Symbol アドレス

index.html の以下を編集：

```js
const ADDRESS = "TANWG4F32RMJT6UEKA2COQPJERCDLHB34RIGBII";
```

任意の Symbol アドレスに変更できます。

---

### 使用するノード

```js
const NODES = [
  "https://testnet1.symbol-mikun.net:3001",
  "https://sym-test-01.opening-line.jp:3001"
];
```

複数ノードからランダムで接続します。

---

## 🛠 動作仕様

### 未承認トランザクション（unconfirmed）
- 背景：黄色
- WebSocket：`unconfirmedAdded/{address}`

### 承認済みトランザクション（confirmed）
- 背景：白
- WebSocket：`confirmedAdded/{address}`

### WebSocket の自動再接続
接続が途切れた場合、2秒後に再接続します。

---

## 🎨 Identicon 生成

送信者の publicKey から 5×5 のビットマップアイコンを自動生成：

```js
icon.src = identiconSvg(sender);
```

HSL 色相は publicKey の先頭バイトを使用。

---

## 🔍 過去履歴の読み込み

初期表示時：

```
/transactions/confirmed?address=${ADDRESS}&pageSize=30
```

---

## 👀 表示イメージ

- 新着トランザクションが上に追加される
- 未承認 → 承認済みに自動更新
- カードクリックで Symbol Explorer を開く

---

## 📄 ライセンス

MIT License
