# AstroPaper on Tofu

AstroPaper is Sat Naing's minimal, responsive Astro blog theme. It includes Markdown and MDX posts, light and dark themes, a local Pagefind search index, RSS, a sitemap, and generated social images. A copy starts with the upstream example articles and branding; its owner can edit the content and configuration in the repository.

## Running the app

- **Tofu framework preset:** `astro`.
- **Repository / project root:** `/` (this directory).
- **Runtime for the build:** Node.js `>=22.12.0` as declared upstream.
- **Package manager:** pnpm 11.3.0, pinned in `package.json` to match upstream CI, using the existing `pnpm-lock.yaml` and `pnpm-workspace.yaml`. This version supports the workspace's `allowBuilds` setting; deployment must not silently select an older pnpm that ignores it.
- **Install:** `pnpm install --frozen-lockfile`.
- **Build:** `pnpm run build` (Astro type checking and build, then Pagefind indexing).
- **Output directory:** `dist/`, with its entry page at `dist/index.html`. The source repository is an Astro app, not a prebuilt static upload.
- **Preview the built output:** `pnpm run preview --host 127.0.0.1 --port 4397`.
- **Database:** none. Posts and pages live in the repository.
- **Accounts and secrets:** none required. No Tofu-owned service account is used.

## Public environment settings

| Name | Required | Purpose |
| --- | --- | --- |
| `PUBLIC_SITE_URL` | Recommended for a published copy | This copy's full public URL, such as `https://my-blog.example/`. Sets canonical links, RSS links, sitemaps, and social-image metadata. Rebuild after changing it or attaching a custom domain. |
| `VERCEL_PROJECT_PRODUCTION_URL` / `VERCEL_URL` | No manual setting | Provider-supplied fallback hostnames when `PUBLIC_SITE_URL` is absent. Local builds fall back to `http://localhost:4321/`. |
| `PUBLIC_GOOGLE_SITE_VERIFICATION` | No | Optional upstream Search Console verification meta tag. Leave unset unless the owner wants it; it loads no tracking script. |
| `ASTRO_TELEMETRY_DISABLED=1` | Recommended during builds | Disables the Astro CLI's build telemetry. It is not a browser setting. |

The blog works without environment settings or secrets. Set `PUBLIC_SITE_URL` to the managed address before the production build so the published site's metadata refers to the copy. A custom domain needs a new build with that address.

## Changes and reasons

1. Added this file to document the gallery's build contract and the upstream source.
2. Adapted `astro-paper.config.ts` to take the deployed site's URL from public environment settings, with provider and local fallbacks. The upstream demo URL would otherwise appear in every copied site's canonical links and feed.
3. Pinned `packageManager` to pnpm 11.3.0, matching upstream CI and verified locally, so builds honor the upstream workspace's dependency build policy. The dependency versions and lockfile remain unchanged.

The upstream name, logo, favicon, example content, `LICENSE`, and copyright notices remain unchanged. The upstream edit-page link still points to the upstream repository; an owner who wants that feature should set it to their own repository, or disable `features.editPost`. Social profile placeholders and author details should be personalized before presenting the blog as one's own.

No analytics or advertising scripts were found in the app source. Astro downloads Google Sans Code from Google Fonts during the build and serves the resulting font files locally. Some upstream example posts embed images hosted on GitHub, Cloudinary, satnaing.dev, and i.ibb.co; those assets need their public hosts to remain available. Search uses the generated Pagefind files and needs no search API or account.

## Upstream and licence

- Upstream: https://github.com/satnaing/astro-paper
- Upstream branch: `main`
- Base upstream commit: `35cfa7fbe0b897306d27670d3819e55d5205f3dd`
- Gallery fork: https://github.com/tofu-gallery/astro-paper
- Adaptations live on `tofu`; `main` is kept as the upstream mirror.
- Licence: MIT, Copyright (c) 2023 Sat Naing, as written in the unchanged `LICENSE`.

## Verification

Verified locally on 2026-10-23 with Node.js 22.22.3 and the pinned pnpm 11.3.0 (the initial install and build also passed on pnpm 12.8.1). Commands below are run from the repository root (the local checkout was `/private/tmp/gallery-batch2/astro-paper`).

```sh
CI=true ASTRO_TELEMETRY_DISABLED=1 pnpm install --frozen-lockfile
CI=true ASTRO_TELEMETRY_DISABLED=1 PUBLIC_SITE_URL=http://localhost:4397 pnpm run build
ASTRO_TELEMETRY_DISABLED=1 pnpm run preview --host 127.0.0.1 --port 4397
curl --fail --silent --show-error --max-time 15 --output /dev/null --write-out 'HTTP %{http_code}\n' http://127.0.0.1:4397/
pnpm exec eslint astro-paper.config.ts
```

The frozen install passed. The complete build passed: Astro checked 55 files with zero errors, warnings, or hints, built 45 pages, and Pagefind indexed 17 pages. The built homepage returned HTTP 200 with the AstroPaper title; `/search/`, `/pagefind/pagefind.js`, `/rss.xml`, and `/posts/adding-new-posts-in-astropaper-theme/` also returned 200. The preview was stopped after verification. ESLint passed for the changed configuration.

Generated homepage canonical links, RSS, sitemap, and robots metadata were checked against the configured `PUBLIC_SITE_URL`. The homepage's font URLs resolve locally, with 20 font files in `dist/_astro/fonts/`, and its external script URL is local to the site. The upstream lockfile, licence, and favicon were compared to the base commit and remain unchanged. This is local build and HTTP verification; no hosted deployment or interactive browser QA was performed.
