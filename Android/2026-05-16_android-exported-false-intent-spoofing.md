# android:exported=false で Intent Spoofing を防ぐ

## 🗓 日付
2026-05-16

## 参考URL
- 参照なし（実装中に発見）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
Service コンポーネントに `android:exported="false"` を指定しないと、外部アプリから Intent を送信されて意図しない動作を起こされる（Intent Spoofing）。

### 2. 以前の知識とどう繋がったか
Activity や BroadcastReceiver の exported 明示は知っていたが、Service も同様の対象であることを再確認。Android 12 以降は未指定だとビルドエラーになるため、明示的指定が必須。

## 💻 コード例 / 使い方
```xml
<service
    android:name=".TimerService"
    android:exported="false" />
```
外部アプリからは起動できなくなり、同一アプリ内からのみ利用可能になる。
