---
description: Understand the structure of a documentation site repository.
sidebar_label: Repository structure
sidebar_position: 2
---

# Doc site repository structure

This page describes the function of each file in your new doc site, based on
[this repository](https://github.com/consensys/docs-template).

## 📁 `.github` folder

```text title="Folder structure"
.github
└── workflows
    ├── algolia-search-scraper.yml
    ├── build.yml
    ├── case.yml
    ├── dependabot.yml
    ├── links.yml
    ├── lint.yml
    ├── release.yml
    ├── spelling.yml
    └── trivy.yaml
```

### 📁 `workflows` folder

Contains all the GitHub actions for the repository.

#### 📄 `algolia-search-scraper.yml`

Action that runs in the background and populates the index that [Algolia uses to search](../configure/search.md).

#### 📄 `build.yml`

Action that builds the docs as they would be built in production, to check for any build errors.

#### 📄 `case.yml`

Action that ensures that all Markdown files have [file names](../contribute/format-markdown.md#file-names)
which are only lower case letters, digits, dashes, or underscores.

#### 📄 `dependabot.yml`

Action that reviews dependencies and automatically updates them if needed.

#### 📄 `links.yml`

Action that checks for broken links using [Linkspector](../contribute/run-link-checker.md).

#### 📄 `lint.yml`

Action that runs `npm run lint` from `package.json`.
It includes Markdown linting, TypeScript linting, and CSS styling.

#### 📄 `release.yaml`

Action that checks all recent commits made to `main` branch and automatically cuts a release in line
with [semantic versioning](https://semver.org/).
This action reads the configuration in `.releaserc.js` in the root directory of this repository.

#### 📄 `spelling.yaml`

Action that checks for spelling errors using [Vale](../contribute/run-vale.md).

#### 📄 `trivy.yaml`

Action that scans for vulnerabilities using [Trivy](https://trivy.dev/).

## 📁 `docs` folder

Contains all the Markdown and related files for the [docs](https://docusaurus.io/docs/docs-introduction)
functionality of Docusaurus.

## 📁 `scripts` folder

Contains Node.js scripts that run outside Docusaurus, typically as post-build steps.

## 📁 `src` folder

Contains all the JSX and CSS files for the [pages](https://docusaurus.io/docs/creating-pages)
functionality of Docusaurus.

```text title="Folder structure"
src
├── css
│   └── custom.css
├── pages
│   └── markdown-page.md
└── theme
    └── DocItem
        └── Layout
            ├── index.jsx
            └── styles.module.css
```

### 📁 `css` folder

Contains any non-scoped CSS files.

:::caution important
We recommend leaving the default `custom.css` file by itself in this folder and not add any other files.
`custom.css` is the [global styles](https://docusaurus.io/docs/styling-layout#global-styles) file
that applies to the entire doc site.
:::

### 📁 `pages`

[Pages](https://docusaurus.io/docs/creating-pages) are one-off standalone pages that don't have
sidebars by default.

You can still [add a Markdown page](https://docusaurus.io/docs/creating-pages#add-a-markdown-page) to
this folder, and it will be rendered with the file name as the path.
Routing is file-based for any `.js` and `.tsx` file.

### 📁 `theme` folder
Contains [swizzled](https://docusaurus.io/docs/swizzling) Docusaurus theme components — local
overrides that replace or wrap the default theme implementation.

## 📁 `static` folder

Contains assets that can be directly copied on build output.
Usually images, stylesheets, favicons, fonts, etc.

See how to [reference your static asset](https://docusaurus.io/docs/static-assets#referencing-your-static-asset).

```text title="Folder structure"
static
├── img
│   ├── favicon.ico
│   ├── logo.svg
│   └── logo_dark.svg
├── index.md
└── robots.txt
```

#### 📄 `index.md`

An AI-facing Markdown entry point served at the root of your site (`/index.md`).
It provides a plain-text product overview and links to the agent-readable resources
(`/llms.txt`, `/llms-full.txt`, `/sitemap.xml`) that AI agents use to discover and navigate
your documentation.

Update this file with your product name, a short description, and links to key sections before
publishing.

#### 📄 `robots.txt`

Crawler permissions file.
The template version explicitly allows all major AI crawlers (GPTBot, OAI-SearchBot,
PerplexityBot, Google-Extended) and includes a `Content-Signal` header declaring that the
content may be used for search indexing, model grounding, and AI training.

Update the `Sitemap:` URL at the bottom to match your deployed site.

## 📁 `.cursor` folder

Contains AI coding assistant configuration for [Cursor](https://cursor.com).

```text title="Folder structure"
.cursor
├── rules
│   ├── content-types.mdc
│   ├── contributor-workflow.mdc
│   ├── editorial-voice.mdc
│   ├── markdown-formatting.mdc
│   └── terminology.mdc
└── skills
    ├── author-page
    │   └── SKILL.md
    └── style-review
        └── SKILL.md
```

### 📁 `rules` folder

Cursor rules are persistent instructions loaded automatically when you work on files matching
their glob patterns.
The template ships with five rules covering editorial voice, terminology, Markdown formatting,
content types (Diataxis), and contributor workflow.

Customize these rules to match your product's terminology, link conventions, and team workflow.

### 📁 `skills` folder

Cursor skills are on-demand instructions loaded when the task matches the skill description.
The template ships with two skills:

- **`author-page`** — scaffolds and drafts new documentation pages to editorial standards.
- **`style-review`** — audits pages for voice, terminology, formatting, and content-type compliance
  before a PR is submitted.

## 📄 `AGENTS.md`

Root-level context file read by AI coding agents (Cursor, Claude, etc.) when they open the
repository.
It describes the documentation areas, editorial standards, critical rules, and a lookup table of
all AI-readiness features with the file to edit and what to customize per site.

Update this file when you rename areas, add products, or change conventions.

## 📄 `.editorconfig`

[EditorConfig](https://editorconfig.org/#overview) is supported by most IDEs and text editors to
provide consistent coding styles for projects using a configuration specification.

## 📄 `.eslintignore`

[ESLint](https://eslint.org/) is used by this project since it contains Javascript, Typescript, and
React code, and lints the code to provide a consistent style for all developers and contributors.

The `.eslintignore` file contains a list of directories for ESLint to ignore when linting.

## 📄 `.eslintrc.js`

Configuration for [ESLint](https://eslint.org/) and accompanying plugins used by it to parse and
lint the code.

## 📄 `.gitignore`

A file containing files and folders for Git to ignore when adding or committing.

## 📄 `.nvmrc`

Contains the Node.js version to use for this project.
It requires installing [nvm](https://github.com/nvm-sh/nvm#installing-and-updating).

## 📄 `.prettierrc`

We recommend using [Prettier](https://prettier.io/) to format all files.
Anything not covered in `.editorconfig` is overridden or specified in this Prettier configuration file.

Running [`npm run format`](run-in-development.md#npm-run-format) runs Prettier to
format and save those changes.

## 📄 `.releaserc.js`

[`semantic-release`](https://github.com/semantic-release/semantic-release) is used to easily keep
track of version changes to documentation.
On push to the `main` branch, the `release` GitHub action takes all necessary commits based on their
type and increments the version according to [semver](https://semver.org/) conventions.
However, this is not strictly necessary, and you can remove this along with its accompanying action.

## 📄 `.stylelintignore`

[StyleLint](https://stylelint.io/) is used to lint CSS files.
This file ignores directories which do not need to be linted.

## 📄 `.stylelintrc.js`

[StyleLint](https://stylelint.io/) configuration for linting.

## 📄 `CHANGELOG.md`

[Semantic Release](https://github.com/semantic-release/semantic-release) automatically updates the
CHANGELOG file with release history and commits appended to each release.
You shouldn't modify this manually.

## 📄 `api.mustache`

This repository by default has the plugin `docusaurus-plugin-openapi-docs` installed to demonstrate
how to integrate OpenAPI documentation directly into Docusaurus.
The `api.mustache` file contains the API template for the plugin when generating the Markdown files.

## 📄 `docusaurus.config.js`

Contains all major Docusaurus configuration which is necessary to configure its behavior.

## 📄 `package-lock.json`

Used by npm when `npm install` is used to lock versions and reduce differences between development
environments if this isn't committed to the repository.
It should not be necessary to edit this file.

## 📄 `package.json`

Used by npm and contains configuration scripts, dependencies, development dependencies, and other
related dependency configurations.

## 📄 `sidebars.js`

Separate `.js` file used by Docusaurus to provide [sidebar configuration](configure-docusaurus.md#sidebar).

## 📄 `tsconfig.json`

This project uses Typescript for React.
The `tsconfig.json` contains Typescript compiler options but isn't used in compilation of the
project and is only for editor experience.

## 📄 `vercel.json`

Vercel configuration file.
The template version includes three sections:

- **`redirects`** — [server-side redirects](https://vercel.com/docs/redirects/configuration-redirects)
  for moved or deleted pages.
  Add an entry here whenever you rename or remove a page.
- **`headers`** — HTTP response headers added to every response.
  The template sets a `Link` header on `/` that advertises `/sitemap.xml`, `/llms.txt`, and
  `/llms-full.txt` to crawlers and agents, and sets `Content-Type: text/markdown` on any URL
  ending in `.md`.
- **`rewrites`** — server-side rewrites that map requests with an `Accept: text/markdown` header
  to the corresponding `.md` file in the build output.
  This enables HTTP content negotiation so AI agents can retrieve Markdown by sending the
  appropriate `Accept` header without knowing the `.md` URL in advance.
