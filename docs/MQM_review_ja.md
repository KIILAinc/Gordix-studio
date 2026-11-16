# GORDIX Studio 日本語UI翻訳 MQM評価レポート

- 評価対象: `KIILAinc/Gordix-studio` ブランチ `kiila/japanese-translate`（コミット `930ac85`）
- 原文: `pfsin555/Gordix-studio` `main`（コミット `1e608c0`）
- 方法: 7つのHTMLファイルを行単位で比較しました（両ブランチは行数が一致し、日本語は `en` スロットに上書きされています）。i18n辞書、三項演算子による文言、HTML初期値、`title` / `aria-label`、G-code出力を確認し、表示箇所のコードも見て判断しました。さらに Chrome で全ページを EN / 中文 / 日本語 で表示して確認しました。
- 行番号は日本語ブランチのものです。

## サマリー

| 重大度 | 件数 |
|---|---|
| Critical | 0 |
| Major | 7 |
| Minor | 22 |

日本語訳そのものは全体として質が高いです。主な問題は次の3点です。
(1) 英語スロットを日本語で上書きしたため、英語UIが失われたこと（構造上の問題）
(2) 一部の文言が訳し漏れていること、またはG-code出力に日本語が混入していること
(3) CAM用語の衝突と不統一（「ダウンカット」「ネスト」「出力」など）

## 指摘一覧

### Major

**M1. 全ファイル（例: index.html 139/148/162、CNC.html 785 ほか）**
- カテゴリ / 重大度: Design and markup / Major
- 英語原文: `data-en="Table Configurator"`、`currentLang` の初期値 `'zh'`、切替ボタン `中文 / EN`
- 現在の日本語: `en` スロットに日本語を上書きし、ボタンを `中文 / 日本語` に変更
- 修正案: `ja` スロットを新設し、`en` を原文に戻します。右上を EN / 中文 / 日本語 の3択タブにします。
- 理由: 英語を選ぶ手段がなくなっています。また、上流（英語）の更新を取り込むたびに日本語との衝突が起きます。保存済みの `gordix-lang='en'` の意味も変わってしまいます。

**M2. params.html 925**
- カテゴリ / 重大度: Accuracy（未翻訳） / Major
- 英語原文: `Down-cut presses chips down, protecting the surface from tear-out.` ほか（`tips.en`）
- 現在の日本語: （未翻訳のため英語のまま表示されます）
- 修正案: `ja` を追加します。例:「ダウンカットビットは切りくずを下へ押さえ込み、表面のささくれ（バリ）を抑えます。」「アップカットビットは切りくずを上へ排出するため、熱がこもりにくく、工具に溶着しにくくなります。」「ストレートビットは上下方向の力がかかりません。」
- 理由: 加工条件の目安カードで、刃の種類の説明だけが英語のまま表示されます。

**M3. params.html 475**
- カテゴリ / 重大度: Terminology / Major
- 英語原文: `Climb milling reduces tear-out.`
- 現在の日本語: ダウンカットで木層のささくれを抑えられます。
- 修正案: 下向き削り（クライムミリング）にすると、木層のささくれを抑えられます。
- 理由: JIS用語では「ダウンカット＝下向き削り」なので、誤訳ではありません。ただし同じ表に「2枚刃**ダウンカット**」（ビットのねじれ方向）の列があるため、利用者は「ダウンカットビットを使え」と読みます。工具の選択を誤らせ、仕上がりに影響する可能性があります。

**M4. draw.html 2456**
- カテゴリ / 重大度: Design and markup / Major
- 英語原文: `` `(Tool: ${dPen === 0 ? 'Laser' : 'Pen Body ' + dPen + 'mm'})` ``
- 現在の日本語: `` `(Tool: ${dPen === 0 ? 'レーザー' : 'ペン軸径 ' + dPen + 'mm'})` ``
- 修正案: 原文（ASCII）に戻します。
- 理由: 出力されるG-codeのコメントに、UI言語と無関係に常に日本語（UTF-8のマルチバイト文字）が入ります。中国語UIでも同じです。ASCII前提のコントローラーや送信ソフトでは、エラーや文字化けの原因になります。

**M5. 全ページ `<html lang="zh-TW">`（例: index.html 2、CNC.html 2）**
- カテゴリ / 重大度: Locale conventions / Major
- 英語原文: （固定の `lang="zh-TW"`）
- 現在の日本語: 変更なし
- 修正案: 表示言語に合わせて `document.documentElement.lang` を `ja` / `zh-TW` / `en` に切り替えます。
- 理由: 日本語の文章が繁体字中国語として扱われます。そのため「直」「角」「骨」などが中国字形のフォントで表示されることがあり、読み上げ・翻訳・検索の言語判定も誤ります。

**M6. index.html 133**
- カテゴリ / 重大度: Accuracy（脱落）＋ Design / Major
- 英語原文: `All tools run locally in browser.`
- 現在の日本語: すべてのツールはブラウザー上で動作します。
- 修正案: すべてのツールはブラウザー内で動作し、ファイルは外部に送信されません。（さらに `data-*` に入れて、言語切替の対象にします）
- 理由: 「locally」（ファイルをサーバーへ送らない）という、利用者の判断材料になる意味が抜けています。また、この文はハードコードのため、中国語UIでも日本語が表示されます。

**M7. table_conf.html 426–430**
- カテゴリ / 重大度: Accuracy / Major
- 英語原文: `Side N / Side S / Side W / Side E / Diagonal D`
- 現在の日本語: 北側 N / 南側 S / 西側 W / 東側 E / 対角 D
- 修正案: 辺 N / 辺 S / 辺 W / 辺 E / 対角線 D
- 理由: 入力値は**辺の長さ**（600～2600 mm）であって、方位ではありません。「北側」と書くと位置の指定に見えてしまいます。

### Minor

| # | ファイル:行 | カテゴリ | 英語原文 | 現在の日本語 | 修正案 | 理由 |
|---|---|---|---|---|---|---|
| m1 | CNC.html 628 / Gcodetrans.html 1334 | Accuracy | tab reaches the material surface (not cut through [at all]) | タブが材料表面まで達しています（底面が切断されません） | タブが材料表面まで残り、タブ部分はまったく切断されません | 「底面」だと通常のタブと区別がつきません。この警告の要点は「タブ部分が一切切れない」ことです |
| m2 | CNC.html 780 | Terminology | Nesting Depth | ネスト階層 | 入れ子階層 | CAMの「ネスト／ネスティング」は板取り配置を指します。ここでは輪郭の内外階層の意味です |
| m3 | CNC.html 767 | Terminology | Overlap Ratio | 重なり率 | オーバーラップ率 | ポケット加工の経路重なりは「オーバーラップ（率）」が一般的です |
| m4 | CNC.html 766 / params.html 470, 524 | Terminology（不統一） | Step Down / DOC | 1回あたりの切込み量 / 切込み深さ | 1パスの切込み深さ（統一） | 同じ概念がページごとに別の語になっています |
| m5 | draw.html 447 | Terminology | Output Width | 出力幅 | 加工幅 | 同じページで「出力」をレーザーパワー（S）の意味でも使っているため、紛らわしくなります |
| m6 | draw.html 463–465 | Terminology / Fluency | Grayscale Power Range (S) / Min Power (S Min) | グレースケール出力範囲 (S) / 最小出力 (S最小) | レーザー出力範囲 (S) / 最小出力 (S Min) | 「S最小」は不自然です。Sはパラメーター名なので原表記を残します |
| m7 | draw.html 455 | Accuracy | Resolution (Step) | 解像度（ステップ） | 解像度（線分長） | 中国語原文は「線段長」です。「ステップ」では何の量かわかりません |
| m8 | vc.html 446 | Audience | Fineness (mm) | 細かさ (mm) | 経路間隔 (mm) | 値はmm単位の経路ピッチ（ステップオーバー）です。「細かさ」では大小の向きがわかりません |
| m9 | vc.html 450, 453 | Terminology | Intaglio / Relief | 陰刻 / 浮き彫り | 沈み彫り / 浮き彫り | 対になる語として「沈み彫り⇔浮き彫り」のほうが自然です（陰刻の対は陽刻） |
| m10 | vc.html 445 | Locale | V-Bit Angle (deg) | Vビット角度 (度) | Vビット角度 (°) | 単位は記号で書くほうが一般的です |
| m11 | vc.html 480, 351 | Accuracy（未翻訳） | 提示資訊 / Notice、確定 / OK | （中国語と英語の併記のまま） | お知らせ / OK（言語ごとに表示） | 日本語UIのダイアログに中国語が出ます |
| m12 | vc.html 399 | Accuracy（脱落） | Carve Cavity (Default Blue Fill) | 凹部を彫刻（青色部分） | 凹部を彫刻（既定・青色部分） | 既定値であることが抜けています |
| m13 | params.html 476 | Terminology（不統一） | Beech / Walnut | ブナ / クルミ | ブナ / ウォールナット | レーザー表では「ウォールナット」と訳しています |
| m14 | params.html 514 | Terminology | Cutter | 刃形 | 刃の種類 | 選択肢はアップカット／ダウンカット／ストレートで、刃の形状ではありません |
| m15 | params.html 462, 522, 646 | Audience | Makita / Makita Dial | Makita RT0700 / Makitaダイヤル | マキタ RT0700 / マキタ ダイヤル | 日本の利用者にはカタカナのブランド表記がなじみます |
| m16 | params.html 499 ほか | Locale（不統一） | 10W 450nm / 3mm | 10W・450 nm / 3 mm | 10 W / 450 nm（数値と単位の間を半角スペースで統一） | 数値と単位の間隔が混在しています |
| m17 | index.html 79, 90 / CNC.html 732 / draw.html 441 | Accuracy / Terminology | Cut Converter / Draw Converter | ルーター切削/レーザーカット、ペン/レーザー彫刻 | データ変換（切削・切断）／データ変換（描画・彫刻） | 「変換ツール」という機能が伝わらず、「カット」と「切断」も混在しています |
| m18 | index.html 112 / Gcodetrans.html 479 | Terminology / Style | Gcode (Real-time) Adjuster | Gcode修正 | Gコード調整 | 「修正」はエラー訂正の意味に読めます。同じ画面内で「Gコード」と「Gcode」も混在しています |
| m19 | index.html 68 / table_conf.html 418 | Accuracy | Table Configurator | テーブル設定 | テーブル寸法設定 | 「テーブル」だけでは表なのか加工台なのか曖昧です。中国語原文は「桌長」（寸法）です |
| m20 | Gcodetrans.html 481 / draw.html 472 / table_conf.html 424 | Audience | File Upload / Upload table.cfg | ファイルをアップロード 等 | ファイルの読み込み / table.cfgを読み込む／保存 | 処理はローカルで行われ、送信されません。「アップロード」はM6の説明と矛盾します |
| m21 | Gcodetrans.html 598 | Locale | `${mins} min ${secs} sec` | 3 分 20 秒 | 3分20秒 | 日本語では数字と「分」「秒」の間にスペースを入れません |
| m22 | draw.html 446 | Design and markup | Browse File | ファイルを選択 | 画像を選択 | 幅の狭いボタンで「ファイルを選\|択」と途中で改行されます（実画面で確認） |

### 参考（原文側の問題で、今回は未修正）
- `draw.html` の `Y-Offset: …mm`、`vc.html` の `(Max: … mm)`、`Gcodetrans.html` の `3D REALTIME VIEW` は、原文の時点で全言語とも英語のハードコードです。
- `params.html` の `typeEn`（切断／彫刻）は画面に表示されません。

## 実施した修正（ブランチ `kiila/i18n-en-zh-ja`）
- `lang-tabs.js` を新規追加: 右上に EN / 中文 / 日本語 のタブを表示します。選択は `localStorage` の `gordix-lang` に保存し、ページ間で共有します。`<html lang>` の切り替え、矢印キーでの操作、モバイル幅での詰め表示にも対応しています。
- 初期言語は「保存済みの設定 → ブラウザーの言語 → 日本語」の順に決めます。
- 全7ページ: 英語原文を `en` に戻し、`ja` 辞書を追加しました（上記の修正を反映）。三項演算子の文言を3言語のマップに変更しました。
- `service-worker.js`: キャッシュを v2.6 に上げ、`lang-tabs.js` をキャッシュ対象に追加しました。
- G-code出力は原文どおり ASCII に戻しました。
