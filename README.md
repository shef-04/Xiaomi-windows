# Xiaomi-windows
XiaomiのPoco F6 Pro をwindowsにつなげてスマホ画面と音声をPCへキャプチャ出力

```bash
cd C:\Android\scrcpy-win64-v3.3.3
```
いつも使うやつ
```bash
.\scrcpy -b 16M --max-fps 120 --audio-dup --max-size 1080 -d
```

# scrcpy チートシート（v3.3.x / 高画質ゲーム寄りに改訂）

## 0) 目的別・コピペ用（まずはこれ）

| やりたいこと                            | コマンド（コピペOK）                                                                                             |
| --------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **高画質・低遅延（USB / 基本形）**            | `scrcpy --max-fps=60 --video-bit-rate=16M --audio-source=output` ([GitHub][3])                          |
| **高画質優先（USB / 画質寄せ）**             | `scrcpy --video-codec=h265 --max-fps=60 --video-bit-rate=20M`  ※H.265は画質↑だが遅延はH.264より増えがち ([GitHub][3]) |
| **“端末でも音を鳴らしつつ”ミラー（Android 13+）** | `scrcpy --audio-dup`（=`--audio-source=playback` が暗黙）([GitHub][4])                                       |
| **軽量ワイヤレス（遅延と安定の妥協点）**            | `scrcpy --tcpip --max-fps=30 --video-bit-rate=8M --max-size=1024` ([GitHub][5])                         |
| **画面OFF気味で録画（PC負荷も減らす）**          | `scrcpy --turn-screen-off --no-control --no-playback --record=game.mkv` ([GitHub][6])                   |

> メモ：`--max-size` は「縦横どっちも指定値以下」に縮めるので、スマホが縦長（例：2400×1080）だと **1024指定で思ったより小さくなりがち**。画質が欲しい時は **そもそも付けない**か、値を上げるのが吉。([GitHub][3])

---

## 1) デバイス選択・接続（USB/無線）

* 複数端末があるとき：

  * `--serial SERIAL` / `-s SERIAL`
  * `--select-usb` / `-d`
  * `--select-tcpip` / `-e` ([GitHub][5])
* ワイヤレス（自動）

  * USBでつないだ状態で：`scrcpy --tcpip`（IP検出→TCP/IP有効化→接続までやる）([GitHub][5])
  * すでに端末が待受中なら：`scrcpy --tcpip=192.168.1.1`（既定 5555）([GitHub][5])

---

## 2) 画質・遅延の“効く順”チューニング

* 解像度：`--max-size=VALUE` / `-m VALUE` ([GitHub][3])
* ビットレート：`--video-bit-rate=16M` / `-b 16M`（旧 `--bit-rate` ではなく **video-bit-rate** 側に統一）([GitHub][3])
* FPS制限：`--max-fps=60`（端末が120Hzなら 120 も試す価値あり）([GitHub][3])
* コーデック：

  * `--video-codec=h264`（低遅延寄り）
  * `--video-codec=h265`（画質効率寄り）
  * `--video-codec=av1`（対応端末がまだ少なめ）([GitHub][3])
* さらに詰める：

  * `--list-encoders` → `--video-encoder=...` を固定（端末によって当たり外れある）([GitHub][3])
* バッファ（ゲーム用途は基本 **触らない**）

  * 既定は「ほぼゼロで低遅延」。Wi-Fiでカクつくときだけ `--video-buffer=50` とかを検討。([GitHub][3])

---

## 3) 音声（落とし穴だけ先に）

* 音声転送は **Android 11+**。Android 11 は開始時にアンロックが必要。([GitHub][4])
* 端末側でも音を鳴らしたいなら：

  * **Android 13+**：`--audio-dup` が簡単（ただしアプリが opt-out すると取れない）。([GitHub][4])
* バッファ：

  * `--audio-buffer=40/100`（音切れがあるときに調整）
  * `--audio-output-buffer` は“最後の手段”。([GitHub][4])

---

## 4) 入力（ゲーム向けの実用設定）

* 読み取り専用：`--no-control` / `-n` ([GitHub][7])
* 低遅延っぽい操作感に寄せる：`--keyboard=uhid -K` / `--mouse=uhid -M` ([GitHub][7])
* WASD が変になる系のとき：

  * `--prefer-text` は **ゲームでは逆効果**になりやすい（文字として入れるため）。([GitHub][8])
* キーリピートが重いゲーム：`--no-key-repeat` ([GitHub][8])

---

## 5) 配信/録画（OBSと相性良い）

* 録画：`--record=file.mkv`（拡張子でコンテナ決定 / `--record-format` も可）([GitHub][6])
* 録画だけして表示しない：`--no-playback --no-window --record=file.mp4` ([GitHub][6])
* 便利ショートカット：

  * ピクセル等倍：`MOD+g`
  * 黒帯除去：`MOD+w`
  * FPS表示：`MOD+i` ([GitHub][9])

---
