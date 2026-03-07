# [再利用なクラスをジェネリックを利用して実装する]

## 🗓 日付
2026-03-06

## 💡 学んだこと（要約）
1. 引数 `Modifier`の`(modifier: Modifier = Modifier)`は「modifier」というパラメータをMdifier型で定義し、引数に渡されなかったらプレーンなModifierオブジェクトをデフォルトで設定するよ！というだけだった...
2. `wrapContentSize`は親から指定されたサイズ指定を無視して、コンテンツを描画するコンポーザブルだとわかった。
3. Vector Assetダイアログの使い所
- Google標準の「Material Icons」を使いたいとき
  - もしかしてGoogleのロゴとか取れたりする...?
  - Vecvor Assetの下のCrip Artをクリックする -> めっちゃアイコン出てくる
  - メッチャクチャ種類あるやないかい！！アイコン死ぬほど探してたぞ...
- インポート時にサイズ(.dp)を変更したいとき
- アイコンの不透明度(Alpha)をあらかじめ指定したいとき
- SVGでエラーや表示崩れが起きたとき

## 💻 コード例 / 使い方
```kotlin
1.
@Composable 
fun DiceWithButtonAndImage(modifier: Modifier = Modifier) {
}
2.
fun Modifier.wrapContentSize(
    align: Alignment = Alignment.Center,
    unbounded: Boolean = false
) = this.then(
    if (align == Alignment.Center && !unbounded) {
        WrapContentSizeCenter
    } else if (align == Alignment.TopStart && !unbounded) {
        WrapContentSizeTopStart
    } else {
        WrapContentElement.size(align, unbounded)
    }
)
private val WrapContentSizeCenter = WrapContentElement.size(Alignment.Center, false)
private val WrapContentSizeTopStart = WrapContentElement.size(Alignment.TopStart, false)

