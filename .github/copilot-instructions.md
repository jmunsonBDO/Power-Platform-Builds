# GitHub Copilot Build Instructions

Treat all files under `staffing-project-setup-copilot-spec/` as normative. If requirements conflict, use this priority: security and DLP requirements, placeholder contract, project requirements, solution design, UX design.

## Mandatory authoring tools
- Use the Microsoft Power Platform Skills marketplace.
- Use the Canvas Apps plugin and Canvas Authoring MCP server for the canvas app. Generate `.pa.yaml`, validate it, correct all errors, and sync it with an active Power Apps Studio coauthoring session.
- Use the Power Automate plugin and its available flow-authoring MCP tools for the cloud flow. Discover current commands and schemas instead of guessing them.
- Do not hand-author unsupported app or flow package internals where plugin or MCP capability exists.

## Implementation rules
- Build inside a named unmanaged solution with environment variables and connection references.
- Prefer Dataverse for request state, audit data, extracted fields, validation issues, and child resource rows. Store input files in a secured SharePoint library and persist their links in Dataverse.
- Never hard-code production URLs, mailboxes, environment IDs, connector IDs, secrets, or personal names.
- Use least privilege and comply with tenant DLP policy.
- Create an Outlook draft only. Do not add a Send email action.
- Use deterministic parsing and lookup logic where possible. AI extraction must return structured JSON with confidence and evidence and must be deterministically validated.
- Never invent values. Missing or uncertain values become `[NEEDS INPUT]` and a validation issue.
- Use only the exact `{{TOKEN}}` placeholders in the approved HTML template. Replace only those defined in the placeholder contract.
- Preserve HTML structure, inline styles, links, and table formatting. Clone the complete prototype resource row for additional resources.
- HTML-encode inserted plain text. Permit only sanitized HTTPS anchors in approved link fields.
- Make processing idempotent. One request version may have at most one active draft.
- Serialize processing per request and use optimistic row-version checks.
- Record field-level provenance without copying full confidential document text into telemetry.

## Completion gate
Do not claim completion until all app YAML validates, Flow checker errors are resolved, acceptance tests are recorded, all defined placeholders resolve or become `[NEEDS INPUT]`, the missing-information section is correct, the message is saved as a draft and not sent, and deployment settings are externalized.
