# RoomをFlowで繋ぐ：StateFlow・ViewModelFactory・ID設計

## 🗓 日付
2026-04-16

## 参考URL
- meshigen ISSUE #47: 図鑑一覧をRoomデータソースへ接続

## 💡 学んだこと（要約）

### 1. Flow と StateFlow の使い分け
- `Flow`：DAO層でデータを流す。`suspend` を外してreturn型を `Flow<List<T>>` にする
- `StateFlow`：ViewModel層でUIに公開する状態。Hot streamで「今の値を保持しながら配信」できる
- UIでの購読は `collectAsStateWithLifecycle()`。画面がバックグラウンドのとき自動停止してリソースを節約できる

### 2. ViewModelFactory が必要な理由
- `CollectionRepository` などの依存引数を持つViewModelは `viewModel()` 単体で生成できない
- `ViewModelProvider.Factory` を実装し `create()` 内で依存を注入する
- 将来はHiltなどのDIフレームワークに置き換えられる

### 3. 用途別ID設計の重要性
- `gourmetId`：グルメ詳細遷移用（`gourmets.id` を参照）
- `collectionId`：コレクション個別の更新・削除用（`gourmet_collection.id` を参照）
- IDを1本化すると、同じグルメを複数登録した際に誤更新や遷移不整合が発生する

### 4. クラス参照 vs インスタンス参照
- `ViewModel::method` はクラス参照（コールバックとして渡すとインスタンスが必要になりエラー）
- `viewModel::method` はインスタンス参照（バインド済みなのでそのまま渡せる）

### 2. 以前の知識とどう繋がったか
- UDFのパターン「イベントは上、状態は下」を学んでいたが、Flow/StateFlow + Factory導入でより実践的な構成が整った
- ID設計は「外部キーと主キーの責務を分ける」DB設計の基本に通じる

## 💻 コード例 / 使い方

```kotlin
// DAO: Flow化（suspendを外す）
@Query("SELECT ...")
fun getAllWithGourmet(): Flow<List<CollectionWithGourmet>>

// ViewModel: StateFlowに変換して公開
private val _uiState = MutableStateFlow(CollectionListUiState())
val uiState: StateFlow<CollectionListUiState> = _uiState.asStateFlow()

// ViewModelFactory: 依存引数を持つViewModelを生成
class CollectionListViewModelFactory(
    private val repository: CollectionRepository
) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        return CollectionListViewModel(repository) as T
    }
}

// UI: ライフサイクル考慮の購読
val uiState by viewModel.uiState.collectAsStateWithLifecycle()
```
