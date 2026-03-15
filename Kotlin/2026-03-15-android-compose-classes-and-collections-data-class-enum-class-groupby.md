# [Kotlin practice: Classes and Collectionsで学ぶdata class・enum class・groupBy]

## 🗓 日付
2026-03-15

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [演習: クラスとコレクション](https://developer.android.com/codelabs/basic-android-kotlin-compose-practice-classes-and-collections?hl=ja)
- [単一要素を取得する](https://kotlinlang.org/docs/collection-elements.html#retrieve-by-position)

## この回のゴール
- 今日できるようになりたいこと
- 学習前にあいまいだったこと
- 「何が分かれば前進か」を一言で書く

## 学習前の理解
- ここまでは分かっていた:
- ここがまだ曖昧だった:
- 先に自分が予想していたこと:

## Codelab / Task ごとの記録
<!-- 全Taskを埋めなくてOK。つまずいたTask、理解が進んだTaskだけ残す -->

### Task 1: [data classの実装]
- やったこと:
    与えられた情報からdata classを実装した
- 自分が最初に考えたこと:
    `description`は空欄でもOK、という指示があったので型の後ろに?をつけること
- 実際のコード / 解答との差分:

実装したコード
```kotlin
data class Event(
    val title: String,
    val description: String?,
    val daypart: String,
    val durationInMinutes: Int,
)

fun main() {
	val event = Event(
        title = "Study Kotlin",
        description = "Commit to studying Kotlin at least 15 minutes per day.",
        daypart = "Evening",
        durationInMinutes = 15
    )
    
	println(event)
}
```
解答のコード
```kotlin
data class Event(
    val title: String,
    val description: String? = null,
    val daypart: String,
    val durationInMinutes: Int,
)
```
- ここで学んだこと:
    - nullに対する設計はできていたが、descriptionがインスタンス生成時に引数として渡されない可能性について考慮できていなかったことに気づけた。
- まだ不安なこと:
    - この課題に対しては特になし。

### Task 2: [enum classの実装]
- やったこと:
    enumクラスの作り方を忘れてしまい、調べながら作成した。
- 自分が最初に考えたこと:
    enumクラスはデータの選択肢を入れることは分かっていたが、その記述方法がわかっていなかった。
- 実際のコード / 解答との差分: 

実装したコード
```kotlin
data class Event(
    val title: String,
    val description: String? = null,
    val daypart: String,
    val durationInMinutes: Int,
)

enum class Daypart {
    MORNING
    AFTERNOON
    EVENING
}

fun main() {
	val event = Event(
        title = "Study Kotlin",
        daypart = Daypart.EVENING,
        durationInMinutes = 15
    )
    
	println(event)
}
```
解答のコード
```kotlin
data class Event(
    val title: String,
    val description: String? = null,
    val daypart: Daypart, // データ型をDaypartに変更する
    val durationInMinutes: Int,
)

enum class Daypart {
    MORNING,
    AFTERNOON,
    EVENING,
}
```
- ここで学んだこと:
    - enumクラスはカンマ区切りで記述する
    - 実際に呼び出すときは`クラス名.case`として型を揃える必要がある
    - enumクラスを使うためにString型ではなく、データ型(`Daypart`)を指定する必要があるということ
- まだ不安なこと:
型を合わせる概念をふわっとしか掴めてないこと

### Task 3: [生成されたインスタンスをまとめる]
- やったこと:
    mutableListOf<T>()を使ってリスト化した
- 自分が最初に考えたこと:
    listOfを使って`.add`で追加すればよいのでは？
    listOfでは読み込み専用なので、mutableListOfを利用する必要がある。
- 実際のコード / 解答との差分:

実装したコード
```kotlin
data class Event(
    val title: String,
    val description: String? = null,
    val daypart: Daypart,
    val durationInMinutes: Int,
)

enum class Daypart {
    MORNING,
    AFTERNOON,
    EVENING,
}

fun main() {
    
    val events = mutableListOf<Event>(
        Event(title = "Wake up", description = "Time to get up", daypart = Daypart.MORNING, durationInMinutes = 0),
        Event(title = "Eat breakfast", daypart = Daypart.MORNING, durationInMinutes = 15),
        Event(title = "Learn about Kotlin", daypart = Daypart.AFTERNOON, durationInMinutes = 30),
        Event(title = "Practice Compose", daypart = Daypart.AFTERNOON, durationInMinutes = 60),
        Event(title = "Watch latest DevBytes video", daypart = Daypart.AFTERNOON, durationInMinutes = 10),
    )
	println(events)
    
    // 追加もできる
    events.add(Event(title = "Check out latest Android Jetpack library", daypart = Daypart.EVENING, durationInMinutes = 45))
    println(events)
}
```
解答のコード
```kotlin
val events = mutableListOf<Event>(event1, event2, event3, event4, event5, event6)
```
- ここで学んだこと:
    - `listOf`と`mutableListOf`の使い分けを実装を通して知ることができた
- まだ不安なこと:
    - 特になし。思った通りのやり方でOKだった。

### Task 4: [イベントの絞り込み]
- やったこと:
    filter関数利用して`durationInMinutes`が60未満のものを抽出した。
- 自分が最初に考えたこと:
    60分未満のイベントを抽出するから`filter`関数で良いのではと考えた。
- 実際のコード / 解答との差分:

実装したコード
```kotlin
fun main() {
    
    val events = mutableListOf<Event>(
        Event(title = "Wake up", description = "Time to get up", daypart = Daypart.MORNING, durationInMinutes = 0),
        Event(title = "Eat breakfast", daypart = Daypart.MORNING, durationInMinutes = 15),
        Event(title = "Learn about Kotlin", daypart = Daypart.AFTERNOON, durationInMinutes = 30),
        Event(title = "Practice Compose", daypart = Daypart.AFTERNOON, durationInMinutes = 60),
        Event(title = "Watch latest DevBytes video", daypart = Daypart.AFTERNOON, durationInMinutes = 10),
    )
    
    events.add(Event(title = "Check out latest Android Jetpack library", daypart = Daypart.EVENING, durationInMinutes = 45))
    println(events)
    
    // 60分未満を絞り込み
    val eventsShortTime = events.filter() {
        it.durationInMinutes < 60
    }
    
    println(eventsShortTime.size)
}
```
解答のコード
```kotlin
val shortEvents = events.filter { it.durationInMinutes < 60 }
println("You have ${shortEvents.size} short events.")
```
絞り込みの考え方は同じだった。違いは、変数名と出力方法で、解答では `shortEvents` という名前を付けて、`You have ...` の形式で結果を表示していた。
- ここで学んだこと:
    - 最初は`events.filter { it.durationInMinutes < 60 }`をそのまま記述していたが、再利用することを前提に記述するならば、新しい変数を準備して格納してやらないといけないことに気づけた。
- まだ不安なこと:
    - 特になし。最初の考えの通りで実装できた。

### Task 5: [グルーピングする]
- やったこと:
- 自分が最初に考えたこと:
    groupBy()関数を使ってグルーピングすれば実装できるのでは、と考えた。
- 実際のコード / 解答との差分:

実装したコード
```kotlin
// 時間帯でグルーピング
val groupedEvents = events.groupBy {it.daypart}
println(groupedEvents)

val morningEvents = groupedEvents[Daypart.MORNING] ?: emptyList()
val afternoonEvents = groupedEvents[Daypart.AFTERNOON] ?: emptyList()
val eveningEvents = groupedEvents[Daypart.EVENING] ?: emptyList()

println("Morning: ${morningEvents.size} events")
println("Afternoon: ${afternoonEvents.size} events")
println("Evening: ${eveningEvents.size} events")
```
解答のコード
```kotlin
val groupedEvents = events.groupBy { it.daypart }
groupedEvents.forEach { (daypart, events) ->
    println("$daypart: ${events.size} events")
}
```
groupByでグルーピングする考え方は同じだった。
解答では、出力する際に、繰り返し処理を利用することで、もっと簡潔に記述していた。
- ここで学んだこと:
    - 同じような処理をする時は、もっと簡単にできないかを考えたほうが、わかりやすく記述できると学んだ。
- まだ不安なこと:

### Task 6: [最後の要素を取得する]
- やったこと:
    [単一要素を取得する](https://kotlinlang.org/docs/collection-elements.html)を確認して、最後の要素を取得するlast()を使って、コードを簡素化した。
- 自分が最初に考えたこと:
    「単一要素を取得する」の中に`last()`があり、これは最後の要素を取得するんだろうなと考え、実装しようと思った。
- 実際のコード / 解答との差分:

実装したコード
```kotlin
// before
println("Last event of the day: ${events[events.size - 1].title}")
//after
println("Last event of the day: ${events.last().title}")
```
解答のコード
```kotlin
// なし。実装と全く同じ
```
- ここで学んだこと:
    - Kotlinには便利な関数がたくさんあることがわかった。
- まだ不安なこと:
    - 便利な関数を覚えきれず、冗長なコードを書いてしまいそうなこと。

### Task 7: [クラスを拡張する]
- やったこと:
- 自分が最初に考えたこと:
    `Event.durationOfEvent() {}`で60分を境界として`short`と`long`をif文で分岐すれば良いと考えた。
- 実際のコード / 解答との差分:

実装したコード
```kotlin
// これではダメだった。
val Event.durationOfEvent() {
    if (Event.durationInMinutes < 60) {
        "short"
    } else {
        "long"
    }
}

println("Duration of first event of the day: ${events[0].durationOfEvent}")

// トップレベルに以下を記述
val Event.durationOfEvent: String
    get() = if (durationInMinutes < 60) "short" else "long"

fun main() {
    println("Duration of first event of the day: ${events.first().durationOfEvent}")
}
```
解答のコード
```kotlin
val Event.durationOfEvent: String
    get() = if (this.durationInMinutes < 60) {
        "short"
    } else {
        "long"
    }
```
- ここで学んだこと:
    - 拡張メソッドで解決しようとすると、設問にあった`println("Duration of first event of the day: ${events.first().durationOfEvent}")`では呼び出せず`durationOfEvent()`とする必要があった。
    - そこで拡張プロパティを使おうとしたが、拡張プロバティを宣言するときはローカルで宣言できず、トップレベルがクラス内に置く必要があった。
- まだ不安なこと:
    - 拡張プロパティや拡張メソッドに関する現在の理解レベルでは応用が効いていないので実務運用ができないと感じていること。
