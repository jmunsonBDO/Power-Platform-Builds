# Initial GitHub Copilot CLI Prompt

Read `.github/copilot-instructions.md` and every file under `staffing-project-setup-copilot-spec/` before changing anything. Build the solution in small, validated increments.

1. Inspect installed Microsoft Power Platform plugins and list the Canvas Apps and Power Automate commands and MCP tools actually available. Do not guess command names.
2. Configure the Canvas Authoring MCP server from the active Power Apps Studio coauthoring session.
3. Create the responsive canvas app defined in `UX_DESIGN.md` using the Dataverse model and configuration in `SOLUTION_DESIGN.md`. Generate and validate every `.pa.yaml` file and correct all errors.
4. Use the Power Automate plugin and available flow-authoring MCP tools to create the solution-aware orchestration flow and modular child flows or scopes. Create an Outlook draft only and never send.
5. Implement the exact case-sensitive `{{TOKEN}}` contract in `PLACEHOLDER_CONTRACT.md`. Do not implement token aliases, bracket syntax, legacy token repair, or fuzzy placeholder matching.
6. Implement extraction, reference matching, resource-row cloning, HTML safety, human review, idempotency, security, and missing-information behavior exactly as documented.
7. Add tests and deployment documentation. Run Canvas validation and Flow checker. Stop and report blockers requiring a tenant administrator, new connector, licensing decision, DLP exception, or preview-feature approval.
8. Use the following OneDrive file locations for samples of the external production reference tables
   1. AccountSummary: https://bdoskunkworks-my.sharepoint.com/:x:/g/personal/jmunson_bdoskunkworks_onmicrosoft_com/IQBOkEBw3mtITa5Qo3-xjSJmAd_1AXEDKqMfscB2eCllxj8?e=9kBOhd
   2. SalesRouting: https://bdoskunkworks-my.sharepoint.com/:u:/g/personal/jmunson_bdoskunkworks_onmicrosoft_com/IQDGn_FTptgPT7k9wWoGq8WFAbV9yDlv4ehrIs8LUh3CGEY?e=3CRSxj
8. Produce a final implementation report listing components, environment variables, connection references, validation results, test results, blockers, and manual deployment steps.

Do not weaken requirements to make validation pass. Do not fabricate unavailable source data. Do not modify the HTML template except by replacing approved placeholders, removing their approved optional value nodes, and cloning the prototype resource row.
