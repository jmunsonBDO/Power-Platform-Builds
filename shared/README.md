# Shared Assets

This folder contains reference data and resources shared across multiple Power Platform projects in this monorepo.

## Contents

| File / Folder | Description |
|---------------|-------------|
| `Nicknames_Reference.csv` | BDO people nickname/alias table. Used by any project that performs person-name matching (scope leader resolution, routing lookups, etc.). Columns: `Canonical Name`, `Alias`, `Active`. |
| `theme/` | BDO Power Apps color palette. Contains the canonical hex reference (`BDO_Colors.yaml`), a paste-ready `App.Formulas` snippet (`BDO_PowerApps_NamedFormulas.yaml`), and a component library definition (`BDO_ComponentLibrary_Theme.pa.yaml`). See `theme/README.md` for usage. |

## Usage

Each project that needs name alias matching should reference this file's path in its `SOLUTION_DESIGN.md` and initialize its Dataverse Name Alias table from it during deployment.
