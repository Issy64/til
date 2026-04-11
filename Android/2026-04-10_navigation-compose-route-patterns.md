# Navigation Compose のルート管理パターン

## 🗓 日付
2026-04-10

## 💡 学んだこと（要約）
### 1. 何を知ったか
Navigation Compose でのルート定義・遷移・UI制御の実践パターン3つを整理した。

### 2. 以前の知識とどう繋がったか
Unit4 で学んだ NavHost/NavController の基礎が、実アプリ（Meshigen）での設計判断に繋がった。

## 💻 コード例 / 使い方

### ルート定義の集約
- Version Catalog + `sealed object MeshigenDestination` でルートを一元管理
- `detail/{gourmetId}` のような引数名は定数化して一貫性を保つ

### AppShell + BottomNavigation + NavHost の最小構成
- `launchSingleTop` / `popUpTo` / `restoreState` でタブ遷移の履歴暴発を防止

### 引数付き遷移と表示条件分岐
- 一覧→詳細: route + 引数取得で遷移
- 詳細画面中の BottomBar 非表示: 現在の route による表示条件分岐
- UI はイベント通知、NavHost は遷移決定という責務分離が有効
