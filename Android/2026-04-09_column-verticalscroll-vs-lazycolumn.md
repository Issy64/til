# Column+verticalScroll vs LazyColumn の判断基準

## 🗓 日付
2026-04-09

## 参考URL
- [Lists and grids - Android Developers](https://developer.android.com/develop/ui/compose/lists)

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
要件に `Column + verticalScroll` と明記されている場合は、`LazyColumn` より要件準拠を優先する。ただし判断理由を言語化できるようにしておく。

### 2. 以前の知識とどう繋がったか
「LazyColumnの方が高性能」という一般論に引っ張られがちだが、今回の判断軸は「ヘッダー固定」「要素数が少ない」「可読性と保守性」。要件と技術的妥当性の両面で判断する。

## 💻 コード例 / 使い方
### 選択基準
| 条件 | 推奨 |
|------|------|
| 要素数が多い（数十〜） | `LazyColumn` |
| ヘッダー固定+少数要素 | `Column + verticalScroll` |
| 要件で明示されている | 要件に従う |
| パフォーマンスが問題 | `LazyColumn` に移行 |
