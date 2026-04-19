# Android Architecture: Repository / ViewModel / Screen の責務分離

## 🗓 日付
2026-04-18

## 参考URL
- なし（meshigen開発中の気づき）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
詳細画面の実装では Repository → ViewModel → Screen の3層に責務を分離することで、
テスト可能でUIの状態抜け漏れが減る設計になる。

### 2. 以前の知識とどう繋がったか
- ScreenがDAOに直接アクセスすると依存関係が複雑になる
- ViewModelに `UiState` の状態機械を持たせるとUIの分岐条件が明確になる
- 「押しても変わらない」バグはViewModel内のID保持漏れが原因であることが多い

## 💻 コード例 / 使い方

### Repository層
```kotlin
interface DetailRepository {
    suspend fun getByGourmetId(gourmetId: String): GourmetCollection?
    suspend fun updateFavoriteByGourmetId(gourmetId: String, isFavorite: Boolean)
    suspend fun deleteByGourmetId(gourmetId: String)
}
```

### ViewModel層（UiState + ID保持）
```kotlin
sealed class DetailUiState {
    object Loading : DetailUiState()
    data class Ready(val item: GourmetCollection) : DetailUiState()
    object NotFound : DetailUiState()
}

class DetailViewModel @Inject constructor(
    private val repository: DetailRepository
) : ViewModel() {
    private val _uiState = MutableStateFlow<DetailUiState>(DetailUiState.Loading)
    val uiState: StateFlow<DetailUiState> = _uiState.asStateFlow()
    private var currentGourmetId: String? = null  // ← load()時に必ずセット

    fun load(gourmetId: String) {
        currentGourmetId = gourmetId  // ← これを忘れるとfavorite/deleteが効かない
        viewModelScope.launch {
            val item = repository.getByGourmetId(gourmetId)
            _uiState.value = if (item != null) DetailUiState.Ready(item)
                             else DetailUiState.NotFound
        }
    }

    fun onToggleFavoriteClick() {
        val id = currentGourmetId ?: return
        viewModelScope.launch { repository.updateFavoriteByGourmetId(id, ...) }
    }
}
```

### Screen層
```kotlin
@Composable
fun DetailScreen(gourmetId: String, viewModel: DetailViewModel = hiltViewModel()) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()
    LaunchedEffect(gourmetId) { viewModel.load(gourmetId) }

    when (val state = uiState) {
        is DetailUiState.Loading  -> LoadingContent()
        is DetailUiState.Ready    -> ReadyContent(
            item = state.item,
            onToggleFavoriteClick = viewModel::onToggleFavoriteClick,
            onDeleteClick = viewModel::onDeleteClick
        )
        is DetailUiState.NotFound -> NotFoundContent()
    }
}
```

**バグのヒント**: favoriteボタンが効かない → `currentGourmetId` が `load()` でセットされているか確認する
