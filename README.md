# zmk-config-meteorite40 (personal fork)

[iwk7273/zmk-config-meteorite40](https://github.com/iwk7273/zmk-config-meteorite40) の個人フォーク。meteorite40_low (ロープロファイル) を DTS ソース直接編集で運用する。

## 前提

- **ZMK Studio は使わない**。一度 Studio でキーマップを書き換えると、以降ソース由来の変更は反映されない。万一 Studio で触ってしまった場合は Studio UI の "Restore Stock Settings" を実行してから本リポジトリの運用に戻る。
- キーマップ編集対象: `config/meteorite40_low.keymap`

## 編集ループ

```
編集 (config/meteorite40_low.keymap)
 ↓
git commit && git push
 ↓
GitHub Actions が UF2 を自動ビルド (.github/workflows/build.yml)
 ↓
bin/fetch-uf2 で UF2 をローカルに取得
 ↓
キーボードをブートローダーモード (/ + N) にして UF2 をドラッグ&ドロップ
```

## UF2 の取得と書き込み

最もシンプルなのはワンコマンド版 `bin/flash`:

```sh
./bin/flash
```

これで **UF2取得 → ブートローダー検出待ち → 書き込み → 自動リセット** まで一気通貫です。実行後にキーボードで `/` + `N` 長押し（または Xiao BLE のリセットボタン2連打）でブートローダーモードに入ると、自動的に書き込みが走ります。

取得だけしたい場合:

```sh
# 最新成功ビルドの UF2 を ~/Downloads/mt40 に取得（デフォルトはロープロ版）
./bin/fetch-uf2

# ノーマル版を取りたい場合
./bin/fetch-uf2 normal

# 保存先を変える場合
./bin/fetch-uf2 low /path/to/dir
```

### 手動で書き込む場合の注意

Finder 経由でドラッグ&ドロップすると `エラーコード -36` が出ることがあります。macOS の拡張属性が FAT ファイルシステムに書けないのが原因なので、ターミナルから `-X` オプション付きでコピーしてください:

```sh
cp -X ~/Downloads/mt40/meteorite40_low-xiao_ble__zmk-zmk.uf2 /Volumes/XIAO-SENSE/
```

※ v1.0 時代のファームウェアは `/` + `Q` でブートローダー移行（v2.0 以降は `/` + `N`）。

## キーマップ編集時の参考

- [docs/layout.md](docs/layout.md) — 物理キーと position 番号の対応、現在のデフォルト配置
- `keymap-drawer/` — push 後に [caksoylar/keymap-drawer](https://github.com/caksoylar/keymap-drawer) が SVG を自動生成してここに配置する

## よく使う DTS ビヘイビア早見表

| 記法 | 動作 |
|---|---|
| `&kp Q` | キー押下 (Q) |
| `&kp LC(A)` | Ctrl + A |
| `&kp LG(LS(S))` | Cmd + Shift + S |
| `&mt LSHIFT A` | ホールドで Shift、タップで A |
| `&lt 2 SPACE` | ホールドで Layer 2、タップで SPACE |
| `&mo 3` | 押下中のみ Layer 3 |
| `&tog 4` | Layer 4 のトグル |
| `&trans` | 透過（下位レイヤーに委譲） |
| `&none` | 無効 |
| `&bootloader` | ブートローダーモードへ |
| `&sys_reset` | ソフトリセット |
| `&bt BT_SEL 0` | Bluetooth プロファイル 0 |
| `&bt BT_CLR` | 現行プロファイルのペアリング解除 |
| `&mkp MB1` | マウス左クリック |

meteorite40 固有のビヘイビア:

| 記法 | 動作 |
|---|---|
| `&mosk WIN_KEY MAC_KEY` | OS 切替でキー動作を変える (OS-Switch Key pair) |
| `&mossm MOD` | プリセット修飾キーペア (Win:Ctrl / Mac:Cmd など) |
| `&mossmk MOD KEY` | 装飾キー+通常キー (OS 別) |
| `&mck_mt MOD KEY` | タップ＝通常キー / ホールド＝OS 別修飾キー |
| `&alt_cmd_tab` | スマートトグル (Win:Alt+Tab / Mac:Cmd+Tab) |
| `&mck_mt_st MOD 0` | スマートトグル+OS別修飾キーのタップホールド |
| `&ccfg C_CPI_DN` など | カスタムコンフィグキー (layer_4 参照) |
| `&bt_os BT OS` | BT プロファイル + OS フラグ同時切替 |

## レイヤー追加・コンボ追加について

コンボの追加、レイヤー数の追加、Behaviors の追加など、ZMK Studio では扱えない変更は **このリポジトリを編集して push** するだけで反映される（ビルド完了後に UF2 を書き込む）。編集は `config/meteorite40_low.keymap` の `combos { ... }` / `behaviors { ... }` / `keymap { ... }` を直接触る。

## アカウント情報

- フォーク先: https://github.com/keyaki-dev/zmk-config-meteorite40
- upstream: https://github.com/iwk7273/zmk-config-meteorite40
