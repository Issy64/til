# Compose TextField と state 管理

## 🗓 日付
2026-04-06

## 参考URL
- [TextField - Jetpack Compose](https://developer.android.com/jetpack/compose/text/user-input)

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
Compose の `TextField` は自分で状態を持たず、外側の state を表示するだけ。`onValueChange` でその state を更新することで画面が再コンポーズされる。

### 2. 以前の知識とどう繋がったか
View 時代の `EditText` は内部で文字列を持っていたが、Compose では「state は外で管理し、UI はその反映」という一方向データフローに統一されている。

## 💻 コード例 / 使い方

```kotlin
// ローカル state で管理（画面ローカルな一時状態の場合）
var moodText by rememberSaveable { mutableStateOf("") }

OutlinedTextField(
    value = moodText,
    onValueChange = { moodText = it },
    label = { Text("今の気分は？") }
)
```

### rememberSaveable vs ViewModel の使い分け
| 状態 | 推奨 |
|------|------|
| 画面ローカルな一時状態（入力中文字列など） | `rememberSaveable` |
| 非同期処理・他画面共有・ビジネスロジック | `ViewModel + StateFlow` |

仮UI段階では ViewModel を導入せず、`rememberSaveable` でシンプルに実装する方が責務を増やしすぎず理解しやすい。
