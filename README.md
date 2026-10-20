# Deploy and Host WAHA on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/waha-production?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=waha-production)

[WAHA](https://github.com/devlikeapro/waha) (WhatsApp HTTP API) connects a WhatsApp number to a REST API. Scan a QR code once, then send and receive messages, media, reactions and group events from n8n, Chatwoot, Make, or your own code through HTTP calls and webhooks. This template runs the official WAHA Core image on the GOWS engine.

## About Hosting WAHA

It's one service and a Railway volume.

- **GOWS engine, no browser.** WAHA's default engine (WEBJS) drives a headless Chrome. This template uses GOWS, WAHA's Go engine that speaks the WhatsApp protocol directly: a smaller image (770 MB instead of 1.1 GB), no Chromium processes, and about 250 MB of RAM in our tests.
- **Sessions survive redeploys.** The linked-device session and downloaded media are stored on the volume, so redeploys and image updates don't log your number out.
- **Locked from the first boot.** The API needs the generated `WAHA_API_KEY`, and the dashboard and Swagger docs need a generated password. Nothing is open by default.
- **Pinned version.** The template uses a specific WAHA release, not `latest`, so a redeploy never upgrades you by surprise.

## Common Use Cases

- WhatsApp triggers and replies in n8n workflows
- A WhatsApp inbox in Chatwoot for support teams
- Notifications, reminders and AI chatbots on WhatsApp from your own backend

## Dependencies for WAHA Hosting

- A phone with WhatsApp to link as a device (like WhatsApp Web)

### Deployment Dependencies

- [WAHA on GitHub](https://github.com/devlikeapro/waha)
- [WAHA documentation](https://waha.devlike.pro/docs/overview/introduction/)
- [WAHA engines (WEBJS, NOWEB, GOWS)](https://waha.devlike.pro/docs/how-to/engines/)
- [Template source on GitHub](https://github.com/two-workspaces/waha)

### Implementation Details

**Link your number.** Open `https://<your-app>.up.railway.app/dashboard`, sign in with `WAHA_DASHBOARD_USERNAME` and `WAHA_DASHBOARD_PASSWORD` from the Variables tab, and enter the server URL and `WAHA_API_KEY` when the dashboard asks for them. Start the `default` session and scan the QR code from WhatsApp > Linked devices on your phone.

**Use the API.** Send the key in the `X-Api-Key` header:

```bash
curl https://<your-app>.up.railway.app/api/sendText \
  -H "X-Api-Key: <WAHA_API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{"session": "default", "chatId": "12025550123@c.us", "text": "Hello from Railway"}'
```

The full API is documented at `https://<your-app>.up.railway.app/` (Swagger, same login as the dashboard).

**Receive messages.** Set a webhook URL on the session (in the dashboard or with `WHATSAPP_HOOK_URL` and `WHATSAPP_HOOK_EVENTS=message`). For n8n, install the `@devlikeapro/n8n-nodes-waha` community node. Apps in the same Railway project can call `http://${{WAHA.RAILWAY_PRIVATE_DOMAIN}}:3000` with the same key; that traffic isn't billed as egress.

**WAHA Core vs Plus.** This is the free Core edition, which runs one WhatsApp session (`default`). Multiple numbers on one server and some extras need [WAHA Plus](https://waha.devlike.pro/docs/how-to/plus-version/), a paid image from the WAHA project.

**Switching engines.** Some features land in WEBJS first. To switch, change the image tag to `devlikeapro/waha:latest-<version>` and `WHATSAPP_DEFAULT_ENGINE` to `WEBJS`, then relink your number (sessions aren't portable between engines). Expect several times the RAM.

**Resources and cost.** WAHA idles at about 250 MB of RAM on Railway, so it fits the Hobby plan easily.

**Updates.** Change the image tag to a newer `devlikeapro/waha:gows-<version>` and redeploy; the session on the volume is kept.

**Use it responsibly.** WAHA is an unofficial client, not the WhatsApp Business API. Bulk or unsolicited messages get numbers banned, so message only people who expect it.

## Why Deploy WAHA on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying WAHA on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
