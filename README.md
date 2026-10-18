# sdd-marketplace

A [Claude Code](https://code.claude.com) plugin marketplace for **[sdd-kit](https://github.com/daniel-torresc/sdd-kit)**, the spec-driven development (SDD) methodology for AI coding agents, and for the plugins curated to work alongside it.

sdd-kit is the foundation: it organizes the development process. It deliberately ships no technology, architecture or testing guidance, and no integrations. Those come from other plugins, and this marketplace is where the ones that work well with sdd-kit are collected, whether they are maintained here or elsewhere.

## Plugins

| Plugin | What it is | Source |
|---|---|---|
| `sdd-kit` | The SDD methodology as a set of skills: onboarding, then Define, Design, Build, Verify, Document and Release for every feature | [daniel-torresc/sdd-kit](https://github.com/daniel-torresc/sdd-kit) |

Being listed here means the plugin was reviewed and judged useful alongside sdd-kit. It doesn't mean this repository maintains it. Each plugin keeps its own licence, issues and releases.

## Install

In a Claude Code session:

```
/plugin marketplace add daniel-torresc/sdd-marketplace
/plugin install sdd-kit@sdd-marketplace
```

Or from your shell:

```bash
claude plugin marketplace add daniel-torresc/sdd-marketplace
claude plugin install sdd-kit@sdd-marketplace
```

On Claude Code 2.1.275 or later, one command adds the marketplace and installs the plugin:

```
/plugin install sdd-kit --marketplace daniel-torresc/sdd-marketplace
```

Without a GitHub SSH key, set `CLAUDE_CODE_PLUGIN_PREFER_HTTPS=1` so plugins are cloned over HTTPS.

### For a whole team

To register the marketplace for everyone who works in a repository, run this once in that repository and commit the `.claude/settings.json` it writes:

```bash
claude plugin marketplace add daniel-torresc/sdd-marketplace --scope project
```

Teammates get the marketplace once they trust the folder, and then install the plugins they need.

### Updates

Background auto-update is off by default. Turn it on under **Marketplaces** in `/plugin`, or update by hand:

```bash
claude plugin marketplace update sdd-marketplace
claude plugin update sdd-kit@sdd-marketplace
```

A plugin updates when its `version` changes. A new commit without a version bump doesn't reach users.

## Adding a plugin

Every plugin is one entry in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json).

### An external plugin

A plugin that lives in its own repository is listed by reference, and its files are not copied here:

```json
{
  "name": "<plugin-name>",
  "source": {
    "source": "github",
    "repo": "<owner>/<repo>",
    "ref": "<tag>",
    "sha": "<full 40-character commit SHA>"
  },
  "description": "<one line: what it brings to an SDD project>",
  "category": "<category>",
  "tags": ["<tag>"]
}
```

- **Pin it.** Set `ref` to a release tag and `sha` to its commit, so the marketplace serves the version that was reviewed and not whatever the plugin's main branch holds later. To offer a newer version, review it and update the pin.
- **For a plugin inside a larger repository**, use `{ "source": "git-subdir", "url": "<owner>/<repo>", "path": "<dir>" }`. For a repository outside GitHub, use `{ "source": "url", "url": "https://…/repo.git" }`.
- **Check its licence** allows the use you intend.

### A custom plugin maintained here

A plugin with no repository of its own lives in `plugins/<plugin-name>/`, with its own `.claude-plugin/plugin.json`, and is listed with a relative path:

```json
{ "name": "<plugin-name>", "source": "./plugins/<plugin-name>", "description": "<one line>" }
```

### Rules for every entry

- **The entry's `name` matches the `name` in the plugin's `plugin.json`.** The entry name is what people install (`<name>@sdd-marketplace`); the manifest name prefixes the plugin's skills.
- **Set `version` in one place only**, preferably the plugin's `plugin.json`, never in both.
- **Never rename an entry.** Changing a `name` breaks existing installs. To change what users see, set `displayName`. If a rename or removal is unavoidable, record it in a top-level `renames` map.
- **Validate before every commit,** and install from your local checkout once:

  ```bash
  claude plugin validate .
  claude plugin marketplace add ./      # from this repository's root
  claude plugin install <plugin-name>@sdd-marketplace
  ```

## Using listed plugins in an SDD project

Installing a plugin makes its skills available, and sdd-kit doesn't load them on its own. A project chooses which skills it relies on in its skills catalogue, `.sdd/skills.yaml`, which sdd-kit's onboarding helps establish and which the human approves. Record the install command in the entry's `source`, so anyone joining the project can set it up:

```yaml
- skill: <plugin-name>:<skill-name>
  covers: [<stack concern, constitution ID, or pattern>]
  use-when: [design, build]
  why: "<what it brings to this project>"
  source: "claude plugin install <plugin-name>@sdd-marketplace"
  status: approved
```

## License

Copyright 2026 Daniel Torres Candil.

The marketplace catalogue and documentation in this repository are licensed under the [Apache License, Version 2.0](LICENSE). Each listed plugin is covered by its own licence, which this repository doesn't change: sdd-kit is Apache-2.0, and external plugins keep whatever licence their authors chose.
