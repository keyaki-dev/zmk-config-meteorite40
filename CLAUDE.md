# meteorite40 キーマップ編集ガイド

自作40%キーボード **meteorite40（ロープロファイル版）** の個人フォークで、DTSソースを直接編集しGitHub ActionsでビルドしてUF2を書き込む運用です。

## リポジトリ

- 本ディレクトリ: `~/projects/zmk-config-meteorite40`
- origin: `keyaki-dev/zmk-config-meteorite40`
- upstream: `iwk7273/zmk-config-meteorite40`
- **編集対象**: `config/meteorite40_low.keymap`（ノーマル版は使わない）
- 物理レイアウト定義: `config/meteorite40_low.json`
- ビルド対象も両方定義されている（`build.yaml`）が、使うのは `_low` のみ

## 編集から反映までの標準フロー

```
① config/meteorite40_low.keymap を編集
② git add <file> && git commit -m "<prefix>: <説明>"
③ git pull --rebase origin main     # ← 必須
④ git push origin main
⑤ Actions でビルド (3〜5分)
⑥ ./bin/flash
⑦ プロンプトで / + N 長押し (or Xiao BLE リセットボタン2連打)
⑧ 自動で書き込み完了
```

### なぜ `git pull --rebase` が必須か

`.github/workflows/draw-keymap.yml` が push のたびに走り、`keymap-drawer` bot が `keymap-drawer/*.svg` と `*.yaml` を自動コミットして remote を進めます。ローカルが古いと fast-forward で reject されるため、push 前に pull --rebase します。

## ヘルパースクリプト

| コマンド | 用途 |
|---|---|
| `./bin/flash [low\|normal]` | UF2取得→ブートローダー待ち→書き込み→アンマウント検知までワンコマンド |
| `./bin/fetch-uf2 [low\|normal] [dest]` | 最新成功ビルドのUF2を取得のみ（保存先デフォルト `~/Downloads/mt40/`） |

両者とも repo slug は `git config --get remote.origin.url` から取る（`gh repo view` は upstream を優先する挙動がある）。

## 重要な制約と落とし穴

### ZMK Studio は使わない
Studio で触ると以降 `.keymap` 変更が反映されなくなる。触ってしまったら Studio UI の "Restore Stock Settings" を実行してソース運用に戻す。

### macOS Finder で UF2 をドロップするとエラーコード -36
FAT に拡張属性が書けないのが原因。`cp -X` を使う。`bin/flash` 内では対応済み。

### flash スクリプトの既知の不安定性
ブートローダー検出直後の `cp` が稀に失敗することがある。失敗時は手動で:
```sh
cp -X ~/Downloads/mt40/meteorite40_low-xiao_ble__zmk-zmk.uf2 /Volumes/XIAO-SENSE/
```

### コミット規約（親CLAUDE.mdより）
- 1機能1コミット
- メッセージは日本語、プレフィックス（feat/fix/docs/refactor/chore/ci 等）を先頭に
- `Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>` を末尾に

## 現在のキーマップ構造（2026-04 時点）

Karabiner Elements の挙動を meteorite40 に移植した設計。

### Layer 0: BASE

- **QWERTY**（US配列、ローマ字入力）
- 左小指 (pos 10): `&mt LSHIFT A` — ホールドで Shift、タップで A
- 右小指 (pos 19): `&mt RSHIFT BSLH` — ホールドで Shift、タップで `\`
- 右下 (pos 31): `&lt 4 SLASH` — ホールドで Layer 4（SETTING）、タップで `/`
- 中央ボタン (pos 25, 26): `&mkp MB1`, `&mkp MB2`

### 親指 (pos 32〜39)

```
[⌘/英数][L1/Space][Alt/Tab][--] | [--][Ctrl/Esc][L2/Enter][⌘/かな]
   32      33        34     35    36    37        38        39
```

pos 35, 36 は `&none`（将来マウス操作用に予約）。

### Layer 1 (display-name: "L1") — Karabiner Space Layer 相当

```
Row 0: 1 2 3 4 5 | 6 7 8 9 0
Row 1: Sft/` [ ] ; ' | ← ↓ ↑ → trans
Row 2: none x5 | MB4 MB5 | - = Home End trans
Row 3: trans x6 | BSPC trans
```

- pos 10: `&mt LSHIFT GRAVE`（Layer 1 中でも左小指 Shift を維持）
- pos 38: `&kp BSPC`（BASE の Enter 位置で Backspace）

### Layer 2 (display-name: "NUM / ARROW") — 既定のまま、未決定

ユーザーの方針: ウィンドウ操作（Mission Control / Spaces切替）に差し替える予定。発動キーは Layer 0 pos 38 のホールド (`&lt 2 ENTER`)。

### Layer 3 (FUNCTION): F1〜F10、既定のまま

### Layer 4 (SETTING): カスタムコンフィグ＋Bluetooth、既定のまま

### コンボ（BASEレイヤーのみ運用）

| キー | 発動 |
|---|---|
| Q+W | Tab |
| D+F | LANG1（かな） |
| J+K | LANG2（英数） |
| Z+X | Esc |

Layer 2 のコンボ（旧 line_home / line_end 等）は削除済み。

### ホールドタップのパラメータ

- `&mt`: `tap-preferred`, `tapping-term-ms = 200`, `quick-tap-ms = 150`
- `&lt`: `tap-preferred`, `tapping-term-ms = 150`, `quick-tap-ms = 150`

## keymap-drawer

- 出力先: `keymap-drawer/meteorite40_low.svg` と `meteorite40.svg`
- push のたびに `.github/workflows/draw-keymap.yml` が走って更新
- display-name を `&lt` 系のキーに表示するので、レイヤー名が他のキー名と被らないよう注意（例: Layer 1 を "SPACE" にすると `&lt 1 SPACE` が "SPACE/SPACE" と描画される → "L1" 等に）

## よく使う DTS 記法

| 記法 | 動作 |
|---|---|
| `&kp Q` | Q入力 |
| `&kp LC(A)` | Ctrl+A |
| `&kp LG(LS(S))` | Cmd+Shift+S |
| `&mt LSHIFT A` | ホールド=Shift、タップ=A |
| `&lt 2 SPACE` | ホールド=Layer 2、タップ=Space |
| `&mo 3` | 押下中のみ Layer 3 |
| `&tog 4` | Layer 4 トグル |
| `&trans` | 透過（下位レイヤーに委譲） |
| `&none` | 無効 |
| `&bootloader` | ブートローダーへ |
| `&bt BT_SEL 0` | BTプロファイル切替 |
| `&mkp MB1` | マウス左クリック |

### meteorite40 固有のビヘイビア

| 記法 | 動作 |
|---|---|
| `&mosk WIN_KEY MAC_KEY` | OS切替でキー動作を変える |
| `&mossm MOD` | プリセット修飾キーペア（Win:Ctrl/Mac:Cmd 等） |
| `&mossmk MOD KEY` | 装飾キー+通常キー（OS別） |
| `&mck_mt MOD KEY` | タップ=通常キー、ホールド=OS別修飾キー |
| `&alt_cmd_tab` | スマートトグル（Win:Alt+Tab / Mac:Cmd+Tab） |
| `&ccfg C_CPI_DN` etc | カスタムコンフィグキー（Layer 4 参照） |
| `&bt_os BT OS` | BT+OS同時切替 |

## Karabiner Elements との同期

Mac 内蔵キーボードでは meteorite40 接続時に Karabiner を無効化する運用だが、近い挙動を Karabiner 側でも再現してある（`~/.config/karabiner/karabiner.json`）。

### Karabiner に入っている内容

1. Caps Lock → Left Control
2. 左⌘タップ → 英数 / 右⌘タップ → かな
3. Ctrl + Space → Enter
4. Space ホールド → `layer-space_v2` Layer 発動（QWERTY→数字、HJKL→矢印、etc.）
5. `\` ホールド → Right Shift、タップ → `\`（`to_if_held_down` 方式、threshold 100ms）
6. コンボ Q+W=Tab / D+F=英数 / J+K=かな / Z+X=Esc（Space Layer 無効時のみ）

### Karabiner で入れていない内容（試して外した）

- A の mod-tap (hold=Shift, tap=A): tap-preferred で 100ms まで下げても遅延感が残り **使いづらかったため削除**。A は素のAキー。
  - 原因: Karabiner の mod-tap はキーリリースまでタップ確定を待つ構造のため、原理的にリリース時まで入力が見えない。ZMK の tap-preferred とは違う。
  - 教訓: ホームロウmod を Karabiner で再現するのは体感が悪い。**ZMK 側 (meteorite40) では問題なく動くので、そちらだけで運用**。

### meteorite40 側のキーマップを変えた時

- 親指・小指以外の基本的な変更: Karabiner 側はあまり関係しないので無視してOK
- Space Layer (Layer 1) の内容を変えた: Karabiner 側の `layer-space_v2` も合わせて更新するか検討
- コンボの追加/削除: Karabiner 側の simultaneous ルールも同期

## ユーザーの方針・好み

- **キーマップ設計は自分で決める**。仕組み化（ビルド・書き込み・可視化）だけサポートしてほしい
- **一つずつ順番に修正**したい。複数変更を詰め込まない
- **実装前に大量の質問を並べるのは嫌**。迷ったら小さな選択肢で短く聞く
- **設定に関する質問の回答はAskUserQuestionで選択肢形式**で受けることが多い
- SVG で視覚確認しながら進めるのを好む
- 書き込み結果の確認は自分で行う（タイピングして感触を確かめる）

## メモリへの保存

別セッションでもこの作業を思い出せるように、ユーザー memory に以下を保存済み:
- ファイル: `~/.claude-personal/projects/-Users-yamashita/memory/meteorite40_workflow.md`
- インデックス: 同ディレクトリの `MEMORY.md`

キーマップ設計が大きく変わったら、上記 memory も更新すること。

## トラブルシュート

| 症状 | 対処 |
|---|---|
| `git push` が rejected | `git pull --rebase origin main` してから再 push |
| `gh` コマンドで古い run が返る | `git config --get remote.origin.url` で repo が origin を向いているか確認 |
| `./bin/flash` の cp でエラー | 手動で `cp -X` を実行（flash 側のタイミング問題） |
| ブートローダードライブが出ない | `/ + N` のホールドを長めに、または Xiao BLE の物理リセットボタン2連打 |
| ビルドが失敗 | `gh run view <id> --log-failed -R keyaki-dev/zmk-config-meteorite40` でログ確認 |
| keymap-drawer が `&lt X Y` を "Y/Y" と描画 | 該当 Layer の display-name を別の短い名前（L1, L2 など）に変更 |
