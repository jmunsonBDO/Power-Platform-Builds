# Acceptance Tests

## Required tests
1. Valid PDF plus one valid EWS creates one draft and sends nothing.
2. Multiple EWS files preserve file and row order and create one HTML row per included resource.
3. Fixed Fee Margin rows are excluded regardless of case or whitespace.
4. Missing DM hours becomes `[NEEDS INPUT]` and creates a missing-information item.
5. A fuzzy organization candidate pauses for confirmation; rejection produces N/A managed-services fields.
6. An exact organization match maps true service fields and nonblank lead fields.
7. An unmatched scope leader pauses for review.
8. A nickname match works only through an active Name Alias record.
9. A missing explicit start date uses local processing date plus configured 14 calendar days.
10. Month conversion uses configured WeeksPerMonth and records its rule version.
11. Fixed-fee, T&M, and retainer cases render without invented terms.
12. Missing GCM ID, CRM link, or Sales Folder produces `[NEEDS INPUT]`.
13. Unsafe or non-HTTPS links are blocked and reported.
14. HTML outside approved placeholder nodes remains unchanged.
15. Every defined template placeholder is detected in preflight using exact case-sensitive `{{TOKEN}}` syntax.
16. A missing required placeholder fails preflight with an actionable template validation issue.
17. A misspelled, case-changed, bracket-style, or unidentified token fails preflight and is not auto-corrected.
18. Duplicate trigger or retry does not create a second active draft.
19. Transient connector failure retries; validation failure does not.
20. Coordinator edits resolved values, regenerates, and sees the new values.
21. Unauthorized users cannot access request records or documents.
22. Flow checker has no errors and accepted warnings are documented.
23. Canvas YAML validates through Canvas Authoring MCP and syncs to Power Apps Studio.

## Definition of done
- The solution imports into a clean test environment using deployment settings.
- Connection references and environment variables bind correctly.
- Every test records evidence, result, tester, and date.
- No secrets or environment-specific IDs exist in source.
- Draft content contains no unresolved or unknown double-brace placeholder.
- Security, DLP, preview, licensing, retention, and support reviews are recorded.
