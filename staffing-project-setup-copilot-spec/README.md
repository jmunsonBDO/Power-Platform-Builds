# Staffing and Project Setup Drafting Solution

This repository is the build contract for GitHub Copilot CLI. It defines a Power Apps canvas app and Power Automate cloud flow that collect project inputs, extract facts from a SOW PDF and one or more Estimate Worksheets, enrich the facts from approved reference data, preserve an HTML template, and create an editable Outlook draft for a staffing coordinator.

## Build order
1. Read `.github/copilot-instructions.md`.
2. Read all files under `staffing-project-setup-copilot-spec/`.
3. Install and use the Canvas Apps plugin with the Canvas Authoring MCP server.
4. Build, validate, and sync every `.pa.yaml` file through an active Power Apps Studio coauthoring session.
5. Install and use the Power Automate plugin with its available flow-authoring MCP server.
6. Implement and record the acceptance tests.
7. Do not publish until preview-feature, licensing, DLP, security, privacy, and support reviews are complete.

## Required source assets
Place these files in `reference-assets/` without changing their names:
- `Staffing_Request_Email_Body_Template.htm`
- sample SOW PDF
- sample EWS `.xlsx`
- `AccountSummaryExport.xlsx`

Configure the SharePoint location for the ITS Sales & Delivery Routing source through an environment variable. Do not hard-code it.

## Key invariants
- The approved template uses only exact `{{TOKEN}}` placeholders defined in `docs/PLACEHOLDER_CONTRACT.md`.
- The solution creates a draft only. It never sends automatically.
- The solution never invents missing values.
