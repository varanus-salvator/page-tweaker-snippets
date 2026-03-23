# Page Tweaker Snippets

Community-maintained snippet library for the [Page Tweaker](https://github.com/varanus-salvator/page-tweaker) browser extension. Add custom JavaScript and CSS tweaks for any website.

## How it works

The Page Tweaker extension fetches `index.json` from this repo. Users can browse, search, and install snippets directly from the extension popup.

## Snippet format

Each snippet in `index.json`:

```json
{
  "id": "unique-id",
  "name": "Human-readable name",
  "domain": "example.com",
  "description": "What this snippet does",
  "js": "document.querySelector('.annoyance')?.remove();",
  "css": ".popup { display: none !important; }"
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `id` | yes | Unique identifier, e.g. `site-purpose` |
| `name` | yes | Short descriptive name |
| `domain` | yes | Domain to match (without `www.`) |
| `description` | no | What the snippet does |
| `js` | no | JavaScript to inject into the page |
| `css` | no | CSS to inject into the page |

## Contributing

1. Fork this repo
2. Add your snippet to `index.json`
3. Open a pull request

### Guidelines

- **One snippet per purpose** — don't bundle unrelated tweaks
- **Use a clear `id`** — format: `domain-purpose`, e.g. `reddit-old-design`
- **Test your snippet** — make sure it works in the Page Tweaker extension
- **Keep it minimal** — smallest possible JS/CSS to achieve the goal
- **No malicious code** — no tracking, data exfiltration, or credential harvesting
