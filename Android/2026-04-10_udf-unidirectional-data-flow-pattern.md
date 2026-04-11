# UDF（Unidirectional Data Flow）導入パターン

## 🗓 日付
2026-04-10

## 💡 学んだこと（要約）
### 1. 何を知ったか
UDF導入を4ステップに分解する実践パターンを整理した。契約型分離→ViewModel導入→Screen のステートレス化→仕上げ。

### 2. 以前の知識とどう繋がったか
DessertClicker で学んだ ViewModel + MutableStateFlow の基礎が、Meshigen での本格的な UDF 導入に発展した。

## 💻 コード例 / 使い方

### Step 1: 契約型分離
- `HomeUiState` / `HomeRecommendationUiState` / `HomeUiEvent` を画面から切り出す

### Step 2: ViewModel + StateFlow
- `HomeViewModel` + `MutableStateFlow` + `onEvent` で状態遷移の中心を UI から外す
- 入力ガード（`isBlank()`）は ViewModel 側に寄せると整合性が高い

### Step 3: Screen のステートレス化
- `collectAsStateWithLifecycle` で state を収集
- Screen は `state + event` のみ受け取る純粋な描画関数に
- 依存追加は別コミットに分けると履歴が読みやすい

### Step 4: 仕上げ
- no-op イベント（`ImeDone` など）の削除
- 最小修正 + 手動確認 + Issue/PR の `Closes` 記載で完了を明確に
