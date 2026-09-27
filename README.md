# SmartFlow Content Engine

An AI content system that researches, writes, designs, and publishes LinkedIn posts on a schedule, with a human approval loop over Telegram. Built and maintained by SmartFlow Lab Ltd, and forked to run the Evervia Innovations company page.

It has gone through three versions, each solving a limitation of the last.

## Version history

| Version | What it added | Why |
|---|---|---|
| v1: Sheets generator | Topics read from Google Sheets, posts written by OpenAI, results written back | Prove the idea: AI drafting from a structured topic bank |
| v2: Telegram approval | Commands sent from Telegram (`viral:`, `authority:`, `dm:`), live web context via Tavily, approve / redo / skip before posting | v1 had no human check and no fresh context |
| v3: Content engine | Scheduled daily runs, no-repeat topic selection, Claude captions, structured infographic briefs, GPT-Image-1 visuals, redo by post ID, LinkedIn API publishing | Remove manual prompting entirely while keeping a human in the loop |

v1 lives in [ai-linkedin-content-automation](https://github.com/onuigbonnamdi/ai-linkedin-content-automation) (archived). The v2 demo video and screenshots are in this repo. The v3 workflow export is `smartflow-linkedin-content-engine.json`.

## How v3 works

```
Daily trigger (09:00)
   |
   v
Google Sheets: industries, automation types, post tracker
   |
   v
No-repeat picker  -->  chooses an unused niche x automation combination
   |
   v
Claude: writes the LinkedIn caption
   |
   v
Claude: turns the caption into a structured infographic brief
        (headline, 3 problems, 3 steps, result, call to action)
   |
   v
GPT-Image-1: renders a 1024x1536 infographic
   |
   v
Telegram: sends post + image for review   -->  LinkedIn API: register upload,
   |                                            upload image, create post
   v
Post tracker: logs the combination so it is never reused

Redo path:  reply "redo <post ID>" in Telegram
   -> finds the original combination -> regenerates caption and image
   -> marks the old post as redone -> confirms in Telegram
```

## Design decisions

**Combinations, not topics.** Posts are built from a niche and an automation type (for example, appointment booking for dental clinics). Tracking used pairs gives far more non-repeating posts than a flat topic list.

**Two model calls, two jobs.** One Claude call writes for people; a second converts that caption into a strict, fielded brief. Keeping the brief structured makes image generation predictable and stops the visual drifting from the caption.

**Human in the loop by default.** Every post reaches Telegram first. A one-line redo command regenerates a weak post without opening n8n.

**No-code where it is the fastest reliable path.** n8n handles scheduling, retries, and integrations. Selection, parsing, and binary handling are written as code nodes, where logic needs to be exact.

## Evervia edition

The same engine was forked to run the Evervia Innovations company page, with extra safeguards added after live testing:

- **Statistic verification:** a node checks every number in the infographic brief against the caption and strips any that are not there, added after the image model invented a figure
- **Live grounding:** captions draw on Evervia's live forecast API and real customer survey quotes instead of static website text
- **Double-pass topic reuse:** each topic can be reframed once for business and once for household audiences
- **Token expiry reminders:** Telegram alerts before the LinkedIn access token expires
- **Varied layouts:** two infographic designs chosen at random per post

The Evervia workflow is private; this repo documents the shared engine.

## Tech stack

- n8n (self-hosted on a Hetzner VPS)
- Anthropic Claude (captions and structured briefs)
- OpenAI GPT-Image-1 (infographics)
- Google Sheets (topic bank and post tracker)
- Telegram Bot API (review and redo)
- LinkedIn API (image upload and UGC posts)

## Setup

1. Import `smartflow-linkedin-content-engine.json` into n8n.
2. Create credentials for Anthropic, OpenAI, Google Sheets, and Telegram, and attach them to the matching nodes.
3. Replace the placeholders in the workflow:
   - `YOUR_GOOGLE_SHEET_ID`
   - `YOUR_TELEGRAM_CHAT_ID`
   - `YOUR_LINKEDIN_ACCESS_TOKEN` (better: move it into an n8n credential)
   - `YOUR_ORG_ID` (your LinkedIn organisation ID)
4. Create a Google Sheet with three tabs:
   - **Industries:** `Sector`, `Industry / Niche`
   - **Automation Types:** `Automation Type`, `What It Does`
   - **Post Tracker:** receives one row per post
5. Activate the workflow.

In this export the LinkedIn publishing branch is disconnected, so posts go to Telegram for manual publishing. Connect it after the image extraction step once your LinkedIn API access is approved.

## Author

**Nnamdi Onuigbo**, Founder and AI Systems Engineer, SmartFlow Lab Ltd and Evervia Innovations Ltd
