# Android Repository パターン — ViewModel と DB 処理の分離

## 🗓 日付
2026-04-30

## 参考URL
- なし（meshigen開発での実装から）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
ViewModel から DAO を直接呼ばず Repository を窓口にすることで、取得手段の詳細を ViewModel から隠せる。

### 2. 以前の知識とどう繋がったか
Repository を挟むことで:
- ViewModel は「おすすめをください」と依頼するだけでよくなる
- DB実装を後で変更しても ViewModel 側は変えなくてよい
- `GourmetEntity`（DB用）と `HomeRecommendation`（UI用）を分け、責務が明確になる

## 💻 コード例 / 使い方（あれば）

```kotlin
// interface（窓口の約束）
interface HomeRepository {
    suspend fun getRecommendation(): HomeRecommendation?
}

// 実装
class HomeRepositoryImpl(
    private val gourmetDao: GourmetDao
) : HomeRepository {
    override suspend fun getRecommendation(): HomeRecommendation? {
        val gourmets = gourmetDao.getAll()
        val selected = selectRecommendation(gourmets) ?: return null
        return toHomeRecommendation(selected)
    }

    // 選定ロジックを private 関数に分ける（将来 AI/ランダム選択に差し替えやすい）
    private fun selectRecommendation(gourmets: List<GourmetEntity>): GourmetEntity? =
        gourmets.firstOrNull() // first() は空リストでクラッシュ → firstOrNull() を使う

    private fun toHomeRecommendation(gourmet: GourmetEntity) = HomeRecommendation(
        id = gourmet.id, name = gourmet.name,
        description = gourmet.description, category = gourmet.category,
    )
}
```

ポイント:
- `suspend fun` にする理由: DAO 呼び出しは非同期処理として扱われるため
- `firstOrNull()` vs `first()`: 後者は空リストでクラッシュするため前者を使う
- 選定ロジックを private 関数に分ける（将来 AI/ランダム選択に差し替えやすい）
- `GourmetEntity`（DB用）と `HomeRecommendation`（UI用）を別クラスに分ける → 責務の明確化
- 今回は ViewModel への接続は次 Issue に分けた（Issue の粒度を守る）
