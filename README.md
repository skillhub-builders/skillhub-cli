# skillhub-cli

CLI for the [Skillhub](https://skillhub.builders/) AI skills marketplace. Install `SKILL.md` packages directly into your AI coding environment.

## Install

```bash
npm install -g skillhub-cli
```

Or run without installing:

```bash
npx skillhub-cli install <skill-name>
```

## Commands

### `skillhub install <skill-name>`

Download and install a public skill from the registry.

```bash
skillhub install pdf
skillhub install docx
```

The CLI resolves transitive dependencies automatically. If `pdf` requires `markdown-utils`, both are installed in the correct order.

```
Resolving pdf…
  + 1 dependency: markdown-utils
Installing markdown-utils… ✓ Installed markdown-utils
Installing pdf… ✓ Installed pdf
```

### `skillhub install --bundle <slugs>`

Install multiple skills in one command. Shared dependencies are deduplicated server-side.

```bash
skillhub install --bundle pdf,docx,pptx
```

```
Resolving bundle (3 skill(s))…
  + 1 auto-resolved dep(s): markdown-utils

Installing 4 skill(s):
  ✓ markdown-utils
  ✓ pdf
  ✓ docx
  ✓ pptx
```

### `skillhub install @orgSlug/skillSlug`

Install a private organization skill. Requires a `SKILLHUB_ORG_TOKEN`.

```bash
export SKILLHUB_ORG_TOKEN=<your-org-token>
skillhub install @acme/internal-reviewer
```

Private skills are namespaced under `@orgSlug/` so they cannot collide with public skills.

### `skillhub list`

Show all installed skills from the lockfile.

```bash
skillhub list
```

```
Installed skills:

  pdf                            1.2.0        2025-03-18T10:00:00.000Z
  docx                           1.0.3        2025-03-18T10:01:00.000Z
```

### `skillhub update <skill-name>`

Check if a newer version is available and install it.

```bash
skillhub update pdf
```

```
✓ Updated pdf 1.2.0 → 1.3.0
```

If already up to date:

```
✓ pdf is already up-to-date (1.2.0)
```

### `skillhub remove <skill-name>`

Remove a skill from the lockfile. Alias: `uninstall`.

```bash
skillhub remove pdf
# or
skillhub uninstall pdf
```

> Note: this removes the entry from the lockfile but does not delete the local `SKILL.md` files.

## Where files are installed

| Path | Description |
|------|-------------|
| `~/.claude/skills/<name>/SKILL.md` | Loaded by Claude Code automatically |
| `~/.codex/skills/<name>/SKILL.md` | Loaded by Codex (if `~/.codex` exists) |
| `~/.skillhub/lock.json` | Lockfile tracking installed versions |

Private org skills are namespaced:

```
~/.claude/skills/@acme/internal-reviewer/SKILL.md
```

## Lockfile

`~/.skillhub/lock.json` tracks what's installed:

```json
{
  "skills": {
    "pdf": {
      "name": "pdf",
      "version": "1.2.0",
      "installedAt": "2025-03-18T10:00:00.000Z"
    },
    "@acme/internal-reviewer": {
      "name": "@acme/internal-reviewer",
      "version": "2.0.1",
      "installedAt": "2025-03-18T10:05:00.000Z"
    }
  }
}
```

## Environment variables

| Variable | Description |
|----------|-------------|
| `SKILLHUB_API_URL` | Override the registry URL (default: `https://skillhub.dev`) |
| `SKILLHUB_ORG_TOKEN` | Bearer token for private org skill installs |

Example using a local registry during development:

```bash
SKILLHUB_API_URL=http://localhost:3000 skillhub install pdf
```

## Contributing

```bash
git clone https://github.com/skillhub-builders/skillhub-cli
cd skillhub-cli
npm install
npm run build
```

Run tests:

```bash
npm test
```

Run in dev mode (no compile step):

```bash
npm run dev -- install pdf
```

## License

MIT
