# OpenAI Docs Index Pattern

## Source

- OpenAI: `https://openai.com/zh-Hans-CN/index/harness-engineering/`

## Goal

Use `AGENTS.md` as the short root map, then hand off into `docs/index.md` for the deeper documentation tree.

`docs/index.md` is not a duplicate README. It is the durable navigation layer for the repo's real working knowledge.

## What To Mirror From The Article

Do not copy OpenAI's exact folder names. Copy the shape:

1. short root map
2. deeper docs index
3. durable sections for active work, historical work, product or architecture knowledge, references, and quality rules

The article's example is not a single flat `docs/index.md` only. It shows a layered docs tree with indexed subdomains, such as `design-docs/index.md` and `product-specs/index.md`. The right takeaway is hierarchical indexing:

- `AGENTS.md` at the root
- a top-level docs map when the docs tree is broad enough
- nested `index.md` files inside major doc domains when one flat index would become noisy

## Recommended `docs/index.md` Sections

Use the smallest set that fits the project:

### 1. Current Start Points

Always include the current high-signal entrypoints, for example:

- current product spec
- current active contract or execution plan
- current verification command
- current architecture map

### 2. Product And Architecture

Point to the durable docs that explain what the system is and how it is shaped:

- product specs
- architecture docs
- service or module maps
- important constraints

### 3. Active Work

List the files a future agent should read to continue the current effort:

- active execution plans
- active contracts
- latest handoffs
- latest QA notes

### 4. History

Point to completed or archived work when the repo is large enough to need it:

- completed execution plans
- closed handoffs
- important historical decisions

### 5. References

Point to stable lookup material:

- generated references
- external-interface references
- operational runbooks
- coding rules or quality standards

## Matching By Project Size

### Small Project

Keep `docs/index.md` compact:

- current start points
- product spec
- current contract
- verification entrypoint

### Medium Project

Add:

- architecture section
- active work section
- references section
- child index pages under larger doc groups when a single root index starts flattening too much context

### Long-Running Or Large Project

Add:

- active work
- history
- quality or reliability rules
- generated or reference docs
- nested indexes for major doc domains so the root docs map stays navigable

## Anti-Patterns

- duplicating README content instead of mapping real docs
- listing every file in the repo with no prioritization
- hiding the active plan or current contract below generic sections
- keeping stale paths after docs move
- putting deep docs guidance back into `AGENTS.md` instead of the docs index

## Minimal Example

```md
# Docs Index

## Current Start Points
- `specs/current.md`
- `plans/active/current.md`
- `../scripts/verify.sh`

## Product And Architecture
- `architecture/index.md`
- `specs/`

## Active Work
- `plans/active/`
- `handoffs/`
- `qa/`

## References
- `references/`
- `quality.md`
```
