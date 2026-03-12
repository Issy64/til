# [TipTimeで学ぶComposeの自動テスト入門]

## 🗓 日付
2026-03-12

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [UIと状態を操作する](https://developer.android.com/courses/pathways/android-basics-compose-unit-2-pathway-3?hl=ja)

## 💡 学んだこと（要約）

### 1. 自動テスト
  - 自動テストとは、コードが正しいことを確認するために、専用のコードを書いて検証を自動化すること。
  - 手動も大事だけど、Androidでは、一度書けば、ほぼ自動でできる。
  - 機能コードとテストコードは、できるだけ近いタイミングで書いた方が品質を保ちやすい。
#### ローカルテスト(ユニットテスト)
  - コードの一部分(関数、クラス、プロパティ)をテストできる。
  - デバイスやエミュレータは不要。
  - Android Studioは、ローカルテストを自動で実行できる機能有り
  - testディレクトリはmainディレクトリと同じパッケージ構成にすることが多い
  - @VisibleForTestingアノテーション <br>
    本来公開範囲は広くしたくないけど、しゃあなし公開範囲を広くしてるから気を付けろよ！という記述
  >このアノテーションは、クラス、メソッド、またはフィールドの公開範囲（Visibility）が、本来必要な範囲よりも緩められていることを示します。これは、そのコードをテスト可能にするために意図的に行われたものです。 <br>
  オプションとして、テスト用でなければ本来どの公開範囲であるべきだったかを指定できます。これにより、プロダクションコード（本番用コード）内からの意図しないアクセスをツールで検知できるようになります。
#### インストルメンテーションテスト
  - 端末やエミュレータ上で動くテスト。
  - Android API、プラットフォーム機能、実際のUI操作を含む動作をテストできる
  - ユーザー操作をシミュレートし、アプリが適切に反応するかチェックする。
  - テストコードは(Android Application Package)APKに組み込まれる

### 2. ローカルテストを作成する
  - testディレクトリ配下にテストファイルを作成する <br>
```
  app
 └─ src
     ├─ main
     │   └─ java/...
     └─ test
         └─ java/...
```
  ディレクトリ構成は、テスト対象に対応するようにmain側と同じディレクトリ構成にすることが多い <br>
  Testのメソッドには`@Test`アノテーションを付与する <br>
  テストは最後にアサーションで結果を確認する。
  ####  アサーションがワカラン 
  - 「この結果になるはず」という条件を確認するための記述
  - 今回の学習ではassertEquals(期待する結果, メソッドの実行結果)のように記述し、正しいか判定した。

  - assertEqualsの引数は、`expected`（期待される結果）を先に書き、`actual`（実際の計算結果）を後に書くのがルール
  
### 3. インストルメンテーションテストを作成する
  - androidTestディレクトリ配下にテストファイルを作成する <br>
```
  app
 └─ src
     ├─ androidTest
     │   └─ java/...
     └─ main
         └─ java/...
```
  - テキスト入力、ボタン操作、画面表示など、ユーザー操作に近い流れでテストを書く
  - DesignペインのInteractive Modeで手動確認していたようなUI操作を、テストコードで自動実行して確認・判定するイメージ 
  #### @get:Ruleがワカラン
   

## 💻 コード例 / 使い方
2. ローカルテストの構築
```kotlin
package com.example.tiptime

import org.junit.Assert.assertEquals
import org.junit.Test
import java.text.NumberFormat

class TipCalculatorTests {
    @Test
    fun calculateTip_20PercentNoRoundup() {
        val amount = 10.00
        val tipPercent = 20.00
        val expectedTip = NumberFormat.getCurrencyInstance().format(2)
        val actualTip = calculateTip(amount = amount, tipPercent = tipPercent, false)

        assertEquals(expectedTip,actualTip)
    }
}
```
3. インストルメンテーションテストを作成する
```kotlin
package com.example.tiptime

import androidx.compose.ui.test.junit4.createComposeRule
import androidx.compose.ui.test.onNodeWithText
import androidx.compose.ui.test.performTextInput
import com.example.tiptime.ui.theme.TipTimeTheme
import org.junit.Rule
import org.junit.Test
import java.text.NumberFormat

class TipUITests {

    @get:Rule
    // UIテスト用のRuleを作成し、JUnitにテストルールとして認識させる
    val composeTestRule = createComposeRule()

    @Test
    //テスト対象の画面を構築する
    fun calculate_20_percent_tip() {
        composeTestRule.setContent {
            TipTimeTheme() {
                TipTimeLayout()
            }
        }
        //UIに対して、金額とチップのパーセンテージを入力する
        composeTestRule.onNodeWithText("Bill Amount").performTextInput("10")
        composeTestRule.onNodeWithText("Tip Percentage").performTextInput("20")

        //UIに表示されるチップの期待される金額を設定する
        val expectedTip = NumberFormat.getCurrencyInstance().format(2)

        //期待されるUI表示が存在するかを検証する
        composeTestRule.onNodeWithText("Tip Amount: $expectedTip").assertExists("No node with this text was found.")
    }
}
```