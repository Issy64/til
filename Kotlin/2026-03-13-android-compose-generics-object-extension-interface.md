# [Compose学習ログ：ジェネリックとobjectと拡張機能の基礎整理]

#### 明確な理解に至らず、今後の実装によって、もう少し理解を深めてからZenn記事に投稿する

## 🗓 日付
2026-03-13

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [ジェネリック、オブジェクト、拡張機能](https://developer.android.com/codelabs/basic-android-kotlin-compose-generics?hl=ja)
    - [スコープ関数](https://kotlinlang.org/docs/generics.html)
    - [列挙型クラス](https://kotlinlang.org/docs/enum-classes.html)
    - [データクラス](https://kotlinlang.org/docs/data-classes.html)
    - [オブジェクトの式と宣言](https://kotlinlang.org/docs/object-declarations.html)
    - [スコープ関数](https://kotlinlang.org/docs/scope-functions.html)

## 💡 学んだこと（要約）
### 1. 再利用可能なクラス【汎用データ型(ジェネリック)】
- ジェネリックは、クラスや関数の中で使う型を固定せず、あとから具体的な型を指定できる仕組み。
- 同じ構造のまま、`String`、`Boolean`、`Int` など異なる型を扱いたいときに使う。
```kotlin
class クラス名 <汎用データ型> (
    val プロパティ名: 汎用データ型,
)
```
として表すことができる。

### 2. 列挙型クラス【enum class】
- Kotlinにある特別なタイプのクラスで、取りうる値をあらかじめ限定した型を作成できる。
- 「選べる値が決まっている」ということを表すのに使える
```kotlin
enum class enum名 {
    case1, case2, case3
}
```

### 3. データクラス【data class】
- データを保持することを目的としたクラスで、プロパティに応じたメソッド(値比較など)が自動生成される。
- data class はデータ保持向けの便利機能が自動で付く
    - equals()
    - hashCode(): 主に HashSet や HashMap などのハッシュベースのコレクションで使われる
    - toString()
    - componentN(): component1()、component2()（以下同様）
    - copy()

### 4. シングルトンオブジェクト【object】
- `object` で定義したものはシングルトンとして扱われ、すでに唯一のインスタンスが存在している。
- object で定義すると、そのオブジェクトのインスタンスが1つだけ作られる
- そのため、`StudentProgress()` のように `class` と同じ感覚で新しいインスタンスを作ろうとするとエラーになることを確認した。
![alt text](/res/singleton.png)

#### コンパニオンオブジェクト
- Kotlinのクラスとオブジェクトは、他の型の内部で定義することができる。
- `companion object` はクラス内に定義する特別なオブジェクトで、プロパティや関数にクラス名から直接アクセスできる
- これにより、クラスに関連する共有データや処理を整理して簡潔にまとめられる。

### 5. プロパティとメソッドでクラスを拡張する
<!-- - 既存のクラスに新しくプロパティやメソッドを追加することができる -->
<!-- - プロパティの追加は`クラス名.プロパティ名`で宣言できる -->
<!-- - メソッドの追加も`クラス名.関数名`で宣言できる -->
<!-- - getter、setterはプロパティ宣言の直後にインデントして記述する。 -->
- 既存の型に、新しくプロパティや関数を追加するような書き方ができる。
- 拡張プロパティは `型.プロパティ名` の形で定義できる。
- 拡張関数は `型.関数名` の形で定義できる。
- getter や setter は、プロパティ宣言の後に続けて記述する。

### 6. インターフェースを利用した拡張関数
```kotlin
interface インターフェース名{
    インターフェースの中身
}
```

### 7. スコープ関数
- オブジェクトの名前を参照せずに、オブジェクトを一時的な文脈として扱いやすくする関数
- スコープ関数という名称は、渡される関数の本体が、スコープ関数が呼び出されるオブジェクトのスコープを引き継ぐことに由来する。
- よく見るスコープ関数
    - let()
    - apply()

## 用語の整理
- クラス
- 型
- オブジェクト
- companion object
- 拡張
- コントラクト（インターフェースは実装すべきプロパティや関数の約束）
- 組み込みデータ型
- getter、setter
- スコープ関数
- インスタンス


## 💻 コード例 / 使い方
1. 再利用可能なクラス <br>

【冗長なクラス作成】
```kotlin
//穴埋め問題
class FillInTheBlankQuestion(
    val questionText: String,
    val answer: String,
    val difficulty: String,
)

//正誤問題
class TrueOrFalseQuestion(
    val questionText: String,
    val answer: Boolean,
    val difficulty: String,
)

//計算問題
class NumericQuestion(
    val questionText: String,
    val answer: Int,
    val difficulty: String,
)
```
【汎用データ型を使用したリファクタ後】
```kotlin
class Question<T>(
    val questionText: String,
    val answer: T,
    val difficulty: String,
)

fun main() {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", "medium")
    val question2 = Question<Boolean>("The sky is green. True or false", false, "easy")
    val question3 = Question<Int>("How many days are there between full moons?", 28, "hard")
}
```

<hr>

2. 列挙型クラス<br>
```kotlin
class Question<T>(
    val questionText: String,
    val answer: T,
    val difficulty: Difficulty,
)

//列挙型クラスを実装
enum class Difficulty{
    EASY, MEDIUM, HARD,
}

fun main() {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM) //クラス名.case名で呼び出し
    val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
}
```

<hr>

3. データクラス<br>
```kotlin
data class Question<T>(
    val questionText: String,
    val answer: T,
    val difficulty: Difficulty,
)

enum class Difficulty{
    EASY, MEDIUM, HARD,
}

fun main() {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    
    println(question1.toString())
}
```

<hr>

4. シングルトンオブジェクト <br>
```kotlin

```
【コンパニオンオブジェクト】
```kotlin
data class Question<T>(
    val questionText: String,
    val answer: T,
    val difficulty: Difficulty,
)

enum class Difficulty{
    EASY, MEDIUM, HARD,
}

class Quiz {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    
    companion object StudentProgress {
        var total: Int = 10
        var answered: Int = 3
    }
}

fun main() {
    
    println("${Quiz.answered} of ${Quiz.total} answered.")
}
```

<hr>

5. プロパティとメソッドでクラスを拡張する <br>

【プロパティの追加】
```kotlin
val Quiz.StudentProgress.progressText: String 
    get() = "${answered} of ${total} answered" //

fun main() {
    println(Quiz.progressText)
}
```
【メソッドの追加】
```kotlin
fun Quiz.StudentProgress.printProgressBar() {
    repeat(Quiz.answered) {
        print("▓")
    }
    repeat(Quiz.total - Quiz.answered) {
        print("▒")
    }
}

fun main() {
	Quiz.printProgressBar()
}
```

<hr>

6. インターフェースを利用した拡張関数 <br>
```kotlin
data class Question<T>(
    val questionText: String,
    val answer: T,
    val difficulty: Difficulty,
)

enum class Difficulty{
    EASY, MEDIUM, HARD,
}

// インターフェース
interface ProgressPrintable {
    val progressText: String
    fun printProgressBar()
}

class Quiz: ProgressPrintable {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    
    override val progressText: String
    	get() = "${answered} of ${total} answered"
    
    companion object StudentProgress {
        var total: Int = 10
        var answered: Int = 3
    }
    
    override fun printProgressBar() {
        repeat(Quiz.answered) {
            print("▓")
        }
        repeat(Quiz.total - Quiz.answered) {
            print("▒")
        }

        println()
        println(progressText)
	}
}


fun main() {
	Quiz().printProgressBar()
}
```

<hr>

7. スコープ関数 <br>

【let】
```kotlin
fun printQuiz() {
    // 冗長な記述
    println(question1.questionText)
    println(question1.answer)
    println(question1.difficulty)
    println()
    println(question2.questionText)
    println(question2.answer)
    println(question2.difficulty)
    println()
    println(question3.questionText)
    println(question3.answer)
    println(question3.difficulty)
    println()
    
    //let()で記述
    question1.let {
        println(it.questionText)
        println(it.answer)
        println(it.difficulty)
    }
    println()
    question2.let {
        println(it.questionText)
        println(it.answer)
        println(it.difficulty)
    }
    println()
    question3.let {
        println(it.questionText)
        println(it.answer)
        println(it.difficulty)
    }
    println()
}

```
【apply】
```kotlin
fun main() {
	Quiz().apply {
        printQuiz()
    }
}
```

