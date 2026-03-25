# [Firebaseを構築してみたい]

## 🗓 日付
2026-03-24

## 参考URL
- [Firebase](https://firebase.google.com/?hl=ja)

## 💡 学んだこと（要約）
- Firebaseを自分で構築できてなかったので、やってみたい。

## Firebaseを構築してみる(part1)
###  Firebaseとは
Googleが提供しているアプリ開発のための協力な裏方(バックエンドツール)
- Firebaseの種類(代表的なもの)
    - Authentication（ユーザー認証） <br>
    - Cloud firestore（データベース） <br>
    - Cloud Storage（ファイル保存） <br>
    - アナリティクス（分析） <br>

- Firebaseを使うメリット
    - リアルタイム性
    - スモールスタート
    - Androidとの相性

### 実際に構築してみる
1. Firebaseプロジェクトを作成
![alt text](/res/2026-03-24_create_firebase_01.png)
![alt text](/res/2026-03-24_create_firebase_02.png)
流れに沿って進めるだけで簡単

2. アプリの登録
- Androidアプリを選択
![alt text](/res/2026-03-24_create_firebase_03.png)
- その他情報を追加
![alt text](/res/2026-03-24_create_firebase_04.png)

3. SDKの設定
- gradle(Project)に設定を記述する
```kotlin
id("com.google.gms.google-services") version "4.4.4" apply false
```
- gradle(App)に設定を記述する
```kotlin
id("com.google.gms.google-services")
```

4. サービスの追加
```kotlin
implementation(platform("com.google.firebase:firebase-bom:34.11.0"))
implementation("com.google.firebase:firebase-firestore")
```
### Firestoreの設定
- エディション選択
- データベースID、ロケーション
- 構成(セキュリティモード)
    - 本番モード
    - テストモード

![alt text](/res/2026-03-24_create_firebase_05.png)
- データベースの作成を完了すると管理画面に
![alt text](/res/2026-03-24_create_firebase_06.png)

### Firestoreの構造
- 構造の作成については明日実施。