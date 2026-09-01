# MD//WORKS PROVENANCE
## User Guide — Beta

MD//WORKS PROVENANCE is a Markdown writing environment designed to preserve a **tamper-evident record of the writing process**.

It records writing activity such as sessions, direct input, editing events, and Paste provenance, then packages that evidence together with the document in a self-contained HTML Report. A Finalized Report can later be checked with a browser-based Verifier.

MD//WORKS PROVENANCE is not an AI detector, plagiarism detector, misconduct detector, or identity-proofing system.

Its purpose is narrower and more transparent:

> **Record the writing process. Preserve the evidence. Make later changes detectable.**

This guide is written for:

- students and academic writers
- researchers and authors
- instructors and supervisors
- third-party reviewers who need to inspect a Report
- institutions reviewing many submitted Reports

---

# 1. Before you start

## 1.1 Recommended environment

MD//WORKS PROVENANCE runs as a self-contained HTML application in a web browser.

For the current beta, the recommended environment is:

- **Windows**
- **current Chrome or Edge**

Other browsers may still work, but some file operations differ depending on File System Access API support.

For example:

- Chrome / Edge may open a native Save dialog and remember the selected file handle during the current session.
- Browsers without File System Access API support may use a download-based fallback instead.

If your course or institution specifies a browser, follow that instruction.

## 1.2 No installation is required

Open the provided MD//WORKS PROVENANCE Editor HTML file in your browser.

Depending on how the application is distributed, your browser may also offer an “install as app” option. This is optional and is not required for MD//WORKS PROVENANCE itself.

## 1.3 One Report at a time

You may open multiple browser tabs or windows for different Reports.

However, MD//WORKS PROVENANCE is not a cloud workspace manager. Each Report is stored in its own HTML file.

Avoid editing the same saved Report simultaneously in multiple tabs and then saving both copies to the same file.

---

# 2. The basic workflow

The normal workflow is:

```text
Open Editor
   ↓
Write / Edit / Paste
   ↓
Save Report
   ↓
Working Report (.html)
   ↓
Submit Report
   ↓
Final Signature
   ↓
Finalized Report (.html)
   ↓
Verify with Report Verifier or Overview Verifier
```

There are two kinds of Report files.

## 2.1 Working Report

A **Working Report** is the editable version used during writing.

It contains:

- the current Markdown document
- recorded writing events
- session information
- summary information
- integrity metadata

A Working Report can be reopened later and writing can continue in a new Session.

## 2.2 Finalized Report

A **Finalized Report** is created through **Submit Report**.

It includes a server-issued Final Signature and is intended to be the version submitted or shared for verification.

Finalization does not permanently lock the Editor. You can continue editing afterward, but the next ordinary Save produces a Working Report again, without reusing the previous Final Signature.

---

# 3. The Editor interface

The interface is divided into several areas.

## 3.1 Menu and title bar

The top of the window contains the main file and view controls.

Typical actions include:

- Open Report
- Save Report
- Save Report As…
- Submit Report
- Import Word (.docx)…
- Print Preview…
- Emergency Recovery…
- Preview / Preview Focus
- Record
- Fullscreen / Zen-related display controls

## 3.2 Formatting toolbar

The toolbar provides shortcuts for common Markdown structures, including:

- headings
- bold
- italic
- strikethrough
- inline code
- block quote
- bullet list
- numbered list
- task list
- code block
- link
- table
- horizontal rule
- table of contents

## 3.3 Editor and Preview

The Editor is where you write Markdown.

The **Preview** button toggles split Preview on and off.

To display only the Preview, use **Preview Focus**. From Preview Focus, use **↩ Editor** to return.

## 3.4 Status bar

The status bar provides a compact view of the current Writing Record.

A typical example looks like:

```text
Active 24m | Unverified 22% | [Input activity] | Anchors: 14
```

These values are descriptive process information, not a score.

---

# 4. Writing Record

Writing Record is the central feature of the Academic edition.

Open it using the **Record** button or by selecting the Writing Record area in the status bar.

A typical panel may show:

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

These values describe what MD//WORKS PROVENANCE recorded during the writing process.

They do **not** determine authorship, AI use, plagiarism, or misconduct.

---

# 5. Understanding input classifications

## 5.1 Direct input

**Direct input** represents recorded writing activity such as:

- keyboard input
- text inserted through Replace operations

Conceptually, it includes:

```text
Typed characters
+
Replace-inserted characters
```

This is a measure of recorded input activity.

It does **not** mean:

> “These characters were definitely written personally by this student.”

## 5.2 Verified Internal

A Paste can be classified as **Verified Internal** when MD//WORKS PROVENANCE can match it to a previous Copy or Cut from the same Report.

The verification may use recorded values such as:

- transferId
- sourceHash
- pastedHash
- sourceEventId

Verified Internal therefore means:

> “This Paste could be matched to an earlier Copy/Cut recorded inside this same Report.”

It does not prove authorship of the copied text itself.

## 5.3 Unverified

A Paste is classified as **Unverified** when it cannot be verified as coming from an earlier Copy/Cut inside the same Report.

This may happen with text pasted from:

- a website
- a PDF
- Microsoft Word
- another MD//WORKS PROVENANCE Report
- another application
- a generative AI tool
- a clipboard where provenance metadata was lost

The important rule is:

> **Unverified ≠ External ≠ AI ≠ Plagiarism ≠ Misconduct**

Unverified is a provenance classification, not a judgment.

## 5.4 Non-internal paste share

The status bar may display a percentage labeled as Unverified or Non-internal paste share.

It is based on recorded input activity, conceptually:

```text
unverifiedPasteChars
/
(
  typedChars
  + replaceInsertedChars
  + unverifiedPasteChars
)
```

Verified Internal Paste is excluded from both numerator and denominator.

This percentage does **not** mean:

- the same percentage of the final document came from an external source
- the same percentage was AI-generated
- the same percentage is plagiarized
- there is that percentage chance of misconduct

---

# 6. Active time and Sessions

## 6.1 Interaction-active time

**Active time** records time associated with interaction in the Editor.

Leaving the browser open without interaction does not simply continue increasing Active time.

Active time is not the same as:

- total study time
- total research time
- reading time
- thinking time
- time spent away from the keyboard

It should therefore be interpreted as a limited process indicator.

## 6.2 Sessions

A new Session begins when the Academic Editor starts or when a saved Report is opened and editing resumes.

Sessions allow a reviewer to see how recorded work was distributed over time.

Session information can help describe the writing process, but it does not prove who was physically at the keyboard.

---

# 7. Copy, Cut, and Paste

## 7.1 Copy and Cut inside the same Report

When MD//WORKS PROVENANCE records a Copy or Cut, it can create provenance information that allows a later Paste in the same Report to be checked.

One Copy may be pasted multiple times and still be verifiable as internal.

## 7.2 Paste from another Report

Even if the text is identical, Paste from a different MD//WORKS PROVENANCE Report is normally Unverified.

The provenance model intentionally verifies transfers **within the same Report**, rather than attempting to infer external origins.

## 7.3 Large Unverified Paste

When an Unverified Paste is large enough to cross the built-in threshold, MD//WORKS PROVENANCE may display a neutral reminder to check citation or source requirements.

This is a citation-awareness prompt.

It is not:

- a warning that misconduct occurred
- a submission block
- an AI detection result

---

# 8. Importing Word documents

Use:

**File > Import Word (.docx)…**

MD//WORKS PROVENANCE converts the Word document locally and inserts the result at the current cursor position.

If text is selected, the selection is replaced by the imported content.

Word Import is recorded as:

```text
type = paste
provenance = unverified
inputSource = word-import
```

The reason is simple: MD//WORKS PROVENANCE did not observe how the Word document itself was created.

The Word file is not considered suspicious. Its prior writing process is simply outside the current Report’s evidence.

## 8.1 Images in Word files

The current beta does not embed imported Word images into the Academic Report as Base64 or blob data.

Text content is prioritized so that Reports remain self-contained and manageable for verification.

## 8.2 Tables

Word table cell text is preserved where possible, but conversion into a Markdown pipe-table structure is not guaranteed.

---

# 9. Images and advanced Markdown

## 9.1 Local images

The current Academic Editor does not support local image embedding through:

- Paste
- Drag & Drop
- Base64 embedding

This keeps Report size under control and avoids large binary payloads that could make verification less reliable.

## 9.2 Current Preview scope

The current beta focuses on common Markdown structures.

Advanced rendering features available in other Markdown environments are not guaranteed here, including:

- LaTeX / KaTeX rendering
- Mermaid diagrams
- Markdown footnote rendering
- special superscript / subscript extensions

If your assignment requires these, follow the instructor’s specified workflow.

---

# 10. Search, Replace, and document navigation

Use:

- **Ctrl+F** for Find
- **Ctrl+H** for Replace

Search supports options such as:

- regular expressions
- case sensitivity

The Outline reflects heading structure and can be used to move through longer documents.

**Format > Insert / Update TOC** can generate or refresh a table of contents from headings.

---

# 11. Saving your work

## 11.1 Save Report

Use:

**File > Save Report**

or:

```text
Ctrl+S
```

This saves a Working Report as HTML.

Do not treat Emergency Recovery as a substitute for Save Report.

## 11.2 Save As

Use **Save Report As…** when you want to create or adopt a different Working Report file.

On supported browsers, future Ctrl+S operations can continue writing to the selected Working Report during the current session.

## 11.3 Offline Save

Working Report Save can be performed without an internet connection.

Server Anchor creation may be unavailable offline, but this does not by itself mean that the local Report is invalid.

---

# 12. Opening and resuming a Report

Use:

**File > Open Report**

or the Open control in the title bar.

When a valid Working Report is opened:

- the document is restored
- the previous Writing Process record is restored
- a new Session begins
- new writing events continue from the saved record

Only content included in the last successful Save is guaranteed to be present in the Working Report.

---

# 13. Emergency Recovery

Emergency Recovery is designed for unexpected browser or PC failure.

MD//WORKS PROVENANCE keeps one temporary text snapshot in browser `sessionStorage`.

Use:

**File > Emergency Recovery…**

The Recovery panel may allow you to:

- copy recovered text
- save recovered text as Markdown
- clear the recovery copy

## 13.1 What Emergency Recovery does not restore

Emergency Recovery does not reconstruct:

- Writing Events
- Sessions
- Event Chain
- Summary
- Manifest
- Server Anchors
- Final Signature

It is text salvage only.

Recovered text pasted back into the Editor is treated as an ordinary Paste and may be classified as Unverified.

---

# 14. Submit and Finalize

Use **Submit Report** when you are ready to create a Finalized Report.

The typical flow is:

```text
Submit Report
   ↓
Ready to Submit
   ↓
review writing summary
   ↓
confirm citation / source check
   ↓
Sign & Finalize
   ↓
Finalized Report
```

The confirmation checkbox is used only in the submission dialog. It is not stored as Writing Process evidence.

## 14.1 Final Signature

Finalization requests a server-issued Ed25519 signature over the final Manifest.

The signature is used so that a Verifier can detect whether the signed Manifest still matches the Report.

A Final Signature does **not** make the HTML file impossible to edit.

MD//WORKS PROVENANCE is designed to be **tamper-evident**, not tamper-proof.

## 14.2 Network requirement

Submit requires an internet connection because Final Signature is server-issued.

## 14.3 Submitting more than once

You may submit more than once.

For example:

```text
Submit
↓
notice a typo
↓
continue editing
↓
Save Working Report
↓
Submit again
```

A later submission receives a fresh finalization timestamp and signature.

Use the newest Finalized Report when that is the version you intend to submit.

---

# 15. Printing and PDF

Use:

**File > Print Preview…**

or:

```text
Ctrl+P
```

Print Preview is intended for:

- proofreading
- review
- printing
- Save as PDF

The printed or PDF output is **not** the Academic Report submission format.

It does not carry the full verifiable Writing Process package in the same way as the Finalized HTML Report.

---

# 16. Verifying a Report

MD//WORKS PROVENANCE provides two verification workflows.

They use the same evidence model but serve different practical needs.

---

# 17. Report Verifier — one Report

The **Report Verifier** is for inspecting a single Academic Report.

Typical use cases include:

- a student showing a thesis-writing record to a supervisor
- a researcher preserving a verifiable record of manuscript development
- an author explaining the writing process to an editor or client
- collaborators reviewing the provenance of a shared draft
- an instructor examining one specific submission in detail
- an individual keeping a verifiable process record for future reference

The Report Verifier can independently check:

- Document Hash
- Event Log Hash
- Event Hash Chain
- Final Chain Hash
- Summary consistency
- Server Anchor signatures
- Final Signature

The Report Verifier is not merely a smaller Overview Verifier.

Its purpose is to make **one portable Report easy to inspect, explain, and share with a third party**.

---

# 18. Overview Verifier — many Reports

The **Overview Verifier** is intended for classes, institutions, and other multi-Report workflows.

Multiple Report files can be loaded together.

Typical overview fields include:

- Student / file label
- Active time
- Sessions
- Non-internal paste share
- Process
- Integrity
- Signature
- Review

A reviewer can select a Report from the list and inspect it in detail.

For a class of many students, the Overview Verifier can handle both:

1. first-pass screening of the whole class
2. detailed inspection of selected Reports

For that reason, institutions may choose to use the Overview Verifier as their primary teacher-facing tool.

---

# 19. Teacher View

Teacher View focuses on interpretation rather than low-level technical detail.

It may include:

## Verification

A summary of checks such as:

- Document Hash
- Event Log Hash
- Event Chain
- Final Chain Hash
- Server Anchors
- Final Signature

## Writing Process

Recorded indicators such as:

- Active time
- Sessions
- Direct input
- Verified Internal
- Unverified
- Non-internal paste share

## Writing Timeline

A session-based view of recorded work over time.

The timeline is descriptive. It does not prove total study time or determine misconduct.

## Document

The final Markdown document for review.

---

# 20. Technical View

Technical View exposes the underlying evidence.

It may include:

## Integrity Details

- recomputed hashes
- Event Chain status
- Final Chain Hash
- Anchor verification
- Final Signature verification
- Summary consistency

## Session History

Recorded Session metadata, including coarse Editor/environment provenance.

MD//WORKS PROVENANCE does not intentionally store:

- raw User-Agent
- IP address
- hardware ID
- exact location
- screen resolution

## Detailed Writing Log

Recorded Events can be inspected by type.

Events may include:

- batched typing
- Paste
- Delete
- Replace
- Undo / Redo
- other recorded editing actions

Some Events may include limited before/after text snippets.

This is not a per-keystroke keylogger.

---

# 21. Understanding integrity results

A Verifier may show states such as:

- Verified
- Review
- Broken
- Signature Invalid
- Summary Mismatch

These are technical integrity states.

They should not be automatically translated into:

- cheating
- plagiarism
- AI use
- identity fraud

For example, a Finalized Report manually edited after submission may fail hash or signature checks. That means the file no longer matches the signed evidence. It does not, by itself, explain why the change occurred.

---

# 22. Server Anchors

Server Anchors provide signed server-side timing evidence for selected Event Hashes.

A valid Anchor can support a limited statement such as:

> The anchored hash had reached the server no later than the recorded server time.

A Server Anchor does not prove:

- the exact time the text was written
- the identity of the writer
- that the full Report already existed in its final form
- that the Report was backed up to the server

Server time and client time serve different purposes and should not be treated as interchangeable.

---

# 23. Editor and environment provenance

Each Session may record coarse information about the Editor and runtime environment.

Examples may include:

- editor name
- editor version
- build identifier
- source revision
- platform
- browser family

This metadata becomes part of the protected process record.

However, it should not be described as runtime attestation.

It records what the application reported about its environment; it does not independently prove that an approved binary was the only software actually running.

---

# 24. Privacy and trust boundaries

MD//WORKS PROVENANCE is designed to preserve process evidence without turning the Editor into a surveillance system.

The Academic Report does not intentionally store:

- IP address
- raw User-Agent
- precise location
- hardware ID
- screen resolution
- local filesystem path
- File System Access handle
- per-keystroke keylogging
- Emergency Recovery text

Normal editing, Preview, local Save, Word Import, Print Preview, and Report verification can operate locally.

Network communication is used for:

- Server Anchors
- Final Signature requests

The Verifier does not need to upload the full Report to an external verification service.

Note that this is separate from ordinary web-server infrastructure. If the Editor or signing service is hosted over HTTPS, server or hosting logs may still process ordinary network metadata according to the deployment environment.

---

# 25. Working with generative AI

MD//WORKS PROVENANCE does not technically prohibit the use of generative AI tools such as ChatGPT, Gemini, or Claude.

Text copied from a generative AI system and pasted into the Editor will normally be recorded as Unverified because it cannot be matched to a previous Copy/Cut inside the same Report.

MD//WORKS PROVENANCE does not determine whether such use is allowed.

Rules for:

- drafting
- translation
- proofreading
- summarization
- brainstorming
- citation of AI assistance
- disclosure of AI use

must come from the relevant course, institution, publisher, or research policy.

---

# 26. Common questions and troubleshooting

## “Sign & Finalize” is disabled

Make sure the citation/source confirmation checkbox has been selected.

During active processing, some controls may also be temporarily disabled to prevent duplicate actions.

## Finalization failed

Check your internet connection and retry.

The Working Report remains editable.

If needed, cancel the dialog, Save Report, and try again later.

## Integrity is Broken or Signature is Invalid

The Report may have been changed after finalization, corrupted, or otherwise no longer match the signed evidence.

If the document needs correction, return to the Working Report, edit it in MD//WORKS PROVENANCE, and Submit again.

## Unverified is high

A high Unverified value is not, by itself, a misconduct finding.

Review the course requirements for citation, source use, and AI use.

## Anchors remain at zero

If the application is offline or cannot reach the anchor service, no new Server Anchors may be created.

Zero Anchors alone is not an Integrity Error.

## Advanced Markdown is not rendered

The current beta prioritizes common Markdown structures and does not guarantee rendering of advanced extensions such as Mermaid, KaTeX, or Markdown footnotes.

---

# 27. Security boundaries

## Paste Provenance can support

- verification of same-Report Copy/Cut → Paste relationships

It cannot prove:

- authorship of pasted text
- external origin
- AI use
- plagiarism
- misconduct

## Editor Provenance can support

- checking that recorded environment metadata remains part of the protected Event record

It cannot prove:

- approved-binary execution
- runtime attestation

## Server Anchors can support

- signed server timing evidence for an Event Hash

They do not prove:

- exact writing time
- identity
- cloud backup of the document

## Final Signature can support

- verification that the Finalized Manifest still matches the server-issued signature

It does not prove:

- identity of the writer
- authorship
- absence of misconduct

---

# 28. Practical recommendations

For students and writers:

- Save often with **Ctrl+S**
- Keep your Working Report until grading or review is complete
- Submit again after making post-submission corrections
- Treat Emergency Recovery only as a last-resort text salvage feature
- Follow the course or institution’s citation and AI-use rules

For instructors and institutions:

- Use **Overview Verifier** for classes and batches
- Use **Report Verifier** when examining or sharing one Report
- Treat Unverified and Active time as process indicators, not verdicts
- Use Technical View when integrity checks require investigation
- Keep academic judgment separate from cryptographic integrity status

For individual writers and researchers:

- Keep Finalized Reports when you want a portable record of how a document was developed
- Share a Report with the Report Verifier when you need to explain your process to a supervisor, collaborator, editor, client, or reviewer

---

# 29. Beta limitations

The current beta has several known limitations.

- Windows + Chrome / Edge is the recommended environment.
- Browser behavior differs for native file pickers, printing, and storage.
- Japanese IME and browser-specific behavior may require environment-specific testing.
- Word table text is preserved, but conversion to Markdown pipe tables is not guaranteed.
- Extremely large documents with tens of thousands of Find matches may cause slow highlight rendering.
- Emergency Recovery is not a formal backup.
- Print/PDF is not the Academic Report submission format.
- The current beta does not provide identity verification.

---

# 30. The principle behind MD//WORKS PROVENANCE

MD//WORKS PROVENANCE is deliberately cautious about what its evidence means.

It does not ask:

> “Was this written by AI?”

It does not claim:

> “This proves who authored the paper.”

Instead, it asks:

> **What writing process was recorded, and can we verify that the record still matches the evidence that was saved and signed?**

That is the role of MD//WORKS PROVENANCE.
