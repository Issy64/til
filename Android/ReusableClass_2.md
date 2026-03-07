# [学習した項目・単語名]

## 🗓 日付
2026-03-07

## 💡 学んだこと（要約）
1. 「by」は「委譲」のことで、`変数 by 型`で宣言し、を良きに計らって変数に代入してくれるという解釈をしたが<br>
  `変数 by 型`ではなく、`プロパティ(変数) by 委譲先(オブジェクト)`であると理解修正した。<br>
  移譲なのでViewModelを渡すこともできる、とも理解できる。

2. rememberはstateless（状態のない）コンポーザブルに値を保持させることのできるコンポーザブルと、解釈していたが、<br>
  statelessなコンポーザブルをstatefulに変えるための道具だと理解修正した。（メモリに保存して忘れないようにする）

3. mutableStateOf()はstatefulなコンポーザブルに初期値を与える、と解釈していたが、<br>
  「値が変わったら再コンポーズする」という機能を持った箱を作るもの、と理解修正した。

## 学んだ内容をZenn記事に投稿
https://zenn.dev/issy_64/articles/c68fa56b7e0e63

## 💻 コード例 / 使い方
```kotlin
private fun DiceWithButtonAndImage(modifier: Modifier = Modifier) {
    var result by remember { mutableStateOf(1) }
    val imageResource = when (result) {
        1 -> R.drawable.dice_1
        2 -> R.drawable.dice_2
        3 -> R.drawable.dice_3
        4 -> R.drawable.dice_4
        5 -> R.drawable.dice_5
        else -> R.drawable.dice_6
    }

    Column(
        modifier = modifier,
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Image(
            painter = painterResource(imageResource),
            contentDescription = result.toString()
        )
        Spacer(modifier = Modifier.height(16 .dp))
        Button(onClick = { result = (1..6).random() }) {
            Text(stringResource(R.string.roll))
        }
    }
}
```