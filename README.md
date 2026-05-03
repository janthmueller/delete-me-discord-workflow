# delete-me-discord-workflow

GitHub Actions workflow for running [`delete-me-discord`](https://github.com/janthmueller/delete-me-discord) v2 on a schedule.

> Warning: automated Discord tooling may violate Discord's [Terms of Service](https://discord.com/terms). Use at your own risk.

This repository is intentionally small. It installs `delete-me-discord>=2,<3`, restores the previous preserve cache when one exists, and runs:

```bash
dmd clean $DELETE_ME_ARGS ... --quiet --redact-sensitive
```

The workflow appends `--quiet --redact-sensitive` so public Action logs do not print normal cleanup output and sensitive values are masked in warnings or errors.

## Setup

1. Fork this repository or copy `.github/workflows/run.yml` into a private repository.
2. Enable GitHub Actions for the repository if GitHub asks you to.
3. Add repository secrets under **Settings -> Secrets and variables -> Actions**:

| Secret | Required | Purpose |
| --- | --- | --- |
| `DISCORD_TOKEN` | yes | Discord user token used by `dmd clean`. |
| `DELETE_ME_ARGS` | yes | Arguments passed after `dmd clean`. Do not include the `clean` command itself. |

If you need token extraction steps, see the [`delete-me-discord` authentication docs](https://janthmueller.github.io/delete-me-discord/getting-started/authentication/).

## Configure `DELETE_ME_ARGS`

Use v2 option names. A daily rolling-retention example:

```text
--include-ids 123456789012345678 --keep-within 2w --fetch-within 2w1d --preserve-cache
```

An example that also keeps the last 20 messages per channel:

```text
--include-ids 123456789012345678 --keep-within 2w --keep-last 20 --fetch-within 1d --preserve-cache
```

Useful v2 options:

| Option | Meaning |
| --- | --- |
| `--include-ids` / `--exclude-ids` | Scope the run to specific guild, category, channel, DM, or Group DM IDs. Unique ID suffixes are accepted. |
| `--keep-within 2w` | Keep messages and reactions newer than two weeks. |
| `--keep-last 20` | Keep the last 20 messages per channel. |
| `--keep-last-scope all` | Count `--keep-last` against all recent messages. This is the v2 default. |
| `--fetch-within 1d` | Only inspect recent history during recurring runs. Choose this carefully. |
| `--preserve-cache` | Persist kept message IDs between scheduled runs. Recommended when combining `--keep-last` with a limited fetch window. |
| `--keep-reactions` | Keep your reactions instead of deleting them. By default, v2 removes your reactions too. |
| `--dry-run` | Preview without deleting. Useful for manual `workflow_dispatch` tests. |

Do not set `--preserve-cache-path` in `DELETE_ME_ARGS`. The workflow sets it to `.cache/delete-me-discord.json` whenever `--preserve-cache` is present, and caches the `.cache` directory between runs.
The cache key changes on every run and uses a restore prefix, so updates from one scheduled run can be picked up by the next one.

The workflow uses shell word splitting for `DELETE_ME_ARGS`. Prefer compact values such as `2w`, `2w1d`, and `1d` instead of quoted strings.

## First Run

Before enabling a schedule, test the same scope locally with `--dry-run`:

```bash
dmd list channels -r 4
dmd clean --include-ids <channel_id_or_suffix> --keep-within 2w --dry-run
```

For rolling retention, the first real run should usually omit a narrow `--fetch-within` so the tool can inspect enough history. After that, add the recurring fetch window to `DELETE_ME_ARGS`.

See:

- [First Run](https://janthmueller.github.io/delete-me-discord/getting-started/first-run/)
- [Rolling Retention](https://janthmueller.github.io/delete-me-discord/guides/rolling-retention/)
- [Preserve Cache](https://janthmueller.github.io/delete-me-discord/guides/preserve-cache/)

## Schedule

The included workflow runs daily at 02:00 UTC:

```yaml
on:
  schedule:
    - cron: '0 2 * * *'
  workflow_dispatch:
```

Change the cron expression in `.github/workflows/run.yml` if you need a different cadence.

## Security Notes

- Prefer a private repository if possible.
- Keep `DISCORD_TOKEN` only in GitHub Actions secrets.
- The preserve cache is stored through GitHub Actions cache, not committed to the repository.
- The workflow forces `--quiet --redact-sensitive` for safer logs. To inspect detailed output, run locally or edit the workflow intentionally.
- Anyone who can modify workflows or read Actions secrets/cache in the repository should be treated as trusted.

## Resources

- [`delete-me-discord` repository](https://github.com/janthmueller/delete-me-discord)
- [`delete-me-discord` documentation](https://janthmueller.github.io/delete-me-discord/)
- [GitHub Actions security hardening](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
