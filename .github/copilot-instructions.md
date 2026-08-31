# GitHub Copilot Build Instructions — Power Platform Monorepo

## Repository layout

Each project lives in its own top-level folder:

```
<project-name>/
├── BUILD_PROMPT.md       ← entry prompt for building; read this first
├── SPEC_PROMPT.md        ← entry prompt for speccing
├── spec/                 ← normative design documents (read-only during build)
├── src/                  ← Power Platform build artifacts (canvas-app/, flows/, dataverse/)
└── deploy/               ← deployment settings templates (no secrets)
shared/                   ← cross-project reference data (e.g., Nicknames_Reference.csv)
```

When working on a specific project, read its `BUILD_PROMPT.md` first. It will identify which spec files are normative and supply any project-specific rules that extend those below.

## Mandatory authoring tools (all projects)
- Use the Microsoft Power Platform Skills marketplace.
- Use the Canvas Apps plugin and Canvas Authoring MCP server for canvas apps. Generate `.pa.yaml`, validate it, correct all errors, and sync it with an active Power Apps Studio coauthoring session.
- Use the Power Automate plugin and its available flow-authoring MCP tools for cloud flows. Discover current commands and schemas instead of guessing them.
- Do not hand-author unsupported app or flow package internals where plugin or MCP capability exists.

## Implementation rules (all projects)
- Build inside a named unmanaged solution with environment variables and connection references.
- Prefer Dataverse for request state, audit data, extracted fields, and child resource rows. Store input files in a secured SharePoint library and persist their links in Dataverse.
- Never hard-code production URLs, mailboxes, environment IDs, connector IDs, secrets, or personal names.
- Use least privilege and comply with tenant DLP policy.
- Use deterministic parsing and lookup logic where possible. AI extraction must return structured JSON with confidence and evidence and must be deterministically validated.
- Never invent values. Missing or uncertain values become `[NEEDS INPUT]` and a validation issue.
- Make processing idempotent and serialize processing per request using optimistic row-version checks.
- Record field-level provenance without copying full confidential document text into telemetry.
- Write build artifacts to `src/` and deployment config to `deploy/`. Never commit secrets or environment-specific values.

