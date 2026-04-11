# ハーネスエンジニアリング — Claude Code の知見管理

## 🗓 日付
2026-04-10

## 参考URL
- [Claude Code Meetup Japan #4](https://claude-code-meetup.connpass.com/)

## 💡 学んだこと（要約）
### 1. 何を知ったか
チームの知見が消えていく問題に対し、「意図の定義→SSOTで契約→ハーネスで完走」の3段階フレームワークが有効。

### 2. 以前の知識とどう繋がったか
自分の CLAUDE.md / Skills 運用と同じ方向性だが、SSOT（PRD.md / SPEC.json / CONTEXT.json）で契約するという考え方が新しかった。

## 💻 コード例 / 使い方

### 1. 意図の定義
- Why を大切にする（何を作るかではなく、なぜ作るか）

### 2. SSOT で契約
- `PRD.md` — プロダクト要件定義
- `SPEC.json` — 技術仕様
- `CONTEXT.json` — プロジェクト文脈

### 3. ハーネスで完走
- **Hooks** — イベント駆動の自動処理
- **Skills** — 再利用可能なプロンプト部品
- **Orchestrator** — 全体の実行制御
