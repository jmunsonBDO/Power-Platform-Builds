# Placeholder Contract

## Token standard
The HTML message template uses only the exact double-brace placeholders below. Matching is case-sensitive. Do not support bracket aliases, legacy spellings, approximate token names, or automatic token repair.

## Defined placeholders
`{{ACCOUNT_OWNER}}`
`{{CLIENT_NAME}}`
`{{PROJECT_NAME}}`
`{{SCOPE_LEADER}}`
`{{RESOURCE_SELECTIONS}}`
`{{RESOURCE_ROLE_1}}`
`{{RATE_1}}`
`{{HOURS_1}}`
`{{DM_NAME}}`
`{{DM_HOURS}}`
`{{TIMELINE_WEEKS}}`
`{{PROJECT_START}}`
`{{PROJECT_END}}`
`{{M365_ATTRIBUTION}}`
`{{AZURE_ATTRIBUTION}}`
`{{STRATEGIC_PARTNER}}`
`{{MANAGED_IT_SERVICES}}`
`{{LEADS_AND_ROLES}}`
`{{GCMID}}`
`{{CRM_LINK}}`
`{{SALES_FOLDER}}`
`{{EWS_LINK}}`
`{{SOW_AMOUNT}}`
`{{BILLING_TYPE}}`
`{{FUNDING_DETAILS}}`
`{{NEW_PROJECT_SETUP_URL}}`
`{{PEAK_URL}}`
`{{FRAMEWORK_DOC_URL}}`
`{{SALES_ROUTING_MATRIX_URL}}`

## Rules
- ACCOUNT_OWNER, CLIENT_NAME, PROJECT_NAME, SCOPE_LEADER
   - Read the SOW title page to find candidates for each placeholder. The title page has the follwoing format:
      - Statement of Work ("SOW")
        CLIENT_NAME [OPTIONAL DASH "-"] PROJECT_NAME

        YYYY-MM-DD
        BDO Digital, LLC
        ACCOUNT_OWNER OR SCOPE_LEADER, ACCOUNT_OWNER OR SCOPE_LEADER
   - ACCOUNT_OWNER: build a list of candidates to be selected in the confirmation form. Begin with the SOW title page names that did not match SalesRouting as Scope Leader. Add any confirmed Account Summary match, using nonblank BusinessDevelopmentRepresentative and CSM values. De-duplicate case-insensitively. Persist the candidate list on the Staffing Request record.
   - CLIENT_NAME and PROJECT_NAME: extract from the approved SOW title pattern. The separator between CLIENT_NAME and PROJECT_NAME may be an en-dash (–, U+2013), em-dash (—, U+2014), or regular hyphen (-). If no separator is found, mark both fields uncertain and flag for coordinator input.
   - SCOPE_LEADER: confirmed SalesRouting "Scope Leader / Queue Owner" column match from any SOW title page candidate. The remaining non-matching candidates become Account Owner candidates. Nickname matching requires an active Name Alias record.
- RESOURCE_SELECTIONS: render an HTML `<ul>` with one encoded `<li>{role} - {hours} Hours</li>` per row in the ResourcesPivot. Exclude any Fixed Fee Margin role.
- RESOURCE row tokens (RESOURCE_ROLE_1, RATE_1, HOURS_1): first included resource from ResourcesPivot replaces the prototype row tokens. Clone the full prototype row for each remaining included resource, preserving formatting. Exclude any Fixed Fee Margin role.
- DM_NAME: SalesRouting Delivery Manager for the exact CRM Professional Service; otherwise `[NEEDS INPUT]`.
- DM_HOURS: use `PMCalcEstimateData` (or fallback `CalculatedEstimateData`) where trimmed Task equals `Independent Risk Review (IRR) - Review Client Deliverables` (case-insensitive), from column `High Hours / Quantity`; otherwise mark unresolved.
- TIMELINE_WEEKS: `{minimum} - {maximum} weeks`.
- PROJECT_START and PROJECT_END: separate `MM/DD/YYYY` values.
- M365_ATTRIBUTION: `Y: POE Workloads Mentioned: {comma-separated workloads}` when found; otherwise replace the placeholder with an empty string.
- AZURE_ATTRIBUTION: `Y: ***Azure Infrastructure mentioned in the scope` when applicable; otherwise replace the placeholder with an empty string.
- STRATEGIC_PARTNER: `Microsoft` if M365 or Azure attribution is present; otherwise replace the placeholder with an empty string.
- MANAGED_IT_SERVICES: Check AccountSummary for an exact or confirmed CLIENT_NAME to OrganizationName account match. The contracted services columns are `IMS`, `ServiceDesk`, `VDBA`, `ManagedDefenseMDR`, and `CSP` (TRUE/FALSE booleans). Output a comma-separated list of column names where the value is TRUE. If no columns are TRUE or there is no account match, use `N/A`.
- LEADS_AND_ROLES: For the same type of AccountSummary account match, output nonblank Account Leaders values preceded by the Account Leaders column name and a trailing colon, separated by `<br>` (HTML line break), one per line in the rendered HTML. Account Leaders columns: `ServiceDeskPodLead`, `NocPodLead`, `EngineeringLead`, `SDSME`, `Architect`. Match workbook headers case-insensitively. If no account match, use `N/A`.
   - Example:
      ```
      ServiceDeskPodLead: Joe Smith<br>SDSME: Jonathan Eads
      ```
- GCMID and CRM_LINK: obtain from the GCM worksheet by label-cell scan ("GCM ID:" and "CRM ID:" labels; read the adjacent cell to the right). When missing, mark unresolved.
- SALES_FOLDER: user-provided HTTPS URL. When missing, mark unresolved.
- EWS_LINK: render each SharePoint EWS URL. For multiple files, render an encoded list of safe anchors.
- SOW_AMOUNT: Use the total amount from the estimate section of the SOW. Currency format `$#,##0.00`.
- BILLING_TYPE: deterministic description using only SOW terms, including fixed-fee milestones or stated retainer cadence/forfeiture terms.
- FUNDING_DETAILS: include only with explicit SOW, user-provided, or approved Sales Folder evidence; otherwise remove the optional value node.

- NEW_PROJECT_SETUP_URL, PEAK_URL, FRAMEWORK_DOC_URL, SALES_ROUTING_MATRIX_URL: HTTPS URLs resolved from environment variables at render time. Validated as HTTPS; treated as link `href` attribute values. Reject non-HTTPS values.

## Safe links
Use `<a href="ENCODED_HTTPS_URL" target="_blank" rel="noopener noreferrer">ENCODED_DISPLAY_URL</a>`. Reject non-HTTPS and unsafe schemes. Do not add transient chat CSS classes.

## Missing information
For an unresolved required field, replace its placeholder with `[NEEDS INPUT]`. Replace the static `<strong>MISSING/UNCERTAIN INFORMATION</strong>` element near `</body>` with:

`<h3>MISSING/UNCERTAIN INFORMATION</h3><ul><li><strong>Field Name</strong>: encoded reason</li></ul>`

If there are no missing/uncertain items, remove the static `<strong>` element entirely.

The final rendered HTML must contain no supported placeholder and no unexpected `{{...}}` expression.
