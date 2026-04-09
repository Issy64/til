# Android Studio Preview の NoSuchFieldError 対処法

## 🗓 日付
2026-04-09

## 参考URL
- なし

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
Preview で `NoSuchFieldError: R$string...` が出た場合、コード不備ではなく IDE Preview のキャッシュ不整合の可能性が高い。

### 2. 以前の知識とどう繋がったか
CLI 側で `clean + compile` が通る場合はコードよりIDE側を疑う、という判断基準が使える。

## 💻 コード例 / 使い方
### 対処手順（上から順に試す）
1. `File > Sync Project with Gradle Files`
2. `Build > Rebuild Project`
3. Preview の Refresh ボタン
4. `File > Invalidate Caches / Restart`

### 判断ポイント
- `./gradlew :app:compileDebugKotlin` が成功する → コードは正しい → IDE側の問題
- `strings.xml` に該当キーが存在する → リソース定義は正しい → キャッシュ不整合
