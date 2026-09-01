# MD//WORKS PROVENANCE — FAQ

MD//WORKS PROVENANCE records a Writing Process and preserves evidence that can later be checked for integrity. It does not determine authorship, AI use, plagiarism, misconduct, or identity.

For operating instructions, see the [User Guide](./Manual.md). For the project overview, see the [README](./README.md).

## 1. What does “Unverified” mean?

`Unverified` means that a Paste could not be matched to a prior Copy/Cut event recorded in the same Report.

It is a provenance classification, not a conclusion about the text:

- Unverified does not mean external.
- Unverified does not mean AI-generated.
- Unverified does not mean plagiarism.
- Unverified does not mean misconduct.

## 2. Does a high Unverified percentage mean I cheated or used AI?

No. Non-internal paste share is a descriptive process metric. It is not an AI probability, plagiarism percentage, or misconduct score. A reviewer must interpret the recorded process in context, including the course or institutional policy.

## 3. What is Verified Internal Paste?

`Verified Internal` means that a Paste was successfully matched to a prior Copy/Cut recorded in the same Report. It describes an observed transfer within that Report. It does not prove who authored the text.

## 4. Can I write in Microsoft Word or Google Docs first?

Technically, yes. However, text imported or pasted into MD//WORKS PROVENANCE will normally be `Unverified` because the Editor did not observe the earlier writing process. Your course or institution decides whether that workflow is allowed.

## 5. Why is Word Import marked Unverified?

MD//WORKS PROVENANCE can record that an import occurred, but it cannot reconstruct how the Word document was created. Imported text is therefore treated as `Unverified` process input.

## 6. What does “Non-internal paste share” mean?

Conceptually, it is calculated as:

```text
unverifiedPasteChars /
(typedChars + replaceInsertedChars + unverifiedPasteChars)
```

`Verified Internal` Paste is excluded. The result is not the percentage of final text taken from outside sources, the percentage that is AI-generated, a plagiarism percentage, or a probability of misconduct.

## 7. Which file should I submit?

When MD//WORKS PROVENANCE submission is required, you will normally submit the **Finalized Report (`.html`)**. PDF and Markdown exports do not carry the same verifiable Writing Process package. Always follow the instructions for your course, supervisor, publisher, or institution.

## 8. What is the difference between Save Report and Submit Report?

**Save Report** creates or updates a **Working Report** that can be reopened and edited. **Submit Report** creates a fresh **Finalized Report** with a Final Signature. The signature makes later changes detectable; it does not make the HTML impossible to modify.

## 9. Can I Submit more than once?

Yes. Each later submission creates a fresh finalization record and Final Signature for the state being submitted.

## 10. What happens if I edit after submitting?

The MD//WORKS PROVENANCE Editor remains usable. After further editing, an ordinary Save returns the current work to a Working Report state. Submit again when you need a new Finalized Report.

## 11. How much history is preserved when I Save?

A saved Working Report preserves the Writing Process through the most recent successful Save. Changes made after that Save are not in that file. Emergency Recovery is separate and is not a Writing Process backup.

## 12. What is Active time?

Active time is interaction-active time recorded in the Editor. It is a reference value and does not prove total study time, thinking time, research time, reading time, or learning time.

## 13. What can a reviewer see?

Depending on the Report, a reviewer can inspect the Document, Sessions, Writing Process summary, Copy/Cut/Paste provenance, Active time, timeline, integrity checks, Server Anchors, Final Signature, and Detailed Writing Log. Some recorded events may include limited before/after text snippets needed to explain a change. MD//WORKS PROVENANCE does not perform per-keystroke keylogging.

## 14. Does MD//WORKS PROVENANCE upload my whole document to a server?

Normal editing and verification are local-first. Network communication is used for Server Anchors and the Final Signature. A full Report does not need to be uploaded to a third-party verification service merely to verify it.

The Academic Report does not intentionally store IP addresses, raw User-Agent strings, hardware identifiers, precise geolocation, screen resolution, local filesystem paths, File System Access handles, per-keystroke keylogging, or Emergency Recovery text. Ordinary hosting and network server logs are a separate deployment issue and should be governed by the operator's policy.

## 15. Can I work offline?

Editing, local Save, Preview, Word Import, Print Preview, and verification can operate locally. New Server Anchors and Submit / Final Signature require network access.

A valid Server Anchor can support that an anchored hash had reached the server no later than its recorded server timestamp. It does not prove the exact time the text was written.

## 16. What is Emergency Recovery?

Emergency Recovery is one temporary text-recovery snapshot stored in browser session storage. It is intended only for text salvage after an interruption.

## 17. Is Emergency Recovery a backup?

No. It does not restore Events, Sessions, the Event Chain, Manifest, Server Anchors, or Final Signature. Save Working Reports regularly.

## 18. What happens if I paste recovered text back into the Editor?

Recovered text is handled as an ordinary Paste. If it cannot be matched to a prior Copy/Cut in the same Report, it will normally be recorded as `Unverified`.

## 19. Can I continue on another computer or browser?

Yes. Move and open a saved Working Report to continue. Emergency Recovery data stays in the browser session where it was created and does not travel with the Report.

## 20. Should I keep both Working and Finalized Reports?

Yes. Keep the Working Report until grading, editorial review, or research review is complete. Also retain the exact Finalized Report that you submitted or shared.

## 21. Is a printed or PDF copy an Academic Report?

No, not by itself. Print and PDF output are useful for reading, proofreading, or separate submission requirements, but they do not contain the same verifiable process package as the Academic Report HTML.

## 22. Does MD//WORKS PROVENANCE detect AI?

No. It records process evidence instead of classifying final text as AI-written or human-written. AI-use rules remain a matter for the relevant course, institution, publisher, or research policy.

## 23. Does a valid Report prove who wrote it?

No. A valid Event Chain, matching hashes, Server Anchors, and a valid Final Signature support integrity claims about the recorded evidence. They do not prove authorship, identity, official-runtime execution, or academic conduct. The system is tamper-evident, not tamper-proof.

## 24. What is the difference between Report Verifier and Overview Verifier?

The **Report Verifier** is a portable interface for one Report. It is useful when a student presents a Report to a supervisor, a researcher to a collaborator, an author to an editor or client, an individual to a third-party reviewer, or an instructor examines one submission.

The **Overview Verifier** is for many Reports. It supports class, course, and institutional workflows, batch review, first-pass screening, and selection of individual Reports for detailed review.

The Overview Verifier may contain the same detailed Teacher View and Technical View functions, but the Report Verifier remains useful as a simple one-Report verification and presentation interface.

