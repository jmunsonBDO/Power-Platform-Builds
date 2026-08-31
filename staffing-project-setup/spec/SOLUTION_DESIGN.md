# Solution Design

## Architecture
1. Canvas app creates a Dataverse request and captures metadata.
2. Files upload to a secured SharePoint path keyed by Request ID.
3. The app invokes the Generate Draft flow with Request ID and expected row version, not raw file bodies.
4. The flow extracts structured data, performs lookups, writes field-resolution/resource/issue records, and returns Needs Review when required.
5. After resolution, the flow renders the approved HTML template and creates an Outlook draft.

## Dataverse model
### Staffing Request
Request ID, Status, CRM Professional Service, Entered Account Owner, Account Owner Candidates (JSON array), Sales Folder URL, Supplemental Content, Subject, Draft ID, Draft URL, Processing Version, Correlation ID, Created By, timestamps, row version, Lock Status, Lock Expiry, Lock Holder.

### Request Document
Request, Document Type, file URL, file name, SHA-256, sequence, uploaded by, uploaded timestamp.

### Field Resolution
Request, Field Key, Extracted Value, Resolved Value, Status, Confidence, Source Document, Evidence Locator, Evidence Excerpt, Resolution Method, Resolved By, Resolved On.

### Resource Selection
Request, EWS Document, Source Sequence, Row Sequence, Resource Role, Rate, High Hours, Included, Source Locator.

### Validation Issue
Request, Field Key, Severity, Reason Code, Message, Blocking, Status, Resolution Note.

### Name Alias
Canonical Name, Alias, Active. Initialize the Dataverse table using `shared/Nicknames_Reference.csv` (manual CSV import via the maker portal — document as a deployment step).

## Externally Maintained Reference Lookups
### SalesRouting  
SalesRouting is a SharePoint page that contains an HTML table providing a lookup of CRM Professional Service to determine its Scope Leader and Delivery Manager. The page is fetched at runtime from the URL stored in the `SalesRoutingURL` environment variable.

**Table structure**: The routing table has five columns. Match columns by partial text (headers contain compound labels):
- Key column: contains "CRM Professional Service" (exact match for lookups)
- Scope Leader column: contains "Scope Leader" (full label: "Scope Leader\nQueue Owner")
- Delivery Manager column: contains "Delivery Manager" (full label: "Delivery Manager\nProject Authority")

The HTML page may contain duplicate table structures from email rendering. De-duplicate rows by CRM Professional Service value before lookups.

### AccountSummary  
AccountSummary is an export of IT Glue Organizations metadata to an Excel table used to look up a client name and determine contracted services and account contacts.
- **Contracted Services columns** (TRUE/FALSE booleans): `IMS`, `ServiceDesk`, `VDBA`, `ManagedDefenseMDR`, `CSP`
  - MANAGED_IT_SERVICES: comma-separated list of columns where value is TRUE; if no TRUE columns, use `N/A`
- **Account Representative columns**: `CSM`, `BusinessDevelopmentRepresentative`
- **Account Leaders columns**: `ServiceDeskPodLead`, `NocPodLead`, `EngineeringLead`, `SDSME`, `Architect`
  - LEADS_AND_ROLES: for each nonblank Account Leaders column, output `ColumnName: Value`, separated by `<br>` in HTML; if no account match, use `N/A`

## Environment variables
SalesRouting URL, AccountSummary file URL, Template file URL, request library/site, mailbox mode, subject pattern, WeeksPerMonth, default start offset, duration inclusivity, fuzzy threshold, maximum file size, allowed extensions, any approved extraction service/model identifiers, lock timeout seconds, `NEW_PROJECT_SETUP_URL`, `PEAK_URL`, `FRAMEWORK_DOC_URL`, `SALES_ROUTING_MATRIX_URL`.

## Flow scopes
1. Initialize and acquire request lock.
2. Load configuration and validate connections.
3. Validate input files, MIME types, extension, size, and hashes.
4. Extract SOW into a versioned JSON schema.
5. For each EWS, read named tables and GCM worksheet values.
6. Read SalesRouting and Account Summary references.
7. Normalize/match values and write resolutions/issues.
8. If blocking review exists, set Needs Review and return.
9. Build the exact `{{TOKEN}}` dictionary and resource-row collection.
10. Render the template with token-aware, DOM-safe replacement.
11. Scan for unresolved defined placeholders and unexpected double-brace tokens.
12. Append the missing-information section when needed.
13. Create Outlook draft, persist draft metadata, and release lock.
14. Catch writes a sanitized error and correlation ID; finally releases the lock.

## EWS Reading Contract

### Resources sheet (resource role data)
Read by sheet name `Resources` and header-row detection (not a named Excel table). The header row contains: `Resource Role`, `Special Designation`, `Rate`, `Billing Role`, `Task Department`, `Low Hours`, `High Hours`. Some EWS versions include a `Workstream` column; ignore it. Skip rows before the header row (instruction/filter rows). Exclude rows whose trimmed `Resource Role` equals `Fixed Fee Margin` (case-insensitive). Skip the `Total` summary row.

### PM task data (DM_HOURS lookup)
Lookup strategy (try in order):
1. Named table `PMCalcEstimateData` on sheet `PM Calcs` — present in newer EWS versions.
2. Named table `CalculatedEstimateData` on sheet `Calcs` — fallback for older EWS versions.

Filter: trimmed `Task` equals `Independent Risk Review (IRR) - Review Client Deliverables` (case-insensitive). Read column `High Hours / Quantity` (single column name containing a slash). If neither table exists or the IRR row is absent, mark DM_HOURS unresolved.

### GCM worksheet (GCMID and CRM_LINK)
Sheet name: `GCM`. Scan cells for a label "GCM ID:" — the value is in the adjacent cell to the right. Scan for "CRM ID:" similarly. Both are free-form cells, not a named table. If absent, mark unresolved.

## Processing lock
Use `Lock Status` (Active/Released), `Lock Expiry` (UTC datetime), and `Lock Holder` (correlation ID) columns on the Staffing Request row. On acquire: check Lock Status and Lock Expiry; if locked and not expired, poll up to `LockTimeoutSeconds` environment variable, then fail with a conflict error. On release (scope 13 and catch scope 14): set Lock Status to Released. Use optimistic row-version checks (Dataverse ETag) on every write.

## Parsing contract

## Reference matching
Normalize trim, Unicode, case, punctuation, whitespace, middle initials, and approved legal suffixes. Exact normalized matches may proceed. Fuzzy matches use **Jaro-Winkler** algorithm against the `FuzzyThreshold` environment variable; fuzzy matches produce candidates and scores but require confirmation. Use the Name Alias table for nicknames.

**SOW title page name handling**: The SOW title page lists two or more comma-separated names (e.g., two, three, or more). Match each name against SalesRouting Scope Leader column (applying Name Alias normalization). The first confirmed match becomes the Scope Leader. All remaining names that do not match become Account Owner candidates and are persisted as the Account Owner Candidates list on the request record. If zero or multiple names match SalesRouting, require coordinator confirmation.

**SOW title split**: The title line may use an en-dash (–, U+2013), em-dash (—, U+2014), or regular hyphen (-) to separate CLIENT_NAME and PROJECT_NAME. Parse all three. If no separator is found, flag both fields as uncertain and require coordinator input.

## Template renderer
- Accept only the exact double-brace placeholders defined in `PLACEHOLDER_CONTRACT.md`.
- Perform preflight to confirm every required placeholder exists at least once. `{{ACCOUNT_OWNER}}` appears twice — both occurrences are replaced. Resource-row tokens (`{{RESOURCE_ROLE_1}}`, `{{RATE_1}}`, `{{HOURS_1}}`) appear once in the prototype row; the row is cloned for each additional resource.
- Parse HTML and clone the complete prototype resource row for each additional resource.
- Replace only text or attribute nodes containing approved placeholders.
- Preserve all other elements, attributes, inline styles, links, and ordering.
- Replace the static `<strong>MISSING/UNCERTAIN INFORMATION</strong>` element with `<h3>MISSING/UNCERTAIN INFORMATION</h3><ul>...</ul>` when missing-info items exist; remove it entirely when there are none.
- Record template and rendered hashes.
- Fail if any approved placeholder or unexpected `{{...}}` token remains after rendering.

## Operations and security
Use solution-aware flows, connection references, environment variables, least-privilege SharePoint and Dataverse roles, approved mailbox permissions, DLP review, audit history, monitoring by status/error code, and an approved retention policy.
