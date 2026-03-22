# SmartFlow Content Engine

An n8n workflow that turns Telegram commands into AI-generated LinkedIn content.

## What it does

- Accepts Telegram commands like:
  - `viral: topic`
  - `authority: topic`
  - `dm: topic`
- Pulls recent web context with Tavily
- Generates styled post drafts
- Sends preview back to Telegram
- Supports:
  - `redo`
  - `approve`
  - `skip`
- On approval:
  - generates a relevant image
  - merges image + text
  - posts to LinkedIn

## Files

- `smartflow-content-engine.json` — exported n8n workflow

## Notes

Before using:
- connect Telegram
- connect OpenAI
- connect LinkedIn
- connect Tavily API
- import into n8n