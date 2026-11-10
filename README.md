# simple-notify-telegram-action

Simple GitHub Action that sends a message through a Telegram bot.

## Usage

### Simple

```yaml
- uses: scottleechua/simple-notify-telegram-action@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    message: Deployed ${{ github.repository }} at ${{ github.sha }}.
```

### On workflow failure

Add as the last steps of a job. `if: failure()` runs them only when an earlier
step in the job failed. The first step takes the first line of the commit
message (or the PR title on `pull_request` events) and cuts it at 60
characters. Events with neither, like `schedule` or `workflow_dispatch`, get
the short commit SHA instead.

```yaml
- name: Get commit title
  if: failure()
  id: commit
  env:
    COMMIT_MSG: ${{ github.event.head_commit.message || github.event.pull_request.title }}
  run: |
    title="${COMMIT_MSG%%$'\n'*}"
    if [ -z "$title" ]; then title="${GITHUB_SHA:0:7}"; fi
    if [ ${#title} -gt 60 ]; then title="${title:0:60}…"; fi
    echo "title=${title}" >> "$GITHUB_OUTPUT"

- name: Notify on failure
  if: failure()
  uses: scottleechua/simple-notify-telegram-action@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    message: |
      ❌ ${{ github.workflow }} failed: ${{ github.repository }} (${{ github.ref_name }})

      Commit title: ${{ steps.commit.outputs.title }}

      ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
```

This sends:

```
❌ CI failed: owner/repo (main)

Commit title: Fix race condition in the deploy script when two jobs st…

https://github.com/owner/repo/actions/runs/123456789
```

### Inputs

| Input | Required | Default | Notes |
| --- | --- | --- | --- |
| `bot-token` | yes | | From @BotFather. Always pass from a secret. |
| `chat-id` | yes | | See setup below. |
| `message` | yes | | Sent as plain text. |
| `disable-link-preview` | no | `true` | |

## Setup

1. Message @BotFather, send `/newbot`, copy the token.
2. Send your bot any message, then open
   `https://api.telegram.org/bot<TOKEN>/getUpdates` and copy `chat.id`.
3. Store both as secrets (`TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`) in the
   calling repo or organization.
