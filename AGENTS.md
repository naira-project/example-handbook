# AGENTS.md

An example Naira handbook: a VitePress site that builds into a container image and that Naira
shows in the portal. The pages are made up. The repository shows the path a team takes to
publish its own handbook.

## Commands

```sh
npm ci
npm run dev        # preview on localhost
npm run typecheck
npm test           # node --test on test/*.test.mjs
npm run build      # site and dist/naira-handbook.json; a broken internal link fails it
```

## Rules

- Pages live in `docs/`. A file's path is its address: do not rename a page without keeping
  one at the old path.
- `.vitepress/theme/`, `docs/public/_naira/`, `scripts/`, `Dockerfile` and `nginx.conf` come
  from the starter in `naira-project/naira` (`handbook/starter`). Change them there first and
  copy them here, so the image contract `naira.handbook/v1alpha1` stays the same.
- Keep every dependency pinned to an exact version.
- Conventional commits, signed off (`git commit -s`).
