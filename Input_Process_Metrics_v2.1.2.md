# MD//WORKS PROVENANCE
## Input Process Metrics / Sequential-Writing Spoofing Countermeasure Specification v2.1.2
### Chunking + Independent Pause Recording + IME / Direct Edit Aggregation Design

Version: 2.1.2-beta-draft / 2026-09-09  
License: AGPL-3.0-only (PROVENANCE) / MIT (MD//WORKS core)

Review scope: Document review of the attached specification. Implementation code, existing Reports, and real-browser behavior have not been verified in this review.
This revision is a specification draft only; it does not implement, publish, or change any existing license.

---

# 1. Background, Purpose, and Design Policy

## 1.1 Background and New Challenges

Claude Computer Use, GPT Work, RPA tools, and other OS-level or browser-level automation can feed AI-generated or otherwise generated text into an Editor one character at a time, in a way that resembles ordinary typing, without using Paste.

Examples include:

- feeding AI-generated text as one-character-at-a-time input events
- inserting fixed or randomized delays
- deliberately mixing in Backspace or deletion operations
- inserting Japanese text directly without using an IME
- mimicking timing distributions that resemble human input

Existing Paste Provenance alone cannot adequately distinguish this type of sequential input that does not use Paste.

At the same time, this specification does not adopt detailed storage of per-character keystroke timestamps or input contents, for the following reasons:

- increased Report size
- runtime overhead
- privacy
- excessive recording of detailed input behavior
- risk of future expansion beyond the intended purpose

---

## 1.2 Requirements

This specification requires the following:

1. Do not store raw per-character keystroke logs.
2. Do not store uncommitted IME text in the Report.
3. Do not store sequences of individual input intervals in the Report.
4. Store the input process as fixed-size aggregate values per Chunk.
5. Allow input interruptions above a defined duration to be recorded as independent Pause events.
6. Distinguish IME composition, direct input, deletion, and similar activity where reasonably observable.
7. Do not double-count changes already represented by existing events such as Paste, Undo, or Redo.
8. Include new aggregate values within Hash Chain protection.
9. Do not replace unavailable information with zero or inferred values.
10. Do not allow the new measurement layer to break document editing, Undo/Redo, Paste Provenance, or related existing behavior.
11. Do not perform automatic human / AI / misconduct classification in the initial implementation.

---

## 1.3 Design Goal

The primary purpose of this feature is:

> **to provide additional observability into the input process, making simple sequential-input automation and ordinary writing processes more directly comparable**

It does not guarantee complete prevention of spoofing.

It also does not prove that:

> “Because this record exists, a human personally wrote the text.”

Increasing the amount of observable input-process information may make simplistic sequential-input spoofing more difficult, but the specification does not guarantee that the cost of spoofing will increase beyond any specific threshold.

---

## 1.4 Lightweight Recording Principle

Do not store detailed timing data in proportion to the number of individual input actions.

The Input Process information stored by each Chunk and Pause event should, in principle, consist of a fixed number of aggregate fields.

Therefore:

> **the additional aggregate information per Chunk / Event is O(1)**

The overall Report size still grows with the number of Chunks, Pauses, and other events. This does not mean that total Report size is O(1) with respect to document length.

---

## 1.5 Initial v2.1 Implementation Scope

The v2.1 Core primarily covers:

- characters inserted by Direct Edit
- characters deleted by Direct Edit
- number of editing operations involving text removal
- IME composition count
- IME composition duration
- number of characters committed through IME
- Chunk elapsed time
- Pause
- Long Pause
- foreground / background state during a Pause
- Capture capability / status
- Fallback / partial recording

The initial implementation does not include:

- individual input intervals
- input-interval histograms
- contextual analysis of Pause location
- typing-speed graphs
- Human / AI score
- Suspicion score
- automatic misconduct classification
- new indicators in Teacher View

---

# 2. Trust Model / Non-Goals

## 2.1 Basic Trust Model

MD//WORKS PROVENANCE records writing and editing events observed by the Editor, together with aggregate values calculated at the time, and allows verification of the internal consistency of the presented record and its agreement with available signatures and external Anchors. The scope of these guarantees follows Section 2.4.

Hash Chains, signatures, Server Anchors, and similar mechanisms are:

> **mechanisms for verifying the integrity and chronological continuity of recorded data**

They do not prove that a human directly caused a recorded event.

The same applies to the following elements added by this specification:

- Chunk
- Pause
- Direct Edit
- deletion operations
- IME composition
- timing information

These are observations or aggregate values about the input process as observed by the Editor. By themselves, they are not grounds for determining human authorship, AI use, or misconduct.

---

## 2.2 Trust Assumptions

This specification assumes the following:

1. An authorized, unmodified MD//WORKS PROVENANCE Editor is being used.
2. Browser events received by the Editor and document state observed within the Editor are treated as observations at recording time.
3. New events and aggregate values are finalized before they are appended to the Hash Chain.
4. New aggregate values are included in the event hash.
5. The Reviewer does not trust only self-reported validation status in the Report; it independently verifies the Hash Chain, Document Hash, signatures, Anchors, and related evidence.

---

## 2.3 What Is Not Trusted or Inferred

The following must not be inferred as authentic solely from observed browser events:

- whether the input actor is human or AI
- whether the input actor is the claimed author or a third party
- whether input originated from a physical keyboard
- whether input originated from RPA or Computer Use
- whether the presence of IME events means human input
- whether the absence of IME events means automated input
- whether a Pause represents thinking
- whether a Pause represents rereading
- whether a Pause represents being away
- whether a Pause represents consulting a reference
- whether a deletion represents correction of a mistake

---

## 2.4 Modified Recording Code and Limits of Verification

A client can potentially generate false values and then append them to a valid Hash Chain. The entire chain can also be regenerated. Therefore, a Hash Chain alone must not be described as guaranteeing that “any modification after sealing will always be detected.”

- **Hash Chain:** verifies the linkage and internal consistency among the presented events.
- **Signature:** verifies that a signature corresponds to the associated private key. Trust that the key belongs to a particular person or authorized Editor requires separate key-management trust.
- **Server Anchor:** verifies agreement with a digest and scope fixed by an external service that the verifier chooses to trust. The time guarantee depends on the specification of that service.

For a self-contained Report without an independently trusted external reference, this specification does not guarantee distinguishability from a completely regenerated but internally consistent record. Signature and Anchor results, and trust in the corresponding key or service, must be displayed separately.

---

## 2.5 Non-Goals

This feature does not aim to provide:

1. proof of human authorship
2. proof that the claimed person authored the text
3. automatic determination of AI use
4. automatic determination of misconduct
5. authentication of the input device
6. authentication of the input actor
7. storage of per-character input timestamps
8. storage of all key operations
9. storage of IME candidate-selection operations
10. storage of uncommitted IME text
11. psychological inference from Pause duration
12. automatic interpretation of deletion operations as “mistakes”
13. complete defense against a modified recording program

---

# 3. Observation Items and Counting Rules

## 3.1 Basic Principle

This specification primarily counts:

> **committed document changes observed by the Editor**

rather than physical key presses.

Uncommitted text during IME conversion, candidate selection, and key operations that do not change the document are not counted as inserted or deleted document characters.

Existing explicit operations such as Paste, Undo, Redo, Replace, Format Markdown, and TOC are distinguished from ordinary direct input and must not be double-counted in Direct Edit aggregates.

---

## 3.2 Terminology

| Term | Definition |
| --- | --- |
| Direct Edit | Direct document editing observed by the Editor |
| Chunk | A group of Direct Edits separated by a Pause or Hard Boundary |
| Pause | An input interruption of at least the defined duration between consecutive observable Activities |
| Long Pause | A Pause at or above the configured threshold |
| IME Composition | A browser-observed composition session from `compositionstart` to `compositionend` |
| Explicit Operation | An explicit operation such as Paste, Undo, Redo, Replace, or Format |
| Hard Boundary | An operation or state that always closes the current Chunk |
| Chunk Elapsed Time | Elapsed time from the first committed Direct Edit in a Chunk to the last committed Direct Edit in that Chunk |
| Capture Status | Status describing how completely Input Process Metrics could be captured within a Session |

---

## 3.3 Character Unit

For implementation compatibility with existing PROVENANCE behavior, v2.1 counts characters as:

> **JavaScript UTF-16 code units**

For ordinary Japanese characters, Latin characters, and digits, this broadly corresponds to visually perceived characters. Some emoji and other Unicode characters may use multiple code units.

The Report records:

```text
characterUnit: "utf16-code-unit"
```

If the character unit is changed in the future, `inputProcessVersion` must be incremented.

---

## 3.4 Direct Insert

Characters added to the document through Direct Edit are recorded as:

```text
directInsertedCharacters
```

Example:

```text
abc
```

entered directly results in:

```text
directInsertedCharacters += 3
```

This does not mean that three physical keys were pressed.

Characters added through Paste, Redo, explicit Replace commands, TOC generation, or similar explicit operations are excluded.

---

## 3.5 Direct Delete

Characters removed from the document through Direct Edit are recorded as:

```text
directDeletedCharacters
```

Example:

```text
abc
↓ Backspace
ab
```

results in:

```text
directDeletedCharacters += 1
```

If 10 characters are selected and deleted in one operation:

```text
directDeletedCharacters += 10
```

---

## 3.6 Deletion Operation

The number of Direct Edit operations that remove text is recorded as:

```text
deletionOperationCount
```

If 10 selected characters are deleted in one operation:

```text
directDeletedCharacters += 10
deletionOperationCount += 1
```

If holding Backspace causes multiple committed deletion changes to be observed, each observed committed deletion change counts as one operation.

The implementation does not infer how many physical long-press actions occurred.

---

## 3.7 Direct Input into a Selection

Example:

```text
abcdef
```

If `cde` is selected and `X` is directly entered, producing:

```text
abXf
```

record:

```text
directDeletedCharacters += 3
directInsertedCharacters += 1
deletionOperationCount += 1
```

However, if the existing PROVENANCE implementation classifies the change as an independent `replace` event, the event type alone must not determine whether the operation is a Direct Edit or an Explicit Operation.

If the user directly types over a selected range and the existing implementation emits a `replace` event, the operation is still semantically a Direct Edit and is included in Input Process Direct aggregates. In contrast, an explicit Editor command such as Replace / Replace All is an Explicit Operation and is excluded from Direct aggregates.

If implementation audit shows that these cases cannot be distinguished from event type alone, add `owner` or equivalent explicit classification metadata. The same document change must never be counted in both `typing` and `replace`.

---

## 3.8 IME Composition

IME is recorded as browser-observed composition activity, not as “conversion time.”

One sequence:

```text
compositionstart
↓
compositionupdate ...
↓
compositionend
```

is treated as one composition session.

Core metrics:

```text
compositionCount
compositionMsTotal
compositionCommittedCharacters
```

---

## 3.9 compositionCount

The number of sessions in which a valid:

```text
compositionstart → compositionend
```

pair was observed.

One composition session must not be interpreted as one word, one phrase, one thought, or any other semantic unit.

---

## 3.10 compositionMsTotal

The cumulative observed duration from `compositionstart` to `compositionend`.

It does not mean:

- thinking time
- pure kanji-conversion time
- human typing time

---

## 3.11 compositionCommittedCharacters

The number of characters ultimately committed to the document through composition.

For example, if IME intermediate states include:

```text
y
や
やm
やま
山
```

those intermediate states are not stored or individually counted as characters.

Only the final committed result, such as `山`, is counted.

`compositionCommittedCharacters` is a subset of `directInsertedCharacters`; the two values must not be added together to calculate total input.

---

## 3.12 Paste

Paste is excluded from Direct Edit aggregates.

When Paste occurs:

1. close the current Chunk
2. finalize an applicable Pause
3. record the existing Paste Provenance event
4. start a new Chunk with the next Direct Edit

Pasted characters must not also be added to:

```text
directInsertedCharacters
```

---

## 3.13 Undo / Redo

Document changes caused by Undo / Redo are excluded from:

```text
directInsertedCharacters
directDeletedCharacters
deletionOperationCount
```

They remain represented by the existing `undo` / `redo` events.

---

## 3.14 Other Explicit Operations

The following known operations are separated from ordinary Direct Edit:

- Replace
- Replace All
- Format Markdown
- Insert / Update TOC
- Markdown formatting commands
- changes independently recorded as Large Delete
- File Open
- History Restore
- other existing independent events

The same document change must not be recorded in both an Explicit Operation and `typing`.

---

## 3.15 Chunk

A Chunk is a recording unit for Direct Edit and successfully completed composition activity.

- Split when the gap between Activities is at least `CHUNK_PAUSE_THRESHOLD_MS = 2000`.
- Also split at Hard Boundaries and at the safe periodic flush described in Sections 5.18 and 5.28.
- Therefore, Chunk count does not represent the number of “natural writing units.”

The Activity model in Section 5 governs these boundaries and distinguishes the gap before IME composition from time spent inside the composition itself.

---

## 3.16 Chunk Elapsed Time

The former `ActiveMs` name is not used. The field is:

```text
chunkElapsedMs
```

Definition:

> Elapsed time from the first committed Direct Edit in the Chunk to the last committed Direct Edit in the Chunk.

This may include short internal periods with no input, so it must not be interpreted as actual active typing time.

For a Chunk containing only one Direct Edit:

```text
chunkElapsedMs = 0
```

is allowed.

---

## 3.17 Pause

A Pause is:

> **an interval of at least the configured duration between consecutive observable Activities during which no qualifying editing input was observed**

Initial threshold:

```text
>= 2000 ms
```

A Pause does not mean:

- thinking
- rereading
- being away
- consulting literature or references
- taking a break

---

## 3.18 Long Pause

Initial classification threshold:

```text
LONG_PAUSE_THRESHOLD_MS = 60000
```

For a Pause of at least 60 seconds:

```text
pauseClass = "long"
```

However, the Input Process feature must not create or end Sessions solely because this threshold was reached.

Long Pause is a separate concept from the existing SessionManager.

---

## 3.19 Pause Visibility

`visibility` describes only page visibility as observed through Page Visibility semantics.

| Value | Rule |
| --- | --- |
| `background` | the interval starts hidden, or hidden is observed at least once during the interval |
| `foreground` | the full interval is observable, visible from start to end, and no hidden state is observed |
| `unknown` | start state or intermediate observation is unknown, and there is no positive evidence of hidden state |

Priority is `background > unknown > foreground`. `blur` alone must not be treated as background. Focus movement within the page, window focus loss, and page visibility must be distinguished. `foreground` does not guarantee user attention, presence, or that the page was unobscured.

---

## 3.20 0 / null / missing

- `0`: the field's relevant scope was observable and the result was zero.
- `null`: the metric applies, but a complete reliable value could not be obtained. Do not store only the observed portion as if it were complete.
- field missing: legacy schema, or the field is not applicable to that event type. A required field missing from supported v1 must not be treated as legacy; it is Semantic Invalid.

An exception is `chunkElapsedMs = null` for a composition-only Chunk with no committed Direct Edit. This means “not applicable,” not measurement failure (Section 4.19).

Once a cumulative field becomes unknown within a Chunk, it remains `null` for that Chunk. A later valid value or zero must not restore it. A new Chunk may reassess observability.

---

## 3.21 v2.1 Core Metrics

Initial Core:

```text
directInsertedCharacters
directDeletedCharacters
deletionOperationCount

compositionCount
compositionMsTotal
compositionCommittedCharacters

chunkElapsedMs

durationMs
pauseClass
visibility
endedBy
```

---

## 3.22 Deferred Metrics

The initial v2.1 implementation does not record:

```text
inputOperationCount
intervalCount
intervalMsSum
intervalMsMin
intervalMsMax
intervalBins
contextBasis
```

Their necessity will be reevaluated after real-device validation.

---
# 4. Data Model

## 4.1 Basic Policy

v2.1 preserves the existing PROVENANCE Report / Event structure as much as possible.

1. Reuse the existing `typing` event as a Chunk.
2. Do not introduce a new `chunk` event type.
3. Add `pause` and `input-process-status` as new independent event types.
4. Store Input Process information primarily under `meta.inputProcess`.
5. Do not change the meaning of existing fields.
6. Give the Input Process specification its own independent version.
7. Finalize new values before event sealing.
8. Treat Summary as derived from the Event Log.

---

## 4.2 Version / Feature Compatibility

This revision is an update to an unimplemented draft, and the Input Process schema provisionally remains v1. If a different v1 is found to have already been distributed, do not publish incompatible required fields under the same v1; increment the version instead.

```json
{
  "formatVersion": 1,
  "pasteProvenanceVersion": 1,
  "inputProcessVersion": 1
}
```

The placement above is illustrative. Do not overwrite an existing `formatVersion` with `1` without implementation verification. `manifest.inputProcessVersion` indicates the output schema understood by that Writer; it does not guarantee that all historical Sessions were measured with that schema. Prefer each Session's sealed metadata when interpreting mixed Reports.

Before release, verify that old Reviewers correctly handle the new event types, zero-`charDelta` typing events, and unknown metadata. If they do not, do not claim compatibility; either increment `formatVersion` or explicitly state the minimum compatible Reviewer version. Continuing to edit an old Report must not alter old Events or their hashes.

---

## 4.3 Session Start

Each `session-start` event records the Input Process specification and capabilities actually used for that Session.

Example:

```json
{
  "id": "evt-...",
  "sessionId": "sess-...",
  "type": "session-start",
  "timestampClient": "2026-09-09T10:00:00.000Z",
  "charDelta": 0,
  "beforeSnippet": "",
  "afterSnippet": "",
  "meta": {
    "editorProvenanceVersion": 1,
    "inputProcess": {
      "version": 1,
      "characterUnit": "utf16-code-unit",
      "chunkPauseThresholdMs": 2000,
      "longPauseThresholdMs": 60000,
      "maxChunkElapsedMs": 5000,
      "compositionSettlementTimeoutMs": 1000,
      "clockDiscontinuityToleranceMs": 5000,
      "enabled": true,
      "initialCaptureStatus": "full",
      "initialIssueCodes": [],
      "capabilities": {
        "beforeInput": true,
        "inputEvent": true,
        "compositionEvents": true,
        "visibilityState": true,
        "monotonicClock": true
      }
    }
  }
}
```

---

## 4.4 typing = Chunk

One `typing` event represents one Chunk.

Example:

```json
{
  "id": "evt-abc123",
  "sessionId": "sess-xyz789",
  "type": "typing",
  "timestampClient": "2026-09-09T10:00:12.840Z",
  "charDelta": 18,
  "beforeSnippet": "",
  "afterSnippet": "abcdefghijklmnopqr",
  "meta": {
    "operations": 24,
    "insertedCharacters": 18,
    "deletedCharacters": 0,
    "inputProcess": {
      "directInsertedCharacters": 22,
      "directDeletedCharacters": 4,
      "deletionOperationCount": 4,
      "compositionCount": 3,
      "compositionMsTotal": 4210,
      "compositionCommittedCharacters": 14,
      "chunkElapsedMs": 2840,
      "hasCommittedDirectEdit": true,
      "closedBy": "inactivity",
      "captureStatus": "full",
      "issueCodes": []
    }
  }
}
```

---

## 4.5 Existing Diff Fields vs Input Process Cumulative Values

The meaning of existing fields:

```text
meta.insertedCharacters
meta.deletedCharacters
```

must not change.

New fields:

```text
meta.inputProcess.directInsertedCharacters
meta.inputProcess.directDeletedCharacters
```

represent cumulative Direct Edit activity observed within the Chunk.

Example:

```text
ab
↓ enter c
abc
↓ Backspace
ab
↓ enter d
abd
```

The final document diff is:

```text
ab → abd
```

while Input Process records:

```text
directInsertedCharacters = 2
directDeletedCharacters = 1
deletionOperationCount = 1
```

---

## 4.6 Zero Final-Diff Chunk

Example:

```text
abc
↓ enter x
abcx
↓ Backspace
abc
```

Even though the final document equals the starting document, the event is retained because Input Process activity occurred.

Example:

```json
{
  "type": "typing",
  "charDelta": 0,
  "meta": {
    "insertedCharacters": 0,
    "deletedCharacters": 0,
    "inputProcess": {
      "directInsertedCharacters": 1,
      "directDeletedCharacters": 1,
      "deletionOperationCount": 1,
      "compositionCount": 0,
      "compositionMsTotal": 0,
      "compositionCommittedCharacters": 0,
      "chunkElapsedMs": 860,
      "hasCommittedDirectEdit": true,
      "closedBy": "inactivity",
      "captureStatus": "full",
      "issueCodes": []
    }
  }
}
```

Chunk retention condition:

```text
document changed from chunk start
OR
input-process activity exists
```

---

## 4.7 Empty Chunk

If all of the following are zero or absent and there is no meaningful Input Process activity, do not create a `typing` event:

```text
directInsertedCharacters = 0
directDeletedCharacters = 0
deletionOperationCount = 0
compositionCount = 0
```

---

## 4.8 Pause Event

A Pause is recorded as an independent Event.

```json
{
  "id": "evt-pause123",
  "sessionId": "sess-xyz789",
  "type": "pause",
  "timestampClient": "2026-09-09T10:00:30.000Z",
  "charDelta": 0,
  "beforeSnippet": "",
  "afterSnippet": "",
  "meta": {
    "inputProcess": {
      "durationMs": 3420,
      "pauseClass": "normal",
      "visibility": "foreground",
      "endedBy": "direct-edit",
      "captureStatus": "full",
      "issueCodes": []
    }
  }
}
```

---

## 4.9 Pause Fields

| Field | Type | Meaning |
| --- | --- | --- |
| `durationMs` | number / null | Input interruption duration |
| `pauseClass` | string | `normal / long / unknown` |
| `visibility` | string | `foreground / background / unknown` |
| `endedBy` | string | First qualifying Activity observed after the Pause |
| `captureStatus` | string | `full / degraded / unavailable` |
| `issueCodes` | array | Known measurement issues |

---

## 4.10 endedBy

Required enum:

```text
direct-edit
composition-start
paste
copy
cut
undo
redo
explicit-operation
session-end
document-switch
finalization
unknown
```

This distinguishes a Pause ended by a new Activity from one terminated because the observation interval ended. `session-end / document-switch / finalization` do not imply that input resumed. Do not infer the reason for a Pause from `endedBy`.

---

## 4.11 timestampClient

For a Pause Event:

```text
timestampClient = Pause start time
```

Duration is calculated using a monotonic clock.

Hash Chain order is determined by Event array order and `previousHash`; do not reorder solely by `timestampClient`.

---

## 4.12 Capture Status Hierarchy

An Event's `captureStatus` describes the availability of the required measurements for that Event: `full / degraded / unavailable`. If some applicable required values are null, use `degraded`; if all applicable measurement values are unavailable, use `unavailable`. Semantically not-applicable null values are excluded from this determination.

At Session start, save `initialCaptureStatus` and capabilities in the sealed `session-start` event. Capabilities describe API availability; they do not guarantee delivery of all events for every input path. A capability that cannot be determined should be `null` rather than an assumed boolean.

Changes during the Session are recorded using the status events in Section 4.20; never rewrite `session-start` after it has been sealed. Use a status event even when there is no typing or Pause event on which to attach the failure. Session-level display is derived by recomputing the start state, status history, and individual Events. Recovery later in a Session does not erase earlier missing observation.

---

## 4.13 Issue Codes

Finite v1 set (array must be duplicate-free, lexicographically sorted, and contain at most 10 entries):

```text
input-without-beforeinput
composition-interrupted
composition-settlement-uncertain
clock-discontinuity
timing-unavailable
unclassified-change
metrics-exception
capture-disabled
observation-gap
operation-classification-uncertain
```

If the same issue occurs multiple times within one Event, keep only one instance of that code. Technical View counts indicate “number of Events carrying that code,” not the true number of underlying occurrences.

---

## 4.14 Summary

Derived values may be added to the Report summary.

Example:

```json
{
  "summary": {
    "inputProcess": {
      "version": 1,
      "chunkCount": 184,
      "directInsertedCharacters": 6184,
      "directDeletedCharacters": 764,
      "deletionOperationCount": 521,
      "compositionCount": 346,
      "compositionMsTotal": 482300,
      "compositionCommittedCharacters": 3981,
      "pauseCount": 71,
      "pauseDurationMsTotal": 438200,
      "longPauseCount": 4,
      "availability": "complete"
    }
  }
}
```

---

## 4.15 Summary Is Not Authoritative

`summary.inputProcess` is a derived value that can be recomputed from the Event Log.

Where possible, the Reviewer recomputes values from:

```text
payload.events
```

If recomputed Event values do not match Summary:

```text
Summary consistency: Mismatch
```

must be displayed.

---

## 4.16 Report Availability

| Value | Rule |
| --- | --- |
| `legacy-not-recorded` | all Sessions predate Input Process Metrics |
| `unsupported` | all measured portions use unknown schemas that cannot be interpreted |
| `unavailable` | a supported schema is present, but no valid measurement is available, including disabled capture |
| `partial` | valid values exist, but legacy Sessions, disabled periods, missing data, errors, or unsupported schemas are mixed in |
| `complete` | all supported scope was observed with no missing, disabled, or unsupported interval |

An empty Session with a supported schema and functioning measurement may be considered complete with zero activity. Status events must be included when evaluating missing observation; absence of `typing` events is not proof of complete capture.

Semantic Invalid is a separate axis and must always be shown independently; invalid values are excluded from aggregation. If Integrity is unverified or failed, recomputed values must be presented as values derived from an unverified record.

---

## 4.17 Hash Chain

After all fields of an Event, including `meta.inputProcess`, are finalized, pass the Event through the existing process:

```text
event
↓
previousHash
↓
canonicalization
↓
SHA-256
↓
hash
```

Input Process values must never be modified after sealing.

---

## 4.18 Semantic Validation

Hash Chain validation and semantic validity of Input Process values are separate.

For example, if:

```json
{
  "directDeletedCharacters": -5
}
```

is correctly hashed, the following state is valid:

```text
Integrity: Verified
Input Process semantic validation: Invalid
```

---

## 4.19 Required Fields and Semantic Consistency

Each v1 `typing` event must include all seven Core Input Process values, `hasCommittedDirectEdit` (boolean), `closedBy`, `captureStatus`, and `issueCodes`. The seven Core values are the three Direct values, the three composition values, and `chunkElapsedMs`.

`closedBy`: `inactivity / max-duration / explicit-operation / visibility / window-blur / save / session-end / document-switch / finalization / capture-change`.

- counts, character counts, and integer millisecond values must be non-negative safe integers or `null`. `NaN` / `Infinity` must be prohibited before JSON generation.
- if `hasCommittedDirectEdit=false`, then `chunkElapsedMs=null` (Not applicable). If true, `chunkElapsedMs` is either a non-negative value or `null` when unavailable.
- a single committed Direct Edit may have `chunkElapsedMs=0`. Do not treat `compositionMsTotal > chunkElapsedMs` as Invalid.
- for `full` Direct aggregates: `directInsertedCharacters - directDeletedCharacters = event.charDelta`.
- when both values are known: `compositionCommittedCharacters <= directInsertedCharacters`.
- when `deletionOperationCount` and `directDeletedCharacters` are known: `0 <= count <= deleted`; if `deleted=0`, then `count=0`.
- a Pause must have `charDelta=0` and empty snippets. Known duration must be at least the Session threshold; under 60000 is `normal`, at or above 60000 is `long`. `durationMs=null` with `pauseClass=unknown` is allowed only when it is independently known that the interval crossed the Pause threshold but exact duration is unavailable. If threshold crossing itself is unknown, represent the condition with `input-process-status`, not a Pause Event.
- logical conditions are evaluated against thresholds recorded in the Session. Numeric examples in this document are the initial defaults.
- unknown enum values or missing required fields are Invalid under supported v1. Unknown optional additional fields must be preserved and included in hash verification.

For `input-process-status` Events, require all of the following:

- `charDelta = 0`
- `beforeSnippet = ""`
- `afterSnippet = ""`
- `meta.inputProcess.captureStatus` is one of `full / degraded / unavailable`
- `meta.inputProcess.issueCodes` contains only the v1 enum defined in Section 4.13, is duplicate-free, lexicographically sorted, and has at most 10 entries
- no document-change Core metrics (Direct values, composition values, `chunkElapsedMs`, Pause fields)
- do not emit consecutive status Events that are completely identical to the preceding effective measurement state

Combinations in which state and Issue Codes contradict each other are Semantic Invalid. At minimum, `captureStatus = "full"` must not coexist with `capture-disabled`, `timing-unavailable`, `observation-gap`, or `metrics-exception`. Any future Issue Code must explicitly define the Capture Status values with which it is allowed to coexist.

## 4.20 Measurement-State Change Event

Capture disablement, recovery, observation gaps, and similar state changes that cannot be represented by `typing` or `pause` are recorded as sealed Events at the known position in the event sequence.

```json
{
  "type": "input-process-status",
  "charDelta": 0,
  "beforeSnippet": "",
  "afterSnippet": "",
  "meta": {
    "inputProcess": {
      "captureStatus": "degraded",
      "issueCodes": ["observation-gap"]
    }
  }
}
```

This does not represent metrics for a particular editing Event; it is a notification that the measurement state changes from that point onward. Ordinary Events still carry their own `captureStatus`. Required fields such as `id`, `sessionId`, and `timestampClient` are included in actual Events. Do not repeatedly emit identical status Events; record each state transition once. If the Core event queue itself fails, recording of this Event cannot be guaranteed, and `recordingStatus` must become `incomplete`.

## 4.21 Summary Aggregation Rules

- Aggregate Direct values exactly once from Events semantically classified as Direct Edit. This normally includes `typing` and Direct-origin `delete` events described in Section 5.20. If a user's direct replacement of a selected range is represented by a `replace` Event in the existing implementation, include it when Direct ownership is explicitly recorded.
- Exclude Paste / Undo / Redo / Cut and explicit Editor-command `replace` operations such as Replace / Replace All from Direct totals. Do not decide exclusion based only on Event type name.
- For each metric, if any applicable Event contains `null`, a missing required field, or a Semantic Invalid value, the complete Report total is `null`. Likewise, do not produce a complete total when the relevant scope is unknown because of legacy Sessions or similar gaps.
- A separate `observedTotals` object may contain the subtotal of known values only. Do not present that subtotal as a complete total padded with zeros.
- `coverage` may contain derived counts such as `knownEventCount / unknownEventCount / notApplicableEventCount` per metric and `legacySessionCount / unsupportedSessionCount / disabledSessionCount` for the Report.
- If all applicable Events are valid and the metric values are all zero, the complete total is zero. If the applicable scope itself cannot be determined, the complete value is `null`.
- `pauseCount` counts only Pauses with known durations that are confirmed to cross the threshold. Unknown-duration Pauses are counted separately in `unknownDurationPauseCount`. `longPauseCount` includes only Pauses whose long classification is known.
- `chunkCount` is the number of v1 `typing` Events. Do not mix legacy typing-event counts into the new Chunk count.
- If availability is `partial`, all observed subtotals must be labeled as covering only the observed range, and undefined proportions such as “percentage of time observed” must not be calculated.
- Aggregate first by Session, then at Report level. If Sessions use different thresholds, display that thresholds vary by Session rather than treating all Pauses as if they shared a universal 2-second threshold.

---

# 5. Chunk / Pause Generation Algorithm

## 5.1 Basic Objectives

The implementation must:

1. avoid double-counting intermediate IME state
2. keep Paste and other explicit operations out of `typing`
3. avoid storing per-keystroke timestamps
4. avoid inferring unavailable values
5. use the existing Event Queue / Hash Chain
6. ensure Input Process measurement does not interfere with document editing

---

## 5.2 Chunk Boundaries and the Existing Batch

The 2-second and 5-second thresholds serve different purposes:

```text
CHUNK_PAUSE_THRESHOLD_MS = 2000
MAX_CHUNK_ELAPSED_MS = 5000
```

Two seconds is the inactivity threshold for a Chunk boundary. Five seconds is a technical upper bound on the amount of continuous input left unflushed.

Do not remove the existing 5-second batch unconditionally. Before implementation, audit dependencies on log flushing, autosave, and Undo boundaries, and consolidate only the Input Process log boundary into the new scheduler. The same change must never be recorded by both old and new schedulers.

A 5-second max-duration split does not create a Pause and must not alter Undo/Redo editing-transaction boundaries. The concrete algorithm is defined in Section 5.28.

---

## 5.3 Internal State

Do not model the implementation with a single enum only. Track the following orthogonal state dimensions:

- recording: `pendingChunk` present / absent
- operation: none / Direct candidate / explicit operation / composition / composition settling
- gap: `lastActivityEnd` present / absent, plus gap candidate
- measurement quality: full / degraded / unavailable

A previously committed Chunk may still exist while COMPOSING. Never confuse uncommitted composition state with the committed document snapshot.

---

## 5.4 Temporary State

At minimum, keep:

```text
pendingChunk
pendingBeforeInput
pendingComposition
pendingPause
lastKnownValue
chunkTimer
```

Document snapshots remain in temporary memory only and are not added to the Report. Snapshot memory and diff computation may scale with document size; the O(1) claim does not apply to all temporary processing costs.

---

## 5.5 Clocks

Human-readable time:

```text
wall clock / ISO timestamp
```

Duration:

```text
monotonic clock
```

Stored durations must be non-negative integer milliseconds produced with `Math.floor`. Within a Chunk, accumulate duration as real values and floor once when storing; Report totals are sums of stored integer values.

---

## 5.6 Browser Events

Where supported, use:

```text
beforeinput
input
compositionstart
compositionend
paste
visibilitychange
blur
```

Treat `input` as the primary confirmation that the document actually changed.

---

## 5.7 beforeinput

`beforeinput` stores a temporary pre-change snapshot.

Example:

```json
{
  "beforeValue": "...",
  "selectionStart": 120,
  "selectionEnd": 125,
  "inputType": "insertText",
  "startedMono": 12345.6
}
```

`beforeinput` by itself must not change character counts.

---

## 5.8 Activity and Direct Edit Processing

1. At `beforeinput`, retain the candidate start time and pre-change state.
2. Confirm an actual document change via `input` or the completion notification of an existing command.
3. Determine the operation owner and record the change exactly once (Section 5.29).
4. Only after the candidate becomes a valid Activity, finalize the gap leading up to its start time.
5. Reserve event order as previous Chunk → Pause → current Activity; if Direct, aggregate it into the Chunk.
6. On completion, update `lastActivityEnd` with the Activity end time.

A `beforeinput` that is cancelled, a prevented change, or Backspace at the beginning of the document does not end a gap. A preceding candidate may temporarily suspend a Timer, but do not seal a Pause until the Activity is confirmed valid.

For IME, the Activity interval runs from the start of a valid composition to its completion. For explicit operations, it runs from command start to processing completion. Therefore, the gap is “previous Activity completion → next Activity start.” Do not count asynchronous Replace processing time as Pause.

Copy counts as an Activity only when the existing Copy provenance path recognizes a successful operation. Autosave, cursor movement, scrolling, typing in the search field, and mere focus movement are not Activities.

---

## 5.9 Chunk Timer

When 2 seconds have elapsed since the last qualifying Activity completion, and there is no unresolved operation or composition, flush the committed Chunk.

The gap start is always `lastActivityEnd`, never the Timer callback time. Preserve a gap candidate from the moment the prior Activity ends, including visibility state at that point.

Example: input completes at t=0, the Timer fires at t=2s, and the next input starts at t=5s. The Pause duration is 5000ms, not 3000ms. The Timer only promotes the candidate; do not seal the Pause until the next Activity or observation cutoff determines the interval.

---

## 5.10 pendingPause

The gap candidate stores only a constant number of values tied to the Session and Document:

```text
sessionId / document identity
startedMono = lastActivityEndMono
startedWall = lastActivityEndWall
startVisibility
backgroundObserved
visibilityObservationComplete
timingReliable
```

Leading idle from Session start to the first qualifying Activity is not a Pause. Do not discard `lastActivityEnd` merely because a Hard Boundary leaves no current Chunk. Gaps after Paste or Copy are also eligible.

---

## 5.11 Finalizing a Pause

At the start of the next valid Activity, calculate the gap from the previous Activity completion within the same Session and Document.

- raw gap below threshold: no Pause.
- raw gap at or above threshold: floor the entire interval to integer milliseconds and store it in `durationMs`. Determine `pauseClass` by comparing the stored `durationMs` with the thresholds.
- if timing is unreliable but some other observation establishes that the Pause threshold was crossed, a Pause Event may be stored with `durationMs=null`, `pauseClass=unknown`, `captureStatus=degraded`, and appropriate Issue Codes. Because exact duration is unknown, do not count it in known-threshold totals or long classification.
- if it is not even known whether the Pause threshold was crossed, do not create a Pause Event. Instead emit `input-process-status` with `observation-gap` and, where applicable, `timing-unavailable`; do not infer a Pause.

Flush the previous `typing` Chunk first, then reserve the Pause before the Event that ends it. Timer and Activity paths consume the same gap state exactly once; delayed Timers must not create duplicates.

---

## 5.12 Activity Start Time

Where possible, use:

Direct Edit:

```text
beforeinput start
```

IME:

```text
compositionstart
```

Paste:

```text
paste operation start
```

Undo / Redo and similar operations:

```text
operation start
```

as the Pause endpoint.

In degraded environments, use an observable time such as `input`, but mark Capture Status as `degraded`.

---

## 5.13 IME Start

At `compositionstart`, temporarily retain:

```text
composition start time
before value
selection
```

Normally suspend the Chunk Timer during composition.

---

## 5.14 IME Intermediate State

Do not count intermediate states such as `insertCompositionText` as independent Direct Edits.

Do not store uncommitted IME text in the Report.

---

## 5.15 IME End and Exactly-Once Settlement

Do not assume that an additional `input` always follows `compositionend`. Also support browsers where the final document state is already reflected before the end notification. See [W3C UI Events](https://www.w3.org/TR/uievents/#events-composition-input-events).

Keep a temporary composition identifier and associate the pre-composition snapshot with the settled post-composition snapshot. The Editor adapter determines the final state according to the browser's actual event ordering and must not aggregate related `input` twice. A separate ordinary input immediately afterward must not be absorbed into the previous composition.

The maximum post-end settlement wait is `COMPOSITION_SETTLEMENT_TIMEOUT_MS = 1000` (provisional, recorded in `session-start`). Do not routinely wait the full second; finalize immediately when the state is known. Timer expiry alone is not proof of settlement. If settlement remains uncertain, record `composition-settlement-uncertain`, set unknown aggregate values to `null`, and do not block editing indefinitely.

When a valid start/end pair is settled:

```text
compositionCount += 1
compositionMsTotal += compositionendMono - compositionstartMono
compositionCommittedCharacters += committed inserted amount
directInsertedCharacters += committed inserted amount
directDeletedCharacters += committed removed amount
if deleted > 0: deletionOperationCount += 1
```

Selection replacement and reconversion must account for both insertion and deletion. Intermediate candidates are never counted. `compositionCommittedCharacters` is a subset of Direct insertion. If the existing path semantically belongs to an explicit-operation owner, do not double-count it as Direct; if classification is uncertain, use `null` plus an Issue Code.

---

## 5.16 Pause Before IME

Example:

```text
previous Direct Edit
↓ 4 seconds
compositionstart
↓ 3 seconds
compositionend
```

The Pause is approximately 4 seconds.

Do not include composition duration in the Pause.

---

## 5.17 Composition Cancel / Commit to the Same Document State

If a valid composition begins and ends, but the settled document equals the pre-composition document, increment `compositionCount` by 1 and record the duration. Final-diff-based inserted and deleted amounts are zero.

Reconversion/recommit to identical text cannot be distinguished from cancellation using the final document alone, so do not create a “cancel count” metric. Retain a composition-only `typing` event with `hasCommittedDirectEdit=false` and `chunkElapsedMs=null` (Not applicable).

If the same Chunk also contains a separate committed Direct Edit, calculate `chunkElapsedMs` from those Direct Edit intervals.

---

## 5.18 Hard Boundary

The following are Hard Boundaries by default:

```text
Pause >= 2000 ms
Paste
Copy
Cut
Undo
Redo
Replace
Replace All
Format Markdown
TOC
Markdown formatting command
Large Delete independently recorded as an event
File Open
History Restore
Document switch
Session end
Report finalization
```

Before a Hard Boundary, flush the current committed Chunk. Do not include uncommitted composition text; finalize or fall back according to Sections 5.15, 5.21, and 5.25.

---

## 5.19 Copy

Copy does not change the document, but it is a Chunk Boundary because it is preserved as an independent PROVENANCE Event in the event sequence.

Copy does not change Direct Edit character totals.

---

## 5.20 Large Delete

Preserve the existing independent `delete` Event. When a direct selection deletion or similar operation is classified as Large Delete, that Event must include:

```text
directInsertedCharacters = 0
directDeletedCharacters = actual removed UTF-16 amount
deletionOperationCount = 1
captureStatus / issueCodes
```

Do not add the same deletion to `typing`, but include the `delete` Event once in the Report's Direct totals. This specification does not change the existing threshold such as 100 characters. Cut, Undo, and Replace must not be reclassified into Direct-origin Large Delete merely because they remove text.

Therefore, the phrase “separated from Direct Edit” in Section 3.14 refers to Event-type separation; it does not mean excluding Direct-origin Large Delete from Report-level Direct totals.

---

## 5.21 blur / visibilitychange

On window `blur` or document `hidden`, flush committed Chunks. Blur of the Editor element alone is not an independent Hard Boundary; toolbar operations are handled by their command paths.

These events do not change the start or end points of an existing gap. Visibility is tracked from previous Activity completion, so a transition to hidden before the 2-second threshold is still captured.

During composition, do not flush uncommitted text. If a previously committed Chunk can be safely separated, it may be flushed; otherwise defer it. If composition completion cannot be confirmed after returning, transition to the abnormal/fallback path.

---

## 5.22 Timer Throttling

Do not assume `setTimeout(2000)` fires exactly after two seconds.

At the next qualifying Activity, reevaluate:

```text
currentMono - lastActivityMono
```

The Timer is only an aid; monotonic clock difference is authoritative.

---

## 5.23 Long Pause

At 60 seconds or more:

```text
pauseClass = "long"
```

The Input Process module must not start or end a Session solely because of Long Pause.

Existing SessionManager rules remain separate.

---

## 5.24 Ordinary Save

Settle any composition and Chunk that can be safely finalized, then capture the document snapshot and corresponding Event Queue cutoff as a pair. Wait for the queue through that cutoff, then save that exact snapshot. Edits made while saving belong to the next save and must not be combined with an earlier document snapshot.

An unfinished gap is not finalized merely because Save occurred; preserve it in memory within the same live Session. A Save flush uses `closedBy=save`, but Save itself does not create a Pause or alter Undo boundaries.

After application restart or loading another Document, do not carry an in-memory gap forward. Reopening an intermediate saved Report begins a new Session; do not infer elapsed time after the previous saved endpoint. If composition is still uncommitted and a consistent Report snapshot cannot be produced, preserve the ordinary text-salvage path and do not falsely indicate that a consistent Report save succeeded.

---

## 5.25 Submit / Finalization

Finalization establishes one internally consistent snapshot.

1. Separate new changes from the snapshot, using a short editing lock or equivalent revision freeze.
2. Determine whether any active composition can be validly settled. If not, do not seal uncommitted text as history; postpone Submit and ask the user to commit/cancel the composition. Normal editing and text salvage remain available.
3. Record the finalization cutoff and flush the current committed Chunk.
4. Finalize the trailing gap within the same Session up to the cutoff. Do not create a Pause if it is below threshold. Use `endedBy=finalization`.
5. Wait for the reserved Event Queue to complete. If it fails, do not sign the Report as complete.
6. Derive Summary, Document Hash, Event Log Hash, and Final Chain Hash from the same snapshot, then perform signing / Anchoring according to the existing design.
7. Do not add time spent choosing a save destination, communicating with a server, or requesting a signature to the trailing Pause.

A retry after save cancellation or failure must not append duplicate trailing Chunks, Pauses, or signatures to the same snapshot. If editing resumes, follow the existing finalization lifecycle and create a new snapshot. If the document and Core Events are consistent, missing Input Process values alone do not prohibit signing.

---

## 5.26 Event Order

Example:

```text
typing
↓
5-second gap
↓
paste
```

must produce:

```text
typing
pause
paste
```

After its duration is known, the Pause is queued before the Event that ended it.

---

## 5.27 Most Important Invariants

### No Double Counting

The same document change must not be counted in more than one category.

Example:

```text
Paste 100 characters
```

must not produce both:

```text
paste.pastedCharacters = 100
```

and:

```text
typing.inputProcess.directInsertedCharacters = 100
```

### No Mutation After Seal

After Event sealing, never modify fields such as:

```text
durationMs
compositionMsTotal
directDeletedCharacters
```

---

## 5.28 Periodic Flush During Continuous Input

When a `pendingChunk` has existed for 5000ms, flush at the first safe point outside composition or an unresolved operation. Evaluate the time limit using raw monotonic values and also check at the next Activity in case the Timer was delayed.

- use `closedBy=max-duration`; do not create a Pause; retain `lastActivityEnd`.
- do not split composition; a long composition may cause a Chunk to exceed 5000ms.
- flushing `pendingChunk` must not split the editing Undo stack.
- if timing is unknown, an auxiliary Timer may still perform a safety flush, but do not invent elapsed-time values.
- this limit does not guarantee crash-safe persistence. Separately verify that existing autosave frequency and recovery behavior are not degraded.

## 5.29 Operation Owner, Diff, and Serialization

Assign every committed change to exactly one owner using existing command identifiers and composition correlation. Priority: known Paste/Cut/Undo/Redo/Replace/Format and other explicit operations, then composition, then known Direct Edit, then unclassified. Do not infer human/AI status or source from `inputType` alone.

- `input` events caused by explicit operations belong to that owner and must not be counted again by the Direct listener.
- audit drop / drag moves, file insertion, History Restore, and programmatic `value` changes. If there is no existing classification, do not let them silently fall into Direct; preserve Core diff as `unclassified-change`.
- for Direct diff, prefer an existing committed edit range / Editor patch. If unavailable, use a consistent minimal single-replacement diff by removing the common prefix and non-overlapping suffix. If the actual inserted amount cannot be determined unambiguously from the operation, use `null`.
- keep UTF-16 as the comparison unit. Use the Editor's already normalized document representation, including existing newline normalization; do not add Unicode normalization solely for Input Process.
- if ordinary replacement produces the same document state and no actual change is observable, count Direct values as zero. Do not reconstruct keypress counts.
- never expose snapshots or uncommitted composition text in debug logs, exceptions, or telemetry.
- reserve Event Queue ordering synchronously before asynchronous hashing. Do not silently skip failed Events. Use the existing seal specification for the set of hashed fields; do not accidentally include the event's own hash or signature where the current format excludes them.

## 5.30 Session / Document Boundaries

At Session end, Document switch, File Open, or History Restore, flush the committed Chunk of the old Document and record the trailing gap up to the observable cutoff in the old Session. Use `endedBy=session-end` or `endedBy=document-switch`; do not create a Pause below threshold.

Do not carry gap, composition, or clock origin into a new Session or another Document. If SessionManager closes a Session before 60 seconds of inactivity, that Session may never contain a Long Pause. Do not change SessionManager merely to create Long Pauses.

If page exit or a crash prevents normal closure, do not infer unsaved input after the last persisted Event. On BFCache restoration or another detected observation interruption, record `observation-gap` and resume from a new observation interval.

---
# 6. Compatibility, Exceptional Cases, and Fallback

## 6.1 Fail-Soft Principle

Input Process measurement must not take priority over document editing.

Even if some metrics cannot be obtained, continue the following where reasonably possible:

- Editor operation
- Save
- Submit
- Core provenance recording

Do not infer unavailable values.

---

## 6.2 Determine by Capability, Not Browser Name

Do not decide support solely from names such as Chrome or Firefox.

Record the capabilities actually available at Session start.

---

## 6.3 Capture Status

Use:

```text
full
degraded
unavailable
```

Failure of only some metrics must not automatically make the entire Session `unavailable`.

---

## 6.4 No beforeinput

Even when `beforeinput` cannot be obtained, continue recording if a safe document diff can be calculated from:

```text
lastKnownValue
↓
input
↓
currentValue
```

Use `degraded` Capture Status where appropriate.

---

## 6.5 beforeinput Only

If `beforeinput` occurs but no corresponding `input` occurs, do not count it as a document change.

---

## 6.6 input Only

If `input` occurs without a corresponding `beforeinput`, a diff against `lastKnownValue` may be used as fallback.

The Issue Code:

```text
input-without-beforeinput
```

may be attached.

---

## 6.7 Unknown or Empty inputType

Do not discard a document change merely because `inputType` is unknown or empty. Preserve the existing Core diff where possible. However, the ability to calculate a text diff is separate from the ability to classify it as Direct.

If command context or equivalent evidence cannot determine the owner, exclude it from Direct aggregates and mark it with `unclassified-change` or `operation-classification-uncertain`. Do not infer automation merely because `inputType` is unknown.

---

## 6.8 Document Change Without a Known Event Path

If the document changes without a known path, do not automatically classify it as ordinary `typing`.

Treat it as:

```text
unclassified-change
```

or mark Capture Status as `degraded`.

---

## 6.9 Composition Events Unavailable

If composition events are unavailable:

```text
compositionCount = null
compositionMsTotal = null
compositionCommittedCharacters = null
```

Do not use zero, because zero would mean “observable and no composition occurred.”

If Direct Edit diff can still be calculated, other metrics may continue to be recorded.

---

## 6.10 compositionstart Without compositionend

Do not invent an end time.

Issue:

```text
composition-interrupted
```

Use `null` for affected timing information.

Preserve document diff if it can be obtained safely.

---

## 6.11 compositionend Only

If there is no corresponding start, do not generate a duration.

---

## 6.12 Duplicate compositionstart

If a new `compositionstart` occurs while a previous composition remains open, do not treat the previous composition as normally completed.

---

## 6.13 Background During Composition

If a composition spans a background interval and duration reliability cannot be established, the initial v2.1 implementation uses the conservative fallback:

```text
compositionMsTotal = null
```

If committed character information can still be obtained reliably, preserve it.

---

## 6.14 Speech Input, Predictive Input, and Assistive Technology

If these are observed as ordinary `input`, do not infer the source.

Do not generate values such as:

```text
inputSource = human
inputSource = AI
inputSource = voice
```

---

## 6.15 Autocorrect

If an autocorrection or similar operation appears as one multi-character document change, that diff may be recorded.

Do not use it to classify human vs AI input.

---

## 6.16 Monotonic Clock Unavailable

If a monotonic clock is unavailable, time-based metrics such as:

```text
chunkElapsedMs
durationMs
compositionMsTotal
```

become `null`.

Non-time metrics such as character counts may continue.

---

## 6.17 Clock Discontinuity

For the same observation interval, record `clock-discontinuity` if any of the following are detected:

- wall-clock delta and monotonic delta differ by more than 5000ms
- monotonic time moves backward
- time origin changes

Set only the affected durations to `null`, then resume from a new baseline.

Do not attempt to distinguish wall-clock adjustment from Sleep using this difference alone. Even where a normal monotonic delta remains available, the initial version conservatively treats the affected interval as missing because the cause is unknown. Previously sealed valid intervals are not changed.

If both clocks advance similarly during Sleep, this method may not detect the Sleep interval. Even when a duration can be measured, do not infer presence or thinking. The use of a monotonic clock for duration is based on [W3C High Resolution Time](https://www.w3.org/TR/hr-time-3/); implementation differences during Sleep must be checked on real devices.

---

## 6.18 Internal Input Process Exception

If an additional Input Process calculation throws an exception, retain the existing Core Event where its ordinary typing diff can still be obtained safely.

For example, allow:

```text
Input Process: Partial
Integrity: Verified
```

---

## 6.19 Distinguish from Hash Chain Failure

Do not conflate Input Process metric acquisition failure with:

```text
Event sealing failure
Hash Chain append failure
```

A Core provenance recording failure must affect the Report-level state, for example:

```text
recordingStatus = incomplete
```

---

## 6.20 Legacy Report

If `inputProcessVersion` is absent, treat the Report as:

```text
legacy-not-recorded
```

Do not interpret the absence as zero.

---

## 6.21 Continue Editing a Legacy Report in a New Editor

Do not rewrite old Events.

For example, allow:

```text
Session 1: Legacy – not recorded
Session 2: Input Process v1
```

Report Availability becomes `partial`.

---

## 6.22 Future Version

If a Reviewer opens an unknown version such as:

```text
inputProcessVersion = 2
```

show:

```text
Unsupported version
```

Continue verification of understandable components such as Hash Chain, Document Hash, and Signature where possible.

---

## 6.23 Unknown Fields

Do not delete unknown fields when verifying hashes.

Even if their semantics are unknown, canonicalize the stored Event as a whole when performing hash verification.

---

## 6.24 New Pause Event and Old Reviewers

Compatibility-test whether old Reviewers can process the unknown `pause` Event.

Do not interpret an unknown Event type itself as tampering.

---

## 6.25 MD//WORKS Import

Regression-test that the MD//WORKS-side Report importer can still extract the Markdown document correctly when new Input Process fields are present.

---

## 6.26 Semantic Validation

Do not silently normalize any of the following into valid values:

- negative counts
- negative durations
- `NaN`
- `Infinity`
- wrong type
- unknown enum

A correctly hashed Report may still have Semantic Validation = `Invalid`.

---

## 6.27 Fallback Priority

1. accurately available → normal recording
2. some supporting information unavailable → Degraded
3. specific metric unavailable → that field is `null`
4. unclassifiable change → Unclassified / Partial
5. Input Process measurement unavailable → Unavailable
6. Core provenance failure → Report Incomplete

---

## 6.28 UI Notification

Do not show a Toast for every minor fallback.

Record such details in Technical View.

However, notify the user for significant conditions such as:

- Input Process entirely Unavailable
- Core Event recording failure
- Hash Chain recording failure

---

## 6.29 Browser Crash / Power Loss

A sudden termination can lose an unflushed Chunk, pending Pause, or active composition. The proposed 5-second periodic flush reduces this exposure but does not provide durable or crash-proof persistence.

On recovery, verify consistency between the saved document and sealed Events. If the missing diff is unknown, treat it as incomplete / `observation-gap`. Do not reconstruct past input or timing by inference. A new Crash Recovery / Durable Event Buffer is outside this specification, but the implementation must not degrade existing save/recovery behavior.

---

# 7. Technical View Display Specification

## 7.1 Purpose

Technical View is:

> **a view for examining observed facts and recording quality, not a judgment screen**

It should allow inspection of:

- Integrity
- Recording Status
- Input Process Version
- Capture Capability
- Input Process Metrics
- Session differences
- Fallback / Issues
- Event details

---

## 7.2 Separation from Teacher View

In the initial v2.1 release, do not add new Input Process Metrics to Teacher View.

Teacher View integration is considered separately after beta validation.

---

## 7.3 Three Distinct Display Axes

### Integrity

Internal consistency of the presented record and agreement with signatures / external Anchors. Guarantee scope follows Section 2.4.

### Capture / Availability

How completely Input Process information was obtained.

### Observed Metrics

Values actually observed and recorded.

Do not merge these into one generic “Verified” status.

---

## 7.4 Status Summary

Example:

```text
Technical View

Integrity
✓ Document Hash        Verified
✓ Event Chain          Verified
✓ Final Chain Hash     Verified
✓ Final Signature      Verified

Recording
● Recording Status     Complete
● Input Process        Complete
● Input Process Schema v1
● Paste Provenance     v1
```

If no signature is present:

```text
Final Signature        Not present
```

If verification fails:

```text
Final Signature        Invalid
```

These states must remain distinct.

---

## 7.5 Color

- Green: technical verification success
- Yellow / Orange: Partial / unavailable / warning
- Red: hash mismatch, schema invalid, or similar technical failure
- Gray: Legacy / not present / unsupported

Do not color a metric red or green solely because its numeric value is large or small.

---

## 7.6 Input Process Summary

Example:

```text
Input Process Metrics

Schema                    v1
Availability              Complete
Character unit            UTF-16 code unit
Chunk threshold           2.0 s
Long-pause threshold      60 s

Typing chunks             184
Directly inserted chars   6,184
Directly deleted chars      764
Deletion operations         521

IME compositions            346
IME committed chars       3,981
IME composition duration  8m 02s

Input pauses                71
Long pauses                  4
Measured pause duration   7m 18s
```

---

## 7.7 Prohibited Labels

Do not use:

```text
Mistakes
Thinking time
Human typing time
Natural typing
AI-like input
Suspicious pauses
Correction score
Human score
AI score
Authenticity score
```

---

## 7.8 Pause Display

Example:

```text
Input Pauses

Pauses ≥ 2.0 s           73
Long pauses ≥ 60 s        4
Observed pause subtotal 12m 38s
Foreground pauses         41
Background observed       27
Unknown visibility         5
Unknown-duration pauses    2
```

Do not display this as `Thinking time`.

---

## 7.9 IME Display

```text
IME Composition

Composition sessions       346
Committed characters     3,981
Measured composition time 8m 02s
```

Explanation:

> Composition metrics represent browser-observed composition activity. They do not identify the input source or prove human typing.

---

## 7.10 Direct Edit Display

```text
Direct Editing

Inserted characters       6,184
Deleted characters          764
Deletion operations          521
```

Do not automatically calculate correction rate, mistake rate, or similar interpreted metrics.

---

## 7.11 Capture Capability

For each Session, allow display such as:

```text
beforeinput           Available
input                 Available
composition events    Available
visibility state      Available
monotonic clock       Available
```

---

## 7.12 Session History

Example:

```text
▼ Session 1
  Started        2026-09-09 09:12
  Platform       Windows
  Browser        Firefox 154
  Editor         MD//WORKS PROVENANCE ...

  Input Process
  Schema         v1
  Capture        Full
  Chunks         68
  Pauses         24
  IME sessions   181
```

This allows Session-by-Session environment differences to be examined.

---

## 7.13 Partial

Example:

```text
Input Process Metrics
Status: Partial

Inserted characters        1,824
Deleted characters           213
Deletion operations           94
IME compositions       Not available
IME duration           Not available
Pauses                        31
```

Do not render `null` simply as `0` or as an unexplained dash.

---

## 7.14 Legacy

```text
Input Process Metrics
Status: Legacy – not recorded

This Report was created before Input Process Metrics were introduced.
No conclusion should be drawn from the absence of these values.
```

---

## 7.15 Mixed Report

```text
Input Process Metrics
Status: Partial

2 of 3 sessions contain Input Process Metrics.
```

Observed subtotals must be labeled:

```text
Recorded sessions only
```

---

## 7.16 Semantic Validation

Example:

```text
Integrity
Event Chain              Verified

Input Process
Schema                   v1
Semantic validation      Invalid
```

Do not conflate hash integrity with metric validity.

---

## 7.17 Issue Codes

Example:

```text
Capture notes

• input-without-beforeinput: 3 Events
• composition-interrupted: 1 Event
• clock-discontinuity: 1 Event
```

Keep the normal Summary from becoming a wall of warnings; expose these details in an expandable technical area.

---

## 7.18 Event Detail

Example:

```text
10:14:22  typing
+18 / -3
Chunk elapsed: 2.8 s
Deletion operations: 2
IME compositions: 3

10:14:37  pause
Duration: 4.3 s
Visibility: foreground

10:14:41  paste
Pasted: 245 chars
Provenance: unverified
```

---

## 7.19 Raw JSON

Technical View may provide:

```text
View Raw Event
View JSON
```

The default display should remain human-readable.

---

## 7.20 Authoritative Source

Technical View independently recomputes values from the Event Log.

Summary is a cache / comparison representation.

On mismatch, display:

```text
Input Process Summary: Mismatch
```

---

## 7.21 No Automatic Classification

The initial v2.1 implementation must not generate:

```text
Likely human
Likely AI
Suspicious
Fraud
Cheating
Human score
AI score
```

Do not produce threshold-based warnings from any single metric either.

---

## 7.22 Persistent Explanation

> **About Input Process Metrics**  
> These values describe editing activity observed by the Editor, including direct edits, composition activity, and input pauses. They do not by themselves prove human authorship or determine AI use or misconduct.

---

## 7.23 No Graphs in the Initial Version

The initial v2.1 Technical View does not display:

- Typing speed graph
- Pause graph
- Histogram
- Heatmap
- Human-like waveform

First present neutral numeric observations; reconsider visualization after beta validation.

---

# 8. Acceptance Test / Validation Plan

## 8.1 Purpose

Confirm all of the following:

1. document editing is not broken
2. counting rules are implemented as specified
3. no double counting occurs
4. `0 / null / missing` remain distinct
5. normal input methods are not misclassified
6. Hash Chain and related mechanisms remain intact
7. legacy Report compatibility is preserved where intended
8. Technical View displays each status correctly

These tests do not evaluate AI-detection accuracy.

---

## 8.2 Priority

### P0 — Release Blocker

- no lost or duplicated document text
- IME works correctly
- Undo / Redo work correctly
- Paste provenance works correctly
- Hash Chain remains correct
- no double counting
- no missing Events at Save / Submit
- `0 / null / missing` semantics correct
- Input Process failure alone does not prevent document saving
- Core functionality works on Windows 11 Firefox / Chrome / Edge, according to the Browser Matrix in Section 8.32

### P1

- Windows 11 Brave
- macOS / iPad, according to the Browser Matrix
- background / Pause behavior
- legacy Reports
- Technical View
- Fallback

### P2

- speech input
- assistive technology
- Computer Use / RPA
- future Experimental metrics

---

## 8.3 Test Types

### Deterministic Test

Use Fake Clock or equivalent mechanisms to test exact boundary behavior.

### Browser Integration Test

Use real browser input.

### Exploratory Input Study

Observe differences produced by IME, speech input, automation, and related input paths.

---

## 8.4 Chunk Boundaries

| ID | Operation | Expected |
| --- | --- | --- |
| AT-CH-01 | a → 500ms → b → 500ms → c | 1 Chunk |
| AT-CH-02 | a → 1999ms → b | 1 Chunk |
| AT-CH-03 | a → 2000ms → b | typing → pause → typing |
| AT-CH-04 | a → 2001ms → b | typing → pause → typing |
| AT-CH-05 | 2 seconds after input | Chunk flush, Pause not yet sealed |
| AT-CH-06 | input completes at t=0, timer at t=2s, next input at t=5s | Pause=5000ms |
| AT-CH-07 | Finalization after input | trailing Pause processing |
| AT-CH-08 | Timer delay | correct after elapsed-time reevaluation |

---

## 8.5 Zero Final Diff

Initial:

```text
abc
```

Operations:

```text
x
Backspace
```

Final:

```text
abc
```

Expected:

```text
charDelta = 0
directInsertedCharacters = 1
directDeletedCharacters = 1
deletionOperationCount = 1
```

Do not discard the `typing` Event.

---

## 8.6 Empty Chunk

Only:

- cursor movement
- Shift
- Ctrl
- Backspace at start of document
- keys that do not change the document

Expected:

```text
typing event = none
```

---

## 8.7 Direct Edit

### AT-ED-01

Enter `abc`:

```text
directInsertedCharacters = 3
```

### AT-ED-02

`abc → ab`

```text
directDeletedCharacters = 1
deletionOperationCount = 1
```

### AT-ED-03

Select 10 characters and Delete:

```text
directDeletedCharacters = 10
deletionOperationCount = 1
```

### AT-ED-04

Select 3 characters → directly enter 1 character, when the existing classification is Direct:

```text
directDeletedCharacters = 3
directInsertedCharacters = 1
deletionOperationCount = 1
```

---

## 8.8 Unicode

Verify:

```text
A
あ
漢
😀
👨‍👩‍👧‍👦
```

Counts must match JavaScript UTF-16 code units.

---

## 8.9 IME

### AT-IME-01

Commit `山` through IME.

Expected:

```text
compositionCount = 1
compositionCommittedCharacters = 1
directInsertedCharacters = 1
```

### AT-IME-02

Commit a multi-character Japanese string.

Intermediate composition text must not be double-counted.

### AT-IME-03

Composition cancel.

Handle zero final document change correctly.

### AT-IME-04

Pause more than 2 seconds while composition remains active.

Do not generate a Pause.

### AT-IME-05

3-second Pause → `compositionstart` → commit after 4 seconds.

Expected:

```text
pause ≈ 3 seconds
composition ≈ 4 seconds
```

---

## 8.10 IME Exceptional Cases

### AT-IME-10

`compositionstart` without an end Event.

Expected:

```text
composition-interrupted
timing = null
```

Editing and Hash Chain should continue where possible.

### AT-IME-11

`compositionend` only.

Do not invent duration.

---

## 8.11 Paste

### AT-PA-01

External 100-character Paste.

```text
paste.pastedCharacters = 100
```

Do not also add 100 to Direct Edit.

### AT-PA-02

Verified Internal Paste.

Existing Paste Provenance must remain correct, with no double counting.

### AT-PA-03

Cut → Paste.

Do not duplicate Cut/Paste changes in Direct Edit.

### AT-PA-04

`typing → 4 seconds → Paste`

Order:

```text
typing
pause
paste
```

---

## 8.12 Undo / Redo

### AT-UR-01

`typing → Ctrl+Z`

Undo-related deletion must not be added to Direct Edit.

### AT-UR-02

`typing → Undo → Redo`

Order:

```text
typing
undo
redo
```

### AT-UR-03

Japanese IME commit → Undo.

Undo stack must remain intact.

P0.

---

## 8.13 Explicit Operations

Verify:

- Replace
- Replace All
- Format Markdown
- TOC
- Heading
- Bold
- Italic
- List
- Task
- Link
- Table
- Large Delete

Expected:

- correct Chunk boundary
- correct existing Event
- no double counting in Direct Edit
- operations that support Undo remain undoable

---

## 8.14 Pause

### AT-PS-01

2.5-second foreground Pause.

```text
pauseClass = normal
visibility = foreground
```

### AT-PS-02

65 seconds.

```text
pauseClass = long
```

The Input Process module must not independently create a new Session.

### AT-PS-03

Switch to another tab for 10 seconds.

```text
visibility = background
```

### AT-PS-04

Multiple foreground/background transitions.

Background observation must be preserved correctly.

---

## 8.15 Timer Throttling

Even when the Timer callback is delayed, calculate the correct Pause from monotonic Activity-to-Activity time.

---

## 8.16 Clock / Sleep

### AT-CL-01

PC Sleep → resume.

Do not fabricate an abnormal foreground Pause.

### AT-CL-02

Advance wall clock substantially while monotonic clock remains unchanged.

Detect:

```text
clock-discontinuity
```

### AT-CL-03

Monotonic clock unavailable.

Time-based metrics become `null`; non-time metrics continue.

---

## 8.17 beforeinput Fallback

### AT-FB-01

Normal `beforeinput`:

```text
Capture = Full
```

### AT-FB-02

Disable `beforeinput`:

```text
Capture = Degraded
```

Preserve document diff where possible.

### AT-FB-03

`beforeinput` only, no `input`.

Do not count a document change.

---

## 8.18 Unclassified Change

Cause a document change through an unknown path.

Expected:

```text
unclassified-change
```

or Partial.

Do not silently classify it as Direct Edit.

---

## 8.19 0 / null / missing

### AT-NA-01

Composition capability available, English-only direct input:

```text
compositionCount = 0
```

### AT-NA-02

Composition capability unavailable:

```text
compositionCount = null
```

### AT-NA-03

Legacy Report:

```text
field missing
Legacy – not recorded
```

### AT-NA-04

Legacy Session + new Session:

```text
Report availability = partial
```

---

## 8.20 Save

### AT-SV-01

Save immediately after input.

Current Chunk must not be lost.

### AT-SV-02

Ordinary Save while a Pause is pending.

Do not finalize an unfinished Pause solely because Save occurred.

### AT-SV-03

Save immediately after IME.

Do not double-count document text and composition.

### AT-SV-04

Artificially fail an Input Process calculation.

Expected:

```text
Save = success
Input Process = Partial
Integrity = Verified
```

---

## 8.21 Finalization

### AT-SU-01

Normal Finalization.

Follow the defined processing order.

### AT-SU-02

Event Queue still has pending work.

Do not calculate Final Hash before the queue reaches the required cutoff.

### AT-SU-03

Sign a Partial Report.

Allow:

```text
Integrity = Verified
Input Process = Partial
```

---

## 8.22 Hash Chain

### AT-HS-01

Normal Report → Verified.

### AT-HS-02

Modify `pause.meta.inputProcess.durationMs` → Hash mismatch.

### AT-HS-03

Modify `directDeletedCharacters` → Hash mismatch.

### AT-HS-04

Modify only Summary:

```text
Event Chain = Verified
Summary consistency = Mismatch
```

---

## 8.23 Semantic Validation

Create correctly hashed invalid data:

```text
directDeletedCharacters = -4
```

Expected:

```text
Integrity = Verified
Input Process semantic validation = Invalid
```

---

## 8.24 Legacy

### AT-LG-01

Open an old Report in the new Reviewer.

Input Process = Legacy.

### AT-LG-02

Continue editing an old Report in the new Editor.

Old Session remains unrecorded; new Session uses v1.

### AT-LG-03

Open a new Report in MD//WORKS.

Markdown document is extracted successfully.

---

## 8.25 Future Version

```text
inputProcessVersion = 99
```

Expected:

```text
Input Process = Unsupported
```

Continue understandable Integrity verification.

---

## 8.26 Technical View

Verify each of the following states independently:

```text
Integrity Verified + Input Complete
Integrity Verified + Input Partial
Legacy
Unsupported
Integrity Failed
Semantic Invalid
Summary Mismatch
```

They must not be conflated.

---

## 8.27 Prohibited UI Phrasing

The following must not be generated as classification results or metric names in either English or Japanese UI. Do not use a naïve substring test that would also forbid explanatory text such as “does not prove human authorship.”

```text
Human
AI-generated
Suspicious
Cheating
Mistakes
Thinking time
Human score
AI score
Authenticity score
```

---

## 8.28 Speech Input

The goal is not to identify speech input, but to confirm:

- Editor works correctly
- recording works correctly
- no crash
- no invalid IME metrics
- no false AI label

---

## 8.29 Assistive Technology

Where possible, test accessibility input methods.

Confirm that alternate input paths do not break the Editor or Report.

---

## 8.30 Computer Use / RPA

Enter the same text using:

1. human input
2. Computer Use
3. RPA
4. Paste

Compare observed values.

The purpose is:

> to observe differences

not to measure automated-input detection accuracy.

---

## 8.31 Performance

Test at least:

```text
10,000 characters
50,000 characters
100,000 characters
```

Evaluate:

- typing latency
- IME responsiveness
- Chunk flush
- Technical View
- Save / Submit
- memory
- Report size

Initial criterion:

> no clearly perceptible interaction degradation compared with the current version.

---

## 8.32 Browser Matrix

| OS | Browser | Priority |
| --- | --- | --- |
| Windows 11 | Firefox | P0 |
| Windows 11 | Chrome | P0 |
| Windows 11 | Edge | P0 |
| Windows 11 | Brave | P1 |
| macOS | Safari | P1 |
| macOS | Chrome | P1 |
| iPadOS | Safari | P1 |
| Android | Chrome | P2 |

---

## 8.33 Ordinary Regression Tests

At minimum:

```text
Open
Save
Submit
Preview
Find / Replace
Format
TOC
Undo / Redo
Copy / Cut / Paste
Paste Provenance
Writing Record
Teacher View
Technical View
Theme
Outline
```

---

## 8.34 Console

As a general rule:

```text
Uncaught exception = 0
Unhandled promise rejection = 0
```

Repeated identical warnings during ordinary input are also unacceptable.

---

## 8.35 Beta Use

Run a small beta while clearly marking Input Process Metrics as Experimental.

Primary goals:

- whether typing feels abnormal
- IME issues
- Capture Status behavior
- Technical View comprehension
- incorrect display
- browser-specific issues

Do not require collection of Report body text or other unnecessary content.

---

## 8.36 Defect Classification

### Class A — Editing Integrity

- lost text
- broken IME
- Undo unavailable
- Paste failure

→ Release Blocker

### Class B — Provenance Integrity

- Hash Chain mismatch
- reversed Event order
- double counting
- missing Event during Save

→ Release Blocker

### Class C — Input Process Measurement

- IME duration unavailable in a particular browser
- visibility unknown

If Fallback behaves as specified, this is not necessarily a Release Blocker.

---

## 8.37 v2.1 Core Acceptance Criteria

### Required

All P0 tests pass.

### P1

No major known defect.

Where capture is unavailable, Degraded / Unavailable is shown accurately.

### P2

Even if unresolved:

- Core editing is not broken
- values are not fabricated
- Human / AI status is not falsely classified

---

## 8.38 Detection Performance Is Not an Acceptance Criterion

The v2.1 Acceptance Test evaluates only whether:

> measurements are recorded correctly

It does not evaluate accuracy in distinguishing humans from AI.

---

## 8.39 Additional Required Tests Added in This Revision

The following are P0. Separate unit tests using Fake Clock from real-browser tests where appropriate. These do not need to be performed before implementation; they are Acceptance requirements for the implementation.

| ID | Condition | Expected |
| --- | --- | --- |
| AT-RV-01 | committed at t=0, timer at t=2s, resume at t=5s | one Pause with duration=5000 |
| AT-RV-02 | gaps of 1999.6ms / 2000ms | compare before rounding; first no Pause, second creates Pause |
| AT-RV-03 | input every 1 second for 30 seconds | flush at safe ~5000ms points, no Pause, Undo behavior preserved |
| AT-RV-04 | 10-second composition | no intermediate text sealed, aggregate exactly once after completion, exceeding limit allowed |
| AT-RV-05 | no input after compositionend / final input before / related input after | no infinite wait, settle exactly once, do not absorb unrelated next input |
| AT-RV-06 | replace 3 selected characters with 1 via IME | if Direct: +1/-3, deletion operation 1, IME committed 1 |
| AT-RV-07 | valid composition with no final document change | compositionCount=1, Direct values 0, elapsed=null and Not applicable |
| AT-RV-08 | edit at t=0, hidden at t=1s, resume at t=5s | Pause=5000, background |
| AT-RV-09 | in-page focus move / window blur only | do not classify as background without hidden evidence |
| AT-RV-10 | edit → Copy → long gap → Paste | split gap around Copy, correct ordering, no double count |
| AT-RV-11 | cancelled beforeinput then later valid input | cancelled candidate does not end Pause |
| AT-RV-12 | repeated ordinary Save during a gap → input | do not split or duplicate the gap; finalize once on input |
| AT-RV-13 | exit after Save → restart | do not carry clock/gap; new Session |
| AT-RV-14 | Session end / Document switch | trailing record belongs to old Session; no cross-Document linkage |
| AT-RV-15 | small delete / independent Large Delete | both contribute exactly once to Direct deletion total |
| AT-RV-16 | drop, Cut, toolbar Undo, browser historyUndo input path | identify owner or expose missing classification; never misclassify as Direct |
| AT-RV-17 | valid value → capture failure → valid value within Chunk | cumulative metric remains null; Summary does not fabricate complete total |
| AT-RV-18 | disabled interval with no typing → recovery | status Events make Session/Report partial; no hidden gap |
| AT-RV-19 | mixed thresholds, schema versions, and legacy Sessions | interpret by Session and expose observed subtotals / coverage |
| AT-RV-20 | editing and queue delay during Save/Submit | document and Event Log use same snapshot; queue order preserved |
| AT-RV-21 | Submit cancelled → retry | no duplicate trailing Pause / Chunk for the same snapshot |
| AT-RV-22 | uncommitted composition at Submit | do not seal uncommitted text; instruct user; text salvage remains possible |
| AT-RV-23 | Summary only modified | Event Chain independently verified; Summary mismatch; signature result evaluated separately if Summary is signed |
| AT-RV-24 | inconsistent `full` values, missing required field, wrong type | even with valid hash, Semantic Invalid and excluded from aggregation |
| AT-RV-25 | open new two Event types in an old Reviewer | measure compatibility; if unsupported, define compatibility policy before release |
| AT-RV-26 | per-key measurement and Report output | do not store raw key sequence, intermediate IME, or input interval sequence in logs / telemetry |

## 8.40 Performance Evaluation Details

On the same device, browser, and document, compare the current version and the modified version at least three times each. Record input-processing p50/p95, long synchronous tasks, Save/Submit time, peak memory, Event count, and Report size. Include 10k/50k/100k-character documents and 30 minutes of continuous input.

Provisional investigation budget: if the added measurement layer increases p95 processing time by more than 5ms on a 100k-character document, or repeatedly causes synchronous stalls of 50ms or more, investigate the cause. This is a pre-measurement budget, not a final threshold; fix it only after empirical results. Pay particular attention to any path that computes a full-document diff on every key.

No benchmark or Acceptance Test has been performed at this document-review stage.

---
# 9. Open Issues / Implementation Decisions

## 9.1 Status Values

Each item uses one of the following finite status values. Conditions are defined in the relevant text.

```text
Resolved
Experimental
Deferred
Rejected
Audit-required
Provisional
Recommended
```

`Resolved` means that the design decision is resolved within this draft. It does not mean implementation is complete or verified on real devices.

---

## 9.2 Chunk Threshold

```text
2000 ms
```

Status:

```text
Experimental
```

Use as the initial default and reevaluate after beta testing.

Candidates:

```text
1500
2000
2500
3000 ms
```

---

## 9.3 Long Pause

```text
60000 ms
```

Status:

```text
Experimental
```

Used only for classification.

Do not use as an Input Process Session boundary.

---

## 9.4 Separation from Existing SessionManager

Status:

```text
Resolved
```

Existing SessionManager behavior and Input Process Long Pause are separate concepts.

Do not share the same constant.

Example:

```text
SESSION_IDLE_THRESHOLD_MS
INPUT_LONG_PAUSE_THRESHOLD_MS
```

---

## 9.5 Existing 5-Second Typing Batch

Status: `Audit-required`

Separate 2-second inactivity from the 5000ms safe flush (Sections 5.2 and 5.28). Before implementation, audit whether the existing batch shares behavior with Undo, Save, or History. Do not claim that it has already been “replaced with 2 seconds” before that audit.

---

## 9.6 Reuse of typing Event

Status:

```text
Resolved
```

Reuse the existing `typing` Event as a Chunk. Do not add a new `chunk` Event type.

---

## 9.7 Zero Final-Diff Chunk

Status:

```text
Resolved
```

Retain the Event when Input Process activity exists.

---

## 9.8 Composition Cancel

Status:

```text
Experimental
```

When a valid `compositionstart → compositionend` is observed, record the composition activity even if final document change is zero.

Validate browser differences.

---

## 9.9 Meaning of Composition

Status:

```text
Resolved
```

Count `compositionstart → compositionend` as one session.

Do not interpret it as one word, one phrase, or other linguistic unit.

---

## 9.10 Background During Composition

Status:

```text
Resolved
```

If duration reliability is unknown, use `null` for the affected timing value.

Preserve committed character information where reliably available.

---

## 9.11 Pause Endpoint

Status:

```text
Resolved
```

Full capture:

```text
actual activity start
```

Degraded capture:

```text
observable input time
```

---

## 9.12 Trailing Pause

Status:

```text
Experimental
```

Initial policy:

- do not finalize on ordinary Save
- do finalize on Finalization

Reevaluate usefulness in beta.

---

## 9.13 Copy as Hard Boundary

Status:

```text
Resolved
```

Copy remains a boundary so the event sequence stays aligned with existing Copy provenance.

---

## 9.14 Markdown Formatting Boundary

Status:

```text
Resolved
```

Formatting operations recognized as explicit Editor commands are Hard Boundaries.

---

## 9.15 Selection Replacement

Status:

```text
Audit-required
```

Audit cases in which the existing PROVENANCE implementation records selection replacement as `replace`.

By specification, a user directly typing into a selected range has Direct ownership, while explicit Editor commands such as Replace / Replace All have Explicit Operation ownership. If Event type alone cannot distinguish them, add `owner` or equivalent classification metadata.

The same change must never be recorded in both `typing` and `replace`.

---

## 9.16 Large Delete

Status: `Resolved`

Preserve the existing `delete` Event. For direct deletion, require the Input Process fields defined in Section 5.20. Do not duplicate it in `typing`, but include it in Direct totals.

---

## 9.17 Field Names

Status:

```text
Resolved
```

The new Input Process layer uses:

```text
directInsertedCharacters
directDeletedCharacters
deletionOperationCount
```

This avoids ambiguity with existing `insertedCharacters / deletedCharacters` fields.

---

## 9.18 inputOperationCount

Status:

```text
Rejected
```

Do not include in the initial implementation because event granularity differs substantially by environment.

---

## 9.19 Input Interval Statistics

Status:

```text
Deferred
```

Do not implement initially:

```text
intervalCount
intervalMsSum
intervalMsMin
intervalMsMax
intervalBins
```

---

## 9.20 Pause contextBasis

Status:

```text
Deferred
```

Do not implement initially because semantics are unstable across language, Markdown, code, URLs, and other content.

---

## 9.21 endedBy

Status:

```text
Resolved
```

Keep as Core because it is low-cost and useful for checking Event timeline structure.

---

## 9.22 Independent Pause Event

Status:

```text
Resolved
```

Use a standalone `pause` Event.

Do not embed a `pauseBeforeMs` field only in the following Event.

---

## 9.23 Pause Event Growth

Status:

```text
Experimental
```

Measure Report size and Reviewer performance for 30-minute, 1-hour, and long-document cases.

Do not compress Pause Events in the initial implementation.

---

## 9.24 Issue Codes

Status: `Resolved`

The 10 codes in Section 4.13 are the canonical v1 set. Arrays are duplicate-free and lexicographically sorted. Adding a new code requires schema-compatibility review.

---

## 9.25 Capture Status

Status:

```text
Resolved
```

Session/Event:

```text
full
degraded
unavailable
```

Report:

```text
complete
partial
unavailable
legacy-not-recorded
unsupported
```

---

## 9.26 Summary Mismatch

Status:

```text
Resolved
```

Summary mismatch alone does not mean that the entire Report is Tampered.

Reviewer-recomputed Event values are authoritative for display.

---

## 9.27 formatVersion

Status:

```text
Provisional
```

Initially test compatibility with:

```text
formatVersion = 1
inputProcessVersion = 1
```

Reevaluate if old Reviewers or related consumers fail.

---

## 9.28 Meaning of Manifest Version

Status:

```text
Resolved
```

`manifest.inputProcessVersion` indicates the Input Process schema emitted by that Writer. Interpret each Session using its own sealed version.

It does not mean that every Session contains metrics of the same version.

---

## 9.29 Future Unknown Fields

Status:

```text
Resolved
```

Do not exclude unknown fields from hash verification.

Skip only their semantic interpretation.

---

## 9.30 Privacy

Status:

```text
Resolved
```

The new Input Process feature does not store:

- individual key timestamp
- raw input interval sequence
- uncommitted IME text
- new detailed document context

Do not increase stored textual context beyond the existing Snippet behavior for Input Process purposes.

---

## 9.31 Duration Precision

Status:

```text
Resolved
```

High-resolution monotonic time may be used internally.

Stored non-negative duration is floored to integer milliseconds, as described in Section 5.5.

Technical View normally displays human-readable values such as:

```text
8m 02s
```

Raw Detail may display integer milliseconds. Storage precision does not guarantee actual measurement precision or exact human input timing.

---

## 9.32 Teacher View Integration

Status:

```text
Deferred
```

Consider separately only after confirming at least:

1. stable behavior across multiple browsers
2. no major interpretive problem with IME or other input methods
3. beta users understand the meaning
4. actual value for instructors
5. UI design that does not conflate metrics with automatic misconduct detection

---

## 9.33 Automated Detection

Status:

```text
Deferred
```

Treat automated classification as a separate problem from the v2.1 Input Process specification.

If needed in the future, design a separate:

```text
Assessment / Interpretation Specification
```

---

## 9.34 Computer Use / RPA

Status:

```text
Experimental
```

The goal is to observe metric differences, not to measure detection rate.

---

## 9.35 Browser Support

Status:

```text
Experimental
```

Primary focus:

```text
Windows Firefox
Windows Chrome
Windows Edge
```

Next stage:

```text
Windows Brave
macOS Safari
macOS Chrome
iPad Safari
```

---

## 9.36 Performance Budget

Status:

```text
Experimental
```

Compare with the current version as baseline.

Initial criterion:

> no clearly perceptible interaction degradation for users.

Set quantitative thresholds after real measurements.

---

## 9.37 Feature Flag / Beta Build

Status:

```text
Recommended
```

The initial implementation should preferably allow Input Process Metrics to be separated as an Experimental feature or beta build.

Example:

```text
ENABLE_INPUT_PROCESS_METRICS = true
```

Always record `enabled` at Session start. When disabled, set `initialCaptureStatus=unavailable` and include `capture-disabled` in `initialIssueCodes`. Configuration changes apply from the next Session rather than silently disabling an active Session. Runtime failures use the state Events in Section 4.20.

The presence of a Feature Flag itself is not evidence of trustworthiness.

---

## 9.38 Fail-Soft

Status:

```text
Resolved
```

Input Process failure:

```text
Editor continues
Save continues
Capture = degraded / unavailable
```

Core provenance failure:

```text
recordingStatus = incomplete
```

Keep these states clearly separate.

---

# 10. Initial v2.1 Implementation Scope Summary

Implement:

```text
inputProcessVersion

Session capability
Capture status

typing Chunk:
  directInsertedCharacters
  directDeletedCharacters
  deletionOperationCount
  compositionCount
  compositionMsTotal
  compositionCommittedCharacters
  chunkElapsedMs
  hasCommittedDirectEdit
  closedBy

input-process-status:
  captureStatus
  issueCodes

pause:
  durationMs
  pauseClass
  visibility
  endedBy

issueCodes
Report availability
Summary recomputation
Technical View
Semantic validation
```

Do not implement initially:

```text
input intervals
histograms
contextBasis
typing speed
graphs
Human / AI classification
Suspicion score
Teacher View integration
```

---

# 11. Core Design Principles of v2.1

1. **Separate observed facts from interpretation.**
2. **Separate Hash Chain integrity from authenticity of the input actor.**
3. **Observe committed editing results rather than physical key actions.**
4. **Do not double-count Paste, Undo, Redo, or similar operations as Direct Edit.**
5. **Do not store intermediate IME state.**
6. **Do not call Pause “thinking time.”**
7. **Do not call deletion a “mistake.”**
8. **Do not replace unavailable values with zero.**
9. **Do not replace legacy unrecorded values with zero.**
10. **Do not let Input Process measurement failure break document editing.**
11. **Include new aggregate values within Hash Chain protection.**
12. **Hash Chain does not prove who or what generated the recorded value.**
13. **v2.1 does not automatically classify human / AI / misconduct.**
14. **Validate observed facts first in Technical View.**
15. **Consider Teacher View integration separately after real-world validation.**

---

# 12. Preconditions for Implementation

Before implementation with Codex or another coding agent, confirm at least:

- [ ] agreement on Trust Model / Non-Goals
- [ ] Core metric names finalized
- [ ] 2-second Chunk threshold accepted as Experimental default
- [ ] Long Pause separated from existing SessionManager semantics
- [ ] audit existing 5-second batch / Undo / autosave dependencies and define 5000ms safe flush
- [ ] operation-owner and IME-settlement adapter design
- [ ] exactly-once recording across Session boundaries and Finalization retries
- [ ] measurement-state Event and per-metric Summary coverage
- [ ] compatibility policy for mixed versions and old Reviewers
- [ ] additional P0 cases in Section 8.39 finalized
- [ ] reuse `typing` Event
- [ ] retain zero-final-diff Chunks
- [ ] independent Pause Event
- [ ] Hard Boundary list
- [ ] `0 / null / missing` semantics
- [ ] Capture Status rules
- [ ] Fail-soft rules
- [ ] provisional `formatVersion` policy
- [ ] initial Technical View display scope
- [ ] P0 Acceptance Test list

After implementation, proceed to Experimental beta only after all P0 Acceptance Tests pass.

---

## Status

**v2.1.2-beta-draft**

This specification defines the initial implementation and validation of Input Process Metrics.

Input Process Metrics are Experimental in the initial stage and must not be used for Teacher View assessment, automatic classification, AI-use determination, or misconduct determination.

After real-device comparison across IME, browsers, speech input, assistive technology, automation, and related environments, update the specification as needed in v2.2 or later.

---

# 13. v2.1.2 Review Findings and Change Log

| Issue | Impact | Reflected in |
| --- | --- | --- |
| Pause start could be read as Timer-fire time | Pause would always be about 2 seconds too short | 5.8–5.11 |
| IME start/end relationship with Chunk time was ambiguous | IME pre-gap, replacement deletion, and cancellation could be miscounted | 4.19, 5.15–5.17 |
| Wording could imply input is mandatory after compositionend | indefinite wait or double counting | 5.15, 8.39 |
| only 2-second inactivity flush | long continuous input could remain unflushed too long | 5.2, 5.28 |
| simply removing the 5-second batch | could affect Undo, autosave, or recovery | 9.5, Section 12 |
| new Large Delete values were optional | large deletions could disappear from totals | 5.20, 4.21 |
| no storage location for Session measurement-state changes | measurement stoppage during inactivity could be hidden | 4.12, 4.20 |
| totals with null / legacy Sessions were undefined | incomplete values could be displayed as complete totals | 4.16, 4.21 |
| blur and hidden could be conflated | incorrect background classification | 3.19, 5.21 |
| editing during Save/signing and retry behavior were undefined | snapshot inconsistency or duplicate Events | 5.24–5.25 |
| insufficient gap handling at Session / Document boundaries | time from another Document could be linked | 5.30 |
| Hash Chain guarantee was overstated | regenerated internally consistent logs could be over-trusted | 2.4, 7.3 |
| schema requirements, not-applicable values, and invalid values were ambiguous | Reviewer implementations could diverge | 4.19 |
| outer code-fence / notation inconsistencies | broken Markdown display or inconsistent implementation names | entire document |
| relationship between direct selection replacement and `replace` Event | Direct totals could omit or double-count changes | 3.7, 4.21, 9.15 |
| insufficient Semantic Validation for `input-process-status` | Reviewer interpretation could diverge | 4.19–4.20 |
| unknown-duration Pause criteria were too broad | intervals with unconfirmed threshold crossing could be mislabeled as Pauses | 4.19, 5.11 |
| P0/P1 browser priority inconsistency | contradictory Acceptance criteria | 8.2, 8.32 |

This revision makes the 5000ms safe flush, 1000ms settlement ceiling, status Event, and `hasCommittedDirectEdit / closedBy` design more concrete. These remain subject to implementation-code review and real-device testing. Existing raw logs such as Snippets remain under their prior handling, but fine-grained Chunk / Pause recording may allow some operation timing to be approximated. O(1) applies only to the number of additional fields per Event; it does not guarantee complete behavioral anonymity or O(1) editing computation.

# 14. Technical References

Checked 2026-09-09. These references describe browser observation models; they are not evidence that this product behaves identically on all supported platforms. Thresholds such as 2 seconds, 5 seconds, and 60 seconds are design choices in this specification, not values recommended by the standards.

- [W3C UI Events — Input Events During Composition](https://www.w3.org/TR/uievents/#events-composition-input-events): relationship between composition and input. Supports the design decision not to require a post-`compositionend` input as the only settlement path.
- [W3C Input Events Level 2](https://www.w3.org/TR/input-events-2/): `beforeinput` / `input`, `inputType`, and classification of editing intent. Standards text alone does not eliminate browser-specific differences, so real-device validation remains required.
- [W3C High Resolution Time](https://www.w3.org/TR/hr-time-3/): distinction between monotonic clocks used for duration and wall-clock time.
- [WebKit Bug 225610](https://bugs.webkit.org/show_bug.cgi?id=225610): implementation-side report concerning `performance.now()` behavior during Sleep. This does not imply that the same behavior exists in all current environments.
