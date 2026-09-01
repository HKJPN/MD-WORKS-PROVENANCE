# MD//WORKS PROVENANCE 取扱説明書 β版

## はじめに

MD//WORKS PROVENANCE は、学術レポートや論文のWriting Process（執筆プロセス）を記録し、その記録の整合性を検証できるようにするMarkdownエディタおよび検証ツール群です。
単なるテキスト編集にとどまらず、Interaction-active time（操作が行われていた時間）やテキストの入力方法（直接入力、Paste等）をバックグラウンドで記録し、最終提出時には改変を検知するためのサーバー署名を付与できます。本マニュアルは、レポートや論文を執筆する「学生・研究者・執筆者」と、記録されたWriting Processを確認する「教員・指導者・評価者・第三者レビュアー」の双方に向けたガイドです。

本β版は通常版MD//WORKSの操作感を維持しつつ、学術用途で誤解や提出事故を生む機能を整理したものです。学生を監視するツールではなく、自分のWriting Processを記録し、その過程を提出できるエディタとして設計されています。

---

## 1. 起動と画面構成

### 1-1. 起動とアプリ化

インストールは不要です。提供されたMD//WORKS PROVENANCE EditorのHTMLファイルをブラウザで開いて利用します。

β版では **Windows上のChromium系ブラウザ（Chrome / Edge等）を推奨環境** とします。Firefox等ではFile System Access APIの有無により、保存操作がファイル選択ではなくダウンロード方式になる場合があります。Safari / iPad等を含むブラウザ間では挙動差があり得るため、授業で指定された環境がある場合はその指示に従ってください。

ブラウザの「アプリとしてインストール」機能の利用可否は、配布方法（ローカルHTML / Web配布）やブラウザ側の仕様に依存します。MD//WORKS PROVENANCE本体の必須機能ではありません。

### 1-2. 対応環境と複数タブでの作業

複数のReportを扱う場合は、別タブや別ウィンドウでEditorを開いて作業できます。ただし、MD//WORKS PROVENANCEはクラウド上の「Workspace」を管理する仕組みではありません。

各Reportの正式な作業状態は **Save Reportで保存したHTMLファイル** に保持されます。Emergency Recoveryは各ブラウザセッション内の一時的なテキスト救済用であり、複数タブ間で共有される正式な保存領域ではありません。同じ保存先ファイルを複数のタブから同時編集・上書きする運用は避けてください。

### 1-3. 画面構成

画面は主に以下のエリアで構成されています。

1. **メニューバー / タイトルバー**: ファイル操作、提出（Submit）、全画面表示などを行います。DEEPボタンはAcademic版ではRecordボタンに置き換えられています。
2. **ツールバー**: 見出し、太字、リストなどのMarkdown装飾をワンクリックで挿入します。
3. **エディター画面 / プレビュー画面**: 左側で執筆し、右側で実際の表示を確認できます。
4. **ステータスバー**: 画面下部に `Active 24m | Unverified 22% | [Input activity bar] | Anchors: 14` を常時表示します。クリックするとCurrent Writing Recordパネルが開きます。
   - **Active**: 実際にキー操作等があった活動時間のみを計測したInteraction-active timeです。放置時間は含みません。
   - **Unverified**: Non-internal paste share（%）です。同じReport内の先行Copy/Cutと照合できなかったPasteの割合を示します。`unverifiedPasteChars / (typedChars + unverifiedPasteChars + replaceInsertedChars)` で計算され、Verified Internalは分子・分母から除外されます。最終本文の何%が外部由来かを意味するものではありません。
   - **Input activity bar**: Direct input / Verified internal / Unverified の3区分を色で示す小型バーです。
   - **Anchors**: Server Anchor（サーバー側の暗号学的証跡）の数です。クラウドバックアップではありません。

---

## 2. ファイルを作成・開く

### 2-1. 新規作成と既存ファイルを開く

新しいレポートを作成する場合は、**新しいMD//WORKS PROVENANCE Editorを開き、空のEditorから執筆を開始します。** 現β版のFileメニューには「New / 新規作成」コマンドはありません。

既存のAcademic Report（HTMLコンテナ）の続きを書く場合は、**File > Open Report** またはタイトルバーの **Open** ボタンを使用します。保存したHTMLファイルには最後にSave Reportした時点までの文書とWriting Processが含まれており、読み込み後は新しいSessionが開始され、以前の記録に続けて編集記録が追加されます。

### 2-2. Wordファイルを読み込む (.docx) 

**ファイル > Import Word (.docx)...** から、Wordファイルを読み込んでMarkdownに変換し、現在のカーソル位置へ挿入します。選択範囲がある場合は、選択範囲を削除してから挿入されます。文書全体を置換する動作ではありません。

Wordから取り込まれた文章は、既存のPasteと同様に `type: paste / provenance: unverified` として記録されます。内部的には `meta.inputSource = "word-import"` としてWord由来であることが記録されます。

読み込み完了時には、取り込んだ文字数と **Unverified inputとして記録されたこと** が通知されます。Wordファイル自体が問題だからではなく、その作成過程をAcademic Editor側で確認できないためUnverifiedになるものです。

### 2-3. Emergency Recovery（緊急テキスト復元）

ブラウザやPCの予期せぬ停止に備え、Editorはブラウザの `sessionStorage` に **最新1件の一時的な本文テキストスナップショット** を保持します。未保存の本文が失われた場合は、**File > Emergency Recovery...** を開き、救出可能なテキストがないか確認してください。

Emergency Recoveryでは、復旧テキストをクリップボードへコピーしたり、`*-recovered.md` として保存したりできます。自動的に元のReportへ復元する機能はありません。

*注意: Emergency Recoveryはテキスト救済のみの機能です。Writing Process、Event Chain、Manifest、Server Anchor、Final Signature等を復元するものではなく、正規のReportバックアップでもありません。こまめに **Save Report (Ctrl+S)** でWorking Reportを保存してください。Recoveryから取り出した文章をEditorへ貼り付ける場合は通常のPasteとして扱われ、同一Report内の先行Copy/Cutと照合できなければUnverifiedになります。*

## 3. 本文の入力と装飾（Markdown）

### 3-1. 基本的な入力と装飾

Markdown形式で文章を作成します。ツールバーを使えば、見出し（H1/H2/H3）、太字、リスト、チェックリスト、表などを簡単に挿入できます。

### 3-2. 画像の挿入について

現β版のAcademic Editorでは、**ローカル画像のBase64埋め込みや画像ファイルのPaste / Drag & Dropには対応していません。** 画像ファイルをドラッグした場合は `Local image embedding is not available in Academic mode.` と通知されます。

また、現β版のAcademic PreviewはMarkdown画像記法を画像として描画する機能を備えていません。図や画像をレポートで使用する必要がある場合は、授業・提出方法の指示に従い、必要に応じて通常のリンクとして参照してください。

この制限は、Reportの肥大化や、埋め込み画像データがVerifier処理へ与える負荷を避けるためのものです。

### 3-3. 学籍情報の記載（授業で指定される場合）

授業で指示がある場合は、レポート本文の冒頭に学籍番号・氏名等を通常の本文として記載してください。例:

```markdown
学籍番号: s123456
氏名: 山田太郎
```

これらの文字列も本文の一部としてDocument Hashの対象になります。

**現β版では、本文冒頭の学籍番号・氏名・メールを自動抽出して本人確認したり、ファイル名と自動照合したりする機能は実装されていません。** 提出ファイル名の規則（例: `学籍番号_課題名.html`）が指定されている場合は、担当教員の指示に従ってください。

本文に記載された氏名や学籍番号は、記載された本人が実際に執筆したことを証明するものではありません。

### 3-4. 学術的な装飾

現β版のAcademic Previewは、見出し、強調、取消線、インラインコード、引用、リスト、タスク、コードブロック、リンク、表、水平線などの基本的なMarkdown表示を中心にしています。

通常版MD//WORKSにある一部の高度な表示機能は、現β版Academic Editorにはまだ搭載されていません。特に、以下をAcademic Previewで自動描画することは現時点では保証していません。

- 上付き・下付き文字の専用Markdown拡張
- Markdown脚注の自動リンク表示
- LaTeX / KaTeX等による数式レンダリング
- Mermaid図のレンダリング

必要な場合は、授業で指定された記法・提出方法を使用してください。

## 4. プレビューと文書の仕上げ

### 4-1. プレビューで確認する

タイトルバーの **Preview** ボタンは、EditorとPreviewの **分割表示をオン / オフ** します。

Previewだけを大きく表示したい場合は、Preview内の **Focus** ボタン、または **View > Preview Focus** を使用します。Preview Focusからは画面右上の **↩ Editor** で編集画面へ戻れます。

つまり、Previewボタンを繰り返し押して「分割 → Focus → Editor」と循環する仕様ではありません。

### 4-2. 検索・置換と正規表現

**編集 > Find (Ctrl+F) / Replace (Ctrl+H)** で検索・置換パネルが開きます。正規表現や大文字・小文字の区別をサポートしています。

### 4-3. Markdown整形と目次の挿入

**フォーマット > Insert / Update TOC** を実行すると、文書内の見出し構造を解析し、クリック可能な目次を自動生成します。提出前の最終仕上げに便利です。

### 4-4. 各種表示モード

**表示** メニューから、テーマ（Paper / Midnight / Warm）、行番号の表示、行の折り返し、集中モード（Zen Mode）などを好みに合わせて切り替えられます。初回起動時は保存テーマがなければPaper（白背景）が選択されます。レポート執筆ツールという意識付けのためです。

---

## 5. Writing Record（執筆プロセスの記録）

Academic版の中心的な機能です。Editor上で記録対象となるWriting Processはバックグラウンドで記録され、提出後にその記録の整合性や執筆過程を確認するための材料となります。これらの値だけで著者性や不正行為を自動判定するものではありません。

### 5-1. Writing Recordとは

タイトルバーの **Record（◷）** ボタンを押すか、ステータスバーの `Active | Unverified | Anchors` 領域をクリックすると「Current Writing Record」パネルが開きます。ここでは、現在のReportについて記録されたActive timeや、どのような入力方法が記録されているかをリアルタイムで確認できます。この記録はオフにすることはできません。

パネル例:

```
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

このパネルは学生自身の振り返り用であり、不正スコアや警告画面ではありません。

### 5-2. テキスト入力の分類

入力された文字は、以下のステータスに分類されて記録されます。

* **Direct input（直接入力）**: キーボードから直接タイピングされた文字と、置換操作で挿入された文字（Replace Inserted）です。概念的には `Typed + Replace Inserted` です。最終本文中の「学生自身が書いた文字数」を意味するものではなく、編集活動としての入力量です。
* **Verified internal（検証済み内部コピー）**: 同じレポート内で先行するCopy/Cutと、`transferId / sourceHash / pastedHash / sourceEventId` が正常に対応したPasteです。同一Report内の転送であることを確認できたことを意味し、文章の著者性そのものを証明するものではありません。
* **Unverified（未検証）**: 同一Report内の先行Copy/Cutとの対応を検証できなかったPasteです。外部アプリ、Web、Word Import、別Report、Clipboard metadata loss、Browser差などが含まれ得ます。**Unverifiedだからといって外部由来だと断定できるわけではありません。** `Unverified ≠ External ≠ Improper ≠ AI ≠ Plagiarism` です。

*注意: 「Unverified」が多いからといって直ちに不正（剽窃）とは見なされません。Non-internal paste shareとして教員に提示されますが、不正スコアや自動Review条件にはなりません。引用する場合は適切な出典表記を行ってください。*

### 5-3. アクティブ執筆時間とセッション管理

* **Active time**: アプリ上で実際にキー操作等を行っていた「活動時間」のみを計測します。放置している時間はカウントされません。総学習時間、思考時間、読書時間を意味しません。
* **Sessions**: Academic Editorを起動したとき、またはReportをOpenして編集を再開したときに新しいSessionが開始されます。保存された各Sessionの開始時刻やInteraction-active time等をもとに、複数日にまたがる執筆の流れを確認できます。

### 5-4. Paste時のフィードバック

Unverified Pasteで **200文字以上** の貼り付けがあった場合、警告色ではなく通常色のToastで、貼り付けた文字数と「必要に応じて引用・出典を確認してください」という趣旨のメッセージが短時間表示されます。

これはPasteを禁止したり、不正を判定したりするものではなく、引用・出典確認を促す中立的なナッジです。Verified InternalのPasteでは、このLarge Unverified Paste通知は表示されません。

### 5-5. AIツール利用時の注意点

ChatGPT、Gemini、Claude等の生成AIからテキストをコピーしてMD//WORKS PROVENANCEへ貼り付けた場合、通常は同一Report内の先行Copy/Cutとして照合できないため **Unverified** として記録されます。

ただし、MD//WORKS PROVENANCEは **AI利用を検出・判定するツールではありません。** UnverifiedはAI生成、外部由来、剽窃、不正を意味する分類ではありません。

生成AIによる文章生成、翻訳、校正、要約等をどこまで利用できるかは、大学・授業・課題ごとのAI利用ポリシーに従ってください。必要な場合はAI利用の申告や引用・出典表記も担当教員の指示に従ってください。

## 6. 保存とレポート提出（Submit）

### 6-1. 作業中の保存（Save Report）

執筆途中のデータを保存する場合は、必ず **ファイル > Save Report (Ctrl+S)** を使用してください。保存されるのは、文書本文とWriting Process（Event Chain, Summary, Manifest等）を含んだ**提出用と同じHTMLコンテナ形式の作業用ファイル**です。Markdown .mdファイルやPDFではありません。

ブラウザのタブを閉じると未保存のデータは失われる可能性があります。Emergency Recoveryは自動バックアップではないため、こまめな保存が必要です。オフラインでも保存できます。

### 6-2. 別PCでの継続

Save Reportで保存したWorking ReportのHTMLファイルを移動すれば、別のPCやブラウザでも編集を再開できます。Report内には最後に保存した時点までの文書とWriting Processが含まれており、開いた後は新しいSessionとして編集が続きます。Emergency Recoveryの一時データは引き継がれません。

### 6-3. Submit Report（最終提出レポートの作成）

レポートが完成し、教員へ提出する準備ができたら以下の操作を行います。

1. タイトルバーの **Submit（✓）** ボタン、または **File > Submit Report** を選択します。
2. 「Ready to Submit」画面が表示され、総文字数、Active writing time、Sessions、Unverified Paste文字数、Non-internal paste shareなどが最終確認として表示されます。
3. 引用や参考文献の記載を確認し、チェックボックス **`I have checked any citations and source references required for this document.`** をオンにします。このチェックは確認画面だけで使用され、ReportやWriting Processへ保存されません。
4. **Sign & Finalize** ボタンを押します。
5. File System Access API対応ブラウザでは、まず提出用HTMLの保存先を選択します。その後サーバーへFinal Signatureを要求し、署名済みの **Finalized Report** を選択した保存先へ書き込みます。File System Access API非対応ブラウザでは、Finalized Reportのダウンロードが開始されます。

Final Signatureは「ファイルを改ざん不可能にする」ものではなく、**署名後に内容が変更された場合にVerifierで不整合を検出できるようにするための暗号署名（Ed25519）** です。Submitにはインターネット接続が必要です。

Submitは必要に応じて何度でも実行できます。Submit後に誤字を発見した場合は、そのまま編集を続け、通常SaveするとWorking Reportとして保存されます。修正後に再度Submitすると、新しい時刻・Manifest・Final Signatureを持つFinalized Reportが作成されます。実際に提出するときは、最後に作成した最新のFinalized Reportを提出してください。SubmitでEditorがロックされることはありません。

### 6-4. 印刷 / PDFへの書き出し

**ファイル > Print Preview... (Ctrl+P)** から、レポートを印刷、またはPDFとして保存できます。Previewからの印刷のみ維持しており、Markdownソースの直接印刷は削除されています。

*注意: Print Previewには `Print output is for review or proofreading. It is not an Academic Report submission.` と表示されます。PDF/印刷物にはAcademic ReportのWriting ProcessやFinal Signatureを検証するための提出情報は含まれないため、Academic HTML形式での提出が指定されている場合はFinalized HTMLを提出してください。教員の指示に従ってください。*

---

## 7. Report Verifier / Overview Verifier の使い方

Academic Report（HTMLファイル）について、**Writing Process記録の整合性、Hash Chain、Manifest、Server Anchor、Final Signature等を検証し、記録された執筆過程を確認するための専用ツール群**です。著者本人であることや学術不正の有無を自動的に証明・判定するツールではありません。

Verifierには用途の異なる2つの画面があります。

- **Report Verifier**: 1つのReportを詳細に確認するためのVerifier。学生、研究者、著者などが、自分のWriting Processを第三者へ説明・提示する用途にも向きます。
- **Overview Verifier**: 複数のReportを一覧化し、必要なReportを選んで詳細確認するためのVerifier。授業や大学等の多人数運用に向きます。

両者は同じEvidence modelを検証します。Overview VerifierはReport Verifierの検証内容を実質的に内包していますが、Report Verifierには**単一Reportをシンプルに提示・共有する独立した用途**があります。

### 7-1. 検証ツールの役割と起動方法

提供される `report-verifier.html`（単一Report確認用）と `overview-verifier.html`（一覧・詳細確認用）をブラウザで開いて使用します。

VerifierはReportに含まれるDocument / Events / Manifest等からSHA-256ハッシュやEvent Chainを独立再計算し、Reportに含まれるServer AnchorやFinal Signatureを、同梱された信頼済み公開鍵を用いて **Ed25519署名検証** します。これらの検証処理はブラウザ内でローカルに行われ、検証のためにReport本文をサーバーへ送信して照合する仕組みではありません。

なお、**SHA-256はハッシュ、Ed25519は電子署名方式**であり、役割が異なります。

### 7-2. Report Verifier（単一Reportの検証）

1つのAcademic Reportを読み込み、Writing ProcessとIntegrityを詳細に確認します。

主な用途は以下です。

* 学生が卒業論文・修士論文等のWriting Processを指導教員へ提示する
* 研究者が原稿や論文の作成過程を共同研究者等へ説明する
* 著者やライターが原稿の作成過程を編集者・依頼者へ示す
* 個人が将来のために、自分のWriting Processを検証可能な形で保存・確認する
* 教員が1件だけ提出Reportを詳細確認する

Report Verifierでは、Document Hash、Event Log Hash、Event Chain、Final Chain Hash、Summary consistency、Server Anchor、Final Signature等を独立検証し、Teacher View / Technical ViewからWriting Processを確認できます。

Report Verifierは、Reportの著者本人であることやAI利用、不正行為を判定するものではありません。**「このReportに記録されているWriting ProcessとIntegrity情報が整合しているか」を第三者が確認するためのツール**です。

### 7-3. Overview Verifier（一括読み込み・バッチ評価）

複数のAcademic Report HTMLをまとめてドラッグ＆ドロップすると、Master–Detail形式の一覧が生成されます。

主な一覧項目は以下です。

* **Student**: ManifestにstudentIdが記録されているReportではその値を表示し、存在しない場合は元ファイル名を表示名として使用します。
* **Active time**
* **Sessions**
* **Non-internal paste share**
* **Process**
* **Integrity**
* **Signature**
* **Review**

各見出しからソートでき、行を選択すると下段/右側の詳細表示でTeacher View / Technical Viewを確認できます。処理できなかったHTMLはReport一覧とは分離して表示されます。

**現β版Overview Verifierは、本文冒頭から学籍番号・氏名・メールを自動抽出したり、ファイル名と学籍番号をMatch / Mismatch / Missing判定したりする機能は備えていません。**

Reviewは、Integrity、Signature、Summary consistency等で技術的に確認が必要な状態を絞り込むための表示です。Paste量が多いことだけでReview扱いになるものではなく、Reviewは学術不正判定を意味しません。

大学・授業など多数の提出物を確認する場合は、Overview Verifierだけで一覧確認から個別詳細確認まで完結できます。単一Reportだけを第三者へ提示する場合は、よりシンプルなReport Verifierが適しています。

### 7-4. Teacher View（教員向けダッシュボード）

* **Verification**: Document Hash、Event Log Hash、Event Chain、Final Chain Hash、Anchor、Final Signature等の検証結果を要約して表示します。
* **Writing Process**: Active time、Sessions、Direct input、Verified internal、Unverified、Non-internal paste share等、記録された執筆活動を表示します。
* **Writing Timeline**: SessionごとのInteraction-active time等を日単位のタイムラインとして表示します。これは執筆時期や作業の分布を確認するための参考情報であり、思考時間を証明したり、不正行為を判定したりするものではありません。
* **Document**: 最終的なレポート本文を表示します。

### 7-5. Technical View（技術詳細・イベントログ）

* **Integrity Details**: 独立して再計算されたドキュメントハッシュやイベントログハッシュ、Final Chain Hashの整合性、Server Anchorの検証、Final Signatureの検証結果を確認できます。Warning色が使われるのは `Integrity mismatch / Invalid Signature / Hash Chain failure / Summary inconsistency` など技術的な確認が必要な状態のみです。Paste量が多いこと自体にWarning色は付きません。
* **Session History**: 記録されたセッションの開始・終了時刻、OSやブラウザの粗粒化された情報（Editor Provenance）を確認できます。raw User-Agent、IP、ハードウェアID、画面解像度、正確な位置情報は保存しません。
* **Detailed Writing Log**: Academic Editorに記録されたEvent（一定単位にまとめられたTyping、Paste、Delete、Undo/Redo等）を確認できるログです。「Event type」で絞り込みができ、記録されたEventによっては変更前後のテキストスニペットを確認できます。個々の物理キー入力を1打鍵ずつ記録するキーロガーではありません。

---

## 8. よくあるトラブル

### 8-1. 提出（Submit）に関するトラブル

* **「Sign & Finalize」が押せない**: Ready to Submit画面の引用・出典確認チェックボックスがオンになっているか確認してください。処理中は重複操作防止のため一時的にボタンが無効になる場合があります。インターネット未接続の場合、チェック後にボタン自体は押せてもFinal Signature取得時に失敗し、再試行画面が表示されます。
* **Finalization failedと表示される**: インターネット接続や署名サーバーへの接続を確認してください。Working Reportはそのまま編集可能です。画面の **Retry Finalize** で再試行するか、いったんCancelして必要に応じてSave Reportしてください。
* **Report Verifier / Overview VerifierでIntegrityがBroken / Signature Invalidになる**: Submit後のFinalized Reportをテキストエディタ等で手動変更した場合や、ファイルが破損した場合はハッシュ・署名が一致しなくなります。これは「不正」と自動判定する表示ではなく、**提出後の内容が署名対象と一致しないため確認が必要**という意味です。修正が必要な場合はMD//WORKS PROVENANCEでWorking Reportを編集し、再度Submitしてください。

### 8-2. ペーストの出所（Unverified）に関する疑問

* **Unverifiedが多いと不正になりますか？**: いいえ。Unverifiedは「このPasteを、同一Report内の先行Copy/Cutに由来するものとして検証できなかった」という意味です。外部アプリ、Web、Word Import、別Report、Clipboard metadata loss、ブラウザ差などが含まれ得ますが、**Unverifiedだけから外部由来、AI利用、剽窃、不正を断定することはできません。** 必要な引用・出典やAI利用ルールについては、授業・課題の指示に従ってください。

### 8-3. その他のトラブル

* **高度な数式・Mermaid・脚注がPreviewで期待どおり表示されない**: 現β版Academic Previewでは、LaTeX / KaTeX数式、Mermaid図、Markdown脚注等の専用レンダリングは搭載していません。基本Markdown中心のPreviewです。
* **オフラインでAnchors数が増えない**: オフライン時は新しいServer Anchorを取得できないため、Status barの `Anchors` 数が増えない場合があります。Anchor数が0であること自体はIntegrity Errorではありません。現β版Status barには、`Offline` / `Locally Saved` といった専用接続状態ラベルは表示しません。
* **Submitできない**: Working Reportの編集・通常Saveはオフラインでも可能ですが、Final Signatureを取得するSubmitにはインターネット接続が必要です。

## 付録

### 付録1. 生成AIとの併用について

MD//WORKS PROVENANCEは、ChatGPT、Gemini、Claude等の生成AIツールと併用すること自体を技術的に禁止していません。ただし、生成AIからコピーした文章を貼り付けた場合、通常は同一Report内の先行Copy/Cutとして照合できないためUnverifiedとして記録されます。

MD//WORKS PROVENANCEはAI利用を検出するツールではありません。文章生成、翻訳、校正、要約、構成レビュー等をどこまで利用できるかは、**大学・授業・課題ごとのAI利用ポリシーを優先**してください。機密性の高い研究情報を外部AIサービスへ入力する場合は、所属機関の情報管理ルールにも従ってください。

### 付録2. セキュリティ境界

- **Paste Provenanceで確認できること**: 同一Report内の先行Copy/Cutとの対応。確認できないこと: Pasteされた文章の著者、外部由来の断定、AI利用、学術不正。
- **Editor Provenanceで確認できること**: Report内に記録されたEditor/environment metadataがEvent Chainの一部として保持され、その後の改変を検出できること。確認できないこと: 正規Editor binaryが実際に実行されたことやRuntime attestation。
- **Server Anchorで確認できること**: 対象Event HashについてServer側が署名・時刻情報を付与したこと。意味しないこと: Report本文のCloud Backup、正確な本文執筆時刻、Student identityの証明。
- **Final Signatureで確認できること**: Finalized Manifestに対するServer署名が有効であり、署名対象のManifestとの整合性を検証できること。意味しないこと: 著者本人の身元証明や学術不正の判定。
