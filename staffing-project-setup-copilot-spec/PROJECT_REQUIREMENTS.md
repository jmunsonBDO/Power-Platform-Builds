# Project Requirements

## 1. Purpose
Create a human-in-the-loop solution that drafts a Staffing and Project Setup message from a SOW PDF, one or more EWS workbooks, approved reference data, form input, and an HTML template. A staffing coordinator reviews and edits the Outlook draft before manually sending it to account owners and scope leaders.

## 2. Actors
- Requestor: supplies metadata, files, optional links, and supplemental content.
- Staffing Coordinator: resolves uncertainty, generates, reviews, edits, and manually sends the draft.
- Solution Administrator: maintains connections, environment variables, reference locations, permissions, and monitoring.

## 3. Intake requirements
- CRM Professional Service: required trimmed text of 3 to 5 words.
- Account Owner: required trimmed person name of 2 to 4 words.
- SOW: exactly one PDF with drag-and-drop upload.
- EWS: at least one `.xlsx`, with multiple EWS files supported. Allow drag-and-drop upload.
- Optional supplemental content.
- Show file name, size, replace/remove actions, upload status, and actionable validation errors.

## 4. Processing requirements
- Persist the request before parsing.
- Extract SOW title data, scope-leader candidate, timeline, estimate, billing, attribution, Azure scope, funding, and evidence locations.
- Read EWS content by worksheet and table name, not fixed cell address: `ResourcesPivot` on `Resources`, `PMCalcEstimateData` on `PM Calcs`, and GCM worksheet content for GCM ID and CRM URL.
- Exclude rows whose trimmed Resource Role equals `Fixed Fee Margin`, case-insensitively.
- Preserve EWS order and table-row order across multiple files.
- Resolve SalesRouting by CRM Professional Service and scope-leader candidate.
- Resolve Account Summary by exact normalized organization name first, then fuzzy candidate. Never auto-accept a fuzzy match.
- Present each unresolved placeholder in a form allowing the staffing coordinator to manually resolve each one.
- Build a strict token dictionary, validate it, render the approved template, then create a draft.

## 5. Human resolution requirements
- Show each extracted field with source, status/confidence, and editable resolved value.
- Require confirmation when the client match is fuzzy, the title cannot be split reliably, the scope leader is unmatched, or source values conflict.
- The staffing coordinator controls final To recipients.

## 6. Output requirements
- Create an Outlook draft with an HTML body. Do not send.
- Default subject: `Staffing and Project Setup | {CLIENT_NAME} | {PROJECT_NAME}`, configurable by environment variable.
- Store draft ID, draft link when available, created timestamp, request version, and correlation ID.
- Provide Open Draft, Regenerate Draft, and Return to Review actions.

## 7. Extraction and transformation rules
- Client and project: locate the project title after `STATEMENT OF WORK ("SOW")` or typographic equivalent and split the approved title format. If unreliable, flag both fields.
- Scope leader: search the first SOW page near `BDO Digital, LLC` and match against SalesRouting Scope Leader Queue Owner. Reference the Name Alias table to determine if a matching Full Name exists and use that name for the Scope Leader lookup when necessary. 
- Timeline: preserve minimum and maximum weeks. Convert months with the configured `WeeksPerMonth` value.
- Dates: if no explicit start exists, use the processing local business date plus 14 calendar days. End date uses the maximum duration.
- Money: use two decimals and thousands separators.
- Billing, attribution, strategic partner, managed services, leads and roles, links, and funding follow `PLACEHOLDER_CONTRACT.md`.

## 8. Nonfunctional requirements
- Accessibility: keyboard support, visible focus, labels, linked error summary, 4.5:1 text contrast, and no color-only status.
- Reliability: retry transient connector failures, do not retry validation failures, and prevent duplicate drafts.
- Security: restricted file/request access, least privilege, audit logging, sanitized telemetry, and DLP compliance.
- Maintainability: environment variables, connection references, modular scopes/child flows, named formulas/components, and no magic constants.

## 9. Out of scope
- Automatic staffing decisions.
- Automatic message sending.
- Editing a signed SOW or EWS.
- Unapproved broad email scraping.
- Automatic approval of fuzzy client matches.
