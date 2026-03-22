# SmartFlow Content Engine

An n8n automation that turns Telegram commands into AI-generated LinkedIn posts.

## How it works

1. Send a command via Telegram:
   - viral: topic
   - authority: topic
   - dm: topic

2. The system:
   - pulls live context from the web
   - generates a structured LinkedIn post
   - sends a preview back to Telegram

3. You decide:
   - approve → generates image + posts to LinkedIn
   - redo → regenerates the text
   - skip → cancels

## Demo

See attached:
- Workflow screenshot
- Demo video

## Notes

- Built with n8n
- Uses OpenAI + Tavily
- Fully automated approval flow via Telegram
