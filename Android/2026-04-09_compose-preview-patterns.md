# Compose Preview整備パターン（Screen/Content分離・4パターン検証）

## 🗓 日付
2026-04-09

## 参考URL
- [Compose Preview - Android Developers](https://developer.android.com/develop/ui/compose/tooling/previews)
- [適応レイアウト - Android Developers](https://developer.android.com/develop/ui/compose/layouts/adaptive/support-different-display-sizes)

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
`Screen` と `Content` の責務を分離し、Previewは `Content(...)` に直接データ注入する構成にすると、表示パターンの検証が容易になる。

### 2. 以前の知識とどう繋がったか
MVVM の ViewModel と UI の分離と同じ発想。Preview では状態生成ロジックを切り離し、意図的にデータを固定することで空状態や狭幅の再現が簡単になる。

## 💻 コード例 / 使い方
### Preview の4パターン固定セット
1. **通常表示** — 標準データで正常系を確認
2. **空状態** — データなしでEmpty UIを確認
3. **狭幅（widthDp=320）** — レイアウト崩れがないか確認
4. **ダークテーマ** — 色・コントラストを確認

### チェックリスト
- Previewデータはファイル内で完結しているか
- `Content(...)` へ直接データ注入できるか
- 日付フォーマットは統一されているか
- 長文でEllipsis確認済みか
- ハードコード文言が残っていないか（`strings.xml` 集約）
