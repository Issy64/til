# [Composeの状態管理入門]

## 🗓 日付
2026-03-09

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [UIと状態を操作する：Composeの状態の概要](https://developer.android.com/courses/pathways/android-basics-compose-unit-2-pathway-3?hl=ja)

Android Developers：APIリファレンス
- [キーボードの種類：KeyboardType](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/input/KeyboardType)

## 💡 学んだこと（要約）
### 1.  state(状態)
  - 時間の変化とともに変化する可能性のある、アプリ内の任意の値。
  - 再コンポーズされると、入力された値が初期値にリセットされるが、rememberによってリセットされないように保存される。
  - rememberは、再コンポーズの間 値を保持する。
  - rememberは、呼び出されたコンポーザブル内で値を保持する。
  - mutableStateOfはコンポーザブルが状態を監視して、再コンポーズをトリガーできるようにする。
  - rememberとmutableStateOfの働きによって、状態ををローカルに保存できる。

### 2.  snackbar
  - 画面の下部に表示される簡単な通知として機能する。
  - トーストと違うのは、アクションを含むことができること。
  
  SnackbarHost.ktライブラリでは以下の通り
>このメソッドは、アタッチされている Scaffold の下部にスナックバーを表示（または表示待ちキューに追加）し、そのスナックバーが消えるまで処理を中断（サスペンド）します。<br>
SnackbarHostState は、一度に最大1つのスナックバーのみを表示することを保証します。別のスナックバーが既に表示されている間にこの関数が呼び出された場合、現在のスナックバーが表示され、その処理が完了するまで呼び出し元は中断されます。呼び出し側のコルーチンがキャンセルされた場合、そのスナックバーは表示から消去されるか、あるいは表示待ちのキューから削除されます。<br>
これら一連の仕組みにより、以下のようなスナックバー・キューのきめ細やかな制御が可能になります。

### 3. 「Composable」「Compose」「Composition」の違い
  - Composable：UIを記述する関数。
  - Composition：コンポーズが管理するUIの木構造。
  - Compose：コンポーザブルを実行して、コンポジションを作ること。

### 4. アプリの状態(state)をComposeが追跡できるようにするためには、State型やMutableState型を利用する。
  - State型は変更不可（読み取り専用）
  - MutableState型は変更可能
  - MutableState型は、State型の読み込み機能に加え、書き込み機能も持つ

### 5. itのコールバックについて
  - 単方向データフローのやり方を図解してみた。
    ![alt text](/res/IMG_4068.jpeg)

### 6. ステートホイスティングの実態
  - 保存した状態を他のコンポーザブルでも利用可能にすること。
  - 状態を呼び出し元に移行してコンポーネントをステートレスにできる。
  - ステートホイスティングの導入の実装例<br>
    - value: T <br>
    - onValueChange: (T) -> Unit

### 7. TextFieldのフォーカスを範囲外タップで外したい
  - TextFieldで入力するとき、キーボードの「ｖ」を押して入力を解除する





## 💻 コード例 / 使い方
```kotlin
3.
TextField(
    value = "",
    onValueChange = {},
    modifier = Modifier,
)

5.
var amountInput = "0"
　　　↓
var amountInput: MutableState<String> = mutableStateOf("0")
or
var amountInput = mutableStateOf("0") //型推論を使用して短縮

@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
   var amountInput = mutableStateOf("0")
   TextField(
       value = amountInput.value,
       onValueChange = { amountInput.value = it }, //このitは値が変更された時、書き換わった値を取得してamountInputに代入する。
       modifier = modifier
   )
}

6.
fun EditNumberField(modifier: Modifier = Modifier, ) {
    var amountInput by remember{ mutableStateOf("") } //型推論を使用して短縮
    TextField(
        label = { Text(stringResource(R.string.bill_amount)) },
        value = amountInput,
        onValueChange = { amountInput = it},
        modifier = modifier,
        singleLine = true, //ここで改行有無の指定ができる
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number)
    )
}
```

