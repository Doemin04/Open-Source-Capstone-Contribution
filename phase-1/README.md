# Phase I: Issue Selection & Architectural Verification

## [ACHIEVED] Milestone Checklist
* [x] Perform target code analysis on the live upstream repository.
* [x] Map the failure vector directly to a specific source module and execution block.
* [x] File official tracking state via the CodePath Summer 2026 AI301 S1d course sheet.

---

## Architectural Analysis & Threat Model Scoping

### Upstream Coordinate
* **Target Workspace Link:** [MISP/MISP Issue #9238](https://github.com/MISP/MISP/issues/9238)

### Why I Chose This Issue
I selected this issue because it targets an engineering flaw at the ingestion boundary of an enterprise-level threat intelligence system. In Security Operations Centers (SOC) and incident response automation, tags act as the core classification mechanism for indicators of compromise (IoCs). They define Traffic Light Protocol (TLP) sharing boundaries, threat actor taxonomy mappings (such as MITRE ATT&CK vectors), and severities. 

When a lot of threat intelligence is processed via raw CSV stream ingestion, dropping or misinterpreting tag configurations silently neutralizes downstream alerting, causing intelligence degradation and exposing defensive infrastructure to classification bypasses. Resolving this requires implementing robust input parsing and validation rules inside a multi-language integration ecosystem.

---

## Codebase facts

### Problem Description
When utilizing the csvimport utility inside the event population dashboard, the system parses structural fields (like attributes and values) successfully, but strips or ignores the attribute_tag arrays. The components fail to pass the tag elements into the backend processing loop, causing classification objects to emerge completely empty within the persistent database layer.

### Verified Target Component
Unlike high-level routing components, the failure resides specifically inside the Model layer of the MVC framework, where the data object is built prior to persistence.
* **Target File Path:** `MISP/app/Model/Event.php`
* **Target Execution Block:** `private function __fillAttribute($attribute, $defaultDistribution)`
* **Target Lines:** 5676 to 5703

### Current Behavior
The `__fillAttribute` private logic handles array assignment for incoming fields (type, category, to_ids, value, distribution, sharing_group_id), but contains no instructions to parse, extract, or hold the attribute_tag metadata payload. It strips it implicitly by omission before returning the filtered payload array.

### Expected Behavior
The ingestion loop must map and capture incoming attribute_tag entries safely, routing them through a verification sequence to confirm they match valid platform taxonomies before assigning them back to the active Event data matrix.

---

## Conceptual Solution Approach

To bypass generic patchwork fixes, the solution architecture will incorporate an extended data-mapping hook inside the model controller process:

1. **Extraction Modification:** Refactor `__fillAttribute` to intercept attribute_tag parameters present inside the raw input array.
2. **Input Sanitization:** Apply clean data-scrubbing parameters to ensure special characters common in taxonomy headers (such as colons and equal signs) are parsed correctly without breaking backend delimiters.
3. **Entity Association:** Return the sanitized, fully-formed attribute dictionary complete with its tag payload back into the core execution thread for secure database instantiation.

---

## [NEXT STEPS] Progression to Phase II

To successfully transition to Phase II (Reproduce and Plan), the following engineering steps are scheduled for execution:

1. Create a personal repository fork to `https://github.com/Doemin04/MISP`.
2. Clone the repository locally and stand up the localized development container workspace using the specific platform target versions (Ubuntu 20.04, PHP 7.4).
3. Draft a deterministic test CSV file containing a diverse array of populated threat tags (`tlp:amber`, `malware-type="ransomware"`).
4. Execute a mock ingestion run to trigger the `__fillAttribute` logic and log the exact array structural output to confirm the reproduction state.

* **Navigation:** Proceed to the verification pipeline configuration workspace: [Phase II Log](../phase-2/README.md) (Pending Phase II Initialization)
