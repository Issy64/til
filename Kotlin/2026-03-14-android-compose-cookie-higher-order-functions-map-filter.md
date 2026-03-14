# [Android Compose Cookie題材で学ぶ高階関数（forEach・map・filter・groupBy・fold・sortedBy）]

## 🗓 日付
2026-03-14

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [高階関数を使ってみる](https://developer.android.com/codelabs/basic-android-kotlin-compose-higher-order-functions?hl=ja)
    - [高階関数とラムダ](https://kotlinlang.org/docs/lambdas.html)
    - [forEach()](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/for-each.html)
    - [map()](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/map.html)
    - [filter()](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/filter.html)
    - [groupBy()](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/group-by.html)
    - [fold()](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/fold.html)
    - [sortedBy()](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin.collections/sorted-by.html)

## この回のゴール
- 「高階関数」を自分の言葉で説明できる
- `forEach` / `map` / `filter` / `groupBy` / `fold` / `sortedBy` の役割を区別できる
- 小さなコードで実際に動かせる

## 💡 学んだこと（要約）
### 1. 今日のひとことで要約
- データの出力、データの変更、操作について学習した

### 2. いちばん理解が進んだポイント
- mapの使い方が、UIの入力データをデータベースと通信する型に整形する用途で一番使っている姿を想像できた。

### 3. まだあいまいなポイント
- fold()って実務的にとこで使いますのん？？
- 結局、高階関数ってなんだ？について理解できてない

## 関数ごとのメモ

### 1. forEach
- ひとこと説明: listの中身をループ処理する関数
- 何が入力で、何を返すか: コレクションが入力で、返す値はUnit
- 使いどころ: 取得したデータを全部出力したいとき？
- 自分の理解メモ: 文字列テンプレートでプロパティ使うときは${}で囲った方が良い

### 2. map
- ひとこと説明: listの各要素を変換し、新しいコレクションを作る関数
- 何が入力で、何を返すか: 入力はlistOf(コレクション)、返り値は新しいコレクション型
- 使いどころ: コレクションの中身を一括で変更したいとき？
- 自分の理解メモ: 一括で手を加えたいときに利用するイメージ、mapOfとは無関係。mapは変換の関数、mapOfはMapを作る関数(キーと値)

### 3. filter
- ひとこと説明: Trueになるものを、新しいコレクションとして保存できる関数
- 何が入力で、何を返すか: 入力はコレクション、返り値はフィルターされたコレクション
- 使いどころ: データベースで取得しているデータのフィルタリング？
- 自分の理解メモ: サブセットってなに？ => 例）「もとの集合の一部」softBaked == trueを抜き出したもの

### 4. groupBy
- ひとこと説明: あるコレクションを条件によってグループ化してくれる関数
- 何が入力で、何を返すか: コレクションが入力、条件によって分別されたマップを出力する
- 使いどころ: 取ってきたデータをグルーピングするとき？
- 自分の理解メモ: SQLでいうGroup Byに似ている...！？

### 5. fold
- ひとこと説明: 初期値を設定し、各要素の処理結果を束ねて、最終的に一つの値にまとめる関数
- 何が入力で、何を返すか: 入力はコレクション、出力は束ねた結果を初期値の型で返す
- 使いどころ: コレクション内の要素の情報をひとまとめにしたいとき？現状は合計値ぐらいしか思いつかない...
- 自分の理解メモ: 初期値、要素内を処理に従ってまとめる

### 6. sortedBy
- ひとこと説明: コレクションの任意のプロパティにおいて並び替えのできる関数
- 何が入力で、何を返すか: コレクションが入力で、整列後のコレクションが出力
- 使いどころ: 並び替えのとき？
- 自分の理解メモ: SQLのORDER BYやな。ただし昇順に固定、降順が良ければsortedByDescendingを使う。

## 💻 コード例 / 使い方
```kotlin
class Cookie(
    val name: String,
    val softBaked: Boolean,
    val hasFilling: Boolean,
    val price: Double,
)

val cookies = listOf(
    Cookie(
        name = "Chocolate Chip",
        softBaked = false,
        hasFilling = false,
        price = 1.69,
    ),
    Cookie(
        name = "Banana Walnut", 
        softBaked = true, 
        hasFilling = false, 
        price = 1.49,
    ),
    Cookie(
        name = "Vanilla Creme",
        softBaked = false,
        hasFilling = true,
        price = 1.59,
    ),
    Cookie(
        name = "Chocolate Peanut Butter",
        softBaked = false,
        hasFilling = true,
        price = 1.49,
    ),
    Cookie(
        name = "Snickerdoodle",
        softBaked = true,
        hasFilling = false,
        price = 1.39,
    ),
    Cookie(
        name = "Blueberry Tart",
        softBaked = true,
        hasFilling = true,
        price = 1.79,
    ),
    Cookie(
        name = "Sugar and Sprinkles",
        softBaked = false,
        hasFilling = false,
        price = 1.39,
    )
)

fun main() {
    println("forEach:")
    cookies.forEach { println(it.name) }

    println("\nmap:")
    val names = cookies.map { it.name }
    println(names)

    println("\nfilter:")
    val soft = cookies.filter { it.softBaked }
    println(soft)

    println("\ngroupBy:")
    val grouped = cookies.groupBy { it.softBaked }
    println(grouped)

    println("\nfold:")
    val total = cookies.fold(0.0) { sum, cookie -> sum + cookie.price }
    println(total)

    println("\nsortedBy:")
    val sorted = cookies.sortedBy { it.price }
    println(sorted)
}
```