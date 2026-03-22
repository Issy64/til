# [グリッドを深堀りする]

## 🗓 日付
2026-03-22

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [演習：グリッドを作成する](https://developer.android.com/codelabs/basic-android-kotlin-compose-practice-grid?hl=ja)

## 💡 学んだこと（要約）

## Imageのpainterについて
- `painter = DataSource.topics[0]`だと呼び出せなかった。
    Topicオブジェクトそのものを読んでいるが悪かった。 <br>
    `DataSource.topics[0].image`とすることで画像リソースを渡すことができる。 <br>
    このままでは画像リソースIDを渡しているだけなので、`painterResource()`を使って画像リソースIDを Painter に変換する。 <br>
```kotlin
// これで画像を表示できる
painter = painterResource(DataSource.topics[0].image)
```

## 縦横比
[aspectRaito](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/package-summary#(androidx.compose.ui.Modifier).aspectRatio(kotlin.Float,kotlin.Boolean))
- 縦横比を指定することができるModifier
```kotlin
// 正方形（1対1）にしたいとき
Modifier.aspectRatio(1f)

// 横長（例：16対9）にしたいとき
Modifier.aspectRatio(16f / 9f)
```
Cardの比率固定に使う

## テキストのサイズ変更
[タイポグラフィ](https://developer.android.com/reference/kotlin/androidx/compose/material/Typography)
- styleで設定する
```kotlin

```

## Lazy グリッド
#### グリッド表示の種類
- LazyVerticalGrid (縦方向)
- LazyHorizontalGrid (横方向)
#### プロパティの設定
- columns（列数の設定）
GridCells.Fixed(◯)とGridCells.Adaptive(minSize = ◯.dp) <br>
```kotlin
// 固定で列数を設定
columns = GridCells.Fixed(2)
// 最小サイズを設定する
columns = GridCells.Adaptive(minSize = 128.dp)
```
- contentPadding（グリッド全体のパディング）
```kotlin
contentPadding = PaddingValues(16.dp)
```
- verticalArrangementとhorizontalArrangement（中身同士のパディング）
```kotlin
// 縦方向の隙間
verticalArrangement = Arrangement.spaceBy(8.dp)

// 横方向の隙間
horizontalArrangement = Attangement.spaceBy(8.dp)
```

#### 記事書きながらだったので詳細はZenn記事へ
[JetpackCompose初心者の私がGrid表示を理解しようとする](https://zenn.dev/issy_64/articles/ac9093cc89449d)