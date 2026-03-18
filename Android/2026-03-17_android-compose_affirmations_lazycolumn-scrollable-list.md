# [コードをいじりながらCompose(Image / safeDrawing)を理解しようとする]

## 🗓 日付
2026-03-17

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [スクロール可能なリストを追加する](https://developer.android.com/courses/pathways/android-basics-compose-unit-3-pathway-2?hl=ja#codelab-https://developer.android.com/codelabs/basic-android-kotlin-compose-training-add-scrollable-list)

---

## 💡 学んだこと（要約）

### 1. リストアイテムのデータクラスを作成する
- modelパッケージを作成し、Affirmationをデータクラスで作成する。
- dataパッケージのDatasource.ktにDatasourceクラスを作成し、loadAffirmations()メソッドを作成し、テキストと画像をList形式で定義する。
- `data class Affirmation()`で「こんなデータを扱うよ」と宣言し、`class Datasource() {}`の`loadAffirmations()`で「こんなデータを入れてるよ」と記述している

### 2. アプリにリストを追加する
- `AffirmationCard`コンポーザブルを作成する
- Affirmation型を引数としてaffirmationを宣言する。
- Cardコンポーザブルの中にColumnコンポーザブルで縦並びのImageとTextを配置する
- `contentScale = ContentScale.Crop`で少し遊んでみる。 <br>
なるほど、画像は領域を埋めるように拡大し、合わない部分はトリミングされるのだと分かった。

- リストで表示できるように`AffirmationList`コンポーザブルを作成する
- `AffirmationList`内で`LazyColumn`コンポーザブルでスクロール可能な`Column`リストを配置する
- システムUIと被らないための`safeDrawing`
- scaffoldとinnerPaddingでも同様に対策できるが、AppBerも使ってないシンプルな構成なので、`safeDrawing`で`Padding`したのだろうと思った。
### 3.

---

## 💻 コード例 / 使い方
### 1. リストアイテムのデータクラスを作成する
#### Affirmation.kt(データクラス)
```kotlin
package com.example.affirmations.model

import androidx.annotation.DrawableRes
import androidx.annotation.StringRes

data class Affirmation(
    @StringRes val stringResourceID: Int,
    @DrawableRes val imageResourceID: Int,
)
```
#### Datasource.kt
```kotlin
/*
 * Copyright (C) 2023 The Android Open Source Project
 * Licensed under the Apache License, Version 2.0 (the "License");
 *      https://www.apache.org/licenses/LICENSE-2.0
 */
package com.example.affirmations.data

import com.example.affirmations.R
import com.example.affirmations.model.Affirmation

class Datasource() {
    fun loadAffirmations(): List<Affirmation> {
        return listOf<Affirmation>(
            Affirmation(R.string.affirmation1, R.drawable.image1),
            Affirmation(R.string.affirmation2, R.drawable.image2),
            Affirmation(R.string.affirmation3, R.drawable.image3),
            Affirmation(R.string.affirmation4, R.drawable.image4),
            Affirmation(R.string.affirmation5, R.drawable.image5),
            Affirmation(R.string.affirmation6, R.drawable.image6),
            Affirmation(R.string.affirmation7, R.drawable.image7),
            Affirmation(R.string.affirmation8, R.drawable.image8),
            Affirmation(R.string.affirmation9, R.drawable.image9),
            Affirmation(R.string.affirmation10, R.drawable.image10))
    }
}
```
### 2. アプリにリストを追加する
#### MainActivity
```kotlin
@Composable
fun AffirmationCard(affirmation: Affirmation, modifier: Modifier = Modifier) {
    Card(modifier = modifier) {
        Column() {
            Image(
                painter = painterResource(affirmation.imageResourceId),
                contentDescription = stringResource(affirmation.stringResourceId),
                modifier = Modifier
                    .fillMaxWidth()
                    .height(194.dp),
                contentScale = ContentScale.Crop,
            )
            Text(
                text = LocalContext.current.getString(affirmation.stringResourceId),
                modifier = Modifier.padding(16.dp),
                style = MaterialTheme.typography.headlineSmall,
            )
        }
    }
}

@Preview
@Composable
private fun AffirmationCardPreview() {
    AffirmationCard(
        Affirmation(R.string.affirmation1, R.drawable.image1)
    )
}
```
#### 【.fillMaxWidth()のとき】
```kotlin
Image(
    painter = painterResource(affirmation.imageResourceId),
    contentDescription = stringResource(affirmation.stringResourceId),
    modifier = Modifier
        .fillMaxWidth()
        .height(194.dp),
    contentScale = ContentScale.Crop,
)
```
![alt text](/res/2026-03-17_fillMaxWidth.png)
#### 【.width(194.dp)のとき】
```kotlin
Image(
    painter = painterResource(affirmation.imageResourceId),
    contentDescription = stringResource(affirmation.stringResourceId),
    modifier = Modifier
        // .fillMaxWidth()
        .width(194.dp)
        .height(194.dp),
    contentScale = ContentScale.Crop,
)
```
![alt text](/res/2026-03-17_width.png) <br>
#### 【LazyColumnで配置する】
```kotlin
@Composable
fun AffirmationsApp() {
    val layoutDirection = LocalLayoutDirection.current // 現在のレイアウト方向を取得
    Surface(
        modifier = Modifier // コードラボではパラメータ名がModifierだったが、公式ドキュメントではmodifierになっている。
            .fillMaxSize()
            .statusBarsPadding()
            .padding(
                start = WindowInsets.safeDrawing.asPaddingValues().calculateStartPadding(layoutDirection),
                end = WindowInsets.safeDrawing.asPaddingValues().calculateEndPadding(layoutDirection),
            ),
    ) {
        AffirmationList(
            affirmationList = Datasource().loadAffirmations()
        )
    }
}

@Composable
fun AffirmationList(affirmationList: List<Affirmation>, modifier: Modifier = Modifier) {
    LazyColumn(modifier = modifier) {
        items(affirmationList) { affirmation ->
            AffirmationCard(
                affirmation = affirmation,
                modifier = Modifier.padding(8.dp)
            )
        }
    }
}
```
![alt text](/res/2026-03-17_safeDrawing.png)
#### 試しに8.dpで確認してみる
```kotlin
@Composable
fun AffirmationsApp() {
//    val layoutDirection = LocalLayoutDirection.current // 現在のレイアウト方向を取得
    Surface(
        modifier = Modifier // コードラボではパラメータ名がModifierだったが、公式ドキュメントではmodifierになっている。
            .fillMaxSize()
            .statusBarsPadding()
            .padding(
//                start = WindowInsets.safeDrawing.asPaddingValues().calculateStartPadding(layoutDirection),
//                end = WindowInsets.safeDrawing.asPaddingValues().calculateEndPadding(layoutDirection),
                8.dp
            ),
    ) {
        AffirmationList(
            affirmationList = Datasource().loadAffirmations()
        )
    }
}
```
![alt text](/res/2026-03-17_8dp.png)

---

## 🤔 ハマりポイント / 疑問点

### 1. 私の環境では`Modifier`という頭文字が大文字のパラメータ設定はエラーになってしまった。
![alt text](/res/About_Android_Studio.png)

### 2. `@StringRes`と`@DrawableRes`のアノテーションはなぜ付与しないといけないの？
`Int` 型は何でも受け取れてしまう。文字列リソースのIDも画像リソースのIDも、中身はどちらも `Int` なので、間違った組み合わせで渡してもコンパイルエラーにならない。
```kotlin
// 型的には全部 OK になってしまう（問題！）
Affirmation(stringResourceId = R.drawable.image1,   // 画像を文字列に渡してる
            imageResourceId  = R.string.affirmation1) // 逆も然り
```
`@StringRes` / `@DrawableRes` を付けると、**Lintが「これ、文字列リソースじゃないよ」と警告を出してくれる**。コンパイルエラーにはならないが、IDEがバグを早期発見してくれる安全装置。
> 📖 [アノテーションによるコード検査の改善](https://developer.android.com/studio/write/annotations)

### 3. メソッド シグネチャってどういう意味？
メソッドの「顔」のこと。**名前 + 引数の型・順番** を合わせたもの。
```kotlin
fun calculateTip(amount: Double, tipPercent: Double, roundUp: Boolean): String
//               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
//               これ全体が「シグネチャ」
```
「このメソッドを呼ぶときに必要な情報」をひと目で示す部分。

### 4. `contentScale = ContentScale.Crop`ってどういう処理？
画像をどう表示領域に収めるかを指定するオプション。

| オプション | 動作 |
|---|---|
| `Crop` | アスペクト比を保ちながら領域を**埋める**。はみ出た部分は切り取り |
| `Fit` | アスペクト比を保ちながら領域に**収める**。余白が出ることがある |
| `FillBounds` | アスペクト比を無視して引き伸ばす |

`.height(194.dp)` で高さ固定 + `Crop` の組み合わせで、**カードの高さを揃えつつ画像を綺麗に見せる**のが今回のパターン。
> 📖 [ContentScale（APIリファレンス）](https://developer.android.com/reference/kotlin/androidx/compose/ui/layout/ContentScale)

### 5. `LocalContext.current.getString(affirmation.stringResourceId)`の`LocalContext`って何？
Composeの関数は通常のKotlin関数なので、Androidの `Context`（アプリの設定や端末情報にアクセスする仕組み）に直接触れない。
`LocalContext` という「Compose内で Context を受け取る窓口」が用意されている。

```kotlin
val context = LocalContext.current  // Compose の中で Context を取り出す
context.getString(R.string.affirmation1)  // Context 経由でリソースを取得
```

ちなみに `stringResource(id)` を使えば `LocalContext` なしで同じことができる。Codelabの書き方はやや回りくどい。
```kotlin
// こっちで OK（推奨）
Text(text = stringResource(affirmation.stringResourceId))

// Codelabの書き方（動くけどComposeらしくない）
Text(text = LocalContext.current.getString(affirmation.stringResourceId))
```
> 📖 [CompositionLocal](https://developer.android.com/develop/ui/compose/compositionlocal)

### 6. `scaffold`と`innerPadding`で解決するケース
`safeDrawing` で直接Paddingする方法と `Scaffold` + `innerPadding` を使う方法の使い分け。

| 方法 | 向いているケース |
|---|---|
| `safeDrawing` で直接Padding | AppBarなし・シンプルな画面（今回のAffirmations） |
| `Scaffold` + `innerPadding` | AppBarやBottomBarがある画面 |

`Scaffold` は TopAppBar / BottomBar / FABなどの高さを自動計算して `innerPadding` として渡してくれる。その `innerPadding` をコンテンツに適用することで、バーとコンテンツが重ならなくなる。

```kotlin
Scaffold(
    topBar = { TopAppBar(...) }
) { innerPadding ->
    // innerPadding に AppBar の高さ + システムUI分が入っている
    AffirmationList(modifier = Modifier.padding(innerPadding))
}
```
> 📖 [ウィンドウインセットの設定](https://developer.android.com/develop/ui/compose/system/insets)

---

## 🔗 今回の学習で繋がった知識

-

AffirmationsApp コンポーザブルで、現在のレイアウトの向きを取得し、変数に保存します。これらは、後でパディングを設定するために使用されます。

次に、Surface コンポーザブルを作成します。このコンポーザブルは、AffirmationsList コンポーザブルのパディングを設定します。

の最大幅と最大高さを埋め、ステータスバーのパディングを設定し、左右のパディングを layoutDirection に設定する Modifier を Surface コンポーザブルに渡します。たとえば、LayoutDirection オブジェクトをパディングに変換する場合は、WindowInsets.safeDrawing.asPaddingValues().calculateStartPadding(layoutDirection) のようにします。