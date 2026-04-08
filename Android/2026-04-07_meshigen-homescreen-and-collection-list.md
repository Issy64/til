# meshigen: HomeScreen 仮UI〜CollectionList 土台

## 🗓 日付
2026-04-07

## 参考URL
- [PR #19](https://github.com/Issy64/meshigen/pull/19)

## 💡 学んだこと（要約）

### 1. sealed interface で状態を限定する
- `when` で状態漏れを防ぐ。`data object` は値比較、`data class` は `is` で型判定

### 2. rememberSaveable vs remember
- 保存しやすい型は `rememberSaveable`、独自 sealed interface はまず `remember`

### 3. Screen/Content 分離（State Hoisting）
- 状態保持と描画を分け、Preview に固定状態を注入しやすくする
- テスト・ViewModel 接続時の差し替えにも有利

### 4. LazyColumn の items/item と key
- `items` は複数要素用、`item` は単一ブロック用
- `key = { it.id }` で並び替え時の状態ずれを防ぐ

### 5. UiModel と DB モデルの分離
- UI専用モデルを定義し、Room/Firestore 変更に強くする
- `isFavorite` → `favorite`（Firestore POJO の `is` プレフィックス解釈リスク回避）

### 6. 文言は strings.xml へ、仮データは feature 配下へ
- UI固定文言から `stringResource` で切り出し。ダミーデータは data 層ではなく feature 配下

### 7. Issue 運用
- 完了判定: 実装確認 + ビルド成功 + Issue チェック更新
- コミット粒度は関心事単位。PR に `Closes #番号` で自動クローズ
- 大きい Issue は「基盤→本体→品質→検証」でサブIssue化

## 💻 コード例 / 使い方
```kotlin
// sealed interface で状態限定
sealed interface HomeResultUiState {
    data object Initial : HomeResultUiState
    data class Success(val items: List<RecommendationUiModel>) : HomeResultUiState
}

// when 分岐
when (state) {
    HomeResultUiState.Initial -> { /* 値比較 */ }
    is HomeResultUiState.Success -> { /* 型判定 */ }
}
```
