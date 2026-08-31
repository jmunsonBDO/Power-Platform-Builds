# Power Platform Builds

A monorepo for Power Platform solutions built using Power Platform Skills and its plugins.

## Structure

```
.github/copilot-instructions.md   ← Monorepo-wide Copilot build rules
shared/                            ← Cross-project shared assets
<project-name>/
├── BUILD_PROMPT.md                ← Copilot build entry point
├── SPEC_PROMPT.md                 ← Copilot spec-validation entry point
├── spec/                          ← Normative design documents
├── src/                           ← Power Platform build artifacts
└── deploy/                        ← Deployment settings templates
```

## Projects

| Project | Description | Status |
|---------|-------------|--------|
| [staffing-project-setup](staffing-project-setup/README.md) | Drafts a Staffing and Project Setup email from SOW/EWS documents | Spec complete |
