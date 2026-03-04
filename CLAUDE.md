# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is the Autonolas Improvement Proposals (AIPs) repository — a governance documentation repo for proposing changes to the Autonolas protocol. It contains AIP markdown documents, research PDFs, and proposal templates. It is **not** a code/smart-contract repo.

## Repository Structure

- `content/aips/aip-N/` — Each AIP in its own subdirectory containing the proposal markdown and an optional `imgs/` folder
- `aip-templates/` — AIP markdown template (`aip-template0.md`)
- `docs/` — Research papers and analysis PDFs (governance, staking, bridging, liquidity)

## AIP Workflow

- New AIPs go in `content/aips/aip-N/` using the template from `aip-templates/aip-template0.md`
- Each AIP directory contains the markdown file and an optional `imgs/` subdirectory for images
- Image paths in markdown should be relative: `imgs/filename.png`
- File naming: `[identifier]-aip-[title_abbrev].md` (identifier assigned by reviewer)
- AIP frontmatter includes: title, status, author, shortDescription, discussions, created, updated
- Status progression: WIP → Proposed → Approved → Implemented (or Rejected)
- PRs target the `pending-autonolas-aips` branch

## Commit Conventions

Uses Conventional Commits: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`, `perf:`
