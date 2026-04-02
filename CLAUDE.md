# プロジェクト概要

ZMK Firmware向けの薙刀式v17かな入力モジュール。
大岡俊彦氏考案の薙刀式（http://oookaworks.seesaa.net/article/456099128.html）を
[eswai/zmk-naginata](https://github.com/eswai/zmk-naginata) をベースに個人用カスタマイズしたもの。
フォーク元との親子関係は切っている（意図しないPRを防ぐため）。

# 重要ファイル

- `src/behaviors/behavior_naginata.c` — キーマップ定義（`ngdickana[]`）と入力処理のメイン
- `src/naginata_func.c` — 編集モードの各機能実装（カーソル移動、クリップボード操作など）
- `include/zmk_naginata/naginata_func.h` — 関数宣言
- `include/zmk_naginata/nglist.h` / `nglistarray.h` — 同時押しキー列の管理データ構造

# キーマップ構造（ngdickana[]）

`shift`フィールドと`douji`フィールドのビット演算でキー組み合わせを判定する。

- `shift = NONE, douji = B_X` → Xの単独押し
- `shift = B_SPACE, douji = B_X` → Space+Xの同時押し（親指シフト）
- `shift = B_J, douji = B_X` → J+Xの同時押し（濁音など）

# 標準（eswai/zmk-naginata）からの変更点

| キー | 標準 | この実装 |
|------|------|---------|
| U単独 | BackSpace | さ |

変更の意図：
- U=さ にすることで「さ」を1キーで入力（頻度が高い）

# OS・モード実装メモ

キーマップ・OS切り替えキーの詳細はREADMEの「OS設定」を参照。

- 縦書き/横書きモード（F18/F19）は `ng_next_row/prev_row/next_char/prev_char` にのみ影響する。
- macOS では tategaki フラグは参照されず、常に Emacs 風キーバインド（Ctrl+N/P/F/B）を使用する。
- iOSモードではクリップボード操作にCmd系ショートカットを使用（macOSと共通）。カーソル移動はWindowsと同様の矢印キー（縦書き対応あり）。行頭/行末はCmd+Left/Right。
- Unicode文字入力・IME変換操作（カタカナ/ひらがな/再変換）はiOSモードでは非対応。

# バージョン管理

jj（jujutsu）を使用。コミットには `jj describe`、プッシュには `jj git push` を使う。
ブランチ（bookmark）を作ってからプッシュする: `jj bookmark create <name> -r @ && jj git push --bookmark <name>`
