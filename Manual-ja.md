# MD//WORKS PROVENANCE 取扱説明書 β版

## はじめに

MD//WORKS PROVENANCE は、学術レポートや論文の Writing Process（執筆プロセス）を記録し、その記録の整合性を検証できるようにする Markdown エディタおよび検証ツール群です。

単なるテキスト編集にとどまらず、Interaction-active time（操作が行われていた時間）、Paste Provenance、Direct Edit、IME Composition、Typing Chunk、Input Pause 等をバックグラウンドで記録し、最終提出時には改変を検知するためのサーバー署名を付与できます。

本マニュアルは、レポートや論文を執筆する「学生・研究者・執筆者」と、記録された Writing Process を確認する「教員・指導者・評価者・第三者レビュアー」の双方に向けたガイドです。

MD//WORKS PROVENANCE は、AI利用、不正行為、剽窃、本人性を自動判定するシステムではありません。

本β版は通常版MD//WORKSの操作感を維持しつつ、学術用途で誤解や提出事故を生む機能を整理したものです。

学生を監視するツールではなく、

> **自分のWriting Processを記録し、その過程を検証可能なEvidenceとして残す**

ことを目的としています。

---

## 1. 起動と画面構成

### 1-1. 起動とアプリ化

インストールは不要です。

提供されたMD//WORKS PROVENANCE EditorのHTMLファイルをブラウザで開いて利用します。

β版では **Windows上のChromium系ブラウザ（Chrome / Edge等）を推奨環境** とします。

Firefox等ではFile System Access APIの有無により、保存操作がファイル選択ではなくダウンロード方式になる場合があります。

Safari / iPad等を含むブラウザ間では挙動差があり得るため、授業で指定された環境がある場合はその指示に従ってください。

Input Process Metricsは現在Experimental機能であり、ブラウザ、OS、IME、入力方式の違いによって観測できる情報に差が出る場合があります。

ブラウザの「アプリとしてインストール」機能の利用可否は、配布方法（ローカルHTML / Web配布）やブラウザ側の仕様に依存します。

MD//WORKS PROVENANCE本体の必須機能ではありません。

### 1-2. 対応環境と複数タブでの作業

複数のReportを扱う場合は、別タブや別ウィンドウでEditorを開いて作業できます。

ただし、MD//WORKS PROVENANCEはクラウド上の「Workspace」を管理する仕組みではありません。

各Reportの正式な作業状態は **Save Reportで保存したHTMLファイル** に保持されます。

Emergency Recoveryは各ブラウザセッション内の一時的なテキスト救済用であり、複数タブ間で共有される正式な保存領域ではありません。

同じ保存先ファイルを複数のタブから同時編集・上書きする運用は避けてください。

### 1-3. 画面構成

画面は主に以下のエリアで構成されています。

1. **メニューバー / タイトルバー**  
   ファイル操作、提出（Submit）、全画面表示などを行います。DEEPボタンはAcademic版ではRecordボタンに置き換えられています。

2. **ツールバー**  
   見出し、太字、リストなどのMarkdown装飾をワンクリックで挿入します。

3. **エディター画面 / プレビュー画面**  
   左側で執筆し、右側で実際の表示を確認できます。

4. **ステータスバー**  
   画面下部に次のようなWriting Recordの概要を表示します。

```text
Active 24m | Unverified 22% | [Input activity bar] | Anchors: 14
````

* **Active**
  実際にキー操作等があった活動時間として記録されたInteraction-active timeです。放置時間は含みません。

* **Unverified**
  Non-internal paste share（%）です。同じReport内の先行Copy/Cutと照合できなかったPasteの割合を示します。

  概念的には：

```text
unverifiedPasteChars
/
(
  typedChars
  + unverifiedPasteChars
  + replaceInsertedChars
)
```

で計算されます。

Verified Internalは分子・分母から除外されます。

最終本文の何%が外部由来かを意味するものではありません。

* **Input activity bar**
  Direct input / Verified internal / Unverified の3区分を色で示す小型バーです。

* **Anchors**
  Server Anchor（サーバー側の暗号学的証跡）の数です。クラウドバックアップではありません。

---

## 2. ファイルを作成・開く

### 2-1. 新規作成と既存ファイルを開く

新しいレポートを作成する場合は、**新しいMD//WORKS PROVENANCE Editorを開き、空のEditorから執筆を開始します。**

現β版のFileメニューには「New / 新規作成」コマンドはありません。

既存のAcademic Report（HTMLコンテナ）の続きを書く場合は、

**File > Open Report**

またはタイトルバーの **Open** ボタンを使用します。

保存したHTMLファイルには、最後にSave Reportした時点までの：

* 文書
* Writing Process
* Event Log
* Input Process Metrics
* Session情報
* Integrity metadata

が含まれています。

読み込み後は新しいSessionが開始され、以前の記録に続けて編集記録が追加されます。

### 2-2. Wordファイルを読み込む (.docx)

**ファイル > Import Word (.docx)...**

から、Wordファイルを読み込んでMarkdownに変換し、現在のカーソル位置へ挿入します。

選択範囲がある場合は、選択範囲を削除してから挿入されます。

文書全体を置換する動作ではありません。

Wordから取り込まれた文章は、既存のPasteと同様に：

```text
type: paste
provenance: unverified
```

として記録されます。

内部的には：

```text
meta.inputSource = "word-import"
```

としてWord由来であることが記録されます。

Wordファイル自体が問題だからUnverifiedになるわけではありません。

その作成過程をAcademic Editor側で観測していないためです。

### 2-3. Emergency Recovery（緊急テキスト復元）

ブラウザやPCの予期せぬ停止に備え、Editorはブラウザの `sessionStorage` に **最新1件の一時的な本文テキストスナップショット** を保持します。

未保存の本文が失われた場合は、

**File > Emergency Recovery...**

を開き、救出可能なテキストがないか確認してください。

Emergency Recoveryでは：

* 復旧テキストをクリップボードへコピー
* `*-recovered.md` として保存
* Recovery copyの削除

等を行えます。

自動的に元のReportへ復元する機能はありません。

**注意：Emergency Recoveryはテキスト救済のみの機能です。**

次の情報は復元しません。

* Writing Process
* Input Process Metrics
* Event Chain
* Manifest
* Server Anchor
* Final Signature

正規のReportバックアップでもありません。

こまめに **Save Report (Ctrl+S)** でWorking Reportを保存してください。

Recoveryから取り出した文章をEditorへ貼り付ける場合は通常のPasteとして扱われ、同一Report内の先行Copy/Cutと照合できなければUnverifiedになります。

---

## 3. 本文の入力と装飾（Markdown）

### 3-1. 基本的な入力と装飾

Markdown形式で文章を作成します。

ツールバーを使えば：

* 見出し（H1/H2/H3）
* 太字
* 斜体
* リスト
* チェックリスト
* 表
* 引用
* コード
* リンク

等を簡単に挿入できます。

### 3-2. 画像の挿入について

現β版のAcademic Editorでは、**ローカル画像のBase64埋め込みや画像ファイルのPaste / Drag & Dropには対応していません。**

画像ファイルをドラッグした場合は：

```text
Local image embedding is not available in Academic mode.
```

と通知されます。

また、現β版のAcademic PreviewはMarkdown画像記法を画像として描画する機能を備えていません。

図や画像をレポートで使用する必要がある場合は、授業・提出方法の指示に従ってください。

### 3-3. 学籍情報の記載（授業で指定される場合）

授業で指示がある場合は、レポート本文の冒頭に学籍番号・氏名等を通常の本文として記載してください。

例：

```markdown
学籍番号: s123456
氏名: 山田太郎
```

これらの文字列も本文の一部としてDocument Hashの対象になります。

**現β版では、本文冒頭の学籍番号・氏名・メールを自動抽出して本人確認したり、ファイル名と自動照合したりする機能は実装されていません。**

提出ファイル名の規則が指定されている場合は、担当教員の指示に従ってください。

本文に記載された氏名や学籍番号は、記載された本人が実際に執筆したことを証明するものではありません。

### 3-4. 学術的な装飾

現β版のAcademic Previewは：

* 見出し
* 強調
* 取消線
* インラインコード
* 引用
* リスト
* タスク
* コードブロック
* リンク
* 表
* 水平線

等の基本的なMarkdown表示を中心にしています。

通常版MD//WORKSにある一部の高度な表示機能は、現β版Academic Editorにはまだ搭載されていません。

特に：

* 上付き・下付き文字の専用Markdown拡張
* Markdown脚注の自動リンク表示
* LaTeX / KaTeX等による数式レンダリング
* Mermaid図のレンダリング

は保証していません。

---

## 4. プレビューと文書の仕上げ

### 4-1. プレビューで確認する

タイトルバーの **Preview** ボタンは、EditorとPreviewの **分割表示をオン / オフ** します。

Previewだけを大きく表示したい場合は：

* Preview内の **Focus** ボタン
* **View > Preview Focus**

を使用します。

Preview Focusからは画面右上の **↩ Editor** で編集画面へ戻れます。

### 4-2. 検索・置換と正規表現

**編集 > Find (Ctrl+F) / Replace (Ctrl+H)**

で検索・置換パネルが開きます。

正規表現や大文字・小文字の区別をサポートしています。

### 4-3. Markdown整形と目次の挿入

**フォーマット > Insert / Update TOC**

を実行すると、文書内の見出し構造を解析し、クリック可能な目次を自動生成します。

### 4-4. 各種表示モード

**表示** メニューから：

* テーマ（Paper / Midnight / Warm）
* 行番号
* 行の折り返し
* Zen Mode
* 全画面表示

等を切り替えられます。

---

## 5. Writing Record（執筆プロセスの記録）

Academic版の中心的な機能です。

Editor上で記録対象となるWriting Processはバックグラウンドで記録され、提出後にその記録の整合性や執筆過程を確認するための材料となります。

これらの値だけで著者性や不正行為を自動判定するものではありません。

### 5-1. Writing Recordとは

タイトルバーの **Record（◷）** ボタンを押すか、ステータスバーの：

```text
Active | Unverified | Anchors
```

領域をクリックすると「Current Writing Record」パネルが開きます。

例：

```text
Current Writing Record

Active time              24 min
Sessions                   3
Current document        4,280 chars

Recorded input
Direct input            3,120 chars
Verified internal         840 chars
Unverified                620 chars

Non-internal paste share  16.6%

Events                     84
Server anchors             14
```

このパネルは学生自身の振り返り用でもあり、不正スコアや警告画面ではありません。

### 5-2. テキスト入力の分類

入力された文字は、Writing Record上で主に以下の区分として整理されます。

#### Direct input（直接入力）

Paste等として分類されず、Editor上の直接編集として記録された文字入力や、Replace等で挿入された文字です。

概念的には：

```text
Typed + Replace Inserted
```

です。

これは編集活動としての入力量です。

**最終本文中の「学生自身が物理キーボードで書いた文字数」を意味するものではありません。**

MD//WORKS PROVENANCEはEditorが観測した編集イベントを記録しますが、そのイベントを誰または何が生成したかを認証するものではありません。

#### Verified internal（検証済み内部コピー）

同じReport内で先行するCopy/Cutと：

```text
transferId
sourceHash
pastedHash
sourceEventId
```

等が正常に対応したPasteです。

同一Report内の転送であることを確認できたことを意味し、文章の著者性そのものを証明するものではありません。

#### Unverified（未検証）

同一Report内の先行Copy/Cutとの対応を検証できなかったPasteです。

例：

* 外部アプリ
* Web
* Word Import
* 別Report
* Clipboard metadata loss
* ブラウザ差
* 生成AIからのPaste

等が含まれ得ます。

> **Unverified ≠ External ≠ Improper ≠ AI ≠ Plagiarism ≠ Misconduct**

です。

Unverifiedが多いからといって直ちに不正とは見なされません。

### 5-3. アクティブ執筆時間とセッション管理

* **Active time**
  アプリ上で実際に操作が行われていた時間として記録されたInteraction-active timeです。

  放置時間はカウントされません。

  総学習時間、思考時間、読書時間を意味しません。

* **Sessions**
  Academic Editorを起動したとき、またはReportをOpenして編集を再開したときに新しいSessionが開始されます。

  保存された各Sessionの開始時刻やInteraction-active time等をもとに、複数日にまたがる執筆の流れを確認できます。

### 5-4. Paste時のフィードバック

Unverified Pasteで **200文字以上** の貼り付けがあった場合、警告色ではなく通常色のToastで：

* 貼り付けた文字数
* 必要に応じて引用・出典を確認する旨

が表示されます。

これはPasteを禁止したり、不正を判定したりするものではありません。

### 5-5. AIツール利用時の注意点

ChatGPT、Gemini、Claude等の生成AIからテキストをコピーしてMD//WORKS PROVENANCEへ貼り付けた場合、通常は同一Report内の先行Copy/Cutとして照合できないため **Unverified** として記録されます。

ただし、MD//WORKS PROVENANCEは **AI利用を検出・判定するツールではありません。**

また、AIや自動化ソフトウェアがPasteを使わず、通常の入力に似た形で文章を逐次入力することも技術的には可能です。

そのため：

> **Pasteが記録されていないことは、人間が執筆したことの証明にはなりません。**

Input Process Metricsは、このような場合も含めてEditorが実際に観測した入力過程をより詳しく残すための機能です。

それでも入力主体が：

* 人間
* AI
* RPA
* Computer Use
* 音声入力
* アクセシビリティソフト
* その他の入力方式

のどれであるかを特定するものではありません。

生成AIによる文章生成、翻訳、校正、要約等をどこまで利用できるかは、大学・授業・課題ごとのAI利用ポリシーに従ってください。

### 5-6. Input Process Metrics（Experimental）

MD//WORKS PROVENANCEは、Paste Provenanceに加えて、Editorが観測した入力過程について軽量な集計情報を記録します。

主な項目には次があります。

#### Direct Edit

Editor上で直接編集として観測された文字の挿入・削除です。

#### Deletion Operations

直接編集として観測された削除操作です。

削除が多い・少ないこと自体は、不正や文章品質を意味しません。

#### IME Composition

日本語等で使用されるIME変換について、Composition sessionや確定文字数等を記録します。

IME変換途中の未確定文字列そのものを保存するものではありません。

#### Typing Chunks

直接編集活動を一定の単位にまとめたものです。

現在のExperimental実装では、おおむね：

* 約2秒の無入力で現在のChunkを閉じ、Pause候補を保持
* 連続入力は最大約5秒ごとに安全flush
* 5秒safe flush自体はPauseを生成しない

という記録ルールを使用します。

#### Input Pauses

対象となる編集Activity間で観測された無入力区間です。

確定したPauseが60秒以上の場合は `long` と分類されます。

ただしPauseは：

* 思考時間
* 読書時間
* 調査時間
* 離席時間

を意味しません。

#### Capture Status / Capture notes

ブラウザやIME等からInput Process Metricsをどの程度取得できたかを示す技術情報です。

Input Process Metricsは、AI利用、人間による執筆、不正行為を自動判定するためのスコアではありません。

---

## 6. 保存とレポート提出（Submit）

### 6-1. 作業中の保存（Save Report）

執筆途中のデータを保存する場合は、必ず：

**ファイル > Save Report (Ctrl+S)**

を使用してください。

保存されるのは：

* 文書本文
* Writing Process
* Input Process Metrics
* Event Chain
* Summary
* Manifest

等を含むHTMLコンテナ形式のWorking Reportです。

Markdown `.md` ファイルやPDFではありません。

ブラウザのタブを閉じると未保存のデータは失われる可能性があります。

Emergency Recoveryは自動バックアップではありません。

### 6-2. 別PCでの継続

Save Reportで保存したWorking ReportのHTMLファイルを移動すれば、別のPCやブラウザでも編集を再開できます。

Report内には最後に保存した時点までの文書とWriting Processが含まれており、開いた後は新しいSessionとして編集が続きます。

Emergency Recoveryの一時データは引き継がれません。

### 6-3. Submit Report（最終提出レポートの作成）

レポートが完成し、教員へ提出する準備ができたら以下の操作を行います。

1. タイトルバーの **Submit（✓）** ボタン、または **File > Submit Report** を選択します。
2. 「Ready to Submit」画面が表示されます。
3. 引用や参考文献の記載を確認します。
4. チェックボックス：

```text
I have checked any citations and source references required for this document.
```

をオンにします。

5. **Sign & Finalize** を押します。
6. Final Signature取得後、Finalized Reportが保存されます。

Final Signatureは：

> ファイルを編集不可能にするもの

ではありません。

署名後に内容が変更された場合に、Verifierで不整合を検出できるようにする暗号署名です。

Submitにはインターネット接続が必要です。

Submit後もEditorはロックされません。

修正後は再度Submitしてください。

### 6-4. 印刷 / PDFへの書き出し

**ファイル > Print Preview... (Ctrl+P)**

から印刷・PDF保存できます。

ただしPDF/印刷物はAcademic Report提出形式ではありません。

Writing ProcessやFinal Signatureを完全に検証できる提出物としては、Finalized HTML Reportを使用してください。

---

## 7. Report Verifier / Overview Verifier の使い方

Academic Reportについて：

* Writing Process
* Hash Chain
* Manifest
* Server Anchor
* Final Signature
* Input Process Metrics

等を確認するための専用ツールです。

著者本人であることや学術不正の有無を自動的に証明・判定するものではありません。

Verifierには用途の異なる2つがあります。

* **Report Verifier**
  1つのReportを詳細に確認するためのVerifier。

* **Overview Verifier**
  複数のReportを一覧化し、必要なReportを選んで詳細確認するためのVerifier。

### 7-1. 検証ツールの役割と起動方法

提供される：

```text
report-verifier.html
overview-verifier.html
```

をブラウザで開いて使用します。

VerifierはReportに含まれる：

* Document
* Events
* Manifest
* Summary

等からSHA-256ハッシュやEvent Chainを独立再計算します。

Server AnchorやFinal Signatureは、信頼済み公開鍵を用いてEd25519署名検証します。

これらの処理はブラウザ内でローカルに行われ、検証のためにReport本文を外部サーバーへアップロードする必要はありません。

### 7-2. Report Verifier（単一Reportの検証）

1つのAcademic Reportを読み込み、Writing ProcessとIntegrityを詳細に確認します。

主な用途：

* 学生が卒業論文・修士論文等のWriting Processを指導教員へ提示
* 研究者が原稿作成過程を共同研究者へ提示
* 著者が編集者等へ執筆過程を説明
* 教員が1件だけ提出Reportを詳細確認

Report Verifierでは：

* Document Hash
* Event Log Hash
* Event Chain
* Final Chain Hash
* Summary consistency
* Server Anchor
* Final Signature
* Input Process Semantic Validation

等を確認できます。

### 7-3. Overview Verifier（一括読み込み・バッチ評価）

複数のAcademic Report HTMLをまとめてドラッグ＆ドロップすると、Master–Detail形式の一覧が生成されます。

主な一覧項目：

* **Student**
* **Active time**
* **Sessions**
* **Non-internal paste share**
* **Process**
* **Integrity**
* **Signature**
* **Verification attention**

#### Verification attention

Verification attentionは、次のような**技術的検証**について確認が必要なReportを示します。

例：

* Document Hash mismatch
* Event Log Hash mismatch
* Event Chain failure
* Final Chain Hash mismatch
* Anchor verification failure
* Final Signature failure
* Summary consistency mismatch

表示例：

```text
Attention
```

Verification attentionは、Process Reviewの **Review Priority** とは別の概念です。

```text
Verification attention
= 技術的検証上の確認

Review Priority
= 将来のInput Process解釈に基づく人間レビュー支援
```

Verification attentionは：

* AI利用判定
* 学術不正判定
* 剽窃判定
* 学生のRisk Score

ではありません。

Paste量が多いことだけでVerification attentionになることもありません。

### 7-4. Teacher View（教員向けダッシュボード）

Teacher Viewは、情報科学を専門としない教員でもReportの状態を確認しやすいよう、重要情報を要約して表示します。

基本構成は：

```text
Verification
↓
Process Review
↓
Writing Process
↓
Writing Timeline
↓
Document
```

です。

#### Verification

技術的な検証結果を要約します。

主な表示：

* Record integrity
* Final signature
* Writing process
* Verification attention

`Record integrity`は主にDocument / Event Log / Event Chain等のローカルな整合性を示します。

#### Process Review（Experimental）

Input Process Metricsを理解しやすく整理するためのReview Assistanceカードです。

次の3項目を**別々の軸**として表示します。

```text
Integrity
Capture Quality
Review Priority
```

これらを1つのRisk Scoreとして解釈してはいけません。

##### Integrity

次の4状態があります。

| 表示             | 意味                                                  |
| -------------- | --------------------------------------------------- |
| **✓ Verified** | Reportの整合性とFinal Signatureを検証できた                    |
| **✓ Anchored** | Reportの整合性と有効なServer Anchorを確認できた                   |
| **● Unsigned** | Report内部の整合性は確認できたが、検証済みFinal SignatureまたはAnchorがない |
| **✕ Invalid**  | Reportの整合性を確認できない                                   |

`Unsigned`はIntegrity failureではありません。

そのため警告色ではなく、中立的な表示になります。

Teacher View上部で：

```text
Record integrity
✓ Verified
```

と表示されても、Process Reviewでは：

```text
Integrity
● Unsigned
```

となる場合があります。

これは矛盾ではありません。

前者はローカルな記録整合性を示し、後者はFinal Signature / Anchorまで含む総合的なIntegrity状態を示します。

##### Capture Quality

Input Process Metricsをどの程度十分に観測・解釈できたかを示します。

表示：

* **Good**
* **Limited**
* **Insufficient data**
* **Unsupported**

`Limited`や`Insufficient data`は：

> 学生が怪しい

という意味ではありません。

例えば：

* Browser差
* IME差
* Sessionの一部が旧形式
* timing情報の欠落
* observation gap
* Semantic Validation error
* unsupported schema

等で発生します。

つまりCapture Qualityは：

> **証拠の取得・解釈可能性**

を示す指標です。

学生のRisk Scoreではありません。

##### Review Priority

将来的に、Input Process Patternを人間が確認する際の優先度を示すための領域です。

現β版ではInterpretation thresholdsをまだ有効化していないため、通常：

```text
Review Priority
Not assessed
```

と表示されます。

これは：

* 問題なし
* 問題あり
* High Risk
* Low Risk
* AI利用なし
* AI利用あり

のどれも意味しません。

単に：

> 現Experimental版では入力パターンによるReview判定を実行していない

という意味です。

Process Reviewカードには常に：

> **This is a review aid. It does not determine AI use, authorship, or misconduct.**

という趣旨の説明が表示されます。

##### Show technical details

Process Reviewカードの：

**Show technical details**

を押すとTechnical ViewのInput Process Metricsへ移動します。

#### Writing Process

次のような記録された執筆活動を表示します。

* Active time
* Sessions
* Paste activity
* Verified Internal
* Unverified

これらは評価スコアではありません。

#### Writing Timeline

SessionごとのInteraction-active time等を日単位で表示します。

これは作業の分布を確認するための参考情報であり：

* 思考時間
* 読書時間
* 学習時間
* 不正行為

を証明・判定するものではありません。

#### Document

最終的なレポート本文を表示します。

### 7-5. Technical View（技術詳細・イベントログ）

Technical ViewはTeacher Viewより詳細な技術情報を表示します。

#### Integrity Details

確認可能な項目：

* Document Hash
* Event Log Hash
* Event Chain
* Final Chain Hash
* Server Anchor
* Final Signature
* Summary consistency

Warning系の表示は：

* Integrity mismatch
* Invalid Signature
* Hash Chain failure
* Summary inconsistency

等の技術的な問題に使用されます。

Paste量が多いこと自体にWarning色は付きません。

#### Input Process Metrics（Experimental）

次のような項目を確認できます。

* Schema
* Availability
* Semantic Validation
* Character unit
* Chunk threshold
* Long-pause threshold
* Typing chunks
* Directly inserted chars
* Directly deleted chars
* Deletion operations
* IME compositions
* IME committed chars
* IME composition duration
* Input pauses
* Long pauses
* Foreground pauses
* Background observed
* Unknown visibility
* Unknown-duration pauses

これらは記録された値です。

数値の大小だけでAI利用や不正を判定するものではありません。

#### Capture notes

次のような技術的Issue Codeが表示される場合があります。

```text
input-without-beforeinput
composition-interrupted
composition-settlement-uncertain
clock-discontinuity
observation-gap
timing-unavailable
```

これらは主に：

> **観測品質・Capture Qualityに関する情報**

です。

例えば：

```text
observation-gap
```

は一部の入力過程を十分に観測できなかったことを意味します。

不正やAI利用を意味するものではありません。

#### Semantic Validation

Input Process Eventが仕様上整合しているかを検証します。

そのため：

```text
Integrity: Verified
Input Process: Partial
```

や：

```text
Integrity: Verified
Semantic Validation: Invalid
```

といった組み合わせもあり得ます。

Cryptographic IntegrityとSemantic Validityは別の概念です。

#### Session History

記録されたセッションの：

* 開始・終了時刻
* Editor Version
* Browser family
* Platform
* Input Process schema
* Capture Status

等を確認できます。

ただし：

* raw User-Agent
* IP
* Hardware ID
* Screen resolution
* 正確な位置情報

等はReportへ意図的に保存しません。

#### Detailed Writing Log

Academic Editorに記録されたEventを確認できます。

例：

* Typing Chunk
* Paste
* Delete
* Replace
* Undo / Redo
* Pause
* その他の明示操作

Event typeで絞り込みができます。

記録されたEventによっては変更前後のテキストスニペットを確認できます。

**個々の物理キー入力を1打鍵ずつ記録するキーロガーではありません。**

---

## 8. Integrity / Verification attentionの理解

### 8-1. Integrity 4状態

Process Reviewでは次の4状態を使用します。

#### Verified

Reportのローカル整合性とFinal Signatureを検証できた状態です。

#### Anchored

Reportのローカル整合性と有効なServer Anchorを検証できた状態です。

Final Signatureは検証されていない場合があります。

#### Unsigned

Report内部の整合性は確認できていますが：

* 有効なFinal Signature
* 有効なServer Anchor

が確認されていない状態です。

**Unsigned ≠ Invalid**

です。

#### Invalid

Document Hash、Event Log、Event Chain、Final Chain Hash等のIntegrityを確認できない状態です。

### 8-2. Verification attention

Verification attentionは：

> 技術的な検証項目について確認が必要

という意味です。

例：

* Hash mismatch
* Event Chain failure
* Signature invalid
* Anchor failure
* Summary mismatch

Verification attentionは：

* AI利用判定
* 不正判定
* 剽窃判定
* Review Priority

ではありません。

---

## 9. PrivacyとTrust Boundary

MD//WORKS PROVENANCEは、執筆過程のEvidenceを残しつつ、Editorを監視ツールにしないことを重視しています。

Academic Reportには次の情報を意図的に保存しません。

* IP address
* raw User-Agent
* precise location
* hardware ID
* screen resolution
* local filesystem path
* File System Access handle
* 1打鍵ごとのkey logging
* 1打鍵ごとのraw timing sequence
* IME変換途中の未確定文字列
* Emergency Recovery text

Input Process Metricsでは、個々のキー入力を時刻つきで全件保存するのではなく、Chunk単位等の集計されたProcess情報を記録します。

通常の：

* 編集
* Preview
* Save
* Word Import
* Print Preview
* Report verification

はローカルで実行できます。

Network通信は主に：

* Server Anchor
* Final Signature

に使用します。

---

## 10. よくあるトラブル・質問

### 10-1. Submitに関するトラブル

#### 「Sign & Finalize」が押せない

Ready to Submit画面の引用・出典確認チェックボックスがオンになっているか確認してください。

処理中は重複操作防止のため一時的にボタンが無効になる場合があります。

#### Finalization failedと表示される

インターネット接続や署名サーバーへの接続を確認してください。

Working Reportはそのまま編集可能です。

必要に応じてRetry Finalize、またはCancel後にSave Reportしてください。

### 10-2. IntegrityがInvalid / Signature Invalidになる

Submit後のFinalized Reportをテキストエディタ等で手動変更した場合や、ファイルが破損した場合はハッシュ・署名が一致しなくなります。

これは：

> 「不正が起きた」

という自動判定ではありません。

署名対象のEvidenceと現在のファイルが一致しないため、技術的確認が必要という意味です。

### 10-3. Unsignedは壊れたReportですか？

いいえ。

Unsignedは：

* Report内部のIntegrityは成立
* ただし検証済みFinal SignatureまたはServer Anchorがない

状態です。

Invalidとは異なります。

### 10-4. Record integrityがVerifiedなのにProcess ReviewではUnsignedなのはなぜですか？

`Record integrity`は主に：

* Document Hash
* Event Log Hash
* Event Chain
* Final Chain Hash

等のローカルな整合性を示します。

Process Reviewの`Integrity`は：

```text
Verified
Anchored
Unsigned
Invalid
```

というSignature / Anchorまで含む広い状態です。

そのため：

```text
Record integrity: Verified
Process Review Integrity: Unsigned
```

は矛盾ではありません。

### 10-5. Verification attentionとは何ですか？

Hash、Event Chain、Signature、Anchor、Summary consistency等の技術的検証について確認が必要な状態です。

学術不正判定ではありません。

Process ReviewのReview Priorityとも別です。

### 10-6. Review PriorityがNot assessedなのはなぜですか？

現β版では、Input Process Metricsから：

```text
Routine review
Context review
Manual review recommended
```

等へ分類するInterpretation Thresholdをまだ有効化していません。

そのため：

```text
Not assessed
```

が正常な表示です。

不正の有無を意味しません。

### 10-7. Capture QualityがLimitedなのは学生に問題があるという意味ですか？

いいえ。

Capture Qualityは：

> Input Processをどの程度十分に観測できたか

を示します。

Browser、IME、Session coverage、timing等によってLimitedになることがあります。

### 10-8. Unverifiedが多いと不正になりますか？

いいえ。

Unverifiedは：

> 同一Report内の先行Copy/Cutに由来するものとして検証できなかったPaste

という意味です。

外部由来、AI利用、剽窃、不正を断定することはできません。

### 10-9. オフラインでAnchors数が増えない

オフライン時は新しいServer Anchorを取得できません。

Anchor数が0であること自体はIntegrity Errorではありません。

### 10-10. 高度な数式・Mermaid・脚注がPreviewで表示されない

現β版Academic Previewでは：

* LaTeX / KaTeX
* Mermaid
* Markdown footnotes

等の専用レンダリングは保証していません。

---

## 11. セキュリティ境界

### Paste Provenanceで確認できること

確認できる：

* 同一Report内の先行Copy/Cutとの対応

確認できない：

* Paste文章の著者
* 外部由来の断定
* AI利用
* 剽窃
* 学術不正

### Input Process Metricsで確認できること

確認できる：

* Editorが観測した直接編集活動
* Direct Insert / Deleteの集計
* IME Compositionの集計
* Typing Chunk
* Pause
* Capture Status
* Capture notes

確認できない：

* 人間による執筆の証明
* 入力イベントの物理的発生源
* AI不使用の証明
* 学術不正の判定

### Editor Provenanceで確認できること

確認できる：

* Report内に記録されたEditor / Environment metadataがEvent Chainの一部として保持されていること

確認できない：

* 正規Editor binaryが実際に実行されたこと
* Runtime Attestation

### Server Anchorで確認できること

確認できる：

* 対象Event HashについてServer側が署名・時刻情報を付与したこと

意味しない：

* Report本文のCloud Backup
* 正確な本文執筆時刻
* Student identityの証明

### Final Signatureで確認できること

確認できる：

* Finalized Manifestに対するServer署名が有効であること
* 署名対象Manifestとの整合性

意味しない：

* 著者本人の身元証明
* 人間による執筆の証明
* AI不使用の証明
* 学術不正の判定

---

## 12. 実運用上の推奨

### 学生・執筆者

* **Ctrl+S** でこまめにSave Report
* 採点・審査終了までWorking Reportを保存
* 修正後は再度Submit
* Emergency Recoveryは最終手段
* 授業の引用・AI利用ルールに従う
* Input Process Metricsを自分の評価スコアと考えない

### 教員・大学

* 多人数運用では **Overview Verifier**
* 1件の詳細確認では **Report Verifier**
* UnverifiedやActive timeを判決として扱わない
* Capture Qualityを学生Riskとして扱わない
* Verification attentionは技術的確認項目として扱う
* Process ReviewのReview Priorityと区別する
* Technical Viewは根拠確認が必要な場合に使用
* `Not assessed`をRisk categoryとして解釈しない

### 研究者・個人利用者

* Writing Processを可搬なEvidenceとして残したい場合はFinalized Reportを保存
* 第三者へ説明する場合はReport Verifierと組み合わせて提示

---

## 13. β版の制限

現β版には以下の制限があります。

* Windows + Chrome / Edgeを推奨環境としています。
* BrowserによってNative File Picker、Printing、Storageの挙動が異なります。
* Input Process MetricsはExperimentalです。
* Browser、OS、IME、音声入力、アクセシビリティ入力等により観測可能性が異なります。
* Capture QualityはReportによってGood / Limited / Insufficient data / Unsupportedとなる場合があります。
* Review PriorityのInterpretation Thresholdはまだ有効化されておらず、原則`Not assessed`です。
* Japanese IMEはBrowserごとに挙動差があり得ます。
* Word tableは文字列を保持しますが、Markdown pipe tableへの変換は保証しません。
* 極端に大きい文書ではFind Highlight等が遅くなる場合があります。
* Emergency Recoveryは正式なBackupではありません。
* PDF / PrintはAcademic Report提出形式ではありません。
* 現β版は本人確認機能を提供しません。
* Input Process Metricsは、入力がPhysical Keyboard、Automation、Speech Input、Accessibility Software等のどれから来たかを認証しません。

---

## 14. MD//WORKS PROVENANCEの基本原則

MD//WORKS PROVENANCEは：

> 「これはAIが書いた文章か？」

を最終文章から推測することを目的としていません。

また：

> 「この学生本人が書いたことを証明する」

とも主張しません。

代わりに問いかけるのは：

> **どのようなWriting Processが記録され、その記録が現在も保存・署名されたEvidenceと整合しているか？**

です。

Input Process Metricsはこの考え方をさらに拡張し：

> **編集が行われたという事実だけでなく、Editorがどのような入力過程を観測したかを、可能な範囲で追加記録する**

ための機能です。

ただし：

> **観測を自動判定へ変換しない**

ことが重要な設計原則です。

MD//WORKS PROVENANCEの目的は、最終文章だけから推測することではなく、Writing ProcessそのものをEvidenceとして残すことです。
