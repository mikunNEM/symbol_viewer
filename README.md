# Symbol Live Tx Viewer 📡


![Symbol](https://img.shields.io/badge/Symbol-Blockchain-6B4DF5?style=for-the-badge)
![WebSocket](https://img.shields.io/badge/WebSocket-Real--time-00C7B7?style=for-the-badge)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

**リアルタイムでSymbolブロックチェーンのトランザクションを監視するWebアプリケーション**

[🚀 今すぐ使う](#-使い方) • [✨ 機能](#-機能) • [🔧 カスタマイズ](#-カスタマイズ) • [📖 コード解説](#-コード解説)

</div>

---

## 🎯 このアプリについて

**Symbol Live Tx Viewer** は、Symbolブロックチェーン上の特定アドレスに対するトランザクションをリアルタイムで監視・表示するWebアプリケーションです。

ブラウザだけで動作し、**インストール不要**で使えます。

---

## ✨ 機能

| 機能 | 説明 |
|------|------|
| 🔴 **リアルタイム監視** | WebSocketで未承認・承認済みトランザクションを即座に表示 |
| 📚 **過去履歴表示** | REST APIで過去100件の承認済みトランザクションを自動取得 |
| 🎨 **Identicon** | 送信者アドレスごとに固有のアイコンを自動生成 |
| 🟡🟢 **視覚的区別** | 未承認（黄色背景）/ 承認済み（白背景）で一目で判別 |
| 🕒 **タイムスタンプ** | 承認済みトランザクションの正確な時刻を表示 |
| 🔗 **詳細リンク** | クリックでSymbol Fyiの詳細ページを表示 |
| ✨ **スムーズUI** | フェードイン＆スライドアニメーション |

---

## 🚀 使い方

###  GitHub Pages に直接アクセス

[Symbol Live Tx Viewer](https://mikunnem.github.io/symbol_viewer/)


👆 ブラウザでこのURLにアクセスするだけ！



### 操作方法

1. **自動読み込み**: ページを開くと過去100件の履歴が自動表示
2. **リアルタイム監視**: 新しいトランザクションは自動的に上部に追加
3. **状態変化**: 未承認（黄色）→ 承認済み（白）に自動更新
4. **詳細確認**: カードをクリックで詳細ページを新規タブで開く

---

## 🔧 カスタマイズ

`index.html` をローカルにダウンロード、編集して、自分用にカスタマイズする事もできます。

### 監視対象アドレスの変更

```javascript
// 監視したいSymbolアドレスに変更
const ADDRESS = "YOUR_SYMBOL_ADDRESS_HERE";
```

### 接続ノードの変更

```javascript
const NODES = [
  "https://your-node.com:3001",
  "https://another-node.com:3001"
];
```

### 取得件数の変更

```javascript
// 100件 → 50件に変更する例
const url = `${NODE}/transactions/confirmed?address=${ADDRESS}&pageSize=50&order=desc`;
```

### Mainnetへの切り替え

```javascript
// Mainnet用ノード
const NODES = [
  "https://symbol-mainnet.opening-line.jp:3001",
  "https://001-sai-dual.symbolnode.net:3001"
];

// Mainnet用 epochAdjustment
const epochAdjustment = 1615853185;
```

### カラーテーマの変更

```css
/* ダークモード例 */
body {
  background: #1a1a2e;
}

.tx {
  background: #16213e;
  color: #eee;
}

h1 {
  background: linear-gradient(135deg, #00d2ff, #3a7bd5);
}
```

---

## 📖 コード解説

### 🏗️ 全体構成

このアプリは1つのHTMLファイルで完結しています。

```
HTML
 ├─ スタイル（CSS）
 └─ スクリプト（JavaScript）
      ├─ 設定
      ├─ ユーティリティ関数
      ├─ REST API処理
      ├─ WebSocket処理
      └─ イベントハンドラ
```

---

### 1️⃣ 設定部分

```javascript
const NODES = [
  "https://testnet1.symbol-mikun.net:3001",
  "https://sym-test-01.opening-line.jp:3001"
];
const ADDRESS = "TANWG4F32RMJT6UEKA2COQPJERCDLHB34RIGBII";
const epochAdjustment = 1667250467;
```

**解説**:
- **NODES**: 接続先のSymbolノード（複数指定で冗長性確保）
- **ADDRESS**: 監視対象のSymbolアドレス（39文字）
- **epochAdjustment**: Symbolタイムスタンプ→Unix時間変換用の基準値

---

### 2️⃣ タイムスタンプ変換

```javascript
function formatTimestamp(ts) {
  if (!ts) return "";
  const sec = epochAdjustment + (Number(ts) / 1000);
  return new Date(sec * 1000).toLocaleString("ja-JP", { hour12: false });
}
```

**解説**:
Symbolの独自タイムスタンプ（ミリ秒）を日本語の日時形式に変換

**処理フロー**:
```
Symbol timestamp (ms) 
  ↓ ÷ 1000
秒単位
  ↓ + epochAdjustment
Unix time
  ↓ × 1000 → Date
2025-11-21 12:34:56
```

---

### 3️⃣ Identicon生成

```javascript
function identiconSvg(address) {
  let hash = [];
  for (let i = 0; i < address.length; i++) {
    hash.push(address.charCodeAt(i) & 0xff);
  }
  const hue = hash[0] % 360;
  // SVG生成処理...
}
```

**解説**:
各アドレスに固有の視覚的アイコンを自動生成

**アルゴリズム**:
1. アドレス文字列 → バイト配列に変換
2. 最初のバイトから色相（0-360度）を決定
3. 5×5グリッドに対称パターンを描画
4. SVGをBase64エンコードしてData URIとして返却

**結果**: 同じアドレスは常に同じアイコンになる

---

### 4️⃣ メッセージ復号

```javascript
function decodeMessage(payload) {
  if (!payload) return "(no message)";
  let hex = payload;
  if (hex.startsWith("00")) hex = hex.slice(2);
  const arr = hex.match(/.{1,2}/g);
  const bytes = new Uint8Array(arr.map(h => parseInt(h, 16)));
  return new TextDecoder("utf-8").decode(bytes);
}
```

**解説**:
16進数形式のメッセージをUTF-8文字列に変換

**処理フロー**:
```
"0048656c6c6f"
  ↓ プレフィックス除去
"48656c6c6f"
  ↓ 2文字ずつ分割
["48","65","6c","6c","6f"]
  ↓ 16進数→整数
[72,101,108,108,111]
  ↓ UTF-8デコード
"Hello"
```

---

### 5️⃣ ブロックタイムスタンプ取得

```javascript
async function getBlockTimestamp(height) {
  const NODE = NODES[Math.floor(Math.random() * NODES.length)];
  const res = await fetch(`${NODE}/blocks/${height}`);
  const json = await res.json();
  return json.block.timestamp;
}
```

**解説**:
ブロック高さからブロックのタイムスタンプを取得

**ポイント**:
- ランダムにノードを選択（負荷分散）
- REST APIの `/blocks/{height}` エンドポイントを使用
- エラー時は `null` を返してUI継続

---

### 6️⃣ トランザクションカード生成

```javascript
function createTxElement(hash, msg, state, sender, timestamp) {
  const div = document.createElement("div");
  div.className = `tx ${state}`;
  
  // Identicon
  const icon = document.createElement("img");
  icon.src = identiconSvg(sender);
  
  // メッセージ
  const msgEl = document.createElement("b");
  msgEl.textContent = msg;
  
  // ハッシュ
  const hashEl = document.createElement("div");
  hashEl.textContent = `${state.toUpperCase()} - ${hash}`;
  
  // タイムスタンプ（承認済みのみ）
  if (state === "confirmed" && timestamp) {
    const timeEl = document.createElement("div");
    timeEl.textContent = "🕒 " + formatTimestamp(timestamp);
  }
  
  // クリックイベント
  div.onclick = () => window.open(`https://testnet.symbol.fyi/transactions/${hash}`, "_blank");
  
  document.getElementById("list").prepend(div);
}
```

**解説**:
トランザクション情報をカード形式のDOM要素として生成

**DOM構造**:
```html
<div class="tx confirmed">
  <img class="identicon" src="data:image/svg...">
  <b>メッセージ内容</b>
  <div class="hash">CONFIRMED - HASH...</div>
  <div class="time">🕒 2025-11-21 12:34:56</div>
</div>
```

**特徴**:
- `prepend()` で上部に挿入（新しいものが上）
- クリックでSymbol Fyiの詳細ページを開く

---

### 7️⃣ 状態昇格処理

```javascript
function promote(hash, timestamp) {
  const el = txMap[hash];
  el.classList.remove("unconfirmed");
  el.classList.add("confirmed");
  // ラベル更新、タイムスタンプ追加...
}
```

**解説**:
未承認トランザクションが承認された時の処理

**状態遷移**:
```
未承認
├─ 背景: 黄色 (#fff7d1)
├─ ラベル: UNCONFIRMED
└─ 時刻: なし
     ↓ promote()
承認済み
├─ 背景: 白 (#ffffff)
├─ ラベル: CONFIRMED
└─ 時刻: 🕒 2025-11-21 12:34:56
```

---

### 8️⃣ 過去履歴読み込み

```javascript
async function loadHistory() {
  const url = `${NODE}/transactions/confirmed?address=${ADDRESS}&pageSize=100&order=desc`;
  const res = await fetch(url);
  const json = await res.json();
  const txs = json.data || [];
  txs.reverse().forEach(item => {
    createTxElement(/* ... */);
  });
}
```

**解説**:
REST APIで過去100件の承認済みトランザクションを取得

**クエリパラメータ**:
- `address`: 監視対象アドレス
- `pageSize=100`: 最大100件
- `order=desc`: 新しい順

**なぜ reverse() するのか**:
- APIは新しい順で返す
- UIは古い順に表示したい
- `prepend()` で挿入するため、古い順に処理すると正しい順序になる

---

### 9️⃣ WebSocket接続

```javascript
async function connectWS() {
  const WSURL = NODE.replace("http", "ws") + "/ws";
  ws = new WebSocket(WSURL);
  
  ws.onopen = () => {
    // 接続成功
  };
  
  ws.onmessage = (e) => {
    const data = JSON.parse(e.data);
    if (data.uid !== undefined) {
      uid = data.uid;
      subscribe("block");
      subscribe(`unconfirmedAdded/${ADDRESS}`);
      subscribe(`confirmedAdded/${ADDRESS}`);
    }
  };
  
  ws.onclose = () => setTimeout(connectWS, 2000);
}
```

**解説**:
WebSocketでリアルタイム監視を開始

**接続フロー**:
```
1. WebSocket接続
   ↓
2. UID受信
   ↓
3. 3つのチャネルをサブスクライブ
   - block (ブロック生成)
   - unconfirmedAdded (未承認TX)
   - confirmedAdded (承認済みTX)
   ↓
4. イベント受信待機
```

**自動再接続**:
- 切断時、2秒後に自動的に再接続
- ネットワーク障害からの自動復旧

---

### 🔟 イベントハンドラ

#### 未承認トランザクション

```javascript
addCallback(`unconfirmedAdded/${ADDRESS}`, payload => {
  const tx = payload.data;
  createTxElement(
    tx.meta.hash,
    decodeMessage(tx.transaction.message),
    "unconfirmed",  // 黄色背景
    tx.transaction.signerPublicKey,
    null  // タイムスタンプなし
  );
});
```

**動作**: 黄色背景のカードを新規作成

#### 承認済みトランザクション

```javascript
addCallback(`confirmedAdded/${ADDRESS}`, async payload => {
  const blockTimestamp = await getBlockTimestamp(height);
  
  if (!txMap[hash]) {
    // 新規の場合: 白カード作成
    createTxElement(hash, msg, "confirmed", sender, blockTimestamp);
  } else {
    // 既存の場合: 昇格処理
    promote(hash, blockTimestamp);
  }
});
```

**動作**:
- 既存の未承認カードがある → `promote()` で昇格
- 新規 → 承認済みカードを作成

---

### 1️⃣1️⃣ 初期化処理

```javascript
(async () => {
  await loadHistory();  // 過去履歴を取得
  connectWS();          // WebSocket接続
})();
```

**解説**:
ページ読み込み時に自動実行される即時関数

**起動シーケンス**:
```
ページ読み込み
  ↓
過去100件取得（REST API）
  ↓
画面表示
  ↓
WebSocket接続
  ↓
リアルタイム監視開始
```

---

## 🔄 データフロー図

```
┌─────────────────────────────────────────────┐
│            ページ読み込み                     │
└────────────────┬────────────────────────────┘
                 ↓
        ┌──────────────----──┐
        │  REST API呼び出し   │
        │  過去100件取得       │
        └────────┬───────────┘
                 ↓
        ┌────────────────┐
        │  承認済みカード   │
        │  画面表示        │
        └────────┬────────┘
                 ↓
        ┌────────────────┐
        │ WebSocket接続   │
        │ UID取得         │
        └────────┬───────┘
                 ↓
        ┌────────────────┐
        │ サブスクライブ   │
        │ 3チャネル監視    │
        └────────┬───────┘
                 ↓
     ┌─────────────────────┐
     │   リアルタイム監視     │
     └───────────┬─────────┘
                 ↓
        ┌────────────────┐
        │  イベント受信    │
        └────────┬───────┘
                 ↓
     ┌──────────┴──────────┐
     ↓                     ↓
未承認TX              承認済みTX
  ↓                     ↓
黄色カード         既存カード?
生成               Yes→昇格
                   No→白カード生成
                        ↓
                   タイムスタンプ追加
```

---

## 🎨 UI/UXの工夫

### カラーデザイン

| 要素 | カラー | 意味 |
|------|--------|------|
| 背景 | `#f4f1ff` | Symbolテーマの淡い紫 |
| ヘッダー | グラデーション紫 | ブランドカラー |
| 承認済み | `#ffffff` | 確定した情報 |
| 未承認 | `#fff7d1` | 一時的な状態 |
| ハッシュ | `#6B4DF5` | Symbolの代表色 |

### アニメーション

```css
@keyframes fadeIn {
  from { 
    opacity: 0; 
    transform: translateY(14px); 
  }
  to { 
    opacity: 1; 
    transform: translateY(0); 
  }
}
```

**効果**: 新しいトランザクションが下から14pxスライドしながらフェードイン（0.45秒）

---

## 🛡️ エラーハンドリング

| エラー | 対応 |
|--------|------|
| WebSocket切断 | 2秒後に自動再接続 |
| REST API失敗 | コンソールエラー、UI継続 |
| メッセージデコード失敗 | `"(decode error)"` 表示 |
| タイムスタンプ取得失敗 | null処理でスキップ |
| ノード障害 | 次回接続時に別ノードを選択 |

**特徴**: エラーが発生してもアプリは動き続ける（フォールトトレラント設計）

---

## 🌐 対応環境

### ブラウザ
- ✅ Chrome / Edge（推奨）
- ✅ Firefox
- ✅ Safari
- ⚠️ IE11非対応（ES6+使用のため）

### ネットワーク
- ✅ Symbol Testnet（デフォルト）
- ✅ Symbol Mainnet（設定変更で対応）

---

## 📚 参考リンク

- [Symbol公式ドキュメント](https://docs.symbol.dev/)
- [Symbol REST API仕様](https://symbol.github.io/symbol-openapi/)
- [Symbol Fyi (Explorer)](https://testnet.symbol.fyi/)
- [Symbol Desktop Wallet](https://github.com/symbol/desktop-wallet)

---

## 🐛 トラブルシューティング

### トランザクションが表示されない

**確認事項**:
1. ブラウザのコンソールを開く（F12キー）
2. エラーメッセージを確認
3. ノードが正常か確認

**解決策**:
- ページをリロード
- 別のノードに変更

### メッセージが文字化けする

**原因**: メッセージが暗号化されている

**対応**: 現在のバージョンは平文メッセージのみ対応。暗号化メッセージは `"(decode error)"` と表示されます。

### WebSocket接続が頻繁に切れる

**原因**: ネットワークが不安定またはノード障害

**対応**: 自動再接続機能が2秒後に作動します。複数のノードを設定することで安定性が向上します。

---

## 📝 ライセンス

MIT License

---


[🔝 トップに戻る](#symbol-live-tx-viewer-)

