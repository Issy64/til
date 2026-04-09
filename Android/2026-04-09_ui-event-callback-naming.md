# UI層のイベント受け口名を先行固定する設計

## 🗓 日付
2026-04-09

## 参考URL
- なし

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
イベント受け口名（`onOpenMapClick` など）をUI層で先に固定すると、後続の実装（Navigation・実動作）を差し替えやすくなる。

### 2. 以前の知識とどう繋がったか
インターフェースを先に決めて実装を後から埋めるのは、依存性逆転の原則と同じ考え方。UI層が「何を呼ぶか」を先に決めることで、呼ばれる側の実装変更に強くなる。

## 💻 コード例 / 使い方
```kotlin
@Composable
fun DetailScreen(
    onOpenMapClick: () -> Unit,  // 受け口を先に定義
    onShareClick: () -> Unit,
) {
    Button(onClick = onOpenMapClick) {
        Text("地図を開く")
    }
}
```

### semantics の判断
- a11yの `semantics` は有効だが、「今は考慮しない」が方針なら追加より整合性を優先して外す
- Issueのチェックボックスでも未実施項目は正直に未チェックに残す
