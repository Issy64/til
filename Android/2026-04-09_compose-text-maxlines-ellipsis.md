# Compose Text の maxLines と TextOverflow.Ellipsis

## 🗓 日付
2026-04-09

## 参考URL
- [Compose Text - Android Developers](https://developer.android.com/develop/ui/compose/text)

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
`maxLines` は表示行数の上限を制限し、狭幅時の自動改行も制限対象になる。`overflow = TextOverflow.Ellipsis` と組み合わせると収まらない文字が `...` 表示になる。

### 2. 以前の知識とどう繋がったか
CSSの `text-overflow: ellipsis` と同じ概念だが、Composeでは `maxLines` と `overflow` を両方セットする必要がある。`maxLines` だけだと `Clip`（切り捨て）がデフォルト。

## 💻 コード例 / 使い方
```kotlin
Text(
    text = item.name,
    maxLines = 1,
    overflow = TextOverflow.Ellipsis
)
```

注意点:
- `maxLines + overflow` を入れただけでは「狭幅崩れなし」を完了扱いにしない
- 配置調整や `widthDp=320` 付近のPreviewでの目視確認が別途必要
