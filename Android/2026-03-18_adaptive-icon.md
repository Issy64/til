# [アプリアイコン]

## 🗓 日付
2026-03-18

## 参考URL
Jetpack Compose を用いた Android アプリ開発の基礎
- [アプリアイコンを変更する](https://developer.android.com/codelabs/basic-android-kotlin-compose-training-change-app-icon?hl=ja)

## 💡 学んだこと（要約）
### 1. アイコンたち
#### ランチャーアイコン
- 画面密度 => 1インチあたりのピクセル数(dpi)
    - 中密度画面(mdpi) = 160 dpi
    - 高密度画面(hdpi) = 240 dpi
    - 超高密度画面(xhdpi) = 320 dpi
    - 超超高密度画面(xxhdpi) = 480 dpi
    - 超超超高密度画面(xxxhdpi) = 640 dpi
    - nodpi -> 画面のピクセル密度に関わらず、スケーリングされることを想定していないリソース
    - anydpi -> 任意の密度にスケーリングされるリソース
- ランチャーアイコンのディレクトリは「app/src/main/res」のmipmapフォルダ
- Androidは最も近い大きめな密度バケットのリソースを選択する。 <br>
    例）360 dpiの場合、480 dpiのリソースが選択される。

#### アダプティブアイコン
- フォアグラウンドレイヤとバックグラウンドレイヤ <br>
    Android 8.0(API 26)からアダプティブアイコンがサポート
- ベクター型Drawableとビットマップ画像 <br>
    ベクターはアイコン向き <br>
    ビットマップは写真向き <br>
- ImageAssetからアダプティブアイコンが作れる
- Android 8.0 より前のデバイスにはレガシーランチャーアイコンが使用される。

#### 関連リンクたち
- [Android アイコンのデザイン ガイドライン](https://m3.material.io/styles/icons/designing-icons)
- [アダプティブ アイコン](https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive?hl=ja)
- [Android のアダプティブ アイコンについて](https://medium.com/google-design/understanding-android-adaptive-icons-cee8a9de93e2)
- [アダプティブ アイコンのデザイン](https://medium.com/google-design/designing-adaptive-icons-515af294c783)
- [アダプティブ アイコンの実装](https://medium.com/androiddevelopers/implementing-adaptive-icons-1e4d1795470e)
- [Adaptive Icon Playground アプリ](https://github.com/nickbutcher/AdaptiveIconPlayground)
- [アダプティブ ランチャー アイコンとレガシー ランチャー アイコンを作成する](https://developer.android.com/studio/write/image-asset-studio?hl=ja#create-adaptive)
- [さまざまなピクセル密度のサポート](https://developer.android.com/training/multiscreen/screendensities?hl=ja#TaskProvideAltBmp)
- [アプリアイコンを mipmap ディレクトリに配置する](https://developer.android.com/training/multiscreen/screendensities?hl=ja#mipmap)
- [ベクター型ドローアブルの概要](https://developer.android.com/guide/topics/graphics/vector-drawable-resources?hl=ja)
- [VectorDrawable クラス](https://developer.android.com/reference/kotlin/android/graphics/drawable/VectorDrawable?hl=ja)

## 所管
- Android 8.0からアダプティブアイコンをサポートしているということは <br>
現在から考えるとほぼすべてのアプリでアダプティブアイコンが使用できると考えても良いかもしれない <br>
レガシーランチャーアイコンは、どうしても扱うときにまた確認し直せば良いと思った。

## 💻 コード例 / 使い方
```kotlin
item {
    Spacer(modifier = Modifier.height(8.dp))
    OutlinedButton(
        onClick = viewModel::updateActivity,
        modifier = Modifier.fillMaxWidth(),
        enabled = !isSaving,
    ) { Text("保存") }
}