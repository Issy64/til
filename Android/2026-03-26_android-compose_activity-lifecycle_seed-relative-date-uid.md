# [Activityのライフサイクルをまとめてみる]

## 🗓 日付
2026-03-26

## 💡 学んだこと（要約）

## デモ用の seed データは固定日付より相対日付の方が扱いやすい

Firebase の seed データで Activity の日時を固定日付にしていると、デモ日が変わるたびにデータが古く見えてしまう。  
そのため、`at` のような固定日時ではなく、seed 実行日を基準にした `daysAgo` 形式に変更した。

デモ用途では、seed を実行した時点から見て「直近の活動」に見えることが重要なので、相対日付の方が運用しやすい。

```ts
const seedBaseDate = new Date();

function daysAgo(days: number, hour: number = 10): Timestamp {
  const date = new Date(seedBaseDate);
  date.setHours(hour, 0, 0, 0);
  date.setDate(date.getDate() - days);
  return Timestamp.fromDate(date);
}
```

この形にすると、seed 実行時点を起点として過去13日間にデータを自然に振り分けられる。  
Firebase の seed では投入先 UID を正しく指定する必要がある。  
Firebase の seed を実行するとき、<UID> は Firebase 上の自分のアカウントに対して設定する必要があった。  
今回は Firestore の users コレクションから自分の UID を確認して置き換えたうえで seed を実行し、意図した通りにデータを投入できた。

実行コマンドの構成としては、次の要素を渡していた。

tsx src/seed.ts を実行する。  
npm に「ここから先はスクリプトへの引数」と伝える。  
サービスアカウント JSON を指定する。  
Storage バケット名を指定する。  
投入先の UID を指定する。  
seed 自体が成功しても、表示上の日時が古いままだとデモ用途では見え方が悪くなるため、データ内容と日時設計はセットで確認する必要がある。

## アクティビティのライフサイクル
Android Activity のライフサイクルは操作ごとの差分で覚えると整理しやすい。  
Android では通常の main() ではなく、onCreate() が初期化の起点になる。  
Activity の基本的な流れは次の通り。

onCreate() → onStart() → onResume() ⇄ onPause() → onStop() → onDestroy()  
記録しておくと分かりやすかったのは、ユーザー操作ごとに呼ばれるメソッドが違う点だった。

アプリ初回起動時: onCreate が呼ばれる。  
ホームに戻ってから復帰: onStart が呼ばれる。  
画面回転時: onPause → onStop → onDestroy → onCreate → onStart → onResume  
共有ボタンを押して戻る: onPause → onResume  
Logcat で実際の遷移を確認しながら整理すると、概念だけで覚えるより理解しやすい。
