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

Add as the last step of a job. `if: failure()` runs it only when an earlier
step in the job failed.

```yaml
- name: Notify on failure
  if: failure()
  uses: scottleechua/simple-notify-telegram-action@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    message: |
      ${{ github.workflow }} failed in ${{ github.repository }} (${{ github.ref_name }}).
      ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
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
