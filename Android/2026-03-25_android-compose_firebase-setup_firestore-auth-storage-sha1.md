# [Firebaseを設定する]

## 🗓 日付
2026-03-25

## 参考URL
[Firebase](https://firebase.google.com/?hl=ja)

## 💡 学んだこと（要約）
- Firebaseの構築を完成させる

## Firestoreのコレクションとドキュメント
- そもそもカラムの設定をする必要がない…？

1. importを追加
```kotlin
import com.google.firebase.firestore.firestore
import com.google.firebase.Firebase
```
2. 保存する記述を追加 <br>
※今回はポートフォリオ用に構築することを主題にしているのでAIに頼った
```kotlin
@Composable
fun FirestoreTestScreen(modifier: Modifier = Modifier) {
    val context = LocalContext.current
    // 入力状態を保持する変数
    var name by remember { mutableStateOf("") }
    var age by remember { mutableStateOf("") }

    // Firestoreの窓口を取得
    val db = Firebase.firestore

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        Text(
            text = "Firestore保存テスト",
            style = MaterialTheme.typography.headlineMedium,
            modifier = modifier,
        )

        TextField(
            value = name,
            onValueChange = { name = it },
            label = { Text("名前") },
            modifier = Modifier.fillMaxWidth()
        )

        TextField(
            value = age,
            onValueChange = { age = it },
            label = { Text("年齢") },
            modifier = Modifier.fillMaxWidth()
        )

        Button(
            onClick = {
                // 保存するデータを作成
                val user = UserData(name, age.toIntOrNull() ?: 0)

                // "users" という名前のコレクション（フォルダ）に保存
                db.collection("users")
                    .add(user)
                    .addOnSuccessListener {
                        Toast.makeText(context, "保存成功！", Toast.LENGTH_SHORT).show()
                        name = "" // 入力欄をリセット
                        age = ""
                    }
                    .addOnFailureListener { e ->
                        Toast.makeText(context, "エラー: ${e.message}", Toast.LENGTH_LONG).show()
                    }
            },
            modifier = Modifier.fillMaxWidth()
        ) {
            Text("Firebaseに保存する")
        }


        // 読み取ったユーザー一覧を保持するリスト
        var userList by remember { mutableStateOf(listOf<UserData>()) }

        // 画面が表示された時に一度だけ実行（またはデータ変更を監視）
        LaunchedEffect(Unit) {
            db.collection("users")
                .addSnapshotListener { snapshot, e ->
                    if (e != null) return@addSnapshotListener

                    // FirestoreのデータをUserDataクラスのリストに変換
                    val users = snapshot?.documents?.mapNotNull { it.toObject(UserData::class.java) } ?: listOf()
                    userList = users
                }
        }

        // リストの表示部分
        Text(text = "ユーザー一覧", style = MaterialTheme.typography.titleLarge, modifier = Modifier.padding(top = 16.dp))

        LazyColumn {
            items(userList) { user ->
                Card(
                    modifier = Modifier.fillMaxWidth().padding(vertical = 4.dp)
                ) {
                    Column(modifier = Modifier.padding(8.dp)) {
                        Text(text = "名前: ${user.name}")
                        Text(text = "年齢: ${user.age}")
                    }
                }
            }
        }
    }
}
```

## Fire Storageのセットアップ
- FirestoreのセットアップにはBlazeプランへのアップグレードが必要
- Blazeプランへアップグレード！
- Firestoreと同様にテスト環境で設定

## Authenticationのセットアップ
- AuthenticationのセットアップでGoogleサインインを設定。

## ハマったこと
- 載せ替え直後、パッケージ名とSHA1の競合でログイン処理が失敗した
- Firebaseコンソール側を既存構成と同じにしていたのが原因だった
- パッケージ名を `narabika` から `narabika_issy` に変えて再設定したら解消した

## ビルド・確認メモ
- クリーンビルドは `Clean project -> Assemble project`
- Storage のロケーションが US Central なので、画像アップロードが遅い可能性がある
- プロフィール画像アップロード処理はまだ確認が必要
- ルール変更が Firebase に反映されているか未確認


## AndroidのSingle Activity

### Activity とは何か
- Activity は「画面そのもの」みたいなもの
- LINEでいうと、トーク一覧、チャット画面、プロフィール画面がそれぞれ別Activityのイメージ
- ボタンやUIを載せる土台として理解した

### 最近は 1アプリ = 1 Activity が主流らしい
- Activity は重く、メモリ面で不利になりやすい
- 画面間のデータ受け渡しがしやすい
- Activity遷移よりも、UI切り替えのほうが滑らかなアニメーションを作りやすい
- Navigation Compose で画面切り替えがしやすくなるらしい


