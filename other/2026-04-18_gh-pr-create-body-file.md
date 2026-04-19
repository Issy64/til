# `gh pr create --body-file` で安全なPR本文渡し

## 🗓 日付
2026-04-18

## 参考URL
- なし（meshigen開発中の気づき）

## 💡 学んだこと（要約）
### 1. 何を知ったか（一言）
`gh pr create --body` にバッククォートやMarkdownを直接渡すとシェル展開事故が起きる。
`--body-file` でファイルを渡すと安全。

### 2. 以前の知識とどう繋がったか
- HEREDOCでもバッククォートは展開される
- PRの本文は長く記号が多いため `--body` の直接渡しは壊れやすい

## 💻 コード例 / 使い方

```bash
# ❌ 壊れやすい（バッククォートが展開される可能性）
gh pr create --body "## Summary\n- `someFunction()` を修正"

# ✅ 安全: ファイルに書いてから渡す
cat > /tmp/pr_body.md << 'EOF'
## Summary
- `someFunction()` を修正
- テストを追加

## Test plan
- [ ] 手動確認済み
EOF

gh pr create --title "fix: someFunction を修正" --body-file /tmp/pr_body.md
```
