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
- `shift = NONE, douji = B_SPACE|B_U|B_X` → Space+U+Xの3キー同時押し

## 清音（単独押し）

```
Q(-)  W(き) E(て) R(し) T(←)  Y(→)  U(さ) I(る) O(す) P(へ)
A(ろ) S(け) D(と) F(か) G(っ)  H(く)  J(あ) K(い) L(う) ;(ー)
Z(ほ) X(ひ) C(は) V(こ) B(そ)  N(た)  M(な) ,(ん) .(ら) /(れ)
```

## 親指シフト（Space+〇）

```
Q(-)  W(め) E(り) R(ね) T(S←) Y(S→) U(じゅ) I(よ) O(え) P(ゆ)
A(せ) S(み) D(に) F(ま) G(ち)  H(や)  J(の)  K(も) L(つ) ;(ふ)
Z(ほ) X(ひ) C(を) V(、↵) B(ぬ) N(お) M(。↵) ,(む) .(わ) /(れ)
```

# 標準（eswai/zmk-naginata）からの変更点

| キー | 標準 | この実装 |
|------|------|---------|
| U単独 | BackSpace | さ |
| Space+U | さ | じゅ |
| Space+U+W | なし | ぎゅ |
| Space+U+X | なし | びゅ |
| Space+U+G | なし | ぢゅ |
| Space+U+E | なし | でゅ |

変更の意図：
- U=さ にすることで「さ」を1キーで入力（頻度が高い）
- Space+U=じゅ で「じゅ」を2キー入力（標準は J+R+P の3キー）
- Space+U+〇 で濁音+ゅを3キー入力（標準は4キー）

# バージョン管理

jj（jujutsu）を使用。コミットには `jj describe`、プッシュには `jj git push` を使う。
ブランチ（bookmark）を作ってからプッシュする: `jj bookmark create <name> -r @ && jj git push --bookmark <name>`
