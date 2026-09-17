# MD//WORKS PROVENANCE Sample Reports

日本語訳が[英語の文末](https://github.com/HKJPN/MD-WORKS-PROVENANCE/blob/main/samples/README.md#mdworks-provenance-%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB%E3%83%AC%E3%83%9D%E3%83%BC%E3%83%88)に続きます。  
The Japanese translation follows the English text.

[These synthetic sample Reports](https://github.com/HKJPN/MD-WORKS-PROVENANCE/tree/main/samples) are provided for evaluation and demonstration.

---

## What these three samples show

MD//WORKS PROVENANCE records information about the writing process and also checks whether the submitted Report is still consistent with the record created at finalization.

| Sample | In plain language | Main point |
|---|---|---|
| `01-normal-finalized-report.html` | A normal finalized Report | Writing record and integrity verification are normal. However, It does not guarantee original authorship or that it wasn't ghostwritten. |
| `02-high-unverified-report.html` | A Report containing **a large amount of Paste** that could not be matched to an earlier Copy/Cut in the same Report | **Need for further inspection** |
| `03-integrity-broken-report.html` | A finalized Report whose document text **was modified after finalization** | **Need for further inspection** |

<img src="https://github.com/HKJPN/MD-WORKS-PROVENANCE/blob/main/images/SamplePicE.jpg" alt="SamplePicE.jpg" width="100%"><br>
Figure 1. Three sample files were verified by the `overview-verifier.html`.

### Two important distinctions

> **A high Non-internal paste share does not always mean AI use, plagiarism, or misconduct.**

It means only that some pasted text could not be verified as originating from an earlier Copy/Cut event within the same Report. However, further follow-up with **the author is recommended to determine the reason for such a high proportion of non-internal paste.** For such purposes, more detailed information about the writing process can be viewed from the Technical View tab of the Verifier (indicated by the red frames in the figure).

Likewise:

> **Integrity Broken does not by itself mean academic misconduct.**

It means that the current Report does not match the integrity information recorded at finalization.  Nevertheless, **the reason for the modification must be confirmed, as it could indicate either unintentional file corruption or deliberate tampering.**　You should inspect the writing process using the Technical View tab of the Verifier (indicated by the red frames in the figure)

---

# 01-normal-finalized-report.html

A normally finalized Academic Report.

## Expected result

- **Active time:** 14 min
- **Non-internal paste share:** 0%
- **Process:** Recorded
- **Integrity:** Verified
- **Signature:** Verified
- **Verification attention:** -

## How to read this result

This is the normal example.

The writing process was recorded, the current document matches the integrity information stored in the Report, and the Final Signature can be verified. Use this file to confirm the normal verification workflow. However, It does not guarantee original authorship or that it wasn't ghostwritten.

---

# 02-high-unverified-report.html

A valid Finalized Report containing a high proportion of Unverified Paste.

## Expected result

- **Non-internal paste share:** 85%
- **Process:** Recorded
- **Integrity:** Verified
- **Signature:** Verified
- **Verification attention:** -

## How to read this result

The Report itself is intact.

Its integrity and Final Signature are valid.

However, a large proportion of the recorded input consists of Paste that could not be matched to an earlier Copy/Cut event in the same Report.

In MD//WORKS PROVENANCE:

**Unverified Paste means only that the Paste could not be verified against a previous Copy/Cut event in that same Report.**

It does **not** by itself mean:

- AI-generated text
- plagiarism
- academic misconduct
- unauthorized assistance
- broken file integrity

The pasted text may have come from Word, another document, a Web page, another application, or another source. MD//WORKS PROVENANCE does not identify the original source.

When the proportion is unusually high, an instructor may choose **to ask the author for context if this is relevant to the assignment or review process.**


---

# 03-integrity-broken-report.html

A Finalized Report that was intentionally modified after finalization.

The document text inside the Report was changed after the Final Signature had already been created.

## Expected result

- **Non-internal paste share:** 0%
- **Process:** Recorded
- **Integrity:** Broken
- **Signature:** Verified
- **Verification attention:** Attention

## How to read this result

The writing record and Final Signature are still present.

However, the current document no longer matches the document hash recorded at finalization.

For that reason, the overall Report integrity is shown as **Broken**.

This sample is provided only to demonstrate tamper detection.

Integrity Broken does not by itself determine:

- why the file changed
- who changed it
- whether the change was intentional
- whether academic misconduct occurred

**The reason for the difference should be checked separately.**

Possible explanations include accidental file modification, file corruption, or deliberate tampering.

---

# Quick glossary

| Verifier display             | Plain-language meaning                                                                                                        |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Process / Recorded**       | Writing-process information was recorded in the Report                                                                        |
| **Non-internal paste share** | The proportion of recorded text input consisting of Paste that could not be matched to an earlier Copy/Cut in the same Report |
| **Integrity / Verified**     | The current Report is consistent with the integrity information recorded in it                                                |
| **Integrity / Broken**       | At least one integrity check does not match the recorded information                                                          |
| **Signature / Verified**     | The Final Signature over the final manifest was successfully verified                                                         |
| **Verification attention**   | One or more verification checks require attention                                                                             |

### Important limitation

MD//WORKS PROVENANCE verifies recorded evidence about the writing process and Report integrity.

It does **not** by itself prove authorship, determine AI use, or determine academic misconduct.

---

# MD//WORKS PROVENANCE サンプルレポート

[これらのサンプルレポート](https://github.com/HKJPN/MD-WORKS-PROVENANCE/tree/main/samples)は、評価およびデモを目的として実際にMD//WORKS PROVENANCEで作成されたファイルです。

---

## この3つのサンプルで分かること

MD//WORKS PROVENANCEでは、

1. **どのように文章が入力・編集されたかという執筆記録**
2. **最終化されたReportが、その後も記録された内容と一致しているかという整合性**

を別々に確認します。

| サンプル                              | 一言でいうと                                     | 何を意味し、何をするべきか？                       |
| --------------------------------- | ------------------------------------------ | ------------------------------- |
| `01-normal-finalized-report.html` | 正常に最終化されたReport                            | 執筆記録、整合性、最終署名が正常。ただし、他人の執筆の可能性等まで否定できるものではない  |
| `02-high-unverified-report.html`  | このReport内と照合できない **Copy/Pasteが多い** Report | 必ずしもAI利用、盗用、不正行為とは限らないが、 **要執筆過程確認** |
| `03-integrity-broken-report.html` | **最終化後に本文が変更** されたReport                        | 必ずしも改ざんとは限らなないが、 **要執筆過程確認**　|

<img src="https://github.com/HKJPN/MD-WORKS-PROVENANCE/blob/main/images/SamplePicJ.jpg" alt="SamplePicJ.jpg" width="100%"><br>
Figure 1. `overview-verifier`により3つのサンプルファイルを確認している状態を示した。

### 最初に覚えていただきたい2つの点

> **「非内部貼り付け割合」が高いことは、AI利用、盗用、不正行為を必ずしも意味するものではありません。**

これは、そのPasteが **同じReport内で以前に行われたCopy/Cutと照合できなかった** ことだけを意味します。しかしながら、**なぜこれほど非内部貼り付けの割合が高いのか、執筆者への確認等の対応が必要**と考えられます。このような目的のために、**Verifierの技術表示タブ** から執筆過程のより詳細を表示することができます(図中赤枠タブ)。	

また、

> **「整合性不一致」は、それだけで学業上の不正行為を意味するものではありません。**

これは、現在のReportが、最終化時に記録された整合性情報と一致していないことを意味します。**この場合、意図しないファイルの破損や意図的な改ざんの可能性も含め、変更が加えられた過程の確認が必要となります。**　Verifierの技術表示タブから執筆過程の確認が確認が可能です(図中赤枠タブ)。

--

# 01-normal-finalized-report.html

正常に最終化（ファイナライズ）されたAcademic Reportです。

## 期待される結果

* **非内部貼り付け割合:** 0%
* **執筆記録:** 記録あり
* **整合性:** 検証済み
* **最終署名:** 検証済み
* **検証上の確認:** -

## この結果の読み方

これは通常の状態を示すサンプルです。

執筆過程が記録されており、現在の本文とReport内に保存された整合性情報が一致し、最終署名も正常に検証されています。通常の検証ワークフローが正しく動作することを確認するために使用してください。ただし、他人の執筆の可能性等まで否定できるものではないことに注意が必要です。

---

# 02-high-unverified-report.html

「非内部貼り付け（Unverified Paste）」の割合が高い、有効な最終化済みReportです。

## 期待される結果

* **非内部貼り付け割合:** 85%
* **執筆記録:** 記録あり
* **整合性:** 検証済み
* **最終署名:** 検証済み
* **検証上の確認:** -

## この結果の読み方

このReport自体の整合性には問題ありません。

本文の整合性も最終署名も正常に検証されています。

一方で、記録された入力のかなりの部分が、**このReport内で以前に行われたCopy/Cutと照合できないPaste**として記録されています。

MD//WORKS PROVENANCEにおける「非内部貼り付け」とは、

**同じReport内で以前に行われたCopy/Cutとの対応を確認できなかった貼り付け**

という意味です。

それだけで、以下を意味するものではありません。

* AIによる文章生成
* 盗用
* 学業上の不正行為
* 不適切な支援
* ファイルの整合性破損

貼り付け元としては、Word、別の文書、Webページ、他のアプリケーションなど、さまざまな可能性があります。MD//WORKS PROVENANCEは、そのPasteがどこから来たのかを特定しているわけではありません。**割合が非常に高い場合には、課題の目的や確認の必要性に応じて、執筆者に理由や経緯を確認する**ことが考えられます。


---

# 03-integrity-broken-report.html

最終化のあとに、意図的に本文へ変更が加えられたReportです。

このサンプルでは、最終署名が作成されたあとに、Report内の本文の一部を変更しています。

## 期待される結果

* **非内部貼り付け割合:** 0%
* **執筆記録:** 記録あり
* **整合性:** 整合性不一致
* **最終署名:** 検証済み
* **検証上の確認:** 確認が必要

## この結果の読み方

執筆記録と最終署名は残っています。

しかし、現在の本文が、最終化時に記録された本文と一致していません。

そのため、Report全体の整合性は **「整合性不一致」** と判定されます。

このサンプルは、改変検知の仕組みを確認するためのものです。

「整合性不一致」という結果だけから、以下を判断することはできません。

* なぜファイルが変更されたのか
* 誰が変更したのか
* 意図的な変更だったのか
* 学業上の不正行為があったのか

**意図しない編集、ファイル破損、意図的な改変など複数の可能性があるため、変更理由は別途確認する必要があります。**

---

# 用語の簡単な説明

| Verifierの表示                              | 簡単な意味                                              |
| ---------------------------------------- | -------------------------------------------------- |
| **執筆記録 / Process: Recorded**             | 文章を書いた過程に関する情報がReportに記録されている                      |
| **非内部貼り付け割合 / Non-internal paste share** | このReport内の以前のCopy/Cutと照合できなかったPasteが、記録された入力に占める割合 |
| **整合性 / Integrity: Verified**            | 現在のReportが、保存された整合性情報と一致している                       |
| **整合性 / Integrity: Broken**              | 1つ以上の整合性確認で、保存された情報との不一致が確認された                     |
| **最終署名 / Signature: Verified**           | 最終Manifestに付与された署名を正常に検証できた                        |
| **検証上の確認 / Verification attention**      | 技術的な検証結果に、確認すべき項目がある                               |

### 重要な限界

MD//WORKS PROVENANCEが確認するのは、**記録された執筆過程とReportの整合性**です。

それだけで、

* 本人が執筆したこと
* AIを使用していないこと
* 学業上の不正行為がないこと

を証明・判定するものではありません。
