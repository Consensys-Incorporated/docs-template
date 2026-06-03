---
description: Configure your documentation site so AI agents and LLM tools can discover, read, and cite it accurately.
sidebar_label: Agent readiness
sidebar_position: 1
---

# Configure agent readiness

AI agents, LLM tools, and coding assistants increasingly read documentation directly to answer
developer questions.
This page explains what the template pre-configures for you and what you must customize before
publishing.

## What's already configured

The following features ship ready to use.
You don't need to enable them, but you should understand what they do.

### `llms.txt` and `llms-full.txt` generation

The `docusaurus-plugin-llms` plugin generates two files at build time:

- `/llms.txt` — a structured index of your documentation pages, following the
  [llms.txt standard](https://llmstxt.org/).
  AI agents use this to discover and navigate your site efficiently.
- `/llms-full.txt` — the full documentation corpus concatenated into a single Markdown file,
  suitable for use as a context document in LLM prompts.

The plugin is registered in `docusaurus.config.js`.
See [Customize the LLM index](#customize-the-llm-index) for required setup steps.

### Markdown at `.md` URLs

The `scripts/copy-md-to-build.js` post-build script copies every source `.md` file into the build
output.
This means an agent can append `.md` to any documentation URL and receive the raw Markdown source
(for example, `https://your-site.example.com/get-started/install.md`).

The script also prepends a discovery directive to each file pointing agents to `/llms.txt`.

### Copy page button

The `docusaurus-plugin-copy-page-button` package renders a button in the breadcrumb row of every
documentation page.
Readers can use it to copy the page as Markdown, open the content in ChatGPT, Claude, or Gemini,
or view the raw Markdown source.

The button is positioned via the swizzled `src/theme/DocItem/Layout` component.
No configuration is needed.

### HTTP headers and content negotiation

`vercel.json` sets:

- A `Link` response header on `/` advertising `/sitemap.xml`, `/llms.txt`, and `/llms-full.txt`
  to crawlers and agents.
- `Content-Type: text/markdown` on any URL ending in `.md`.
- Server-side rewrites that map requests with `Accept: text/markdown` to the corresponding `.md`
  file, enabling HTTP content negotiation.

## Required customization

The following items ship with placeholder values.
Update them before your first production build.

### Customize the LLM index

Open `docusaurus.config.js` and update the `docusaurus-plugin-llms` options:

```js
[
  "docusaurus-plugin-llms",
  {
    // highlight-start
    title: "Your product name documentation",
    description:
      "One or two sentences describing what your product does and what the docs cover.",
    // highlight-end
    docsDir: "docs",
    generateLLMsTxt: true,
    generateLLMsFullTxt: true,
    excludeImports: true,
    removeDuplicateHeadings: true,
    logLevel: process.env.CI ? "quiet" : "normal",
    ignoreFiles: ["assets/**", "img/**"],
  },
],
```

If your docs directory has a meaningful top-level structure (for example, separate areas for
different products or audiences), add an `includeOrder` array to control the order of sections
in `llms-full.txt`:

```js
includeOrder: [
  "get-started/**/*",
  "concepts/**/*",
  "how-to/**/*",
  "reference/**/*",
],
```

### Update `static/robots.txt`

Replace the placeholder `Sitemap:` URL with your deployed site's sitemap URL:

```text
Sitemap: https://your-product.example.com/sitemap.xml
```

The rest of the file — AI crawler permissions and `Content-Signal` declarations — is already
configured to allow indexing and AI use and doesn't need to change for most sites.

If your documentation is internal-only or not licensed for AI training, remove or adjust the
`ai-train=yes` signal and the individual `User-agent` allow rules before publishing.

### Set `SITE_URL` for the Markdown build script

The `scripts/copy-md-to-build.js` script prepends a discovery directive to every raw Markdown
file pointing agents to your site's `/llms.txt`.
Set the `SITE_URL` environment variable in your build environment, or update the constant in the
script:

```js
const SITE_URL = process.env.SITE_URL || "https://your-product.example.com";
```

## AI coding assistant setup

The template ships with Cursor rules and skills that help AI coding assistants produce
documentation that meets your team's standards.
Customize these files so the assistant understands your product's specific conventions.

### Update `.cursor/rules/terminology.mdc`

Add your product-specific terms to the table in `.cursor/rules/terminology.mdc`.
This is the most important rule file to customize — without it, an AI assistant may use
inconsistent capitalization or spelling for your product name and key concepts.

Follow the existing table format:

```markdown
| Required term | Do not use            |
|---------------|-----------------------|
| YourProduct   | yourproduct, YOURPRODUCT |
| API key       | api key, API Key      |
```

### Update `.cursor/rules/contributor-workflow.mdc`

Update the sidebar, redirect, and image path guidance to match your repository's actual structure.
In particular:

- If your sidebar is manually defined rather than autogenerated, describe the file and how to add
  entries.
- Update the image folder path to wherever your site stores assets.
- Add any project-specific CI checks contributors need to know about.

### Review the other rule files

The remaining rule files — `editorial-voice.mdc`, `markdown-formatting.mdc`, and
`content-types.mdc` — are generic Consensys standards and usually don't need changes.
Review them once to confirm they match your team's expectations, then adjust any sections that
conflict with your product's conventions.

### Update `.cursor/skills/author-page/SKILL.md`

If your docs site covers more than one product area or uses a content structure that differs from
the standard Diataxis layout, update the **Inputs** and **Step 1** sections to reflect your
actual areas and folder conventions.

### Update `AGENTS.md`

`AGENTS.md` is the root context file read by AI coding agents when they open your repository.
Update it with:

- Your product name and a brief description.
- The correct documentation areas and their paths.
- Any critical rules specific to your product (for example, which APIs must not be invented,
  which pages are generated rather than hand-edited).

A well-written `AGENTS.md` significantly improves the quality of AI-assisted contributions by
giving the agent the context it needs before it reads a single source file.
