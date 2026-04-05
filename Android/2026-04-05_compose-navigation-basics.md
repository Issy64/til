# Compose Navigation（NavController / NavGraph / NavHost）

## 🗓 日付
2026-04-05

## 参考URL
- [Composeで画面間を移動する](https://developer.android.com/codelabs/basic-android-kotlin-compose-navigation?hl=ja)

## 💡 学んだこと（要約）
### 1. NavController・NavGraph・NavHostの役割
- **NavController**: 現在どの画面にいるか（バックスタック）を「状態」として管理する。`rememberNavController()` で生成し、Composableに引数として渡す。
- **NavHost**: 表示する画面を描画するComposable。NavControllerの状態を**観察（observe）**し、変化があれば対応する画面を自動的に再描画する。
- **NavGraph**: 独立したクラスではなく、NavHostのトレーリングラムダ内にDSL（ビルダー構文）で記述される。`composable("ルート名") { 画面のComposable }` の集まりが実体。

### 2. 勘違いしやすいポイント
- NavControllerが「地図（ルート定義）を持つ」→ 実際は地図を持つのはNavGraph。NavControllerは「現在地（状態）」の管理。
- NavGraphが各画面ファイルに分散して定義される → 実際はNavHostの呼び出し1箇所にまとめて書く。
- NavControllerがNavHostを「起動する」→ Composeは宣言的UIなので、NavHostがNavControllerを「引数として受け取り、状態を観察する」関係。

### 3. 気づき
- グラフ理論の「ノード（点）とエッジ（辺）」のイメージがNavGraphの理解に役立つ。
- Kotlinのトレーリングラムダ構文がNavHostとNavGraphの関係を理解する鍵。
- 「呼び出す」と「観察する」の違いがComposeの宣言的UIモデルの本質。

## 💻 コード例 / 使い方
```kotlin
NavHost(navController = ..., startDestination = ...) {
    // ここがNavGraphの定義にあたる（ルートと画面の対応表）
    composable("ルートA") { 画面AのComposable }
    composable("ルートB") { 画面BのComposable }
}
```
