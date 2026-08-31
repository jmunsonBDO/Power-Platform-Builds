# Shared Assets

This folder contains reference data and resources shared across multiple Power Platform projects in this monorepo.

## Contents

| File | Description |
|------|-------------|
| `Nicknames_Reference.csv` | BDO people nickname/alias table. Used by any project that performs person-name matching (scope leader resolution, routing lookups, etc.). Columns: `Canonical Name`, `Alias`, `Active`. |

## Usage

Each project that needs name alias matching should reference this file's path in its `SOLUTION_DESIGN.md` and initialize its Dataverse Name Alias table from it during deployment.
