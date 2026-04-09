# Compose LazyRow + FilterChip によるフィルタ付きリスト

## 🗓 日付
2026-04-08

## 参考URL
- [Compose Lists](https://developer.android.com/develop/ui/compose/lists)
- [Material3 Chip (Compose)](https://developer.android.com/develop/ui/compose/components/chip)
- [Material3 Card (Compose)](https://developer.android.com/develop/ui/compose/components/card)
- [State in Compose](https://developer.android.com/develop/ui/compose/state)

## 💡 学んだこと（要約）
### 1. フィルタ行は LazyRow + FilterChip が相性良い
- 横並び＋可読性が高く、候補数増加にも対応できる
- key は `label` ではなく `id` を使うべき（安定キー）

### 2. 一覧は LazyColumn + items(key = id) で安定キーを付ける
- リスト項目の同一性を安定させ、再コンポーズ時の表示崩れや状態ズレを防ぐ

### 3. カードUIは責務分離する
- `CollectionListScreenContent`（画面全体レイアウト）と `CollectionListCard`（1件表示）を分ける
- LazyColumn 側は「並べる責務だけ」にする

### 4. 仮データは1箇所管理
- フィルタ候補などの仮置きデータは定数化して差し替えポイントを明確にする
- 関数直書きより保守性が上がる

### 5. クリックイベントは状態更新まで設計する
- `onFilterClick = {}` の no-op 放置はNG。UIは押せるのに状態が変わらない問題になる

## 💻 コード例 / 使い方
```kotlin
// フィルタ行
LazyRow {
    items(filters, key = { it.id }) { filter ->
        FilterChip(
            selected = filter.isSelected,
            onClick = { onFilterClick(filter) },
            label = { Text(filter.label) }
        )
    }
}

// 一覧
LazyColumn {
    items(collections, key = { it.id }) { item ->
        CollectionListCard(item = item)
    }
}
```
