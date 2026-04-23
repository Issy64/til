# LINE Messaging API + GAS でチャットボット作成

## 🗓 日付
2026-04-23

## 参考URL
- [LINE Developer Community イベント](https://linedevelopercommunity.connpass.com/event/391408/)

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
LINE公式アカウント + Messaging API + GAS（Google Apps Script）を組み合わせてフレックスメッセージ対応チャットボットが作れる。

### 2. 以前の知識とどう繋がったか（もしあれば）
「APIでDBを読み込み、LINE上で表示させる」感覚はAndroid開発のRoom + UI表示の仕組みに近い。スプレッドシートがDBの代替になっている点が新鮮だった。

## 💻 コード例 / 使い方

### セットアップ手順
1. LINE Official Account を作成（LINE Official Account Manager）
2. Messaging API を有効化（設定 → Messaging API）
3. LINE Developers コンソールでプロバイダー・チャンネルを作成
4. チャネルアクセストークンを発行（Messaging API 設定ページ）
5. スプレッドシートに GAS で REPLY 内容を設定
6. Flex Message Simulator でリッチな JSON を作成 → スプレッドシートに貼り付け
7. GAS を WebApp としてデプロイ → Webhook URL を LINE チャンネルに設定

### REPLY の仕組み
- ユーザーが特定テキストを送信 → GAS が受け取り → スプレッドシートで返信内容を決定 → Flex Message を返信
- Flex Message は JSON で定義（Simulator で視覚的に編集可能）

### 活用ポイント・注意点
- 店舗/サービス導入では **リッチメニューとの組み合わせ必須**（テキスト入力なしに呼び出す導線が必要）
- カルーセル形式で商品紹介などに最適
- チャネルアクセストークンは LINE Messaging API の認証キー。外部漏洩注意
