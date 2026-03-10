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

## 清音（単独押し）

```
Q(-)  W(き) E(て) R(し) T(←)  Y(→)  U(さ) I(る) O(す) P(へ)
A(ろ) S(け) D(と) F(か) G(っ)  H(く)  J(あ) K(い) L(う) ;(ー)
Z(ほ) X(ひ) C(は) V(こ) B(そ)  N(た)  M(な) ,(ん) .(ら) /(れ)
```

## 親指シフト（Space+〇）

```
Q(-)  W(め) E(り) R(ね) T(S←) Y(S→) U(さ) I(よ) O(え) P(ゆ)
A(せ) S(み) D(に) F(ま) G(ち)  H(や)  J(の)  K(も) L(つ) ;(ふ)
Z(ほ) X(ひ) C(を) V(、↵) B(ぬ) N(お) M(。↵) ,(む) .(わ) /(れ)
```

# 標準（eswai/zmk-naginata）からの変更点

| キー | 標準 | この実装 |
|------|------|---------|
| U単独 | BackSpace | さ |

変更の意図：
- U=さ にすることで「さ」を1キーで入力（頻度が高い）

# 編集モードのキーマップ

## JK同時押し（記号入力）

```
W(【】Win限定) E(「」) R(（）)
              S(・確定) D(？確定) F(！確定) G(保存)
Z(――)        X(』)   C(undo)   V(redo)   B(）)
```

## DF同時押し（カーソル・編集操作）

```
               U(コピー) I(ペースト) O(カット) P(カタカナ)
H(←) J(確定エンド) K(再変換) L(→) ;(デリート)
N(シフト左) M(行頭) ,(行末) .(シフト右) /(ひらがな)
```

- DF+M+,（同時）→ 行全体を選択

## OS切り替えキー

| キー | 機能 |
|------|------|
| F15 | Windows モード |
| F16 | macOS モード（デフォルト） |
| F17 | Linux モード |
| F18 | 縦書きモード（MC連続シフト内の行移動方向を縦書き用に変更） |
| F19 | 横書きモード（MC連続シフト内の行移動方向を横書き用に戻す） |

※ `&ng` ビヘイビアが `ZMK_BEHAVIOR_OPAQUE` でキーを消費するため OS には届かず、OS機能との競合は発生しない。

縦書き/横書きモード（F23/F24）は `ng_next_row/prev_row/next_char/prev_char` にのみ影響する。
macOSでは tategaki フラグは参照されず常に Emacs 風キーバインド（Ctrl+N/P/F/B）を使用する。

# バージョン管理

jj（jujutsu）を使用。コミットには `jj describe`、プッシュには `jj git push` を使う。
ブランチ（bookmark）を作ってからプッシュする: `jj bookmark create <name> -r @ && jj git push --bookmark <name>`
