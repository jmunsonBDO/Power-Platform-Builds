# Build Prompt — Staffing Project Setup

Read `.github/copilot-instructions.md` and every file under `staffing-project-setup/spec/` before changing anything. Also read `shared/Nicknames_Reference.csv`. Build the solution in small, validated increments. Write all build artifacts to `staffing-project-setup/src/` and deployment config to `staffing-project-setup/deploy/`.

## Project-specific rules
- Treat all files under `staffing-project-setup/spec/` as normative. If requirements conflict, use this priority: security and DLP requirements, placeholder contract, project requirements, solution design, UX design.
- Create an Outlook draft only. Do not add a Send email action.
- Use only the exact `{{TOKEN}}` placeholders defined in `spec/PLACEHOLDER_CONTRACT.md`. Do not implement token aliases, bracket syntax, legacy token repair, or fuzzy placeholder matching.
- Preserve HTML structure, inline styles, links, and table formatting. Clone the complete prototype resource row for additional resources.
- HTML-encode inserted plain text. Permit only sanitized HTTPS anchors in approved link fields.
- One request version may have at most one active draft.

## Completion gate
Do not claim completion until all app YAML validates, Flow checker errors are resolved, acceptance tests are recorded, all defined placeholders resolve or become `[NEEDS INPUT]`, the missing-information section is correct, the message is saved as a draft and not sent, and deployment settings are externalized in `deploy/`.

## Build steps

1. Inspect installed Microsoft Power Platform plugins and list the Canvas Apps and Power Automate commands and MCP tools actually available. Do not guess command names.
2. Configure the Canvas Authoring MCP server from the active Power Apps Studio coauthoring session.
3. Create the responsive canvas app defined in `spec/UX_DESIGN.md` using the Dataverse model and configuration in `spec/SOLUTION_DESIGN.md`. Generate and validate every `.pa.yaml` file and correct all errors. Save output to `src/canvas-app/`.
4. Use the Power Automate plugin and available flow-authoring MCP tools to create the solution-aware orchestration flow and modular child flows or scopes. Save output to `src/flows/`. Create an Outlook draft only and never send.
5. Implement the exact case-sensitive `{{TOKEN}}` contract in `spec/PLACEHOLDER_CONTRACT.md`.
6. Implement extraction, reference matching, resource-row cloning, HTML safety, human review, idempotency, security, and missing-information behavior exactly as documented. Use sample files in `spec/reference-assets/samples/` and `spec/reference-assets/SalesRouting.htm` for development and acceptance testing.
7. Initialize the Name Alias Dataverse table from `shared/Nicknames_Reference.csv` (manual CSV import — document as a deployment step).
8. Add tests and deployment documentation. Run Canvas validation and Flow checker. Stop and report blockers requiring a tenant administrator, new connector, licensing decision, DLP exception, or preview-feature approval.
9. Produce a final implementation report listing components, environment variables, connection references, validation results, test results, blockers, and manual deployment steps.

Do not weaken requirements to make validation pass. Do not fabricate unavailable source data. Do not modify the HTML template except by replacing approved placeholders, removing their approved optional value nodes, and cloning the prototype resource row.

