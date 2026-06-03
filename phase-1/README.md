# Contribution 1: Bug: Populate from CSV Import not parsing Tags correctly

**Contribution Number:** 1
**Student:** Minh Nguyen
**Issue:** https://github.com/MISP/MISP/issues/9238
**Status:** Phase I Complete

---

## Why I Chose This Issue

I selected this issue because it targets an engineering flaw at the ingestion boundary of an enterprise-level threat intelligence system. In Security Operations Centers (SOC) and incident response automation, tags act as the core classification mechanism for indicators of compromise (IoCs). They define Traffic Light Protocol (TLP) sharing boundaries, threat actor taxonomy mappings (such as MITRE ATT&CK vectors), and severities. 

When a lot of threat intelligence is processed via raw CSV stream ingestion, dropping or misinterpreting tag configurations silently neutralizes downstream alerting, causing intelligence degradation and exposing defensive infrastructure to classification bypasses. Resolving this requires implementing robust input parsing and validation rules inside a multi-language integration ecosystem.

---

## Codebase facts

### Problem Description
When utilizing the csvimport utility inside the event population dashboard, the system parses structural fields (like attributes and values) successfully, but strips or ignores the attribute_tag arrays. The components fail to pass the tag elements into the backend processing loop, causing classification objects to emerge completely empty within the persistent database layer.

### Expected Behavior
The ingestion loop must map and capture incoming attribute_tag entries safely, routing them through a verification sequence to confirm they match valid platform taxonomies before assigning them back to the active Event data matrix.

### Current Behavior
The `__fillAttribute` private logic handles array assignment for incoming fields (type, category, to_ids, value, distribution, sharing_group_id), but contains no instructions to parse, extract, or hold the attribute_tag metadata payload. It strips it implicitly by omission before returning the filtered payload array.

### Affected Components
Unlike high-level routing components, the failure resides specifically inside the Model layer of the MVC framework, where the data object is built prior to persistence.
* **Target File Path:** `MISP/app/Model/Event.php`
* **Target Execution Block:** `private function __fillAttribute($attribute, $defaultDistribution)`
* **Target Lines:** 5676 to 5703

---

## Reproduction Process

### Environment Setup
[To be completed in Phase II - Week 2]

### Steps to Reproduce
[To be completed in Phase II - Week 2]

### Reproduction Evidence
* **Commit showing reproduction:** [To be completed in Phase II - Week 2]
* **Screenshots/logs:** [To be completed in Phase II - Week 2]
* **My findings:** [To be completed in Phase II - Week 2]

---

## Solution Approach

### Analysis
[To be completed in Phase II - Week 2]

### Proposed Solution
[To be completed in Phase II - Week 2]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Refactor `__fillAttribute` to intercept, sanitize, and persist `attribute_tag` entries from raw input streams without discarding key metadata.

**Match:** Extract existing database schema mapping models used for native event tag tracking inside `app/Model/` to replicate identical syntax hooks.

**Plan:** 
1. Modify `__fillAttribute` in `MISP/app/Model/Event.php` to extract incoming `attribute_tag` arrays.
2. Apply data-scrubbing rules to preserve special characters common in taxonomy headers (such as colons and equal signs).
3. Append sanitized dictionaries back to the return array for secure entity instantiation.
4. Update associated unit tests to validate schema integrity.

**Implement:** [To be completed in Phase III - Week 3]

**Review:** [To be completed in Phase III - Week 3]

**Evaluate:** [To be completed in Phase III - Week 3]

---

## Testing Strategy

### Unit Tests
* [ ] Test case 1: Validate array parsing with cleanly formatted standard taxonomy tags.
* [ ] Test case 2: Validate edge-case parsing with complex delimiters (colons, quotation marks, and equal signs).
* [ ] Test case 3: Assert default error propagation postures when input parameters map to non-existent taxonomies.

### Integration Tests
* [ ] Verification of full bulk file ingestion utilizing multi-row CSV payloads.

### Manual Testing
[To be completed in Phase III - Week 3]

---

## Implementation Notes

### Week 1 Progress
Phase I Complete. Isolated issue #9238, analyzed target MVC file layout logic, and defined structural solution approach maps within the tracking architecture.

### Code Changes
* **Files modified:** None (Development lifecycle opens in Phase III)

---

## Pull Request

**PR Link:** [To be completed in Phase IV - Week 4]
**PR Description:** [To be completed in Phase IV - Week 4]
**Maintainer Feedback:** [To be completed in Phase IV - Week 4]
**Status:** Awaiting review

---

## Learnings & Reflections
[To be completed in Phase IV - Week 4]

---

## Resources Used
* MISP App Model Source core: https://github.com/MISP/MISP/blob/master/app/Model/Event.php
* CakePHP 2.x Model handling documentation
