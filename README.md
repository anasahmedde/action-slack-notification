# Slack Notify – GitHub Action

**Project Status:** Active – The project has reached a stable, usable state and is being actively developed.

A GitHub Action to send a message to a Slack channel.

![Slack Notify Action Screenshot](path/to/screenshot.png)

> **Note:** The Site and SSH Host details are only available if this action is run after the Deploy WordPress GitHub Action.

---

## Usage

You can use this action after any other action. Here is an example setup:

1. Create a file named `.github/workflows/slack-notify.yml` in your GitHub repository.

2. Add the following code to `slack-notify.yml`:

   ```yaml
   on: push
   name: Slack Notification Demo

   jobs:
     slackNotification:
       name: Slack Notification
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v2
         - name: Slack Notification
           uses: action-slack-notify@v2
           env:
             SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
   ```

3. Create a `SLACK_WEBHOOK` secret in your repository’s **Settings → Secrets**.
   You can generate a Slack incoming webhook token from the [Slack Incoming Webhooks page](https://api.slack.com/messaging/webhooks).

---

## Environment Variables

By default, this action requires minimal configuration. To customize the Slack notification, set any of the following environment variables:

| Variable           | Default                                               | Purpose                                                                                                                                                                                          |
| ------------------ | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `SLACK_CHANNEL`    | Set during Slack webhook creation                     | Specify the Slack channel where the message should be sent.                                                                                                                                      |
| `SLACK_USERNAME`   | `bot`                                                 | Custom Slack username that sends the message (does not need to be a “real” username).                                                                                                            |
| `SLACK_MSG_AUTHOR` | `$GITHUB_ACTOR` (the person who triggered the action) | GitHub username of the person who triggered the action. Override to specify a different GitHub username.                                                                                         |
| `SLACK_ICON`       | Default Slack icon                                    | URL of the user/bot icon displayed alongside the Slack message.                                                                                                                                  |
| `SLACK_ICON_EMOJI` | —                                                     | If you prefer an emoji instead of a URL, set a valid Slack emoji (e.g., `:bell:`).                                                                                                               |
| `SLACK_COLOR`      | `good` (green)                                        | Color of the vertical stripe in the Slack message. You can pass `${{ job.status }}` for automatic coloring or a hex value (e.g., `#efefef`).                                                     |
| `SLACK_LINK_NAMES` | —                                                     | If set to `true`, enable mentioning users in the Slack message.                                                                                                                                  |
| `SLACK_MESSAGE`    | Generated from the Git commit message                 | The main Slack message in the attachment. It is recommended not to override this unless you have a specific reason.                                                                              |
| `SLACK_TITLE`      | `Message`                                             | Title displayed before the main Slack message.                                                                                                                                                   |
| `SLACK_FOOTER`     | `GitHub Action Slack Notify`                          | Footer text displayed in the Slack message.                                                                                                                                                      |
| `MSG_MINIMAL`      | —                                                     | If set to `true`, removes Ref, Event, Actions URL, and Commit from the message. You can whitelist any of these four values by passing them comma-separated (e.g., `event` or `ref,actions url`). |

---

### Example Action Block

```yaml
- name: Slack Notification
  uses: action-slack-notify@v2
  env:
    SLACK_CHANNEL: general
    SLACK_COLOR: ${{ job.status }}        # or a specific color like 'good' or '#ff00ff'
    SLACK_ICON: https://github.com/someuser.png?size=48
    SLACK_MESSAGE: 'Post Content :rocket:'
    SLACK_TITLE: Post Title
    SLACK_USERNAME: bot
    SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
```

Below screenshot helps you visualize which parts of the message are controlled by each variable:

![Slack Notification Preview](path/to/preview.png)

> **Note:** The Site and SSH Host details are only available if this action is run after the Deploy WordPress GitHub Action.

---

## Hashicorp Vault (Optional)

This action supports retrieving the Slack webhook from Hashicorp Vault. To enable Vault support, define the following GitHub secrets in your repository:

| Secret        | Purpose              | Example Value              |
| ------------- | -------------------- | -------------------------- |
| `VAULT_ADDR`  | Vault server address | `https://example.com:8200` |
| `VAULT_TOKEN` | Vault token          | `s.VaultTokenExample12345` |

Update your `slack-notify.yml` to use Vault:

```yaml
on: push
name: Slack Notification Demo

jobs:
  slackNotification:
    name: Slack Notification
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Slack Notification
        uses: action-slack-notify@v2
        env:
          VAULT_ADDR: ${{ secrets.VAULT_ADDR }}
          VAULT_TOKEN: ${{ secrets.VAULT_TOKEN }}
```

Inside Vault, store the Slack webhook under the path `secret/slack` with a field named `webhook`. When this action runs, it will use `VAULT_TOKEN` to fetch the `webhook` value from `VAULT_ADDR`.

---

*Generated with ❤️ by Anas Ahmed*
