# MD//WORKS PROVENANCE — よくある質問

MD//WORKS PROVENANCEはWriting Processを記録し、後から整合性を確認できる証拠として保持するためのツールです。著者、AI利用、剽窃、不正、本人性を自動判定・証明するものではありません。

操作方法は[取扱説明書](./Manual-ja.md)、製品全体の概要は[README](./README-ja.md)を参照してください。

## 1. 「Unverified（未検証）」とは何ですか？

同じReport内で先に記録されたCopy/Cutイベントと照合できなかったPasteを指します。これはPaste Provenance上の分類であり、文章への評価ではありません。

- Unverified ≠ 外部由来
- Unverified ≠ AI
- Unverified ≠ 剽窃
- Unverified ≠ 不正

## 2. Unverifiedの割合が高いと、不正やAI利用を疑われますか？

割合だけでは判断できません。Non-internal paste shareは、記録された入力過程を説明するための指標です。AI確率、剽窃率、不正スコアではなく、授業・大学・研究・出版等の方針と文脈に沿って確認する必要があります。

## 3. Verified Internal Pasteとは何ですか？

同じReport内で記録された先行Copy/Cutと正常に照合できたPasteです。Report内での移動・再利用を示しますが、その文章を誰が書いたかまで証明するものではありません。

## 4. Microsoft WordやGoogle Docsで先に書いてもよいですか？

技術的には可能です。ただし、外部で作成した文章をMD//WORKS PROVENANCEへ読み込んだり貼り付けたりすると、それ以前の執筆過程をEditorが観測していないため、通常はUnverifiedになります。許可される作業方法は授業・大学等のルールに従ってください。

## 5. Word ImportがUnverifiedになるのはなぜですか？

MD//WORKS PROVENANCEはWord文書を読み込んだ操作自体は記録できますが、そのWord文書がどのように作成されたかは再構成できません。そのため、読み込まれた文章はUnverifiedとして扱われます。

## 6. Non-internal paste shareとは何ですか？

概念上は次の割合です。

```text
unverifiedPasteChars /
(typedChars + replaceInsertedChars + unverifiedPasteChars)
```

Verified Internal Pasteは分子・分母から除外されます。この値は、最終文章に占める外部文章の割合、AI生成率、剽窃率、不正確率ではありません。

## 7. どのファイルを提出すればよいですか？

MD//WORKS PROVENANCE形式での提出を求められた場合は、通常は**Finalized Report（`.html`）**を提出します。PDFやMarkdownには同じ検証可能なWriting Process一式が含まれません。最終的には授業、指導者、投稿先等の指示を優先してください。

## 8. Save ReportとSubmit Reportの違いは何ですか？

**Save Report**は、再度開いて編集を続けられる**Working Report**を保存します。**Submit Report**は、その時点の状態からFinal Signature付きの新しい**Finalized Report**を作成します。署名後の変更は検出できますが、HTMLそのものを変更不能にするわけではありません。

## 9. Submitは複数回できますか？

はい。再度Submitすると、その時点の内容に対して新しいfinalization記録とFinal Signatureが作成されます。

## 10. Submit後に編集するとどうなりますか？

Editorで引き続き編集できます。その後に通常のSaveを行うと、現在の作業はWorking Report状態になります。新しい提出版が必要になったら、もう一度Submitしてください。

## 11. Saveすると、どこまで履歴が残りますか？

最後にSaveが正常完了した時点までのWriting ProcessがWorking Reportに保存されます。それ以後の未保存変更はそのファイルに含まれません。Emergency Recoveryは別機能であり、Writing Processのバックアップではありません。

## 12. Active timeとは何ですか？

Editorが記録したinteraction-active timeです。参考値であり、勉強、思考、調査、読書、学習に費やした総時間を証明するものではありません。

## 13. レビュアーには何が見えますか？

Reportに応じて、Document、Sessions、Writing Process概要、Copy/Cut/PasteのPaste Provenance、Active time、Timeline、整合性検証、Server Anchor、Final Signature、Detailed Writing Logを確認できます。一部イベントには、変更内容を説明するための限定的なbefore/afterテキスト断片が含まれる場合があります。1キーごとのキーロギングは行いません。

## 14. 文書全体がサーバーへアップロードされますか？

通常の編集と検証はlocal-firstです。ネットワーク通信はServer AnchorとFinal Signatureのために使用されます。整合性を検証するだけの目的で、Report全文を第三者の検証サービスへアップロードする必要はありません。

Academic Reportには、IPアドレス、raw User-Agent、ハードウェア識別子、正確な位置情報、画面解像度、ローカルファイルパス、File System Access handle、1キーごとの入力ログ、Emergency Recovery本文を意図的に保存しません。ただし、通常のホスティングやネットワークのサーバーログは別の運用上の問題であり、運営者の方針に従います。

## 15. オフラインで利用できますか？

編集、ローカルSave、Preview、Word Import、Print Preview、検証はローカルで動作できます。新しいServer Anchorの取得とSubmit / Final Signatureにはネットワーク接続が必要です。

有効なServer Anchorは、対象ハッシュが記録されたサーバー時刻以前にサーバーへ到達していたことを支持します。文章が書かれた正確な時刻を証明するものではありません。

## 16. Emergency Recoveryとは何ですか？

ブラウザのsession storageに保持される、一時的なテキスト復旧用スナップショット1件です。中断時の文章救済だけを目的とします。

## 17. Emergency Recoveryはバックアップですか？

いいえ。Events、Sessions、Event Chain、Manifest、Server Anchors、Final Signatureは復元しません。Working Reportを定期的にSaveしてください。

## 18. 復旧したテキストをEditorへ貼り戻すとどうなりますか？

通常のPasteとして処理されます。同じReport内の先行Copy/Cutと照合できなければ、通常はUnverifiedとして記録されます。

## 19. 別のPCやブラウザで作業を続けられますか？

はい。保存済みのWorking Reportを移動して開けば続行できます。Emergency Recoveryのデータは作成されたブラウザセッション内に留まり、Reportと一緒には移動しません。

## 20. Working ReportとFinalized Reportの両方を保管すべきですか？

はい。採点、指導、編集、研究レビュー等が終わるまではWorking Reportを保管し、実際に提出・共有したFinalized Reportもそのまま残すことを推奨します。

## 21. 印刷物やPDFはAcademic Reportですか？

それだけではAcademic Reportではありません。印刷・PDFは閲覧、校正、別途指定された提出に利用できますが、Academic Report HTMLと同じ検証可能なWriting Process一式は含みません。

## 22. MD//WORKS PROVENANCEはAIを検出しますか？

いいえ。最終文章をAI／人間と分類するのではなく、記録されたWriting Processを提示します。AI利用の可否は授業、大学、研究、出版等の方針に従います。

## 23. 有効なReportなら、誰が書いたか証明できますか？

できません。有効なEvent Chain、一致するHash、Server Anchor、Final Signatureは、記録された証拠の整合性を支持しますが、著者、本人性、公式Editorが実行された事実、学術的な適切性を証明しません。MD//WORKS PROVENANCEはtamper-evident（改変を検知可能）であり、tamper-proof（変更不能）ではありません。

## 24. Report VerifierとOverview Verifierはどう使い分けますか？

**Report Verifier**は、単一Reportの検証・提示に使います。学生から指導者、研究者から共同研究者、著者から編集者・依頼者、個人から第三者レビュアーへの提示や、教員による提出物1件の詳細確認に適しています。

**Overview Verifier**は、複数Reportの一覧確認・詳細検証に使います。授業、コース、大学等での一括読込、first-pass review、一覧から選んだReportの詳細確認に適しています。

Overview Verifierは同じ詳細検証機能を含み得ますが、Report Verifierには単一Reportをシンプルに持ち運び、提示・検証する独立した用途があります。

