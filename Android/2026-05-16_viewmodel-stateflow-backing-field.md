# ViewModel の StateFlow を backing field パターンに統一する

## 🗓 日付
2026-05-16

## 参考URL
- 参照なし（リファクタリング中に適用）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
ViewModel で公開する StateFlow は backing field パターン（`_state` + `val state`）で一元管理すると、外部からの誤った書き換えを防げる。

### 2. 以前の知識とどう繋がったか
`MutableStateFlow` をそのまま公開するとどこからでも値を変更できてしまう。backing field で private に隠し、`StateFlow` 型のみ外部公開することで単一書き込み責任が明確になる。

## 💻 コード例 / 使い方
```kotlin
class MyViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(UiState())
    val uiState: StateFlow<UiState> = _uiState.asStateFlow()
}
```
`_uiState` は ViewModel 内部からのみ更新し、外部は `uiState` を observe するだけ。
