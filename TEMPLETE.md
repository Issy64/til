# [学習した項目・単語名]

## 🗓 日付
202X-XX-XX

## 💡 学んだこと（要約）
- ここに一言で、何を知ったかを書く
- 以前の知識とどう繋がったか（もしあれば）

## 💻 コード例 / 使い方
```kotlin
item {
    Spacer(modifier = Modifier.height(8.dp))
    OutlinedButton(
        onClick = viewModel::updateActivity,
        modifier = Modifier.fillMaxWidth(),
        enabled = !isSaving,
    ) { Text("保存") }
}