# MainDispatcherRule とコルーチンテストのハング

## 🗓 日付
2026-05-14

## 参考URL
なし（開発中の気づき）

## 💡 学んだこと（要約）
### 1. 何を知ったか
ViewModel コルーチンテストがハングする原因は CPU負荷や Gradle ではなく、Flow の collect や ViewModel 初期化処理が終わらないケースが主因。

### 2. MainDispatcherRule とは
- `Dispatchers.Main` を `StandardTestDispatcher` などのテスト用 Dispatcher に差し替える JUnit ルール
- `viewModelScope` や `Dispatchers.Main` で動くコルーチンを `runTest` のスケジューラで制御できる
- ただし `while(true)` のように suspend もキャンセル確認もない処理は、Dispatcher を差し替えてもハングする

### 3. 対策セット
1. `MainDispatcherRule` で Main Dispatcher を制御可能にする
2. Flow の collect や ViewModel 初期化が終わらない → `backgroundScope` で起動
3. 明示的な `cancel()` または timeout を設定する
4. `advanceUntilIdle()` の使いすぎを避ける（collect が残っているとハングする）

## 💻 コード例 / 使い方

```kotlin
@get:Rule
val mainDispatcherRule = MainDispatcherRule()

@Test
fun exampleTest() = runTest {
    // 終わらない collect は backgroundScope で起動する
    backgroundScope.launch { viewModel.uiState.collect { } }
    // ... assert
}
```
