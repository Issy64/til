# Room Entity / DAO / Database / Seed

## 🗓 日付
2026-04-13

## 参考URL
- [Room を使用してデータを永続化する](https://developer.android.com/training/data-storage/room)
- [Android Basics with Compose - Unit 6](https://developer.android.com/courses/android-basics-compose/unit-6)

## 💡 学んだこと（要約）

### 1. Room の4層構造
- **Entity**: テーブル構造 + 1行データモデルの定義（`@Entity`）
- **DAO**: DB操作（Insert/Query/Update/Delete）の窓口（`@Dao`）
- **Database**: Entity と DAO を束ねる中核（`@Database`）
- **Provider**: Database の生成・共有・初期化制御（`object` シングルトン）

### 2. `@ColumnInfo` でプロパティ名とDB列名を分離できる
- Kotlin側: `searchKeyword`（camelCase）
- DB側: `search_keyword`（snake_case）
- SQL の `AS`（一時エイリアス）とは別物。`@ColumnInfo` はスキーマ定義レベルの恒久マッピング

### 3. 初回シード投入は二重防御が堅い
- `count()==0` で初回判定 → 2回目以降はスキップ（効率的）
- `OnConflictStrategy.IGNORE` で万一の重複を防御（安全性）

### 4. KSPエラー `unexpected jvm signature V` は依存互換性が原因
- Room / KSP / Kotlin のバージョン不整合で発生
- 今回は Room を `2.8.4` に更新して解消

## 💻 コード例 / 使い方

```kotlin
// Entity
@Entity(tableName = "gourmets")
data class GourmetEntity(
    @PrimaryKey val id: Int,
    @ColumnInfo(name = "search_keyword") val searchKeyword: String,
    val name: String,
    val description: String,
    val category: String
)

// DAO
@Dao
interface GourmetDao {
    @Insert(onConflict = OnConflictStrategy.IGNORE)
    suspend fun insertAll(items: List<GourmetEntity>)

    @Query("SELECT COUNT(*) FROM gourmets")
    suspend fun count(): Int

    @Query("SELECT * FROM gourmets WHERE id = :id")
    suspend fun getById(id: Int): GourmetEntity?
}

// Provider（初回シード投入）
object DatabaseProvider {
    fun get(context: Context): MeshigenDatabase {
        val db = Room.databaseBuilder(context, MeshigenDatabase::class.java, "meshigen.db").build()
        // count()==0 のときだけ insertAll
        return db
    }
}
```

検証SQL:
```sql
SELECT COUNT(*) FROM gourmets;           -- 30であること
SELECT id, COUNT(*) c FROM gourmets GROUP BY id HAVING c > 1;  -- 0行であること
```
