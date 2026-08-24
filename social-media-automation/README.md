# Facebook AI Comment-to-DM Automation 🤖

An AI-powered Facebook Page automation workflow built with **n8n, OpenAI, Google Sheets, and the Facebook Graph API**.

The workflow monitors Facebook Page comments, determines whether a commenter is genuinely requesting a campaign resource, and—when valid—automatically sends the resource by private message and posts a short public reply.

## 🎯 Problem

Manually monitoring campaign comments and replying to resource requests is repetitive and difficult to scale. The goal of this workflow is to automate that process while keeping AI responsible only for the language-understanding decisions that require contextual judgment.

## 💡 How It Works

```text
Facebook Comment
       ↓
    Webhook
       ↓
Verify Facebook Webhook
       ↓
Check Feed Event
       ↓
    AI Agent
       ↓
Google Sheets → Campaign Lookup
       ↓
Structured AI Output
       ↓
 Is comment valid?
    ↙       ↘
  No         Yes
  ↓           ↓
No reply   Facebook Graph API
              ↓
        Private DM + Public Reply
```

## 🧠 Role of AI

The AI's primary responsibility is to understand the user's intent from a Facebook comment. It checks whether the comment genuinely requests the campaign resource and generates appropriate Bengali responses for both the private DM and public reply. Webhook handling, Google Sheets lookup, conditional routing, and Facebook API calls remain deterministic automation tasks. fileciteturn12file0L4-L11

## ✨ Key Features

- Facebook webhook event handling
- Webhook verification using `verify_token` and `hub.challenge`
- Feed-event filtering
- AI-based comment intent classification
- Campaign/resource lookup from Google Sheets
- Bengali AI-generated DM and public reply
- Structured JSON output validation
- Automatic private messaging through Facebook Graph API
- Automatic public comment reply
- Conditional routing for valid/invalid comments
- Spam, irrelevant, abusive, and unsupported comments can be rejected

## 🛠️ Tech Stack

- **n8n** — workflow orchestration
- **OpenAI / GPT-5-mini** — intent understanding and Bengali response generation
- **Google Sheets** — campaign/resource data source
- **Facebook Graph API** — private messages and public comment replies
- **Webhooks** — Facebook event ingestion
- **Structured Output Parser** — reliable AI response schema

## 🔐 Webhook Verification

The workflow separates Facebook webhook verification into two checks:

- `verify_token` confirms that the verification request matches the configured secret.
- `hub.challenge` is returned exactly as received to prove that the webhook endpoint is reachable and responding correctly. fileciteturn12file0L16-L23

## 📋 AI Output Schema

The AI Agent uses a Structured Output Parser and always returns:

```json
{
  "isValid": true,
  "dmMessage": "...",
  "publicReply": "..."
}
```

The parser prevents downstream nodes from breaking because of unpredictable AI output formats such as invalid JSON, missing fields, or invented field names. fileciteturn12file0L28-L36

## 🤖 AI Decision Rules

The AI Agent checks the campaign information from Google Sheets using the Facebook Post ID before deciding whether the comment is valid. A comment is considered valid when it is relevant, the user is clearly requesting the resource, and a matching campaign exists. Irrelevant, spam, abusive, promotional, or unmatched comments should produce empty responses. fileciteturn12file0L171-L192

## 🔄 n8n Workflow Components

1. **Webhook** — receives Facebook events.
2. **Webhook verification IF** — validates `hub.mode` and `hub.verify_token`.
3. **Respond to Webhook** — returns `hub.challenge`.
4. **Feed filter** — accepts Facebook `feed` events.
5. **AI Agent** — interprets the comment and campaign context.
6. **OpenAI Chat Model** — provides the language model.
7. **Google Sheets Tool** — retrieves campaign information by Post ID.
8. **Structured Output Parser** — enforces the expected JSON schema.
9. **Validity IF** — routes valid vs. invalid requests.
10. **Facebook Graph API request** — sends the private DM.
11. **Facebook Graph API request** — posts the public comment reply.
12. **Respond to Webhook** — acknowledges the event.

The supplied workflow JSON confirms these nodes and their connections. fileciteturn12file0L402-L455 fileciteturn12file0L496-L531

## 📌 Example Behavior

### Valid comment

A user asks for the campaign material and the Post ID matches a campaign in Google Sheets.

**AI result:**
- `isValid = true`
- Send the resource link in the private DM.
- Send a short Bengali public reply such as checking the inbox.

### Invalid comment

A user leaves a generic compliment, irrelevant message, spam, abusive content, or a comment for a Post ID with no matching campaign.

**AI result:**
- `isValid = false`
- No DM is sent.
- No public reply is sent.

## 🔒 Security Note

Do not commit real Facebook access tokens, OpenAI API keys, Google credentials, webhook secrets, or other sensitive credentials to this repository. Use n8n credentials/environment variables and sanitized example values instead.

## 📈 Future Improvements

- Add automated logging and monitoring
- Add retry/error-handling paths for Facebook API failures
- Support multiple campaign types and languages
- Add analytics for valid vs. invalid comments
- Add automated tests for webhook payloads and AI output validation
- Add production-grade secret management

## 👨‍💻 Author

**Angkon Biswas**  
AI Automation Engineer | AI Agents | Workflow Automation

[GitHub](https://github.com/angkonbiswas)
