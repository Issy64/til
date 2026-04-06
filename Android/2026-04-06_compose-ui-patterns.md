# Compose UI 組み立てパターン

## 🗓 日付
2026-04-06

## 参考URL
- [Material 3 Typography](https://m3.material.io/styles/typography/overview)
- [Scaffold - Jetpack Compose](https://developer.android.com/jetpack/compose/components/scaffold)

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
Compose で画面を組み立てる時の設計判断が複数わかった：積み上げ順・Typography 選択・Spacer・spacing・キーボード制御。

## 💻 コード例 / 使い方

### 積み上げ順
「骨組み → 見出し → 入力欄 → ボタン → 空状態」の順で組み立てると整理しやすい。

### Scaffold + Column
```kotlin
Scaffold { paddingValues ->
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(paddingValues)
            .padding(16.dp)
    ) { /* コンテンツ */ }
}
```
`Scaffold` は画面外枠として AppBar・Snackbar を後から追加しやすい。

### Material 3 Typography は役割で選ぶ
| 用途 | Typography |
|------|-----------|
| 画面タイトル | `headlineMedium` |
| 説明文・補足 | `bodyMedium` |

見た目のサイズではなく、画面内での役割で選ぶ。

### Spacer は height で指定する
```kotlin
// Good
Spacer(modifier = Modifier.height(16.dp))

// Bad（意図が不明確）
Spacer(modifier = Modifier.padding(8.dp))
```

### isNotBlank() vs isNotEmpty()
```kotlin
// スペースのみ入力を無効にしたい → isNotBlank()
enabled = moodText.isNotBlank()

// isNotEmpty() だとスペースのみでも有効になる
```

### LocalSoftwareKeyboardController でキーボードを閉じる
```kotlin
val keyboardController = LocalSoftwareKeyboardController.current

OutlinedTextField(
    keyboardActions = KeyboardActions(
        onDone = { keyboardController?.hide() }
    ),
    keyboardOptions = KeyboardOptions(imeAction = ImeAction.Done)
)
```
`LocalSoftwareKeyboardController` は自動で使える変数ではなく、`current` で取得が必要。

### spacing は 3段階に絞る
`8dp / 16dp / 24dp` の3段階に絞ることで、余白の種類が増えすぎず一貫した画面になる。
