# CLAUDE.md

Guidance for Claude Code (claude.ai/code) when working in this repository.

## Overview

This repository (`redienss/magento-silverbot`, **public**) is the infra half of SilverBot: `bin/`
(markshust/docker-magento wrappers), `compose*.yaml`, `Makefile`, `env/`, `screenshots/`,
`logo/`, and the project's public-facing `README.md`. The Magento code itself — the four
`Redienss_SilverBot*` modules — lives in the **private** `src` submodule
(`redienss/magento-silverbot-src`), which also holds `src/CLAUDE.md` and the `src/knowledge/`
Obsidian vault (architecture, integration contracts, ops runbooks, ADRs, reference tables, the
`TODO/` task tree). **When working inside `src/`, read `src/CLAUDE.md` and
`src/knowledge/Home.md` first** — this file only covers the parent/infra repo.

Both repos are on branch `main`.

## Git workflow

**Work on a new branch per feature or bugfix, never straight on `main`.** Once the
implementation is complete — built, verified against the local Docker stack where that applies
— **push the branch and open a pull request without waiting to be asked**, so it's visible on
GitHub for review. This is the one push/PR action that doesn't need a fresh go-ahead each time.

**The maintainer reviews, tests and merges the PR into `main` themselves.** Do not merge a PR
and do not push to `main` directly — every change reaches `main` through a PR they merge, even a
one-line fix.

**Exception: bumping the `src` submodule pointer** after a `src` PR is merged is mechanical
bookkeeping — it only records which already-reviewed `src` commit this repo points at, no new
code — so push that straight to `main`, no separate PR needed for it.

**A commit message is one line and concise**, saying what the change does.

## Operations

- Demo/production is the **HP-T630** (`ssh redienss@192.168.1.31`, `~/Sites/SilverBot`),
  serving `https://demo.silverbot.pl/` behind a `cloudflared` tunnel, Magento in **production
  mode**. See `src/knowledge/Operations/Deploying to the T630.md` for the required post-upgrade
  sequence (`setup:di:compile` → `setup:static-content:deploy` → `cache:flush`).
- `bin/magento <cmd>` runs a command in the phpfpm container (`bin/cli bin/magento <cmd>`).
- `compose.override.yaml` is local/uncommitted — it gates the Cloudflare `tunnel` service behind
  an inactive profile on dev machines so `bin/start` doesn't need `env/cloudflare.env`.
