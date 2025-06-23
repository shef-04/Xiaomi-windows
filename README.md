# Xiaomi-windows
XiaomiのPoco F6 Pro をwindowsにつなげてスマホ画面と音声をPCへキャプチャ出力


| やりたいこと          | 代表コマンド                                                   |
| --------------- | -------------------------------------------------------- |
| **高画質配信 (USB)** | `scrcpy -b 16M --max-fps 60 --audio-dup`                 |
| **軽量ワイヤレス**     | `scrcpy --tcpip=5555 -b 8M --downscale 0.8 --max-fps 30` |
| **画面OFFで録画**    | `scrcpy -S --no-control --record=game.mkv`               |
