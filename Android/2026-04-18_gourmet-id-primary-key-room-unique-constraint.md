# `gourmetId` 主軸設計とRoomのUNIQUE制約

## 🗓 日付
2026-04-18

## 参考URL
- なし（meshigen開発中の気づき）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
アプリ層のIDを `gourmetId` に統一し、`collectionId` をDB内部キーに限定することで、
画面導線・更新処理・Repository契約のブレをなくせる。

### 2. 以前の知識とどう繋がったか
- DBの主キーをそのまま画面間で使うと「レコード単位」と「エンティティ単位」が混線する
- 発見図鑑を「1グルメ1件」に制約するには `gourmet_id UNIQUE` + `OnConflictStrategy.IGNORE` + Migrationの3点セットが必要

## 💻 コード例 / 使い方

### ID方針
| キー | 役割 |
|------|------|
| `gourmetId` | アプリ層の主ID（Navigation・Repository・更新導線） |
| `collectionId` | DB内部キー（`gourmet_collection.id`） |

### UNIQUE制約
```sql
CREATE TABLE gourmet_collection (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    gourmet_id TEXT NOT NULL UNIQUE,
    is_favorite INTEGER NOT NULL DEFAULT 0
)
```

### INSERT IGNORE（Room DAO）
```kotlin
@Insert(onConflict = OnConflictStrategy.IGNORE)
suspend fun insertIfNotExists(entry: GourmetCollectionEntity): Long
```

### Migration 2→3（重複整理 + UNIQUE INDEX再作成）
```kotlin
val MIGRATION_2_3 = object : Migration(2, 3) {
    override fun migrate(database: SupportSQLiteDatabase) {
        database.execSQL("DROP INDEX IF EXISTS index_gourmet_collection_gourmet_id")
        database.execSQL("""
            DELETE FROM gourmet_collection
            WHERE id NOT IN (
                SELECT MIN(id) FROM gourmet_collection GROUP BY gourmet_id
            )
        """)
        database.execSQL(
            "CREATE UNIQUE INDEX index_gourmet_collection_gourmet_id ON gourmet_collection(gourmet_id)"
        )
    }
}
```

### SQLのSELECT vs WHERE（よくあるミス）
```sql
-- ❌ 間違い: 比較結果(0/1)を返す式
SELECT gc.gourmet_id = :gourmetId FROM gourmet_collection gc

-- ✅ 正しい: 列の値を返す
SELECT gc.gourmet_id
FROM gourmet_collection gc
WHERE gc.gourmet_id = :gourmetId
```
**覚え方**: `SELECT` = 何を返すか、`WHERE` = どの行を返すか
