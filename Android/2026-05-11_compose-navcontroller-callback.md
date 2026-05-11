# ComposableにNavControllerを直接渡さず callback で通知する

## 🗓 日付
2026-05-11

## 参考URL
- [Navigation with Compose | Android Developers](https://developer.android.com/jetpack/compose/navigation)

## 💡 学んだこと（要約）

### 1. 何を知ったか（一言）
画面Composableに `NavController` を渡すのではなく、「何をしたいか」を表す callback（例: `onOpenDetailClick: (String) -> Unit`）だけを渡し、実際の `navigate(...)` は NavHost に集約すると責務が綺麗に分かれる。

### 2. 以前の知識とどう繋がったか
- UDFは「状態は下へ、イベントは上へ」だが、画面遷移という副作用もこのパターンに乗せられる、と腑に落ちた。
- Preview で `onOpenDetailClick = {}` を渡せばNavigation依存なしに描画確認できる。NavController依存だとPreviewが壊れる問題を避けられる。
- 画面間で受け渡す引数は `gourmetId` のような最小限のIDだけにし、表示情報は遷移先がIDから取り直す。ローカルDBを正とする設計と整合する。

## 💻 コード例 / 使い方

```kotlin
// 画面側：遷移の意思だけを通知する
internal fun HomeScreen(
    onOpenDetailClick: (String) -> Unit,
) { /* ... */ }

// NavHost側：遷移ルールを集約する
HomeScreen(
    onOpenDetailClick = { gourmetId ->
        navController.navigate(
            MeshigenDestination.createDetailRoute(gourmetId),
        )
    },
)
```
