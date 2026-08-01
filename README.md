# Spotty Add-ons

Trigger keywords for [Spotty](https://github.com/spotty/spotty), the Raycast-style
launcher for GNOME. Install any add-on from the Add-ons window in Spotty
(Settings → Add-ons → Browse Add-ons), or import a manifest file manually.

## Repository layout

```
index.json            ← browse listing (shown in the marketplace)
addons/<id>.json      ← one manifest per add-on
```

Spotty fetches `index.json` for the list and `addons/<id>.json` on install.
The default repository URL is:

```
https://raw.githubusercontent.com/<your-user>/spotty-addons/main
```

You can also point the marketplace at a local copy for testing:

```
file:///home/you/spotty-addons
```

## Authoring an add-on

A manifest is a JSON file with three action types:

### `web` — open a URL with the query

```json
{
  "id": "dictionary",
  "name": "Dictionary",
  "word": "dict",
  "description": "Look up a word definition",
  "icon": "accessories-dictionary-symbolic",
  "shortcut": "",
  "version": "1.0.0",
  "author": "you",
  "action": {
    "type": "web",
    "url": "https://www.dictionary.com/browse/{query}"
  }
}
```

Type `dict word` in Spotty → the URL opens with `word` substituted
(URL-encoded). `icon` is a symbolic icon name from the system theme; leave it
empty for a default.

### `files` — search files by extension

```json
{
  "id": "books",
  "name": "Books",
  "word": "book",
  "description": "Find your e-books",
  "icon": "x-office-document-symbolic",
  "shortcut": "",
  "version": "1.0.0",
  "author": "you",
  "action": {
    "type": "files",
    "extensions": ["epub", "pdf", "mobi"]
  }
}
```

An empty `extensions` list means "all files" (like the built-in `find`).

### `shell` — run a command with the query

```json
{
  "id": "download",
  "name": "Download with yt-dlp",
  "word": "dl",
  "description": "Download a video",
  "icon": "folder-download-symbolic",
  "shortcut": "",
  "version": "1.0.0",
  "author": "you",
  "action": {
    "type": "shell",
    "command": "yt-dlp -o '~/Videos/%(title)s.%(ext)s' {query}"
  }
}
```

**Security:** `{query}` is single-quote-escaped by Spotty — user input can
never inject extra shell commands, only the command template you write runs.
Do NOT wrap `{query}` in your own quotes. Because shell add-ons run commands
as the user, Spotty shows the exact command and asks for confirmation at
install time. Users should only install shell add-ons they trust.

### `shortcut`

Optional GNOME-style global shortcut (e.g. `"Super+Ctrl+D"`) that opens
Spotty directly in the add-on's mode. Registered on install, removed on
uninstall.

### Rules

- `id` must be unique, lowercase, and use only letters, digits, `_`, `-`, `.`
- `word` is the trigger text — it must not collide with a built-in trigger
  (`find`, `clip`, `app`, `cmd`, `emoji`, `music`) or another add-on
- keep `name` short; `summary` (index only) shows under the name in the
  marketplace

## Submitting an add-on

1. Write the manifest under `addons/`, add it to `index.json`
2. Open a pull request
