# Email Finder MCP — Free, No Signup, No BS

> Find anyone's work email from their name + company domain.
> Validate any email address. Works in Claude, Cursor, and any MCP client.
> **100% free. No API key. No Hunter.io. No credit card. Ever.**

---

## Why This Exists

Every "free" email finder on GitHub is one of these:
- A thin wrapper around Hunter.io — free for 25 requests, then pay
- A syntax checker that tells you `john@company.com` is "valid" without actually checking
- Abandoned OSINT scrapers that stopped working in 2022

This is different. We built our own email discovery engine — no third-party API, no per-request cost on our end, which means **we can offer it completely free**.

---

## What It Does

| Tool | Description |
|------|-------------|
| `discover_email_by_name` | Find someone's work email from first name, last name, and company domain |
| `validate_email` | Check if a specific email address actually exists |
| `bulk_validate_emails` | Validate up to 100 emails at once |
| `discover_email_by_linkedin` | Find an email from a LinkedIn profile URL |
| `check_api_status` | Check if the service is online |

---

## Connect via MCP (Claude Desktop, Cursor, etc.)

Add this to your MCP config file:

**Claude Desktop** (`~/Library/Application Support/Claude/claude_desktop_config.json` on Mac, `%APPDATA%\Claude\claude_desktop_config.json` on Windows):

```json
{
  "mcpServers": {
    "email-finder": {
      "type": "http",
      "url": "https://www.startuphub.ai/api/mcp"
    }
  }
}
```

**Cursor** (`.cursor/mcp.json` in your project, or global `~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "email-finder": {
      "type": "http",
      "url": "https://www.startuphub.ai/api/mcp"
    }
  }
}
```

That's it. Restart your client and the tools are available.

---

## REST API

Base URL: `https://startuphub-validator-service.onrender.com`

> **Note:** First request after inactivity may take ~10s (Render.com cold start). Subsequent requests are fast.

### Validate a single email

```bash
curl -X POST https://startuphub-validator-service.onrender.com/api/validate-email \
  -H "Content-Type: application/json" \
  -d '{"email": "john@company.com"}'
```

**Response:**
```json
{
  "success": true,
  "email": "john@company.com",
  "isValid": true,
  "reason": "Valid"
}
```

---

### Discover email from name + domain

```bash
curl -X POST https://startuphub-validator-service.onrender.com/api/discover-email \
  -H "Content-Type: application/json" \
  -d '{"firstName": "John", "lastName": "Smith", "domain": "company.com"}'
```

**Response:**
```json
{
  "success": true,
  "email": "john@company.com",
  "all_valid": ["john@company.com"],
  "checked": [
    {"email": "john@company.com", "isValid": true},
    {"email": "j.smith@company.com", "isValid": false}
  ]
}
```

---

### Bulk validate (up to 100 emails)

```bash
curl -X POST https://startuphub-validator-service.onrender.com/api/validate-email \
  -H "Content-Type: application/json" \
  -d '{"emails": ["john@company.com", "jane@company.com"]}'
```

---

### Generate email combinations

```bash
curl -X POST https://startuphub-validator-service.onrender.com/api/generate-combinations \
  -H "Content-Type: application/json" \
  -d '{"firstName": "John", "lastName": "Smith", "companyWebsite": "company.com"}'
```

**Response:**
```json
{
  "success": true,
  "combinations": [
    "john@company.com",
    "john.smith@company.com",
    "jsmith@company.com",
    "j.smith@company.com",
    "..."
  ]
}
```

---

## Rate Limits

| Tier | Limit | How to get it |
|------|-------|---------------|
| Free (default) | Generous — we don't publish exact limits so they can't be gamed | Just use it |
| Higher limits | Available on request | Email [daniel@startuphub.ai](mailto:daniel@startuphub.ai) |

We don't throttle legitimate use. If you're building something cool, just reach out.

---

## Examples

### In Claude (after connecting MCP)

```
"Find the email for Sarah Chen at Sequoia Capital"
→ discover_email_by_name("Sarah", "Chen", "sequoiacap.com")
→ schen@sequoiacap.com ✓

"Is this email real? ceo@fakestartup.io"
→ validate_email("ceo@fakestartup.io")
→ Invalid ✗
```

### In Python

```python
import requests

# Find email
r = requests.post(
    "https://startuphub-validator-service.onrender.com/api/discover-email",
    json={"firstName": "John", "lastName": "Smith", "domain": "company.com"}
)
print(r.json()["email"])  # john@company.com

# Validate email
r = requests.post(
    "https://startuphub-validator-service.onrender.com/api/validate-email",
    json={"email": "john@company.com"}
)
print(r.json()["isValid"])  # True
```

### In JavaScript

```javascript
// Discover email
const res = await fetch('https://startuphub-validator-service.onrender.com/api/discover-email', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ firstName: 'John', lastName: 'Smith', domain: 'company.com' })
});
const { email } = await res.json();
console.log(email); // john@company.com
```

---

## How It Works (Without Giving Away the Secret Sauce)

We don't use Hunter.io, Apollo, Clearbit, or any paid data provider. We built our own validation method that actually checks if an email address exists — not just if the domain has MX records.

This means:
- No per-lookup cost on our end → free for you
- Results are based on real-time checks, not stale databases
- Works on any domain, not just big companies

---

## Built by StartupHub.ai

[StartupHub.ai](https://www.startuphub.ai) is a startup intelligence platform. We built this tool for our own research workflows and decided to open it up.

- Website: [startuphub.ai](https://www.startuphub.ai)
- Email: [daniel@startuphub.ai](mailto:daniel@startuphub.ai)
- Issues / feature requests: open a GitHub issue

---

## Topics

`email-finder` `email-validator` `mcp` `mcp-server` `free-api` `hunter-alternative` `email-discovery` `no-signup` `rest-api` `claude` `cursor`
