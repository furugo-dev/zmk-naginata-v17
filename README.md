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
|  -   |  め  |  り  |  ね  | S←  |    |  S→  |  さ  |  よ  |  え  |  ゆ  |
+------+------+------+------+------+    +------+------+------+------+------+
|  せ  |  み  |  に  |  ま  |  ち  |    |  や  |  の  |  も  |  つ  |  ふ  |
+------+------+------+------+------+    +------+------+------+------+------+
|  ほ  |  ひ  |  を  |  、↵ |  ぬ  |    |  お  |  。↵ |  む  |  わ  |  れ  |
+------+------+------+------+------+    +------+------+------+------+------+
```

- `Space+T` = Shift+←（選択しながら左移動）、`Space+Y` = Shift+→（選択しながら右移動）
- `、` と `。` は句読点の後に自動でEnterが送出されます
- `ほ` / `ひ` / `れ` は単独・シフト両方で同じ文字（どちらでも入力可）

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
| `E+R + 〇` | 定型文入力 |
| `U+I + 〇` | 定型文入力 |

#### J+K 同時押し

```
+------+------+------+------+------+    +------+------+------+------+------+
|      |【】※ |「」  |（）  |  ・  |    |      |      |      |      |      |
+------+------+------+------+------+    +------+------+------+------+------+
|  ……  |  ・  |  ？  |  ！  | 保存 |    |      |      |      |      |      |
+------+------+------+------+------+    +------+------+------+------+------+
|  ――  |  』  | undo | redo |  ）  |    |      |      |      |      |      |
+------+------+------+------+------+    +------+------+------+------+------+
```

- `「」` / `（）` / `【】` は括弧ペアを入力してカーソルを括弧の間に移動
- `T`=`・`（確定なし）、`S`=`・`（確定あり・改行付き）
- `？` / `！` / `……` / `――` / `』` / `）` は記号を入力して確定（改行あり）
- `undo` / `redo` はそれぞれ元に戻す / やり直し
- ※ `【】` は Windows モードのみ有効

#### D+F 同時押し

```
+------+------+------+------+------+    +------+------+------+------+------+
|      |      |      |      |      |    | Home |コピー|ペースト|カット|カタカナ|
+------+------+------+------+------+    +------+------+------+------+------+
|      |      |      |      |      |    | S←  |確定末| 再変換| S→  |  Del |
+------+------+------+------+------+    +------+------+------+------+------+
|      |      |      |      |      |    |S←×7 | 行頭 |  行末 |S→×7 |ひらがな|
+------+------+------+------+------+    +------+------+------+------+------+
```

- `確定末`: 変換を確定して行末へ移動（Enter → End）
- `S←` / `S→`: Shift+← / Shift+→（1文字選択移動）
- `S←×7` / `S→×7`: Shift+← / Shift+→ を7回（7文字選択移動）
- `D+F+行頭+行末`（M と , の同時押し）: 行全体を選択

#### E+R 同時押し / U+I 同時押し（定型文）

よく使う定型文を1操作で入力します。

| キー | 出力 |
|------|------|
| `E+R` → `J` | ありがとうございます。 |
| `E+R` → `N` | お疲れ様です。 |
| `U+I` → `R` | 承知しました。 |
| `U+I` → `F` | 確認いたします。 |

## 標準・フォーク元との差分

薙刀式v17標準（[公式定義ファイル](https://oookaworks.up.seesaa.net/image/E89699E58880E5BC8Fv17.txt)）および
[eswai/zmk-naginata](https://github.com/eswai/zmk-naginata) から以下を変更しています。
なお、フォーク元は標準に準拠しているため、両者の差分はほぼ同一です。

| キー | 薙刀式v17標準 / フォーク元 | このプロジェクト | 変更コミット |
|------|--------------------------|-----------------|-------------|
| `U` 単独 | BackSpace | **さ** | `e5549fe` |

**変更の意図:**

1. **U単独 → さ**: 標準ではUにBackSpaceが割り当てられているが、この実装ではBackSpaceをdefault_layerの別キーに移し、Uを「さ」に割り当てた（さの頻度が高いため）。

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

## OS設定

キーボードから送信するキーコードでOSを切り替えます。
キーマップの任意のレイヤーに以下のキーを配置してください。

| キー | OS / 機能 |
|------|----------|
| `F15` | Windows モード |
| `F16` | macOS モード（デフォルト） |
| `F17` | Linux モード |
| `F18` | 縦書きモード ※ |
| `F19` | 横書きモード ※ |

> `&ng` ビヘイビアが `ZMK_BEHAVIOR_OPAQUE` でキーを消費するため OS には届かず、OS機能との競合は発生しません。

※ 縦書き/横書きモード（F23/F24）は `M+,` 連続シフト内の行移動方向にのみ影響します。Windows/Linux 限定の設定で、macOS では無効です。通常は変更不要です。

## 編集モードへの対応（macOS）

macOSでは下の対応をすることによって編集モードで記号入力（Unicode入力）を行うことができます。

* Macの「Unicode 16進数入力」を入力ソースに追加する
* Karabiner-Elementsをインストール
* unicode_hex_input_switcher.jsonをKarabiner-Elementsで有効にする