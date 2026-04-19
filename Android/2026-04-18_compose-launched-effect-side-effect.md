# Compose の `LaunchedEffect` で副作用を管理する

## 🗓 日付
2026-04-18

## 参考URL
- なし（meshigen開発中の気づき）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
Composable内でロード処理などの副作用を実行するには `LaunchedEffect(key)` を使い、
再コンポーズによる多重呼び出しを防ぐ。

### 2. 以前の知識とどう繋がったか
- Composableは再コンポーズのたびに関数が再実行される
- 副作用を本体に直接書くと `viewModel.load()` が何度も呼ばれる可能性がある
- `LaunchedEffect` はkeyが変わったときだけ再実行されるため安全

## 💻 コード例 / 使い方

### 実用パターン
```kotlin
@Composable
fun DetailScreen(
    gourmetId: String,
    detailViewModel: DetailViewModel = hiltViewModel()
) {
    // gourmetIdが変わったときだけload()を実行
    LaunchedEffect(gourmetId) {
        detailViewModel.load(gourmetId)
    }

    val uiState by detailViewModel.uiState.collectAsStateWithLifecycle()

    when (uiState) {
        is DetailUiState.Loading  -> LoadingContent()
        is DetailUiState.Ready    -> ReadyContent(...)
        is DetailUiState.NotFound -> NotFoundContent()
    }
}
```

### よくあるミス
```kotlin
// ❌ 再コンポーズのたびにload()が呼ばれる可能性
@Composable
fun DetailScreen(...) {
    detailViewModel.load(gourmetId)  // 危険
}

// ✅ LaunchedEffectで副作用を隔離
@Composable
fun DetailScreen(...) {
    LaunchedEffect(gourmetId) {
        detailViewModel.load(gourmetId)
    }
}
```
