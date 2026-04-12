# IoT入門: LINE × Supabase × SPRESENSE 構成

## 🗓 日付
2026-04-11

## 参考URL
- ハンズオン参加（LINEで現実を操作する！IoTカメラとクラウドを繋ぐフィジカルAI入門）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
LINE をUIとして、カメラモジュール（SPRESENSE）で写真を撮影し、Supabase に保存してLINEに返すIoTシステムの構成を体験した。

### 2. 以前の知識とどう繋がったか（もしあれば）
Supabase は PostgreSQL ベースの BaaS として知っていたが、IoT のデータ保存先としても使えることがわかった。LINE Developers も通知の仕組みとして知っていたが、カメラ操作の指示UIとして使えるとは思っていなかった。

## 💻 コード例 / 使い方（あれば）

### 構成要素
| コンポーネント | 役割 |
|---|---|
| meeq SIM | SPRESENSE の通信 |
| SPRESENSE（SONY） | カメラ撮影デバイス |
| MQTT | デバイス ↔ クラウド間のメッセージング |
| Supabase Edge Function | LINEからの指示を受け取り、MQTTでSPRESENSEに転送 |
| Supabase（PostgreSQL） | 撮影画像の保存 |
| LINE Developers | ユーザーの操作UI（撮影指示・画像受信） |
| GitHub Codespaces | 開発環境 |

### 全体の流れ
1. LINEでメッセージを送信（撮影指示）
2. Supabase Edge Function がリクエストを受け取る
3. MQTT経由で SPRESENSE にコマンドを送信
4. SPRESENSE がカメラ撮影し、画像を Supabase テーブルに保存
5. LINE に撮影画像を返信

### 気づき
- 自分で構成図を書いたとき、MQTT と Edge Function の存在を見落としていた
- IoTは「現実→デジタル」と「デジタル→現実」の双方向が成立するのが本質
- 活用アイデア: 工場の包装機の生産状況監視、空調室の温湿度確認など
