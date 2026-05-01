# Repository設計パターンとRoom insertの判定

## 🗓 日付
2026-05-01

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
Repositoryはinterface（何ができるか）とImpl（どう実現するか）に分離し、Room `@Insert` の戻り値 `Long` で新規登録か重複かを判定できる。`OnConflictStrategy.IGNORE` では重複時に `-1L` が返る。

### 2. 以前の知識とどう繋がったか
- Repositoryパターン自体は知っていたが、insert結果から新規/既存を判定してUIに返す実装パターンを実際に書いた
- `stringResource()` はComposable専用なので、ロジック層では `context.getString()` で解決済み文字列を渡す必要がある
- Fake DAOを使えばRoomの動作に依存せずRepositoryの判定ロジックだけをテストできる

## 💻 コード例 / 使い方

### Repositoryの責務
```kotlin
// interface: 何ができるか
interface HomeRepository {
    suspend fun getRecommendation(moodText: String): HomeRecommendation?
}

// impl: どう実現するか（DAOをconstructorで受け取る）
class HomeRepositoryImpl(
    private val gourmetDao: GourmetDao,
    private val collectionDao: CollectionDao,
    private val temporaryAiComment: String,
) : HomeRepository { ... }
```

### Room insertの戻り値で新規/既存を判定
```kotlin
@Insert(onConflict = OnConflictStrategy.IGNORE)
suspend fun insert(item: GourmetCollectionEntity): Long

// 使う側
private const val INSERT_IGNORED = -1L

val insertResult = collectionDao.insert(collectionItem)
val isNewDiscovery = insertResult != INSERT_IGNORED
```

### Kotlinの数値リテラル
```kotlin
val a = -1     // Int
val b = -1L    // Long
val c = -1.0   // Double
val d = -1.0F  // Float
```

### ロジック層での文字列リソース
```kotlin
// NG: stringResource() はComposable専用
// OK: context.getString() で解決済み文字列を渡す
HomeRepositoryImpl(
    gourmetDao = ...,
    collectionDao = ...,
    temporaryAiComment = context.getString(R.string.home_temporary_ai_comment),
)
```

### Fake DAOによるRepositoryテスト
```kotlin
// Fake DAOで戻り値を制御
class FakeCollectionDao : CollectionDao {
    var insertReturnValue: Long = 1L
    override suspend fun insert(item: GourmetCollectionEntity) = insertReturnValue
}

// テストケース
// - insert成功時: isNewDiscovery == true
// - insert -1L時: isNewDiscovery == false
// - 候補なし: null
// - 保存値の検証: gourmetId / moodText / aiComment
```
