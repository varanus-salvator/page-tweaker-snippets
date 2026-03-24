# Page Gremlin

Browser extension that injects custom JS/CSS per domain. Ships with a community snippet library for common annoyances (paywalls, nag banners, etc).

## Install

### Firefox (permanent)

1. Go to [addons.mozilla.org/developers](https://addons.mozilla.org/en-US/developers/)
2. Click **Submit a New Add-on** → **On this site** (for yourself) or **On your own** (self-distributed, unlisted)
3. Zip the extension: `cd page_gremlin && zip -r page-gremlin.zip manifest.json background.js popup.html popup.js icon.svg snippets/`
4. Upload the zip — Mozilla will sign it
5. Download the signed `.xpi` and open it in Firefox — it installs permanently

Or use `web-ext`:

```bash
npm install -g web-ext
cd page_gremlin
web-ext sign --api-key=YOUR_AMO_KEY --api-secret=YOUR_AMO_SECRET
# produces a signed .xpi in web-ext-artifacts/
```

### Firefox (temporary, for development)

1. Go to `about:debugging#/runtime/this-firefox`
2. Click **Load Temporary Add-on**
3. Select `manifest.json` from this repo
4. ⚠️ Disappears on Firefox restart

### Chrome

1. Go to `chrome://extensions`
2. Enable **Developer mode**
3. Click **Load unpacked** → select the repo folder

## How it works

- Click the gremlin icon in the toolbar to open the popup
- Add tweaks per domain: JS, CSS, or both
- Browse and install community snippets from the built-in **Snippet Library**
- Snippets are fetched from `index.json` in this repo

## `gremlin` API

Every snippet has access to the `gremlin` helper object, injected automatically before your code runs.

### JSON-LD

Most news/recipe sites embed structured data in `<script type="application/ld+json">`. These helpers parse it:

| Method | Returns | Description |
|--------|---------|-------------|
| `gremlin.jsonld()` | `Array` | All JSON-LD objects on the page |
| `gremlin.jsonld("Recipe")` | `Array` | Only objects matching a `@type` |
| `gremlin.jsonldFirst("NewsArticle")` | `Object\|null` | First match for a type |
| `gremlin.articleBody()` | `String\|null` | Article text from NewsArticle/Article/BlogPosting |
| `gremlin.recipe()` | `Object\|null` | Recipe JSON-LD (ingredients, instructions, etc.) |

```js
// Get recipe ingredients
const recipe = gremlin.recipe();
if (recipe) gremlin.log(recipe.recipeIngredient);

// Get article text even when hidden behind a paywall
const text = gremlin.articleBody();
if (text) gremlin.log(text);
```

### Page metadata

| Method | Returns | Description |
|--------|---------|-------------|
| `gremlin.meta()` | `Object` | All `<meta>` tags as `{ name: content }` |

### Reader view

Render a clean reading view directly from JSON-LD data — no DOM hacking needed.

| Method | Description |
|--------|-------------|
| `gremlin.reader()` | Auto-detect article/recipe and render |
| `gremlin.reader("article")` | Force article reader view |
| `gremlin.reader("recipe")` | Force recipe reader view |

```js
// Replace the page with a clean reader view
gremlin.reader();
```

### DOM manipulation

| Method | Description |
|--------|-------------|
| `gremlin.remove(...selectors)` | Remove all matching elements |
| `gremlin.reveal(...selectors)` | Force-show hidden elements |
| `gremlin.insertHTML(html, target?)` | Insert HTML at top of target or `<body>` |
| `gremlin.log(...args)` | `console.log` with `[Page Gremlin]` prefix |

```js
// Remove cookie banners
gremlin.remove('.cookie-banner', '#overlay');

// Unhide paywalled content
gremlin.reveal('.article-body');
```

## Snippet format

Each snippet in `index.json`:

```json
{
  "id": "site-purpose",
  "name": "Human-readable name",
  "domain": "example.com",
  "description": "What this snippet does",
  "js": "gremlin.remove('.annoyance');",
  "css": ".popup { display: none !important; }"
}
```

## Writing your own snippets

1. Open the popup on any page
2. Write JS/CSS in the text areas — it auto-saves per domain
3. Or: fork this repo, add a snippet to `index.json`, and open a PR

**Tips:**
- CSS-only snippets run earliest (at `document_start`) — best for hiding elements instantly
- Use `gremlin.reader()` for paywalled articles that have full text in JSON-LD
- Use `gremlin.remove()` / `gremlin.reveal()` for DOM-based fixes
- Keep it minimal — smallest possible code to get the job done
