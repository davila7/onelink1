# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Onelink is a link-in-bio tool where all user data lives in the URL (no database). Profile data is JSON-encoded to base64 and passed as a `data` query parameter. Since URLs can get long, a link shortener (e.g. dub.co) is recommended.

## Commands

```bash
yarn install        # Install dependencies
yarn dev            # Dev server at http://localhost:3000
yarn build          # Production build
yarn preview        # Preview production build
yarn generate       # Static site generation
```

## Architecture

**Stack:** Nuxt 3 + Tailwind CSS + Vue 3 (Composition API with `<script setup>`)

**Data flow:** All profile data is a single reactive object with minified keys (e.g. `n`=name, `d`=description, `i`=image, `ls`=links array). This object is base64-encoded/decoded via `utils/transformer.js` using `js-base64`. No server, no database.

**Key data shape:**
- `n`: name, `d`: description, `i`: image URL
- Social links: `f`(facebook), `t`(twitter), `ig`(instagram), `gh`(github), `tg`(telegram), `l`(linkedin), `e`(email), `w`(whatsapp), `y`(youtube)
- `ls`: array of link objects `{l: label, i: icon, u: url}`

**Pages:**
- `pages/index.vue` — Editor UI (3-column layout: form + preview). Encodes data and copies the generated URL to clipboard on "Publish".
- `pages/1.vue` — Template renderer. Reads `?data=` query param, decodes it, renders via `Templates/Simple`.

**Templates:** The URL path (`/1`) selects the template. Currently only `Templates/Simple.vue` exists. New templates should be added as components under `components/Templates/` with a corresponding page route.

**Components:**
- `AppForm/*` — Editor form sections (Profile, SocialLinks, Links, Preview, Hr)
- `Base/*` — Shared UI primitives
- `ExternalLink.vue` — Renders a single link item
- `Templates/*` — Public-facing bio page templates

**Icons:** Uses `nuxt-icon` with Iconify icon names (e.g. `ph:globe-duotone`).

**Drag & drop:** Links in the editor are reorderable via `vuedraggable`.
