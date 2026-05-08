# Android ViewModel・Repository・UDF 設計パターン

## 🗓 日付
2026-05-08

## 参考URL
- なし（meshigen #64 の実装から）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
ViewModel は UI 状態管理に集中し、DB 操作は Repository に委譲することで責務を分離できる。

### 2. 以前の知識とどう繋がったか
以前は ViewModel から DAO を直接呼ぶ実装をしていたが、Repository を挟むことで「ViewModel = 状態管理」「Repository = データ取得・保存」の役割が明確になった。

## 💻 コード例 / 使い方

### UDF（Unidirectional Data Flow）の流れ
1. ユーザーが入力 → `MoodTextChanged` 発火
2. ViewModel が `moodText` を更新
3. ボタン押下 → `RecommendClicked` 発火
4. ViewModel が `viewModelScope.launch` で Repository を呼び出す
5. 結果を `HomeUiState` に反映
6. Compose が状態を購読して再描画

### viewModelScope.launch で suspend 関数を呼ぶ
```kotlin
viewModelScope.launch {
    val recommendation = repository.getRecommendation(moodText) ?: return@launch
    _uiState.update { currentState ->
        currentState.copy(
            recommendationUiState = HomeRecommendationUiState.Success(
                recommendation = recommendation.toUiModel(),
            )
        )
    }
}
```

### ViewModelFactory（Hilt 未導入時）
```kotlin
class HomeViewModelFactory(
    private val repository: HomeRepository,
) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(HomeViewModel::class.java)) {
            @Suppress("UNCHECKED_CAST")
            return HomeViewModel(repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class: ${modelClass.name}")
    }
}
```

### UIモデルとDBエンティティを分ける
- `GourmetEntity`（DB 層）を画面に直接渡さない
- Repository → ViewModel で `RecommendationUiModel` に変換して画面に渡す
- 画面が Room の内部構造を知らなくてよくなる
