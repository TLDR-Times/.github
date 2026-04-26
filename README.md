# 1. TLDR-Times `.github` Repository

Organization-wide configuration and default community health files for the `TLDR-Times` GitHub Organization.

## 1.1. Repository Role

A repository named `.github` inside a GitHub Organization is treated specially by GitHub. It supplies **default community health files** ("CHFs" — metadata files such as templates, policies, and guidelines that GitHub recognizes by name) and the **organization profile README** to every other repository owned by the same Organization, when those repositories do not provide their own copy.

## 1.2. Scope

| In scope | Out of scope |
|---|---|
| Default issue/PR templates | Per-project source code |
| Default `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, `SECURITY.md`, `SUPPORT.md`, `FUNDING.yml`, `GOVERNANCE.md` | Reusable GitHub Actions workflows (§ 5.1.) |
| Org profile README (`profile/README.md`) | Private-repository defaults (§ 5.2.) |
| Workflow templates (`workflow-templates/`) | Repository-specific overrides |

# 2. Resolution Mechanics

## 2.1. Fallback Algorithm

For each supported file (§ 3.2.), GitHub resolves the effective copy used by a child repository `R` in the Organization as:

1. Look for the file inside `R` itself, in any of the locations listed in § 2.2.
2. If absent in `R`, look for the file inside this `.github` repository, in the same locations.
3. If absent in both, GitHub treats the file as undefined for `R`.

Step 1 takes precedence over step 2; this is the **override rule** (§ 4.1.).

## 2.2. Recognized File Locations

GitHub searches three directories, in this order, within whichever repository is being checked under § 2.1.:

| Order | Path | Notes |
|---|---|---|
| 1 | repository root | e.g. `CONTRIBUTING.md` |
| 2 | `.github/` | e.g. `.github/CONTRIBUTING.md` |
| 3 | `docs/` | e.g. `docs/CONTRIBUTING.md` |

The first match wins; later matches are ignored.

## 2.3. Visibility Constraints

| This repo's visibility | Defaults available to public child repos | Defaults available to private child repos |
|---|---|---|
| `public` | yes | yes |
| `private` | no | yes |

To provide defaults to private repositories only, GitHub recognizes a sibling repository named `.github-private` (private). This repository (`.github`) covers the public-repository case.

# 3. Contents

## 3.1. Current Files

| Path | Purpose |
|---|---|
| `pull_request_template.md` | Default body inserted into the PR description field when a contributor opens a pull request in any Organization repository that lacks its own template. Sections: `# WHY`, `# WHAT`. |
| `README.md` | This document. Not a community health file; visible only when browsing this repository directly. |

## 3.2. Supported File Inventory

Files GitHub recognizes as defaultable from this repository:

| File | Effect |
|---|---|
| `CODE_OF_CONDUCT.md` | Linked from "Community" tab and shown to new contributors. |
| `CONTRIBUTING.md` | Linked when a user opens a new issue or PR. |
| `SECURITY.md` | Surfaced in the "Security" tab and the security advisory flow. |
| `SUPPORT.md` | Linked from the issue creation page. |
| `FUNDING.yml` | Renders the "Sponsor" button. |
| `GOVERNANCE.md` | Linked from the Community tab. |
| `pull_request_template.md` | Pre-fills the PR body. |
| `ISSUE_TEMPLATE/*.md` or `ISSUE_TEMPLATE/*.yml` | Populates the "New issue" template chooser. |
| `ISSUE_TEMPLATE/config.yml` | Configures the issue template chooser (e.g. disables blank issues, adds external links). |
| `profile/README.md` | Renders on the Organization's public profile page at `https://github.com/TLDR-Times`. Only valid in the `.github` repo. |
| `workflow-templates/*` | Appears in the "New workflow" chooser for Organization members. Only valid in the `.github` repo. |

# 4. Usage

## 4.1. Override Behavior

A child repository overrides any default in § 3.2. by committing the same filename in any location from § 2.2. No registration, opt-in, or configuration is required; the existence of the file is the override signal.

## 4.2. Adding a Default

1. Place the file at one of the paths in § 2.2. inside this repository (root, `.github/`, or `docs/`).
2. Commit to the default branch (`main`).
3. Effect is immediate for repositories that do not already define the same file.

## 4.3. Editing the Profile README

Create or edit `profile/README.md` on the default branch. GitHub renders it at the Organization page within seconds of the push. Markdown features available are the same as repository READMEs; private images require the viewer to be authenticated.

# 5. Limitations

## 5.1. Workflows Are Not Inherited

GitHub Actions workflow files (`.github/workflows/*.yml`) are **not** propagated to child repositories. Each repository must contain its own workflow files. Reuse is achieved through:

| Mechanism | Purpose |
|---|---|
| `workflow-templates/` in this repo | Provides starter templates surfaced in the "New workflow" UI; copied on use, not linked. |
| Reusable workflows (`uses: TLDR-Times/<repo>/.github/workflows/<file>.yml@<ref>`) | Live composition; the caller workflow references this or another repo at runtime. |
| Composite/Custom Actions | Encapsulated steps callable via `uses:`. |

## 5.2. Private-Repository Defaults

This repository does not supply defaults to private repositories when its own visibility is `public` only in the direction described by § 2.3.; the matrix in § 2.3. is the authoritative statement.

## 5.3. File-Type Restrictions

| Constraint | Detail |
|---|---|
| Filename casing | Must match GitHub's expected casing exactly (e.g. `CODE_OF_CONDUCT.md`, not `code_of_conduct.md`) on case-sensitive lookups. macOS and Windows local filesystems are case-insensitive but Git preserves the committed casing — verify with `git ls-files`. |
| Branch | Defaults are read from the repository's **default branch** only. Files on other branches have no effect. |
| Symlinks | Not resolved by GitHub's CHF lookup; commit real files. |

# 6. Glossary

| Term | Definition |
|---|---|
| Community Health File (CHF) | A repository file GitHub recognizes by name and surfaces in its UI to guide contributors (templates, policies, funding). Enumerated in § 3.2. |
| Default branch | The branch GitHub treats as canonical for a repository (typically `main`). Configurable in repository settings. |
| Organization | A shared GitHub account owning multiple repositories and members, distinct from a personal account. Here: `TLDR-Times`. |
| Override | A child repository providing its own copy of a file listed in § 3.2., suppressing the default from this repository per § 2.1. step 1. |
| Reusable workflow | A GitHub Actions workflow invoked from another workflow via `uses:`, contrasted with workflow templates (§ 5.1.). |
