# MD//WORKS Provenance

**Don’t guess whether a paper was written by AI. Preserve how it was written.**

[![License: AGPL-3.0-only](https://img.shields.io/badge/License-AGPL--3.0--only-blue.svg)](./LICENSE)
[![Status: Beta](https://img.shields.io/badge/status-beta-orange.svg)](#beta-status)
[![Local-first](https://img.shields.io/badge/design-local--first-4c8bf5.svg)](#privacy-and-trust-boundaries)
[![No analytics](https://img.shields.io/badge/analytics-none-2ea44f.svg)](#privacy-and-trust-boundaries)

**MD//WORKS Provenance** is a writing editor and verification toolkit designed to preserve a **tamper-evident record of the writing process**.

Instead of trying to infer authorship from the final text, it records what happened while the document was being written:

- direct writing and editing activity
- Copy / Cut → Paste provenance inside the same Report
- writing sessions and interaction-active time
- a hash-chained event history
- server anchors during writing
- a final cryptographic signature at submission

The result is a self-contained HTML Report that can be independently checked in a browser.

> **MD//WORKS Provenance is not an AI detector, plagiarism detector, misconduct detector, or identity-proofing system.**  
> It verifies the integrity of recorded evidence about the writing process.

Writers work locally in the browser. A single Report can be shared with a supervisor, collaborator, editor, client, or other reviewer and checked with the **Report Verifier**. Institutions can review many Reports with the **Overview Verifier**. In either case, a Report does not need to be uploaded to a third-party verification service just to check its integrity.

[Documentation](./Manual.md) · [Student FAQ](./FAQ-en.md)

---

## Why this exists

Generative AI changed the question.

For years, academic integrity tools mostly examined the **final submission**:

- Does it resemble published material?
- Does it statistically resemble AI-generated text?
- Does it match another student's work?

Those questions can still be useful. But they do not answer a different question:

> **What writing process was actually recorded before this document was submitted?**

MD//WORKS Provenance focuses on that missing layer.

It does not claim to prove who physically typed every sentence. It does not try to classify prose as human or AI. Instead, it preserves a structured process record and makes later modification detectable.

That makes it useful not only for academic integrity, but also for:

- formative assessment
- writing-process research
- transparent AI-use policies
- supervised coursework
- research training
- reflective writing practice
- independent authors who want to preserve process evidence they can later share

---

## How it works

```text
Student
  │
  │ write / revise / copy / paste
  ▼
MD//WORKS Provenance Editor
  │
  ├─ Writing Events
  ├─ Paste Provenance
  ├─ Sessions / Active Time
  ├─ Event Hash Chain
  └─ Server Anchors
  │
  │ Save Report
  ▼
Working Report (.html)
  │
  │ Submit Report
  │ Final Signature
  ▼
Finalized Report (.html)
  │
  ├──────────────────────┐
  ▼                      ▼
Report Verifier          Overview Verifier
(one Report)             (many Reports)
  │                      │
  └──── local browser verification
```

The ordinary writing workflow remains simple:

1. Open the Editor.
2. Write.
3. Save a Working Report.
4. Submit when ready.
5. Verify the Finalized Report.

---

## The core idea: provenance, not prediction

MD//WORKS Provenance is built around **observable provenance**.

### Verified Internal Paste

If text is copied or cut inside the same Report and later pasted back into that Report, MD//WORKS Provenance can attempt to verify that transfer using recorded provenance metadata.

A successful match is recorded as:

```text
Verified Internal
```

### Unverified Paste

If a Paste cannot be matched to a previous Copy/Cut in the same Report, it is recorded as:

```text
Unverified
```

This may include text pasted from:

- a website
- a PDF
- Microsoft Word
- another MD//WORKS Provenance Report
- a note-taking app
- a generative AI tool
- a clipboard where provenance metadata was lost

So:

> **Unverified does not mean external.**  
> **Unverified does not mean AI.**  
> **Unverified does not mean plagiarism.**  
> **Unverified does not mean misconduct.**

It means only that the Paste could not be verified as a prior Copy/Cut from the same Report.

---

## What can be verified — and what cannot

| MD//WORKS Provenance can verify | MD//WORKS Provenance does not prove |
| --- | --- |
| Document Hash consistency | Who physically authored the text |
| Event Log Hash consistency | Whether AI was used |
| Event Hash Chain continuity | Whether Unverified text came from an external source |
| Final Chain Hash consistency | Whether a student followed course policy |
| Recorded Sessions and interaction-active time | Total thinking, reading, or research time |
| Same-Report Copy/Cut → Paste matches | Plagiarism or misconduct |
| Server Anchor signatures | Exact writing time |
| Final Signature validity | Student identity |

This distinction is intentional.

---

## Student-facing Editor

### Writing Record

A compact status view shows the current process record while the student writes:

```text
Active 24m | Unverified 22% | [Input activity] | Anchors: 14
```

The detailed **Current Writing Record** includes:

- interaction-active time
- sessions
- current document size
- direct input
- Verified Internal Paste
- Unverified Paste
- non-internal paste share
- event count
- server anchor count

These are descriptive process indicators, not a misconduct score.

### Save and resume

A Working Report is saved as a self-contained HTML file containing:

- the current Markdown document
- recorded writing events
- session information
- summary data
- integrity metadata

The student can reopen the Report later and continue in a new Session.

### Word import

`.docx` files can be converted locally and inserted at the current cursor position.

Because MD//WORKS Provenance did not observe how the Word document itself was created, imported text is recorded as:

```text
provenance = unverified
inputSource = word-import
```

### Submit and finalize

Submission is deliberately separate from ordinary Save.

```text
Save Report
→ Working Report

Submit Report
→ Ready to Submit
→ Sign & Finalize
→ Finalized Report
```

The Finalized Report receives a server-issued Ed25519 signature over its final Manifest.

### Emergency Recovery

Emergency Recovery keeps a single temporary text snapshot in the browser session.

It is designed for **text salvage only**.

It is not:

- a backup system
- a Writing Process restore system
- a replacement for Save Report
- an Academic Report

### Print Preview

Print/PDF output is intended for reading, proofreading, and review.

It is **not** the cryptographically verifiable Academic Report submission format.

---

## Verification for individuals and institutions

MD//WORKS Provenance provides two verification workflows built around the same integrity model.

### Report Verifier — one Report

The **Report Verifier** is designed for a single Academic Report.

It is useful not only for instructors, but also for individuals who want to keep a verifiable record of their own writing process and later explain it to a third party. Typical use cases include:

- a student sharing a thesis-writing record with a supervisor
- a researcher documenting how a manuscript developed
- an author or freelance writer showing process evidence to an editor or client
- collaborators reviewing the provenance of a shared draft
- anyone who wants to preserve a portable, independently verifiable writing record

The Report Verifier independently checks:

- Document Hash
- Event Log Hash
- Event Hash Chain
- Final Chain Hash
- Summary consistency
- Server Anchor signatures
- Final Signature

It also provides the same detailed Teacher View / Technical View used for institutional review.

### Overview Verifier — many Reports

The **Overview Verifier** is designed for classes, institutions, and other workflows involving multiple Reports.

Multiple Reports can be loaded together for first-pass review. Typical columns include:

- Student / file label
- Active time
- Sessions
- Non-internal paste share
- Process status
- Integrity
- Signature
- Review

A Report selected from the Overview can then be examined in the same detailed views used for single-Report verification.

This is useful for classes where instructors need to review tens or hundreds of submissions without opening each file separately.

### Same evidence model, different workflow

The distinction is primarily about workflow:

```text
One Report
→ Report Verifier
→ explain / inspect / share the process record

Many Reports
→ Overview Verifier
→ screen the class or collection
→ inspect selected Reports in detail
```

The Report Verifier is therefore not a reduced version of the Overview Verifier. It is the simpler, single-Report interface for personal, research, editorial, and third-party review.

### Teacher View

The Teacher View focuses on interpretation:

- verification status
- writing-process summary
- session-based timeline
- final document

### Technical View

The Technical View exposes the underlying evidence:

- hashes
- chain status
- anchors
- signature status
- session metadata
- event details

The goal is transparency: the process record should remain understandable to writers, educators, reviewers, and technical auditors.

---

## Tamper-evident, not tamper-proof

MD//WORKS Provenance Reports are ordinary HTML files. They can be edited.

What matters is that unauthorized changes become detectable.

Conceptually:

```text
Document
   │
   └─ SHA-256
      └─ Document Hash

Events
   │
   ├─ previousHash
   └─ SHA-256
      └─ Event Hash Chain

Final state
   │
   └─ Manifest
      │
      └─ Ed25519
         └─ Final Signature
```

If the document is changed, the Document Hash no longer matches.

If an event is changed, the Event Hash Chain breaks.

If the signed Manifest is changed, the Final Signature no longer verifies.

This is why MD//WORKS Provenance describes its evidence as **tamper-evident** rather than immutable.

---

## Server Anchor vs Final Signature

They serve different purposes.

### Server Anchor

During writing, the application can submit selected event-hash information to a server.

The server returns a signed anchor with server time.

A valid anchor can support the statement:

> This anchored hash had reached the server no later than this server timestamp.

It does **not** prove:

- the exact moment the text was written
- who wrote it
- that the document was complete at that time

### Final Signature

At submission, the final Manifest is signed with Ed25519.

This lets the Verifier determine whether the finalized Manifest still matches what was signed.

---

## Privacy and trust boundaries

MD//WORKS Provenance is designed to record the writing process without turning the Editor into a surveillance client.

### Not stored in the Academic Report

The Report does not intentionally store:

- IP addresses
- raw User-Agent strings
- hardware identifiers
- precise geolocation
- screen resolution
- per-keystroke keylogging
- local filesystem paths
- File System Access handles
- Emergency Recovery text

Editor/environment information is stored only as **coarse provenance metadata** such as platform and browser family.

That metadata is evidence recorded by the application — it is **not runtime attestation**.

### Network communication

Normal editing, Preview, local Save, Word Import, Print Preview, and verification can operate locally.

Network communication is used for:

- Server Anchors
- Final Signature requests

The Verifier does not need to upload a student's full Report to a remote verification service.

A separate operational note: not storing IP addresses in the Report does not mean that normal web infrastructure is technically incapable of seeing network metadata. Server and hosting logs should be governed separately by the institution's deployment policy.

---

## Quick start

### 1. Open the Editor

```text
academic-editor.html
```

Beta recommendation:

```text
Windows + current Chrome / Edge
```

### 2. Write and save

```text
Ctrl+S
→ Save Report
→ Working Report (.html)
```

### 3. Submit

```text
Submit Report
→ Ready to Submit
→ citation/source check
→ Sign & Finalize
→ Finalized Report (.html)
```

Submission requires a network connection.

### 4. Verify

For a single Report, open:

```text
report-verifier.html
```

For a class or collection of Reports, open:

```text
overview-verifier.html
```

and load the Finalized Report(s).

---

## Where it fits

MD//WORKS Provenance is not intended to replace similarity checking, AI-policy enforcement, or an LMS.

It addresses a different layer.

| Approach | Primary focus | What MD//WORKS Provenance adds |
| --- | --- | --- |
| Similarity checker | Similarity between final text and existing sources | Writing-process evidence |
| AI classifier | Statistical characteristics of the final text | No AI probability; process provenance instead |
| Cloud revision history | Editing history inside one cloud platform | Portable evidence inside the submitted Report |
| LMS submission | Identity / course workflow / file collection | Cryptographic integrity of the process record |
| **MD//WORKS Provenance** | **Recorded Writing Process** | **Portable provenance + hash-chain + signature verification for one Report or many Reports** |

These systems can be complementary.

---

## A practical example

Suppose a 3,000-word report contains:

- several hours of recorded interaction-active writing
- multiple sessions over several days
- normal revision and deletion patterns
- several Verified Internal moves
- a 250-character Unverified Paste
- valid Event Chain
- valid Final Signature

MD//WORKS Provenance does **not** conclude:

> “This paper is authentic.”

Instead, it gives the instructor a more precise statement:

> “This Report contains a consistent, cryptographically verifiable record of these observed writing events, including one Paste that could not be matched to a prior Copy/Cut in the same Report.”

That difference matters.

---

## Beta status

MD//WORKS Provenance is currently in beta and is being tested in real writing workflows.

### Recommended environment

- Windows
- current Chrome / Edge

Other browsers may use fallback file-download behavior where File System Access APIs are unavailable.

### Known limitations

- browser and OS differences can affect native file pickers, IME behavior, and printing
- Word table text is preserved, but conversion to Markdown pipe-table structure is not guaranteed
- extremely large documents with tens of thousands of Find matches can make highlight rendering slow
- Emergency Recovery is not a formal backup
- Print/PDF is not the Academic Report submission format
- the current beta does not provide identity verification

---

## License

### Academic edition

Unless otherwise noted, the MD//WORKS Provenance code in this repository is licensed under:

**GNU Affero General Public License v3.0 only (`AGPL-3.0-only`)**

See [`LICENSE`](./LICENSE) for the legally controlling terms.

> **AGPL does not prohibit commercial use.**  
> Commercial use, modification, and redistribution are permitted subject to the license terms.

### Why AGPL?

In MD//WORKS Provenance, the verification logic is part of the academic trust model.

If an institution modifies the software, the important question should not become:

> “What does the hidden version of the verifier really do?”

AGPL helps keep network-deployed modifications auditable by requiring source availability in the circumstances covered by the license.

The intent is simple:

> **If the process-evidence logic changes, the people relying on it should be able to inspect that logic.**

The `LICENSE` file, not this README, controls the exact legal obligations.

### Standard MD//WORKS

The standard MD//WORKS project is separate and remains subject to its own MIT license.

Publishing the Academic edition under AGPL does not revoke or alter rights already granted under MIT for the standard edition.

### Third-party software

MD//WORKS Provenance may bundle third-party libraries.

Those components remain subject to their own licenses and notices.

Do not remove their copyright or license notices.

---

## What about student papers?

Students retain the copyright and other rights they hold in their own writing.

The AGPL license on MD//WORKS Provenance does not transfer ownership of a student's essay, paper, thesis text, or research writing to the software author.

However, an Academic Report is a self-contained HTML container and may contain both:

1. user-authored content, and
2. AGPL-covered application code.

Those are different layers inside the same file.

When redistributing the Report container, do not remove license notices applicable to the software code or bundled third-party components.

---

## AGPL and network use

AGPL-3.0 Section 13 addresses modified versions of the Program that support remote interaction over a network.

Where that provision applies, users interacting with the modified version must be offered an opportunity to receive the corresponding source of that version.

Distribution of modified copies can also trigger the AGPL's ordinary source-code obligations.

If MD//WORKS Provenance is deployed through an LMS or institutional web service, provide a clear **Source / License** path in the user interface and follow the `LICENSE` terms for the actual deployment model.

---

## Institutional License

For code for which the MD//WORKS Provenance copyright holder owns the necessary rights, an alternative **Institutional License** may be offered.

This is not a “commercial-use license” — AGPL already permits commercial use.

An Institutional License is intended for organizations that need terms such as:

- proprietary modifications
- closed-source integration
- private institutional deployment
- LMS / SSO / grade-system integration
- deployment assistance
- support
- SLA

Third-party dependencies remain governed by their original licenses.

---

## Contributions

To preserve the possibility of both AGPL and alternative institutional licensing, contribution rights need to be clear.

During the beta period:

- bug reports are welcome
- design feedback is welcome
- validation results are welcome
- feature proposals are welcome

Before broadly accepting external code contributions, the project may publish a Contributor License Agreement (CLA) or equivalent contribution policy.

---

## Roadmap

### Level 0 — current beta

- Writing Process recording
- Paste Provenance
- Server Anchor
- Final Signature
- Working / Finalized Reports
- Report Verifier
- Overview Verifier
- local-first writing workflow
- no identity verification

### Level 1

Potential structured Report metadata:

```text
studentId
studentName
studentEmail
assignmentId
courseId
```

Structured identity metadata would still not, by itself, prove authorship.

### Level 2

Potential institutional identity integration:

- OIDC
- university SSO
- Google Workspace
- Microsoft Entra ID

SSO can strengthen the account-to-submission link. It still does not prove who physically authored every part of the document.

---

## Security and academic-integrity disclosure

MD//WORKS Provenance does not guarantee:

- prevention of all cheating
- proof of authorship
- proof of identity
- detection of all AI use
- detection of plagiarism
- perfect reconstruction of every human action

It is a system for preserving and verifying a **recorded writing process**.

If you discover a security issue, signature-verification flaw, provenance bypass, or evidence-semantics problem, please use the repository's designated security contact rather than publishing detailed exploit instructions in a public Issue.

---

## Design principle

MD//WORKS Provenance is built around one idea:

> **Stop guessing from the final text. Preserve the process evidence.**

Not:

> “Was this written by AI?”

But:

> “What writing process was recorded, and can we verify that the record has not been altered?”

That is the problem MD//WORKS Provenance is trying to solve.
