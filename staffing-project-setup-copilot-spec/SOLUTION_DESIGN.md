# Solution Design

## Architecture
1. Canvas app creates a Dataverse request and captures metadata.
2. Files upload to a secured SharePoint path keyed by Request ID.
3. The app invokes the Generate Draft flow with Request ID and expected row version, not raw file bodies.
4. The flow extracts structured data, performs lookups, writes field-resolution/resource/issue records, and returns Needs Review when required.
5. After resolution, the flow renders the approved HTML template and creates an Outlook draft.

## Dataverse model
### Staffing Request
Request ID, Status, CRM Professional Service, Entered Account Owner, Sales Folder URL, Supplemental Content, Subject, Draft ID, Draft URL, Processing Version, Correlation ID, Created By, timestamps, row version.

### Request Document
Request, Document Type, file URL, file name, SHA-256, sequence, uploaded by, uploaded timestamp.

### Field Resolution
Request, Field Key, Extracted Value, Resolved Value, Status, Confidence, Source Document, Evidence Locator, Evidence Excerpt, Resolution Method, Resolved By, Resolved On.

### Resource Selection
Request, EWS Document, Source Sequence, Row Sequence, Resource Role, Rate, High Hours, Included, Source Locator.

### Validation Issue
Request, Field Key, Severity, Reason Code, Message, Blocking, Status, Resolution Note.

### Name Alias
Canonical Name, Alias, Active. Initialize the dataverse table using Nicknames_Reference.csv

## Estarnally Maintained Referencec Lookups  
### SalesRouting  
SalesRouting is an HTML page that contains a table providing a lookup of CRM Professional Service to determin its Delivery Manager

### AccountSummary  
AccountSummary is an export of IT Glue Organizations metadata to an Excel table that will be used to lookup a client name to determine if an client has any contracted services that make the client a Managed Services client and if so determine its account representatives and account leaders are.
* Account Representative columns: CSM, BusinessDevelopmentRepresentative
* Account Leaders columns: ServiceDeskPodLead, NocPodLead, EngineeringLead, SDSME, Architect

## Environment variables
SalesRouting URL, AccountSummary file URL, Template file URL, request library/site, mailbox mode, subject pattern, WeeksPerMonth, default start offset, duration inclusivity, fuzzy threshold, maximum file size, allowed extensions, and any approved extraction service/model identifiers.

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

## Parsing contract
Every extractor returns `{value, status, confidence, evidence}`. Evidence contains document ID plus page or worksheet/table/row location. Values without evidence remain unresolved unless entered by a user or obtained through an approved reference lookup.

## Reference matching
Normalize trim, Unicode, case, punctuation, whitespace, middle initials, and approved legal suffixes. Exact normalized matches may proceed. Fuzzy matches produce candidates and scores but require confirmation. Use the Name Alias table for nicknames.

## Template renderer
- Accept only the exact double-brace placeholders defined in `PLACEHOLDER_CONTRACT.md`.
- Perform preflight to confirm every required placeholder exists exactly once, except explicitly repeatable resource-row mechanics.
- Parse HTML and clone the complete prototype resource row for each additional resource.
- Replace only text or attribute nodes containing approved placeholders.
- Preserve all other elements, attributes, inline styles, links, and ordering.
- Record template and rendered hashes.
- Fail if any approved placeholder or unexpected `{{...}}` token remains after rendering.

## Operations and security
Use solution-aware flows, connection references, environment variables, least-privilege SharePoint and Dataverse roles, approved mailbox permissions, DLP review, audit history, monitoring by status/error code, and an approved retention policy.
