# AI Agent Skill for WriterAgent

This directory provides the skill definitions and setup scripts needed to connect AI agents (like [Hermes Agent](https://github.com/NousResearch/hermes-agent) or other MCP-compatible systems) to the WriterAgent LibreOffice extension.

## 1. How to Install the Skill

Since this skill is hosted directly inside the main `writeragent` repository (to be easily mirrored into a standalone `writeragent-skill` repo), users have a few options to install it:

### Option A: Direct Download via Curl
Because this skill is located within a subdirectory of the WriterAgent repository, the `hermes skills install` command cannot fetch it via URL. Instead, the simplest way is to download the `SKILL.md` file using `curl` (or `wget`):

```bash
curl -o writeragent.md https://raw.githubusercontent.com/KeithCu/writeragent/master/contrib/writeragent-skill/SKILL.md
```
Then, move `writeragent.md` into your agent's `skills/` directory.

### Option B: Local Setup using the Helper Script
If you've already downloaded or cloned the repository:
1. Open your terminal in this directory (`contrib/writeragent-skill/`).
2. Run the helper script: `python3 setup.py`.
3. Follow the prompts to either create a dedicated Hermes `--profile office` or get the copy-paste configuration snippet.

### Option C: Manual Drop-in
You can simply copy `SKILL.md` directly into your agent's `skills/` directory and rename it to something recognizable like `writeragent.md`.

## 2. Profile Management: Default vs. Dedicated

When setting up Hermes, you can either add the WriterAgent MCP server to your **Default Profile** or create a **Dedicated Profile** (e.g., `--profile office`). Here is a breakdown of the benefits:

### Benefits of a Dedicated Profile (`--profile office`)
- **Prevents Tool Clutter:** Hermes ships with dozens of built-in tools. Sending 50+ LibreOffice tools to the LLM on every turn eats up context window and causes confusion. Isolation keeps the AI sharper.
- **Memory Isolation:** A dedicated profile builds specific procedural memories and context about your documents and writing style over time, without polluting your default coding or chat agent.
- **Safety:** Limits the "blast radius." Your autonomous web-research agent won't accidentally have open access to modify your active spreadsheets unless you explicitly use the office profile.

### Benefits of Using the Default Profile
- **"God Mode" Agent:** You can execute complex, cross-domain workflows in one prompt (e.g., *"SSH into my server, summarize the database logs, and chart them in LibreOffice"*).
- **Convenience:** No need to append `--profile office` to your terminal commands; it's always ready.

## 3. How to Test the Setup

Once you've configured Hermes:

1. **Prepare LibreOffice:**
   - Open LibreOffice.
   - Open the WriterAgent Settings (via the menu).
   - Ensure the **MCP Server** is explicitly checked.
   - **Crucial:** Enable the local HTTP server. *(Remember: HTTP must be enabled every time LibreOffice restarts for security reasons).*
   
2. **Launch Hermes:**
   - Run `hermes` in your terminal (or `hermes --profile office` if you used the helper).
   
3. **Execute a Test Prompt:**
   Ask Hermes a question that forces it to interact with the document:
   > "Please review the currently active Writer document and tell me how many paragraphs it has."
   
4. **Verify:**
   You should see Hermes trigger the `get_document_info` tool from the `writeragent` MCP server and return a successful response!

## 4. Other Places to Publish the Skill

If you want to make this skill even more discoverable for users who might not be browsing the `writeragent` codebase, consider these distribution venues:

- **Community Forums / Subreddits:** Since this is based on the open standard format, `SKILL.md` is simply Markdown. You can easily publish this file as a **GitHub Gist** and share the raw link in tutorials across AI tool communities (e.g., on Reddit `/r/LocalLLaMA` or the Nous Research discord). Users can download the gist directly.
- **A Dedicated Mirror Repository:** The dedicated mirror at https://github.com/KeithCu/libreoffice-skill is the recommended home for easy raw URL consumption and agent skill registries. The content is also developed in the main WriterAgent tree under `libreoffice-skill-repo/`.
