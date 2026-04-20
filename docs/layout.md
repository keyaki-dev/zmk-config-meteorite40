# meteorite40_low レイアウトリファレンス

`config/meteorite40_low.keymap` を編集する際に参照する、物理キー位置と position 番号、現在のキーマップの対応表。

## 物理レイアウト（position 番号）

40キー構成。row 3 が親指クラスター。position はキーマップ配列の並び順と一致する。

```
Row 0:  [ 0][ 1][ 2][ 3][ 4]                     [ 5][ 6][ 7][ 8][ 9]
Row 1:  [10][11][12][13][14]                     [15][16][17][18][19]
Row 2:  [20][21][22][23][24][25][26][27][28][29][30][31]
Row 3:       [32][33][34   ][35  ][36    ][37][38][39]
                                         ^MB1=25 MB2=26 は中央マウスボタン
```

| Row | Positions | 備考 |
|---|---|---|
| 0 | 0–9 | 上段10キー。中央 col 5–6 は物理空き |
| 1 | 10–19 | 中段10キー。中央 col 5–6 は物理空き |
| 2 | 20–31 | 下段12キー。position 25/26 は中央マウスボタン (MB1/MB2) |
| 3 | 32–39 | 親指クラスター8キー。幅は 1u / 1.5u / 1.25u / 1.75u など不均一 |

## 現在のキーマップ（デフォルト）

### Layer 0: BASE

```
[Q    ][W    ][E    ][R    ][T    ]                              [Y    ][U    ][I    ][O    ][P    ]
[Sft/A][S    ][D    ][F    ][G    ]                              [H    ][J    ][K    ][L    ][-    ]
[Z    ][X    ][C    ][V    ][B    ][MB1  ][MB2  ][N    ][M    ][,    ][.    ][L4//]
            [LWIN ][Ctl/Tab][Sft/Spc][L1/Spc][L2/Ent][L2/Bsp][L3/Del][RAlt]
```

- `Sft/A` = position 10: `&mt LSHIFT A` — ホールドで Shift、タップで A
- `Ctl/Tab` = position 33: `&mt LCTRL TAB`
- `Sft/Spc` = position 34: `&mt LSHIFT SPACE`
- `L1/Spc` = position 35: `&lt 1 SPACE` — ホールドで Layer 1、タップで SPACE
- `L2/Ent` = position 36: `&lt 2 ENTER`
- `L2/Bsp` = position 37: `&lt 2 BSPC`
- `L3/Del` = position 38: `&lt 3 DEL`
- `L4//` = position 31: `&lt 4 SLASH`

### Layer 1: SYMBOLS（右親指 `L1/Spc` ホールドで発動）

```
[!    ][@    ][#    ][$    ][%    ]                              [    ][(    ][)    ][[    ][]    ]
[^    ][&    ][*    ][\    ][|    ]                              [;    ][:    ]['    ]["    ][=    ]
[    ][    ][    ][    ][    ][MB4  ][MB5  ][`    ][~    ][{    ][}    ][+    ]
            [---  ][---    ][---    ][---    ][---   ][Del    ][---  ][---  ]
```

### Layer 2: NUM / ARROW（右親指 `L2/Ent` または `L2/Bsp` ホールドで発動）

```
[*    ][7    ][8    ][9    ][+    ]                              [    ][    ][    ][Vol- ][Vol+ ]
[/    ][4    ][5    ][6    ][-    ]                              [    ][←   ][↑   ][↓   ][→   ]
[0    ][1    ][2    ][3    ][.    ][MB4  ][MB5  ][    ][    ][    ][    ][    ]
            [---  ][---    ][---    ][---    ][---   ][---    ][---  ][---  ]
```

### Layer 3: FUNCTION（右親指 `L3/Del` ホールドで発動）

```
[F1   ][F2   ][F3   ][F4   ][F5   ]                              [F6   ][F7   ][F8   ][F9   ][F10  ]
[    ][    ][    ][    ][    ]                                    [    ][    ][    ][    ][    ]
[    ][    ][    ][    ][    ][    ][    ][    ][    ][    ][    ][    ]
            [---  ][---    ][---    ][---    ][---   ][---    ][---  ][---  ]
```

### Layer 4: SETTING（右下 `L4//` ホールドで発動）

```
[CPI- ][CPI+ ][Rot- ][Rot+ ][ScTog]                              [BT0  ][BT1  ][BT2  ][BT3  ][BT4  ]
[Sd-  ][Sd+  ][ScH  ][ScV  ][SrSc ]                              [BTCl ][BTClA][    ][    ][    ]
[Save ][Rset ][    ][    ][Scrl2]  [    ][    ][Boot ][    ][    ][    ][    ]
            [---  ][---    ][---    ][---    ][---   ][---    ][---  ][---  ]
```

## コンボ（デフォルト）

| コンボ | position | 発動レイヤー | 動作 |
|---|---|---|---|
| lang1 | 12 + 13 (D + F) | 0 | `LANG1`（かな） |
| lang2 | 16 + 17 (J + K) | 0 | `LANG2`（英数） |
| esc | 20 + 21 (Z + X) | 0 | `ESC` |
| tab | 0 + 1 (Q + W) | 0 | `TAB` |
| line_home | 16 + 17 (J + K) | 2 | Win: HOME / Mac: Cmd+← |
| line_end | 18 + 19 (L + -) | 2 | Win: END / Mac: Cmd+→ |
| doc_home | 16 + 18 (J + L) | 2 | Win: Ctrl+HOME / Mac: Cmd+↑ |
| doc_end | 17 + 19 (K + -) | 2 | Win: Ctrl+END / Mac: Cmd+↓ |

## 編集時の注意

- `position-bindings` / `key-positions` の数字は **position 番号**（0–39）
- キーマップ配列の **並び順と position 番号は一致**。1行目先頭が 0、次が 1…と続き、親指の末尾が 39
- Layer 0 の配置を変えたら、コンボの `key-positions` も整合を取る必要がある
- `&trans` はそのレイヤーでの「透過」、下位レイヤーのキーを使う
- `&none` は「無効」、下位レイヤーにもフォールバックせず何も起きない
