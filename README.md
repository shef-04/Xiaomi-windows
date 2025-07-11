# Xiaomi-windows
XiaomiのPoco F6 Pro をwindowsにつなげてスマホ画面と音声をPCへキャプチャ出力

```bash
cd C:\Android\scrcpy-win64-v3.3.1
```
いつも使うやつ
```bash
scrcpy -b 16M --max-fps 60 --audio-dup --max-size 1080　-d
```

| やりたいこと          | 代表コマンド                                                   |
| --------------- | -------------------------------------------------------- |
| **高画質配信 (USB)** | `scrcpy -b 16M --max-fps 60 --audio-dup --max-size 1080`                 |
| **軽量ワイヤレス**     | scrcpy --tcpip=5555 -b 8M --downscale 0.8 --max-fps 30` |
| **画面OFFで録画**    | `scrcpy -S --no-control --record=game.mkv`               |


## 1. デバイス選択・接続
| カテゴリ           | オプション                             | 説明                                                                    |
| -------------- | --------------------------------- | --------------------------------------------------------------------- |
| **USB/TCP 接続** | `--tcpip[=PORT]` `-j`        | まず USB で握手し、指定ポート（既定 5555）に Wi-Fi 切替 ([raw.githubusercontent.com][1]) |
|                | `--select-usb` / `--select-tcpip` | 同名デバイスが複数ある場合、接続方式を限定                                                 |
| **複数端末**       | `--serial SERIAL` `-s`       | ADB のシリアル番号を直指定                                                       |
|                | `--list-devices`                  | つながっている端末一覧だけを表示                                                      |


## 2. 画面（映像）設定
| カテゴリ       | オプション                                 | 説明                                              |       |                             |
| ---------- | ------------------------------------- | ----------------------------------------------- | ----- | --------------------------- |
| **解像度**    | `--max-size VALUE` `-m`          | 長辺を上限 VALUE px に縮小して転送                          |       |                             |
|            | `--downscale RATIO`                   | 端末 native 解像度に対する倍率（0.1–1.0）                    |       |                             |
| **フレーム**   | `--max-fps FPS`                       | エンコード上限 fps                                     |       |                             |
| **ビットレート** | ★`--video-bit-rate 16M` `-b 16M` | エンコーダの出力上限。旧 `--bit-rate` は廃止 ([github.com][1]) |       |                             |
| **コーデック**  | \`--encoder=h264                      | h265                                            | av1\` | デバイスが HW 対応なら h265/av1 が高効率 |
| **その他**    | `--crop X:Y:W:H`                      | 画面を矩形トリミング                                      |       |                             |
|            | `--lock-video-orientation value`      | 初期の縦横を 0=自由,1=縦,2=横,3=横(逆) に固定                  |       |                             |
|            | `--rotation 0/1/2/3`                  | PC 側で 0°/90°/180°/270° 回転                       |       |                             |



## 3. 音声
| カテゴリ       | オプション                        | 説明                                 |       |                                                                 |
| ---------- | ---------------------------- | ---------------------------------- | ----- | --------------------------------------------------------------- |
| **転送元**    | ★\`--audio-source=playback   | output                             | mic\` | playback=アプリ音だけ／output=端末スピーカー同時OFF／mic=端末マイク ([github.com][1]) |
| **ビットレート** | `--audio-bit-rate 256k`      | AAC ビットレート                         |       |                                                                 |
| **二重再生**   | `--audio-dup`                | 転送しつつ端末側でも鳴らす                      |       |                                                                 |
| **出力先**    | `--audio-out-device="デバイス名"` | Windows なら「スピーカー (Realtek Audio)」等 |       |                                                                 |
| **バッファ**   | `--audio-buffer MS`          | 再生前バッファ長 (ms)                      |       |                                                                 |


## 4. 入力制御（タップ/キー/マウス）
| カテゴリ        | オプション                         | 説明                                                           |                      |                     |
| ----------- | ----------------------------- | ------------------------------------------------------------ | -------------------- | ------------------- |
| **有効/無効**   | `--no-control` `-n`      | 画面表示だけ（INJECT\_EVENTS 権限不要） ([raw.githubusercontent.com][1]) |                      |                     |
|             | `--no-video` / `--no-audio`   | “操作だけ”や“音声だけ”の特殊用途                                           |                      |                     |
| **画面/電源**   | `--turn-screen-off` `-S` | 起動時に端末画面を消灯（ミラーは継続） ([raw.githubusercontent.com][2])         |                      |                     |
|             | `--stay-awake``-w`      | 接続中はスリープさせない                                                 |                      |                     |
| **HID モード** | \`--keyboard=uhid             | aoa`/`--mouse=uhid                                           | aoa`<br>│ `-K` `-M\` | 低遅延で物理 HID として認識させる |
| **その他**     | `--legacy-paste`              | Android < 7 のペースト互換                                          |                      |                     |
|             | `--forward-all-clicks`        | 端末側にすべてのクリックを送る                                              |                      |                     |

## 5. ウィンドウ／UI
| オプション                                                                         | 説明              |
| ----------------------------------------------------------------------------- | --------------- |
| `--fullscreen` / `--window-x` `--window-y` `--window-width` `--window-height` | ウィンドウ配置とフルスクリーン |
| `--always-on-top`                                                             | 最前面固定           |
| `--no-window`                                                                 | GUI なし（録画用途など）  |
| `--window-title "文字列"`                                                        | タイトルを任意文字列に     |


## 6. 録画・配信補助
| オプション                   | 説明                       |                       |
| ----------------------- | ------------------------ | --------------------- |
| `--record=myclip.mkv`   | PC 側で直接録画（拡張子がフォーマットを決定） |                       |
| `--record-format=mp4'   | mkv                    | 明示的にフォーマット指定          |
| `--fps-counter overlay' | stdout                 | 現フレームレート表示／標準出力に数値を吐く |
| `--video-buffer 60`     | 映像バッファ (ms)              |                       |
| `--disable-screensaver` | スクリーンセーバー抑止              |                       |


## 7. ネットワーク／ポート
| オプション                 | 説明                             |
| --------------------- | ------------------------------ |
| `--port 27183`        | PC 側リッスンポート                    |
| `--tunnel-port 27183` | ADB フォワード先ポート                  |
| `--force-adb-forward` | 共有 PC で adb reverse が使えない場合の代替 |

## 8. カメラ・WebCam (V4L2)
| オプション                      | 説明                     |
| -------------------------- | ---------------------- |
| `--list-cameras`           | 端末カメラ ID 一覧            |
| `--camera ID`              | 指定カメラを仮想ウェブカムとして転送     |
| `--v4l2-sink=/dev/video42` | Linux で直接 V4L2 デバイスへ出力 |

## 9. デバッグ＆補助
| オプション                            | 説明                            |      |         |       |
| -------------------------------- | ----------------------------- | ---- | ------- | ----- |
| \`--log-level debug`              | info                          | warn | error | ログ冗長度 |
| `--cleanup`                      | 終了時に端末へ push したサーバーファイルを削除    |      |         |       |
| `--push-target /sdcard/Download` | scrcpy-server.jar の push 先を変更 |      |         |       |
| `--help` / `--version`           | オプション一覧とバージョン表示               |      |         |       |
