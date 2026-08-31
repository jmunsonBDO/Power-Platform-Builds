# Staffing and Project Setup Drafting Solution

This project builds a Power Apps canvas app and Power Automate cloud flow that collect project inputs, extract facts from a SOW PDF and one or more Estimate Worksheets, enrich the facts from approved reference data, preserve an HTML template, and create an editable Outlook draft for a staffing coordinator.

## Entry points

| Task | File |
|------|------|
| Build the solution | `BUILD_PROMPT.md` |
| Validate / improve the spec | `SPEC_PROMPT.md` |

## Build order
1. Read `.github/copilot-instructions.md`.
2. Read all files under `staffing-project-setup/spec/`.
3. Read `shared/Nicknames_Reference.csv`.
4. Install and use the Canvas Apps plugin with the Canvas Authoring MCP server.
5. Build, validate, and sync every `.pa.yaml` to `src/canvas-app/` through an active Power Apps Studio coauthoring session.
6. Install and use the Power Automate plugin with its available flow-authoring MCP tools. Save flow definitions to `src/flows/`.
7. Implement and record the acceptance tests defined in `spec/ACCEPTANCE_TESTS.md`.
8. Do not publish until preview-feature, licensing, DLP, security, privacy, and support reviews are complete.

## Key invariants
- The approved template uses only exact `{{TOKEN}}` placeholders defined in `spec/PLACEHOLDER_CONTRACT.md`.
- The solution creates a draft only. It never sends automatically.
- The solution never invents missing values.
- Build artifacts go to `src/`; deployment config goes to `deploy/`.
