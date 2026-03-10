# [TipTimeで学んだComposeの基礎と詰まったポイント]

## 🗓 日付
2026-03-10

## 参考URL
Android Developers：Jetpack Compose を用いた Android アプリ開発の基礎
- [Unit2アプリUIを作成する：カスタムチップの金額を計算する](https://developer.android.com/codelabs/basic-android-kotlin-compose-calculate-tip?hl=ja) <br>

参考資料
- [テキスト フィールド – マテリアル デザイン 3](https://m3.material.io/components/text-fields/guidelines#5c8a5f07-b1a5-455f-bf76-7ff0d724f6b0)
- [テキスト フィールド – マテリアル デザイン 3](https://m3.material.io/components/switch/overview)

## 💡 学んだこと（要約）
### 1. @StringResは、文字列リソース参照を明示的に示すアノテーション

### 2. エルビス演算子とNull安全の演算子の違い？
  - `?`と`?:`の違いが曖昧だと思った。

### 3. アクションボタン
 - いわゆるエンターキーのところ「アクションボタン」というらしい
    - Search
    - Send
    - Go
    - Next
    - Done

### 4. スイッチ
  - ボタンの中身
    - Track
    - Thumb
    - Icon

### 5. Modifier.wrapContentWidth(Alignment.End)がワカラン
```kotlin
@Composable
fun RoundTheTipRow(
    roundUp: Boolean,
    onRoundUpChanged: (Boolean) -> Unit,
    modifier: Modifier = Modifier) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .size(48.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(text = stringResource(R.string.round_up_tip))
        Switch(
            modifier = modifier
                .fillMaxWidth()
                .wrapContentWidth(Alignment.End),
            checked = roundUp,
            onCheckedChange = onRoundUpChanged,
        )
    }
}
```

### 6. return NumberFormat.getCurrencyInstance().format(tip)がワカラン
```kotlin
private fun calculateTip(amount: Double, tipPercent: Double = 15.0, roundUp: Boolean): String {
    var tip = tipPercent / 100 * amount
    if (roundUp){
        tip = kotlin.math.ceil(tip)
    }
    return NumberFormat.getCurrencyInstance().format(tip)
}
```
`getCurrencyInstance()`は、端末設定のロケールに応じた通貨表示形式にしてくれる。
自分の環境では日本ロケールだったため、円表示になり、小数点以下が表示されなかった。
そのため、`ceil`の挙動確認がしづらかった。
```kotlin
private fun calculateTip(amount: Double, tipPercent: Double = 15.0, roundUp: Boolean): String {
    var tip = tipPercent / 100 * amount
    if (roundUp){
        tip = kotlin.math.ceil(tip)
    }
    return NumberFormat.getCurrencyInstance(Locale.US).format(tip)
}
```
`Locale.US`を明記することで「$」表記に変えることができて、動作確認できた。



## 💻 コード例 / 使い方
```kotlin
3.キーボードオプションを親から設定する
fun TipTimeLayout() {
    var amountInput by remember { mutableStateOf("") }
    var tipInput by remember { mutableStateOf("") }

    val amount = amountInput.toDoubleOrNull() ?: 0.0
    val tipPercent = tipInput.toDoubleOrNull() ?: 0.0

    val tip = calculateTip(amount, tipPercent)

    //略

    EditNumberField(
        label = R.string.bill_amount,
        keyboardOptions = KeyboardOptions.Default.copy(
            keyboardType = KeyboardType.Number,
            imeAction = ImeAction.Next,
        ),
        value = amountInput,
        onValueChanged = { amountInput = it },
        modifier = Modifier
            .padding(bottom = 32.dp)
            .fillMaxWidth()
    )

    //略
}

@Composable
fun EditNumberField(
    @StringRes label: Int,
    value: String,
    onValueChanged: (String) -> Unit,
    modifier: Modifier,
    keyboardOptions : KeyboardOptions
) {
    TextField(
        value = value,
        singleLine = true,
        modifier = modifier,
        onValueChange = onValueChanged,
        label = { Text(stringResource(label)) },
        keyboardOptions = keyboardOptions,
    )
}


