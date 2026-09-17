# キーマップの変更・反映手順

## 概要

このキーボード（Cygnus）は **ZMK Firmware** を使用しています。
キーマップを変更するには、設定ファイルを編集して GitHub Actions でファームウェアをビルドし、キーボードに書き込みます。

---

## 手順

### 1. キーマップファイルを編集する

`config/Cygnus.keymap` を編集してキー割り当てを変更します。

```
config/Cygnus.keymap
```

- キーコードは [ZMK Keycodes](https://zmk.dev/docs/keymaps/key-press) を参照してください。
- レイヤー切り替えには `&mo`、`&lt`、`&to` などを使います。

---

### 2. 変更を GitHub にプッシュする

```powershell
git add config/Cygnus.keymap
git commit -m "Update keymap"
git push
```

プッシュすると GitHub Actions が自動的にビルドを開始します。

---

### 3. GitHub Actions のビルド完了を待つ

1. ブラウザで自分のリポジトリを開く
2. **Actions** タブをクリック
3. 最新のワークフロー実行を選択
4. すべてのジョブが ✅ になるまで待つ（数分かかります）

---

### 4. ファームウェアをダウンロードする

1. ビルド完了後、ワークフロー実行ページ下部の **Artifacts** セクションへスクロール
2. `firmware` をクリックしてZIPファイルをダウンロード
3. ZIPを解凍すると以下のファイルが含まれています：

| ファイル名 | 内容 |
|---|---|
| `seeeduino_xiao_ble-Cygnus_L-zmk.uf2` | 左手側ファームウェア |
| `seeeduino_xiao_ble-Cygnus_R-zmk.uf2` | 右手側ファームウェア |
| `seeeduino_xiao_ble-settings_reset-zmk.uf2` | ペアリングリセット用 |

---

### 5. キーボードに書き込む（フラッシュ）

#### 左手側・右手側それぞれに書き込む

1. キーボードをリセットモード（ブートローダーモード）に入れる
   - リセットボタンを **ダブルクリック**する
   - PC に `XIAO-SENSE` などのドライブとしてマウントされる
2. 対応する `.uf2` ファイルをドラッグ＆ドロップ（またはコピー）
3. 自動的にファイルが書き込まれ、ドライブがアンマウントされる

> **注意:** 左右どちらか一方だけ変更した場合でも、両方に書き込むことを推奨します。

---

### 6. 動作確認

書き込み完了後、キーボードを接続して意図したキーが入力されるか確認します。

---

## ペアリングをリセットする場合

Bluetooth のペアリングに問題がある場合は、`settings_reset-zmk.uf2` を書き込んでリセットします。

1. 左右両方に `settings_reset-zmk.uf2` を書き込む
2. その後、通常のファームウェア（`Cygnus_L` / `Cygnus_R`）を再度書き込む
3. PC・デバイスとペアリングし直す

---

## 参考リンク

- [ZMK Firmware 公式ドキュメント](https://zmk.dev/docs)
- [ZMK Keycodes 一覧](https://zmk.dev/docs/keymaps/key-press)
- [ZMK Behaviors](https://zmk.dev/docs/keymaps/behaviors/key-press)
