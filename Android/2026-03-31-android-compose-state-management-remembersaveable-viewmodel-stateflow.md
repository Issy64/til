# [Compose状態管理]

## 🗓 日付
2026-03-31

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [UIと状態を操作する](https://developer.android.com/courses/pathways/android-basics-compose-unit-2-pathway-3?hl=ja)

## 💡 学んだこと（要約）

## Android Developers
- Composeを用いたAndroidアプリ開発の基礎
- https://developer.android.com/courses/android-basics-compose/course?hl=ja
### Unit4：ナビゲーションとアプリ アーキテクチャ
- PW1アーキテクチャコンポーネントを実施

#### Activityライフサイクルと状態保持の整理
- Androidの画面(Activity)にはライフサイクルがある
- `onCreate()` : 画面を作る
- `onStart()` : 画面が見え始める
- `onResume()` : 操作できる状態になる
- `onPause()` : 一時的に操作できなくなる
- `onStop()` : 画面が見えなくなる
- `onDestroy()` : Activityが破棄される

#### rememberSaveableで保持する
- 画面回転をすると、onDestroy()が実行される
```kotlin
@Composable
private fun DessertClickerApp(
    desserts: List<Dessert>
) {
    var revenue by remember { mutableStateOf(0) }
    var dessertsSold by remember { mutableStateOf(0) }

    val currentDessertIndex by remember { mutableStateOf(0) }

    var currentDessertPrice by remember {
        mutableStateOf(desserts[currentDessertIndex].price)
    }
    var currentDessertImageId by remember {
        mutableStateOf(desserts[currentDessertIndex].imageId)
    }

    Scaffold(...) {...}
}
```
- rememberで保持しているが、画面回転によってActivityが再構成されるとrememberの値も消えてしまう。
- それを保持するために`rememberSaveable`を使う
```kotlin
@Composable
private fun DessertClickerApp(
    desserts: List<Dessert>
) {
    var revenue by rememberSaveable { mutableStateOf(0) }
    var dessertsSold by rememberSaveable { mutableStateOf(0) }

    val currentDessertIndex by rememberSaveable { mutableStateOf(0) }

    var currentDessertPrice by rememberSaveable {
        mutableStateOf(desserts[currentDessertIndex].price)
    }
    var currentDessertImageId by rememberSaveable {
        mutableStateOf(desserts[currentDessertIndex].imageId)
    }

    Scaffold(...) {...}
}
```
- これで画面回転が行われても、値と画像を保持することができるようになった。
#### 縦画面
![alt text](/res/2026-03-31_compose-rememberSaveable_portrait.png)
#### 横画面
![alt text](/res/2026-03-31_compose-rememberSaveable_landscape.png)



### Unit4：ComposeでのViewModelと状態

#### まず概要でつかんだこと
- この codelab は、「画面を作る役」と「データを覚えておく役」を分けて、壊れにくいアプリを作る考え方を学ぶ内容だった
- 作るものは、単語を並び替えて当てるゲーム「Unscramble」
- 学んでいる中心は、`操作 -> 状態更新 -> 画面更新` の流れ

#### 今回の理解の軸
- UI は見た目を表示する役
- ViewModel は状態とロジックを管理する役
- State は「今どうなっているか」を表す情報
- Compose は State の変化に応じて画面を更新する

#### 今回いちばん引っかかったこと
- ViewModel という言葉を最初は「考え方・概念」として捉えていたので、「ViewModel クラスを作ります」と言われたときに少し混乱した
- 概念の話をしているのか、Kotlin のクラス実装の話をしているのかが頭の中で混ざっていた
- 今は、ViewModel は「UI 用の状態とロジックを管理する役割」という概念でもあり、Android ではそれを `GameViewModel` のようなクラスとして実装するのだと理解している

#### _uiState と uiState の理解
- `_uiState` と `uiState` の違いが最初はほとんど分からなかった
- 名前が似ているので、「何が違うのか」「なぜ2つ必要なのか」が見えづらかった
- でも `_uiState` は ViewModel の中で更新するための本体、`uiState` は外に公開する読み取り用の状態だと理解してから整理できた
- アンダースコアは飾りではなく、「内部で使う変更可能な状態」を見分けるための記号だと分かった

```kotlin
private val _uiState = MutableStateFlow(GameUiState())
val uiState: StateFlow<GameUiState> = _uiState.asStateFlow()
```

- `_uiState` は内部で更新する
- `uiState` は外部に読み取り専用で公開する
- `asStateFlow()` は必須ではないが、「外部には読み取り専用で見せる」という意図が分かりやすくなる

#### StateFlow<GameUiState> の理解
- 最初は `StateFlow` と `GameUiState` の関係も曖昧だった
- 今は、`StateFlow` は状態を流す仕組みで、`GameUiState` はその中身の型だと理解している
- つまり `uiState: StateFlow<GameUiState>` は、「GameUiState 型の状態を持って流す StateFlow」という意味

#### 単方向データフローの理解
- 最初は「UI から ViewModel に状態を渡す」と考えてしまっていた
- でも実際には、UI から ViewModel に渡すのは状態ではなくイベント
- ボタン押下や入力変更のようなイベントを UI から ViewModel に伝え、ViewModel が状態を更新する
- 更新された状態は `uiState` として ViewModel から UI に渡される
- 「イベントは上に、状態は下に」と考えると整理しやすかった

#### `collectAsState()` の理解
- `collectAsState()` は、ViewModel の `StateFlow` を Compose 側で受け取って表示に使える形にするためのもの
- 最初は「読み取って代入している」と捉えていたが、実際には一回きりの代入ではなく、状態の更新を追いかけて UI の再描画につなげる役割がある
- つまり `ViewModel -> StateFlow -> collectAsState() -> UI更新` という流れになる

#### mutableSetOf() と mutableStateOf() の違い
- どちらも `mutable` なので最初は似たものに見えた
- でも役割は違う
- `mutableSetOf()` は可変の集合を作る
- `mutableStateOf()` は Compose が変化を監視できる可変状態を作る
- どちらも読み書きできるが、`mutableStateOf()` はUI更新と結びつくところが違う

#### コードを読んで分かったこと
```kotlin
private fun shuffleCurrentWord(word: String): String {
    val tempWord = word.toCharArray()

    tempWord.shuffle()
    while (String(tempWord) == word) {
        tempWord.shuffle()
    }
    return String(tempWord)
}
```

- `tempWord` は `CharArray`、`word` は `String` なので、そのままでは比較対象の型が違う
- `String(tempWord)` にするのは、`CharArray` を `String` に変換して比較するため
- `==` は中身の比較で、`===` は同じインスタンスかどうかの比較
- 今回やりたいのは文字列の内容比較なので、`===` ではなく `==` でよい

#### while の意図
- 最初は「同じにならないように while でシャッフルし続ける」と読んでいた
- でも実際は、「まず1回シャッフルして、もし元の単語と同じだったら、ちゃんと変わるまで再試行する」という意図だと分かった
- つまり `while` はメイン処理ではなく再試行処理

#### 初心者としての引っかかり
- ViewModel を概念として理解していたので、ViewModel クラスを作る話に切り替わったときに混乱した
- `_uiState` と `uiState` が似た名前で、最初は役割の違いが見えづらかった
- `mutableSetOf()` と `mutableStateOf()` はどちらも mutable なので、役割の違いが最初は分かりにくかった
- `==` と `===` の違いをあいまいに理解していると、文字列比較の意図を読み違えやすい
- `import androidx.lifecycle.viewmodel.compose.viewModel` の `compose` が名前解決できず少し詰まったが、原因は Gradle sync をしていなかったことだった
- 依存関係を追加しただけでは IDE はすぐに認識しないので、sync が必要だと分かった

#### 今の理解を一言で
- Compose の ViewModel 学習は、UI と状態管理を分離し、状態を安全に持ちながら画面更新につなげる考え方を学ぶものだった

#### 今の理解の整理
- `GameUiState` のような data class は、画面に必要な値をまとめた状態の入れ物
- ViewModel は、その状態とロジックを管理する役
- UI は `uiState` を受け取って表示する
- ユーザー操作などのイベントは、UI から ViewModel の関数を呼んで伝える
- ViewModel はイベントを受けて状態を更新し、その結果が `uiState` を通して UI に反映される
