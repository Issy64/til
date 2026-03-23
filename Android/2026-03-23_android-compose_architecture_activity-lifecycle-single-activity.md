# [ついに来ましたアーキテクチャ]

## 🗓 日付
2026-03-23

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [アーキテクチャ コンポーネント](https://developer.android.com/courses/pathways/android-basics-compose-unit-4-pathway-1?hl=ja)

## 💡 学んだこと（要約）
### アクティビティのライフサイクル
- onCreate

(DroidKaigi 2025 - [JA] 未経験者・初心者に贈る！40分でわかるAndroidアプリ開発の今と大事なポイント | Shinobu Okano)[https://www.youtube.com/watch?v=6urkj9g2qgw]

### その前にActivityについて少し整理したい
- Activityは画面そのもの
    - ActivityにボタンなどのUI部品を配置する
    - ただ、最近のアプリ開発はシングルアクティビティが主流らしい
- 少しAIを使いながら深堀り
- なぜシングルアクティビティ？
1. Activityは重い
    - OSが管理するので、メモリ消費が多くなる。処理速度もかかる
    - シングルアクティビティでUIを交換するほうがメモリ節約にもなる

2. 画面同士の「データの受け渡し」を楽にするため
    - マルチアクティビティはIntentに渡してやり取りをする
    - 受け渡しにはAndroid OSの力が必要
    https://qiita.com/shira-jun/items/be96b7400ea1457e226f
    - シングルアクティビティだとViewModelによる状態管理と相性が良い

3. 滑らかなアニメーションのため
    - Activityの遷移はOSが決めた遷移の方法になるのでカクっとした遷移になる。