# Room Migration / Collection テーブル追加

## 🗓 日付
2026-04-13

## 参考URL
- [Room データベースの移行](https://developer.android.com/training/data-storage/room/migrating-db-versions)

## 💡 学んだこと（要約）

### 1. Migration の最小3点セット
テーブル追加などスキーマ変更時に必要な3つのステップ:
1. `@Database` の `version` を上げる
2. `Migration(old, new)` を書く（CREATE TABLE 等のSQL）
3. `Room.databaseBuilder(...).addMigrations(...)` に登録する

この3点がセットでないと、既存DBユーザーのアプリがクラッシュする。

### 2. Kotlin デフォルト値と DB デフォルト値は別物
- `favorite = false`: Kotlin でオブジェクト生成時の初期値
- `@ColumnInfo(defaultValue = "0")`: SQL INSERT で省略時のDB列デフォルト
- SQLite の Boolean は実体として `0/1` で扱う

### 3. `@Ignore` は Entity プロパティを永続化対象から外す
- テーブルそのものを無視するわけではない
- JOIN 結果を受け取る `@Embedded` + `@Relation` とは別の用途

### 4. 外部キー列には index を付ける
- 親テーブル更新時の全表走査リスクを下げる
- Room の警告を回避できる
- JOIN クエリの高速化にも有効

### 5. `createdAt` は Long で保持する
- `System.currentTimeMillis()` の戻り値は Long 型
- Int の最大値（約21億）ではミリ秒タイムスタンプを保持できない

## 💻 コード例 / 使い方

```kotlin
// Migration
val MIGRATION_1_2 = object : Migration(1, 2) {
    override fun migrate(db: SupportSQLiteDatabase) {
        db.execSQL("""
            CREATE TABLE IF NOT EXISTS gourmet_collection (
                id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
                gourmet_id INTEGER NOT NULL,
                favorite INTEGER NOT NULL DEFAULT 0,
                created_at INTEGER NOT NULL,
                FOREIGN KEY (gourmet_id) REFERENCES gourmets(id)
            )
        """)
        db.execSQL("CREATE INDEX IF NOT EXISTS index_gourmet_collection_gourmet_id ON gourmet_collection(gourmet_id)")
    }
}

// Database に登録
Room.databaseBuilder(context, MeshigenDatabase::class.java, "meshigen.db")
    .addMigrations(MIGRATION_1_2)
    .build()
```

実機でのDAO確認手順（テストコードなし）:
1. Debug 時だけ一時的に DAO を呼ぶコードを入れる
2. Logcat で `insertedId`、一覧件数、`favoriteAfterUpdate`、`existsAfterDelete` を確認
3. 確認後は一時コードを削除する
