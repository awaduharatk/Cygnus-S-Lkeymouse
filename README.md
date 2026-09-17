無線キーボード　Cygnusのファームウェアです。
使い方などは別途添付のマニュアルをご参照ください。

キーマップの変更・書き込み手順は [KEYMAP_HOW_TO.md](KEYMAP_HOW_TO.md) を参照。

## IME ON / OFF (Windows + US配列)

キーボード側のコンボ（`config/Cygnus.keymap` の `combos`、ベースレイヤーのみ有効）：

| コンボ       | 物理キー | 送信キー   | 動作         |
| ------------ | -------- | ---------- | ------------ |
| `muhenkan`   | S+D      | F13        | IME OFF      |
| `henkan`     | D+F      | F14        | IME ON       |
| `ime_toggle` | F+G      | Ctrl+Space | IME トグル   |

US配列では `INT_HENKAN` / `INT_MUHENKAN` のキーコードが IME に届かないため、次の2段構成にしている。

```
キーボード (F13/F14) → PowerToys (無変換/変換 に再マップ) → Microsoft IME (無変換=IME-オフ / 変換=IME-オン)
```

**PC を新しくしたら、1 と 2 の両方を設定する。** どちらかが欠けると IME ON / OFF にならない。

### 1. PowerToys Keyboard Manager

1. PowerToys をインストール: `winget install Microsoft.PowerToys`
2. PowerToys を起動 → **Keyboard Manager** を有効化
3. **キーの再マップ** に以下を追加：

   | 物理キー | マップ先                  |
   | -------- | ------------------------- |
   | F13      | IME Non-Convert（無変換） |
   | F14      | IME Convert（変換）       |

4. 保存する。PowerToys は常駐させたままにする（常駐していないと再マップが効かない）

### 2. Microsoft IME のキー割り当て

1. `Win + I` → 時刻と言語 → 言語と地域
2. 「日本語」の「…」→ 言語のオプション → **Microsoft IME** → キーとタッチのカスタマイズ
3. 「**キーの割り当て**」を **オン**
4. 以下を割り当てる：

   | キー       | 機能           |
   | ---------- | -------------- |
   | 無変換キー | **IME-オフ**   |
   | 変換キー   | **IME-オン**   |
   | Ctrl+Space | IME-オン/オフ  |

> 「キーの割り当て」の項目が出ない・効かない場合は、同じ画面の「以前のバージョンの Microsoft IME を使う」をオンにしてから設定し直す。

### 3. 反映

**サインアウト → サインイン**（または再起動）する。設定を保存しただけでは起動済みのアプリに反映されないことがある。

### 設定の確認方法

PowerShell で確認できる（読み取りのみ）。

```powershell
# PowerToys: 124(F13)→29(無変換)、125(F14)→28(変換) があれば OK
Get-Content "$env:LOCALAPPDATA\Microsoft\PowerToys\Keyboard Manager\default.json"

# IME: KeyAssignmentMuhenkan = 1(IME-オフ)、KeyAssignmentHenkan = 0(IME-オン) なら OK
Get-ItemProperty 'HKCU:\Software\Microsoft\IME\15.0\IMEJP\MSIME' | Select-Object IsKeyAssignmentEnabled, KeyAssignment*
```

`KeyAssignmentMuhenkan` / `KeyAssignmentHenkan` が無い場合は、手順 2 の割り当てが保存されていない。

### トラブルシューティング

| 症状 | 原因 | 対処 |
| ---- | ---- | ---- |
| D+F / S+D でカタカナ入力になる | 手順 2 の無変換・変換キーの割り当てが未設定で、IME の既定動作になっている | 手順 2 を設定してサインアウト → サインイン |
| D+F / S+D で何も起きない | PowerToys が起動していない、または Keyboard Manager が無効 | PowerToys を起動し、手順 1 を確認 |
| F+G だけ効く | `Ctrl+Space` は PowerToys を経由しないため、1 か 2 のどちらかが未設定 | 上の確認方法でどちらが欠けているか調べる |
