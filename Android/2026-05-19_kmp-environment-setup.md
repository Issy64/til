# Kotlin Multiplatform（KMP）環境構築 on M3 MacBook Air

## 🗓 日付
2026-05-19

## 参考URL
- [Kotlin Multiplatform Wizard](https://kmp.jetbrains.com/)
- [KDoctor - Environment analysis tool](https://github.com/Kotlin/kdoctor)

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
KMP 開発には Android SDK・Xcode・iOS シミュレータがすべて必要で、KDoctor で一括チェックできる。Xcode インストール後に iOS シミュレータを別途ダウンロードしないと起動できないのが落とし穴。

### 2. 以前の知識とどう繋がったか
- Android のみの開発では Xcode は不要だが、KMP では iOS ビルドのために Xcode と iOS シミュレータがセットで必須
- KDoctor は環境依存の警告（`macOS 26.3+ is not yet supported` / `Xcode 26.3+ is not yet supported`）を出すことがあるが、実際の動作には影響しない
- Docker イメージは使っていなくてもストレージを大量に消費し続ける（今回は 35GB）。Xcode 系ツールを入れる前に `docker image prune -a` で整理すると安全

## 💻 コード例 / 使い方

KDoctor による環境チェック（セットアップ完了時の出力）:

```
[✓] Operating System
[✓] Java
[✓] Android SDK
[✓] Xcode
[✓] Gradle Project

Recommendations:
  ! macOS 26.3+ is not yet supported
  ! Xcode 26.3+ is not yet supported
```

詰まりポイントまとめ:

| 問題 | 原因 | 解決策 |
|------|------|--------|
| Xcode インストール後も起動できない | iOS シミュレータが未インストール | Xcode > Platforms からシミュレータをダウンロード |
| ストレージ不足で Xcode が入らない | 不要な Docker イメージが大量占有 | `docker image prune -a` 等で削除 |
| KDoctor の「not yet supported」警告 | ツールが最新 OS に未追従 | 警告は無視して続行可能 |
