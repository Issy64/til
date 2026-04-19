# AGP 9 built-in Kotlin + KSP sourceSets 警告対応

## 🗓 日付
2026-04-19

## 参考URL
- （なし）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
AGP 9では `kotlin.sourceSets` への追加が厳格化され、`android.disallowKotlinSourceSets=false` は暫定回避策であり恒久策にはならない。

### 2. 以前の知識とどう繋がったか
根本解決は「互換のある AGP/Kotlin/KSP 構成に揃える」こと。依存更新時はバージョン固定が再現性の基本（`+` 運用は再現性を崩しやすい）。

## 💻 コード例 / 使い方（あれば）

### 検証コマンド
```bash
./gradlew :app:compileDebugKotlin
./gradlew -Pandroid.disallowKotlinSourceSets=true :app:compileDebugKotlin
gh issue view 49 --json body
gh issue edit 49 --body-file /tmp/issue49_body.md
gh pr create --draft --base main --head Issy64/issue-49-agp9-ksp-sourcesets
```

### 実務の注意点
- `Icons.*` を使うなら `material-icons-extended` 依存が必要
- Issue更新前に本文バックアップを持つ（空本文事故の予防）
- Gradle設定変更とドキュメント追加はコミットを分ける
- `.idea` など関係ない未追跡ファイルはコミットしない
- GitHub操作時はネットワーク不安定を想定して再試行戦略を持つ

### PR作成時のポイント
- PR本文は「概要 / 変更内容 / 動作確認 / 関連Issue」で揃える
- 親Issueをクローズする場合は `Closes #XX` を明記

### 復習Q&A
- Q: `android.disallowKotlinSourceSets=false` は何のために使う？
  A: built-in Kotlin の制約で止まる時の暫定回避。恒久策にはしない。
- Q: `Icons.*` の未解決エラー原因は？
  A: `material-icons-extended` 依存が不足していたため。
- Q: Issueチェックを更新する基準は？
  A: 実際に確認できた証跡がある項目だけ `[x]` にする。
- Q: 今回の本質的な解決方針は？
  A: 互換のある AGP/Kotlin/KSP 構成へ寄せて、回避設定なしで build/sync を通すこと。
- Q: `2.3.6+` のような考え方で注意する点は？
  A: 動的バージョンは再現性を崩しやすい。基本は固定バージョン運用。
- Q: 親Issue #43 をPRに含めるには？
  A: PR本文の関連Issueに `Closes #43` を明記する。
- Q: 今回のコミット分割の意図は？
  A: 「設定変更」と「ドキュメント追加」を分離し、レビューしやすくするため。
- Q: GitHub更新時に本文が空になった時の対処は？
  A: 復元用本文ファイルを作り、`gh issue edit --body-file` で再投入する。事前バックアップが重要。
