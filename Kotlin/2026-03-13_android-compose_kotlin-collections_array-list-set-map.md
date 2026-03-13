# [Kotlinコレクション基礎の学習ログ（配列・List・Set・Map）]

## 🗓 日付
2026-03-13

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [Kotlinでコレクションを利用する](https://developer.android.com/codelabs/basic-android-kotlin-compose-collections?hl=ja)


## 💡 学んだこと（要約）
### 1. Kotlinの配列
- 複数の値を順序付きで保持する方法のひとつ
- 配列には「要素」または「アイテム」と呼ばれる複数の値が含まれている
- 配列内の要素は順序付けられ、インデックス(目次)でアクセスされる
- `val 変数名 = arrayOf<T>(要素1, 要素2, ...)`で宣言できる

### 2. リスト
- サイズ変更可能なコレクション
- 追加・挿入・削除ができる
- リストのコレクション型「List」と「MutableList」
    - `val 変数名 = listOf(要素1, 要素2, ...)`は読み取り専用
    - `val 変数名 = mutableListOf(要素1, 要素2, ...)`は追加・削除できる
- `add()`でリストに要素を追加できる
- `変数名[インデックス番号] = 要素名`でリストの内容を更新できる
- `removeAt(インデックス番号)`、`remove(要素名)`でリストを削除できる

#### forループで反復処理
- `for (要素名 in コレクション名) { 内容 }`

### 3. セット
- インデックスのないコレクション
- リストに比べるとセット内の特定の要素を検索する時間が短い。
- 順序よりも「その要素があるか」を扱うのに向く
- 重複を排除したいケースで使用される

### 4. Mapコレクション
- キーと値で構成されるコレクション
- マップのキーは一意であるが、値は一意ではない
- `mutableMapOf<KT, VT>()`で宣言できる
- `val マップ名 = mapOf(キー to 値、 キー to 値, ...)`でも宣言できる

## 用語の整理
- コレクション
- インターフェース
- サブスクリプト構文
- ハッシュコード
- hashCode()


## 💻 コード例 / 使い方
1. Kotlinの配列
```kotlin
fun main() {
    val rockPlanets = arrayOf<String>("Mercury", "Venus", "Earth", "Mars")
    val gasPlanets = arrayOf("Jupiter", "Saturn", "Uranus", "Neptune") // 型推論で省略可能

    // 配列の加算
    val solarSystem = rockPlanets + gasPlanets
    
    // 配列の変更
    solarSystem[3] = "Little Earth"
    
    // 配列の追加
    //solarSystem[8] = "Pluto" //これではエラーになる
    /*Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 8 out of bounds for length 8*/
    
    println(solarSystem[0])
    println(solarSystem[1])
    println(solarSystem[2])
    println(solarSystem[3])
    println(solarSystem[4])
    println(solarSystem[5])
    println(solarSystem[6])
    println(solarSystem[7])
    // println(solarSystem[8])
    
    // 新しく配列を作って要素の数を増やす
    val newSolarSystem = arrayOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune", "Pluto")

    println(newSolarSystem[8])
}
```

<hr>

2. リスト
```kotlin
fun main() {
    val solarSystem = mutableListOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")

    // リストに追加
    solarSystem.add("Pluto")

    // リストに挿入
    solarSystem.add(3, "Theia")

    // リストの更新
    solarSystem[3] = "Future Moon"
    
    // リストの削除
    solarSystem.removeAt(9) // インデックスで指定する
    solarSystem.remove("Future Moon") // 要素名で指定する
    
    // リスト内の要素の存在確認
    println(solarSystem.contains("Pluto")) //containsで存在確認
    println("Future Moon" in solarSystem) //inで存在確認
}
```
【forループで反復処理】
```kotlin
for (planet in solarSystem) {
    println(planet)
}
```

<hr>

3. セット
```kotlin
// セットで定義
val solarSystem = mutableSetOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")

// セットのサイズを出力
println(solarSystem.size)

//セットに追加
solarSystem.add("Pluto")
println(solarSystem.size)

// 存在確認
println(solarSystem.contains("Pluto"))
println("Pluto" in solarSystem)

// 重複はNG (スルーされる)
solarSystem.add("Pluto")
println(solarSystem.size)

// 削除も可能
solarSystem.remove("Pluto") // 要素名で指定する
println(solarSystem.size)
println(solarSystem.contains("Pluto"))
```

<hr>

4. コレクションをマッピングする
```kotlin
val solarSystem = mutableMapOf(
    "Mercury" to 0,
    "Venus" to 0,
    "Earth" to 1,
    "Mars" to 2,
    "Jupiter" to 79,
    "Saturn" to 82,
    "Uranus" to 27,
    "Neptune" to 14,
)

println(solarSystem.size)

// 要素の追加
solarSystem["Pluto"] = 5
println(solarSystem.size)

// 値の取得
println(solarSystem["Pluto"])
println(solarSystem.get("Theia"))

// 値の削除
solarSystem.remove("Pluto")
println(solarSystem.size)

// 値の更新
solarSystem["Jupiter"] = 78
println(solarSystem["Jupiter"])
```