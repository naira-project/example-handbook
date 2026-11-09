# Example Platform Handbook

An example [Naira](https://github.com/naira-project/naira) handbook: a made-up team handbook
that shows what a team can keep in a Naira handbook, and how a team builds, publishes and
installs one from its own repository. Nothing on its pages is real.

It is built from the
[handbook starter](https://github.com/naira-project/naira/tree/main/handbook/starter) in the
`naira` repository, and its pages show the patterns the starter can render. Build, the image
contract and the upgrade rules are in the
[starter README](https://github.com/naira-project/naira/blob/main/handbook/starter/README.md).

| Path | Purpose |
|---|---|
| `docs/` | The pages. A file's path is its address. |
| `.vitepress/config.mts` | The navigation and the sidebar |
| `package.json` | The handbook's id (`example`), title and owner |
| `.github/workflows/handbook-build.yml` | Builds, signs and publishes the image |
| `.github/workflows/test.yml` | Typecheck, tests and build on every pull request |

Everything else is the starter's, unchanged.

## Preview

```sh
npm ci
npm run dev
docker build -t example .
```

Use the dev server on localhost only (see the starter README).

## Publish

Every push to `main` and every tag `v*` runs `handbook-build.yml`. It calls
`reusable-handbook-build.yml` from
[`naira-github-workflows`](https://github.com/naira-project/naira-github-workflows), which
builds the image, signs it with Cosign (keyless), attests its provenance and pushes it to
`ghcr.io/naira-project/example-handbook`. The `verify` job then checks the signature. The
run summary shows the image reference with its digest.

## Install it in Naira

Add the digest from the run summary to the Naira Helm values:

```yaml
handbookGateway:
  allowedRegistries: [ghcr.io/naira-project]
handbooks:
  - id: example
    contract: naira.handbook/v1alpha1
    label: Example Handbook
    image: ghcr.io/naira-project/example-handbook@sha256:<digest>
```

The handbook opens in the portal's Docs group. DNS, TLS, the same-site rule and signature
checks are in the operator guide,
[`docs/handbooks.md`](https://github.com/naira-project/naira/blob/main/docs/handbooks.md).

## Start your own handbook from this one

1. Create a repository for your handbook and copy this repository's files into it, or fork it.
2. In `package.json`, set `name` (the handbook id, a DNS label of at most 42 characters),
   `naira.title`, `naira.owner`, `repository` and `bugs`.
3. In `.github/workflows/handbook-build.yml`, set `handbook-id` to the same id.
4. Replace the pages in `docs/` and the sidebar in `.vitepress/config.mts`.
5. Push to `main`. The image is published to `ghcr.io/<your-org>/<your-repo>`.
6. Ask the Naira operator to add `ghcr.io/<your-org>` to `handbookGateway.allowedRegistries`
   and your image, pinned by digest, to `handbooks`.

Handbook code runs in the viewer's browser with the viewer's access token: the portal hands
the token to every frame it shows, and a script in a handbook can call the catalog API as the
viewer. Operators install only handbooks whose code their organisation trusts. Keep your
dependencies pinned and review changes to `.vitepress/` and `docs/public/_naira/` like code.
