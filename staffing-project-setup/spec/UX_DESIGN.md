# UX Design

## Screen 1: New Request
Use a responsive one-column layout below 768 px and two columns above 768 px.

### Request details
- CRM Professional Service: required, 3 to 5 words, inline validation.
- Account Owner: required, 2 to 4 words, people-picker enhancement if approved, with plain-text fallback.
- Sales Folder link: optional HTTPS URL.
- Supplemental information: optional multiline text.

### Documents
- SOW drop zone: one PDF.
- EWS drop zone: one or more XLSX files.
- Each attachment card shows type, size, progress, state, Replace, and Remove.

### Actions
- Save Draft Request
- Analyze Documents
- Clear Form

Validate on blur and submit. Show field-level errors and a linked error summary. Disable Analyze until client-side required checks pass, then validate again in the flow.

## Screen 2: Review and Resolve
- Progress states: Uploaded, Extracted, Needs review, Ready for draft.
- Summary cards: client/project, routing, commercial terms, dates, and attribution.
- Editable resource grid: EWS source, order, role, rate, hours, include/exclude, status.
- Reference match card: exact, no match, or fuzzy candidate. Fuzzy candidates require Confirm or Reject.
- Missing/uncertain panel grouped by blocking errors, warnings, and information.
- Evidence drawer: source file and page or sheet/table/row locator, plus an allowed short excerpt.
- Actions: Save Changes, Re-run Lookups, Generate Draft.

## Screen 3: Processing and Result
Show Validate, Extract, Enrich, Render, and Create Draft steps. A failure card shows safe error code, Retry, and Return to Review. Success explicitly says a draft was created and not sent, with Open Draft, Regenerate, and New Request actions.

## Visual and Power Fx requirements
Use Fluent-inspired neutral surfaces, an 8 px spacing scale, standard controls, keyboard access, and icons plus text for status. Centralize validation in named formulas, use `IfError` around connector/flow calls, surface a correlation ID, and reset controls only after persistence succeeds.
