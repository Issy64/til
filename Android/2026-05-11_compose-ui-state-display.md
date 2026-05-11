# Compose画面はuiStateを表示する役割に集中する

## 🗓 日付
2026-05-11

## 参考URL
- [Guide to app architecture | Android Developers](https://developer.android.com/topic/architecture)

## 💡 学んだこと（要約）

### 1. 何を知ったか（一言）
Compose画面はViewModelから渡されたuiStateを表示するだけにし、判定や副作用はRepository/ViewModel側に置くと責務が分離して読みやすくなる。

### 2. 以前の知識とどう繋がったか
- UDF（Unidirectional Data Flow）の理屈は知っていたが、`isNewDiscovery` のような「初回発見かどうか」の判定をCompose内でやらず、上位で計算した値をフラグとして受け取る形で実体化できた。
- 固定文言は `strings.xml` に置くと、Composeコードを触らずに文言修正でき、多言語化にも対応しやすい。プレースホルダー `%1$s / %2$s` を使うと「カテゴリ / エリア」のような複合表示もリソース側で完結する。
- Issueのスコープを守る（ボタンの `onClick` を空にしておく）ことも設計判断の一部で、未完成ではなく「責務を限定するための意図的な空実装」になる。

## 💻 コード例 / 使い方

```kotlin
val discoveryText = if (item.isNewDiscovery) {
    stringResource(R.string.home_recommendation_new_discovery)
} else {
    stringResource(R.string.home_recommendation_already_discovered)
}

Text(
    text = stringResource(
        R.string.home_recommendation_category_area,
        item.category,
        item.area,
    ),
)
```

```xml
<string name="home_recommendation_category_area">%1$s / %2$s</string>
```
