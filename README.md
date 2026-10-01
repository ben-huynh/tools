# Secrets: Command Reference

Run these in Terminal (the Mac Terminal app or VS Code's terminal). Secrets are stored encrypted in the macOS Keychain and loaded as environment variables by `~/.zshrc`.

## Commands

| Command | What it does |
|---|---|
| `addsecret NAME` | Create a new secret, or update it if `NAME` already exists. Prompts for the value twice (nothing shows while you type or paste). |
| `listsecrets` | List all secrets, showing only the first 4 characters of each. |
| `showsecret NAME` | Print one secret's full value. |
| `removesecret NAME` | Delete the secret from Keychain and remove its line from `~/.zshrc`. |

**Naming rules:** letters, numbers, and underscores only; can't start with a number. Names are case-sensitive.
Good: `PARTNER_PASSWORD`, `API_TOKEN_2` · Bad: `partner-password`, `2_TOKEN`

### Examples

```bash
addsecret PARTNER_PASSWORD      # create
addsecret PARTNER_PASSWORD      # update (same command)
listsecrets                     # PARTNER_PASSWORD: abcd...
showsecret PARTNER_PASSWORD     # prints full value; mind your screen
removesecret PARTNER_PASSWORD   # delete
```

## Referencing secrets

### In Thunder Client

Use the name in double curly braces, with **no** `process.env.` prefix:

```
{{PARTNER_PASSWORD}}
```

Works in the URL, query params, headers, body, and auth fields.

| Where | Example |
|---|---|
| Auth tab → Basic | Username: `{{PARTNER_USERNAME}}` · Password: `{{PARTNER_PASSWORD}}` |
| Auth tab → Bearer | Token: `{{PARTNER_TOKEN}}` |
| Header | `Authorization: Bearer {{PARTNER_TOKEN}}` |
| Header | `x-api-key: {{API_KEY}}` |
| URL | `https://api.example.com/v1/items?key={{API_KEY}}` |

If a Thunder Client environment (Global, Local, `.env`, or active) has a variable with the same name, that value is used instead of your secret.

### In Terminal and scripts

Secrets are normal environment variables:

```bash
echo "${PARTNER_TOKEN:0:4}..."    # check it's loaded without showing it all
curl -u "myuser:$PARTNER_PASSWORD" https://api.example.com/v1/items
curl -H "Authorization: Bearer $PARTNER_TOKEN" https://api.example.com/v1/items
```

In code: Node `process.env.PARTNER_TOKEN`, Python `os.environ["PARTNER_TOKEN"]`.

## After adding, updating, or removing a secret

| Where you'll use it | What to do |
|---|---|
| Thunder Client / VS Code | Quit VS Code with **Cmd+Q**, then in Terminal: `cd ~/your/project && code .` |
| Other open Terminal windows | Run `source ~/.zshrc`, or open a new window |
| The Terminal where you ran `addsecret` | Nothing; it's already loaded |

## Quick fixes

| Problem | Fix |
|---|---|
| A secret is missing from `listsecrets` | `addsecret NAME` (re-adds the `~/.zshrc` line) |
| `(missing from Keychain)` in `listsecrets` | `addsecret NAME` and paste the value |
| Thunder Client sends the literal `{{NAME}}` or gets 401 | Check the name and case, check for a same-named Thunder Client env variable, then Cmd+Q and relaunch with `code .` |
| `command not found: addsecret` | Run `source ~/.zshrc` or open a new Terminal window |
| `command not found: code` | In VS Code: Cmd+Shift+P → **Shell Command: Install 'code' command in PATH** |
