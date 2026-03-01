# ZMK 薙刀式 v17 for ふるご

ZMK Firmwareで薙刀式かな入力を実現します。
薙刀式は大岡俊彦氏が考案されたかな入力方式です。

http://oookaworks.seesaa.net/article/456099128.html

このリポジトリは https://github.com/eswai/zmk-naginata をベースに個人用に作っているものです。
フォーク元との親子関係は切っています（意図しないmerge requestを防ぐため）。

## キーマップ

### 基本の仕組み

薙刀式はスペースキーを「親指シフト」として使います。

- 単独押し → 清音
- Space 同時押し（親指シフト） → 別の文字
- 2キー同時押し → 濁音・半濁音・拗音

かなモードのオン/オフは `H+J` の同時押しで行います。

### 清音（単独押し）

QWERTY配列の各キーに以下の文字が割り当てられています。

```
+------+------+------+------+------+    +------+------+------+------+------+
|  Q   |  き  |  て  |  し  |  ←  |    |  →  |  さ  |  る  |  す  |  へ  |
+------+------+------+------+------+    +------+------+------+------+------+
|  ろ  |  け  |  と  |  か  |  っ  |    |  く  |  あ  |  い  |  う  |  ー  |
+------+------+------+------+------+    +------+------+------+------+------+
|  ほ  |  ひ  |  は  |  こ  |  そ  |    |  た  |  な  |  ん  |  ら  |  れ  |
+------+------+------+------+------+    +------+------+------+------+------+
```

- `Q` 単独は何も出力しません（ストローク整理用）
- `T` = カーソル左移動（←）、`Y` = カーソル右移動（→）

### 親指シフト（Space + 〇）

```
+------+------+------+------+------+    +------+------+------+------+------+
|  -   |  め  |  り  |  ね  | S←  |    |  S→  | じゅ |  よ  |  え  |  ゆ  |
+------+------+------+------+------+    +------+------+------+------+------+
|  せ  |  み  |  に  |  ま  |  ち  |    |  や  |  の  |  も  |  つ  |  ふ  |
+------+------+------+------+------+    +------+------+------+------+------+
|  ほ  |  ひ  |  を  |  、↵ |  ぬ  |    |  お  |  。↵ |  む  |  わ  |  れ  |
+------+------+------+------+------+    +------+------+------+------+------+
```

- `Space+T` = Shift+←（選択しながら左移動）、`Space+Y` = Shift+→（選択しながら右移動）
- `、` と `。` は句読点の後に自動でEnterが送出されます
- `ほ` / `ひ` / `れ` は単独・シフト両方で同じ文字（どちらでも入力可）
- `じゅ` は後述の「フォーク元との差分」参照

### 濁音・半濁音

`J` を左手に加えた同時押しで濁音になります（ほぼすべての行）。

```
J+F=が  J+W=ぎ  H+F=ぐ  J+S=げ  J+V=ご
F+U=ざ  J+R=じ  F+O=ず  J+A=ぜ  J+B=ぞ
F+N=だ  J+G=ぢ  F+L=づ  J+E=で  J+D=ど
J+C=ば  J+X=び  F+;=ぶ  F+P=べ  J+Z=ぼ
```

半濁音は `M` または `V` を加えた同時押しです。

```
M+C=ぱ  M+X=ぴ  V+;=ぷ  V+P=ぺ  M+Z=ぽ
```

### 拗音

清音の同時押しに `H`（ゃ）/ `P`（ゅ）/ `I`（ょ）を加えると拗音になります。

```
R+H=しゃ  R+P=しゅ  R+I=しょ
W+H=きゃ  W+P=きゅ  W+I=きょ
G+H=ちゃ  G+P=ちゅ  G+I=ちょ
D+H=にゃ  D+P=にゅ  D+I=にょ
X+H=ひゃ  X+P=ひゅ  X+I=ひょ
S+H=みゃ  S+P=みゅ  S+I=みょ
E+H=りゃ  E+P=りゅ  E+I=りょ
```

濁音拗音は濁音キー（J）も加えます（例: J+W+H=ぎゃ）。

### 編集モード

連続シフト（2キーを同時押ししてシフトキーとして保持）による編集モードがあります。

| 連続シフト | 機能 |
|-----------|------|
| `J+K + 〇` | 記号入力（括弧・句読点など） |
| `D+F + 〇` | カーソル移動・テキスト操作 |
| `M+, + 〇` | 縦書き向け記号・整形 |
| `C+V + 〇` | クリップボード操作・選択 |

## 標準・フォーク元との差分

薙刀式v17標準（[公式定義ファイル](https://oookaworks.up.seesaa.net/image/E89699E58880E5BC8Fv17.txt)）および
[eswai/zmk-naginata](https://github.com/eswai/zmk-naginata) から以下を変更しています。
なお、フォーク元は標準に準拠しているため、両者の差分はほぼ同一です。

| キー | 薙刀式v17標準 / フォーク元 | このプロジェクト | 変更コミット |
|------|--------------------------|-----------------|-------------|
| `U` 単独 | BackSpace | **さ** | `e5549fe` |
| `Space+U` | さ | **じゅ** | `a33caec` |
| `Space+U+W` | （なし） | **ぎゅ** | `a33caec` |
| `Space+U+X` | （なし） | **びゅ** | `a33caec` |
| `Space+U+G` | （なし） | **ぢゅ** | `a33caec` |
| `Space+U+E` | （なし） | **でゅ** | `a33caec` |

**変更の意図:**

1. **U単独 → さ**: 標準ではUにBackSpaceが割り当てられているが、この実装ではBackSpaceをdefault_layerの別キーに移し、Uを「さ」に割り当てた（さの頻度が高いため）。

2. **Space+U → じゅ**: 「さ」の裏（親指シフト+U）を「じゅ」に変更。標準では「じゅ」は `J+R+P` の3キー同時押しだが、2キーで入力できる。

3. **Space+U+〇 → 濁音+ゅ**: Space+UをShiftとして使い、ぎゅ/びゅ/ぢゅ/でゅを3キーで入力できるようにした。標準では4キー同時押しが必要。

## Github Actionsでbuildする場合

[zmk-config](https://zmk.dev/docs/customization)のwest.ymlに2か所を追加
```
manifest:
  remotes:
    - name: zmkfirmware
      url-base: https://github.com/zmkfirmware
    - name: furugo-dev  # ここを追加
      url-base: https://github.com/furugo-dev
  projects:
    - name: zmk
      remote: zmkfirmware
      revision: main
      import: app/west.yml
    - name: zmk-naginata-v17       # ここを追加
      remote: furugo-dev
      revision: main
  self:
    path: config
```

config/boards/your_keyboard/your_keyboard.keymapに薙刀式のコンボとレイヤーを追加
```
#include <behaviors/naginata.dtsi>

/ {

    macros {
        ng_on: ng_on {
            compatible = "zmk,behavior-macro";
            #binding-cells = <0>;
            bindings
                = <&macro_tap &kp LANGUAGE_1 &kp INTERNATIONAL_4 &to 1>
                ;
        };
        ng_off: ng_off {
            compatible = "zmk,behavior-macro";
            #binding-cells = <0>;
            bindings
                = <&macro_tap &kp LANGUAGE_2 &kp INTERNATIONAL_5 &to 0>
                ;
        };
    };

    combos {
        compatible = "zmk,combos";
        combo_ng_on {
            timeout-ms = <300>;
            key-positions = <15 16>; // H, Jのキー位置に書き換えてください
            bindings = <&ng_on>;
            layers = <0>; // デフォルトレイヤーが0の前提
        };
        combo_ng_off {
            timeout-ms = <300>;
            key-positions = <13 14>; // F, Gのキー位置に書き換えてください
            bindings = <&ng_off>;
            layers = <0 1>; // デフォルトレイヤーが0、薙刀式レイヤーが1の前提
        };
    };


    keymap {
        compatible = "zmk,keymap";

        default_layer {
            bindings = <
            // +----------+----------+----------+----------+----------+----------+----------+----------+----------+----------
                &kp K      &kp D      &kp N      &kp F      &kp Q      &kp J      &kp BSPC   &kp R      &kp U      &kp P
                &kp W      &kp I      &kp S      &kp A      &kp G      &kp Y      &kp E      &kp T      &kp H      &kp B
                &kp Z      &kp X      &kp V      &kp C      &kp L      &kp M      &kp O      &kp COMMA  &kp DOT    &kp SLASH
                                      &kp LCMD   &mo LOWER  &mt LSHFT SPACE  &mt LSHFT ENTER  &mo RAISE  &kp RCTRL
                >;
        };

        // 薙刀式に必要な31キー(Enterもシフトキーにしたいときは32キー)を定義してください。
        // &kpの代わりに&ngを使います。通常はデフォルトレイヤーの次に配置してください(レイヤー番号1)。
        naginata_layer { 
            bindings = <
            // +----------+----------+----------+----------+----------+----------+----------+----------+----------+----------
                &ng Q      &ng W      &ng E      &ng R      &ng T      &ng Y      &ng U      &ng I      &ng O      &ng P
                &ng A      &ng S      &ng D      &ng F      &ng G      &ng H      &ng J      &ng K      &ng L      &ng SEMI
                &ng Z      &ng X      &ng C      &ng V      &ng B      &ng N      &ng M      &ng COMMA  &ng DOT    &ng SLASH
                                      &kp LSHIFT &mo LOWER  &ng SPACE  &ng ENTER  &mo RAISE  &kp RCTRL
                >;
        };
 
// 以下省略
```

## ローカルでbuildする場合の例

```
git clone https://github.com/furugo-dev/zmk-naginata-v17.git
cd zmk/app

rm -rf build
west build -b seeeduino_xiao_ble -- -DSHIELD=your_keyboard_left -DZMK_CONFIG="/Users/foo/zmk-config/config" -DZMK_EXTRA_MODULES="/Users/foo/zmk-naginata-v17"
cp build/zephyr/zmk.uf2 ~/zmk_left.uf2

# 分割なら右手も
rm -rf build
west build -b seeeduino_xiao_ble -- -DSHIELD=your_keyboard_right -DZMK_CONFIG="/Users/foo/zmk-config/config" -DZMK_EXTRA_MODULES="/Users/foo/zmk-naginata"
cp build/zephyr/zmk.uf2 ~/zmk_right.uf2
```

## 編集モードへの対応

Macでは下の対応をすることによって編集モードで記号入力を行うことができます。

* Macの「Unicode 16進数入力」を入力ソースに追加する
* Karabiner-Elementsをインストール
* unicode_hex_input_switcher.jsonをKarabiner-Elementsで有効にする