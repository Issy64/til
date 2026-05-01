# POJO変換とアプリ開発における命名規則

## 🗓 日付
2026-05-01

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
Android開発では、データの「層」によって命名の考え方を分ける必要がある。永続化モデル（Room Entity / Firestore DTO）では「事実」を名前にし、アプリ内モデル（Repository返却値 / UiModel）では「状態」を表してよい。

### 2. 以前の知識とどう繋がったか
KotlinのBoolean命名で `isXxx` が自然に使えることは知っていたが、Room EntityやFirestore DTOではPOJO変換時にgetter名の解釈差が問題になりうる。`@ColumnInfo` でDB名とKotlinプロパティ名を分離する手法で両立できることがわかった。

## 💻 コード例 / 使い方

### 永続化モデル: 事実を名前にする
```kotlin
// Room Entity — DBに保存する事実
val gourmetId: Int
val moodText: String
val favorite: Boolean   // isXxxを避ける
val createdAt: Long
```

### DB名とKotlin名の分離
```kotlin
@ColumnInfo(name = "is_favorite")
val favorite: Boolean   // DB上はis_favorite、Kotlin上はfavorite
```

### Firestoreのフィールド名
```text
// おすすめ: 安定したcamelCase
gourmetId, moodText, favorite, createdAt

// 避けたい
isNewDiscovery, getFavorite, mMoodText
```

### アプリ内モデル: 状態を表してよい
```kotlin
// Repository返却値 — 一時的な処理結果
data class HomeRecommendation(
    val id: Int,
    val name: String,
    val isNewDiscovery: Boolean,  // 今回の処理結果なのでOK
)
```
