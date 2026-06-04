---
name: libreoffice-mcp
description: Connect AI Agents (like Hermes) to WriterAgent in LibreOffice so it can read/edit/save real documents (Writer, Calc, Draw).
author: KeithCu  
version: 0.7.2
---

# Connect AI Agents (like Hermes) to WriterAgent

**Dedicated skill repo (for easy consumption):** https://github.com/KeithCu/libreoffice-skill

This SKILL.md is also maintained in the main WriterAgent tree under `libreoffice-skill-repo/`.

This skill connects AI Agents to LibreOffice via the WriterAgent MCP server, giving them the ability to natively read, edit, analyze, and save real documents (Writer documents, Calc spreadsheets, and Draw presentations).

## 1. Prerequisites & Setup

1. **Install LibreOffice** and the **WriterAgent** extension.
2. Open LibreOffice and ensure the **WriterAgent** sidebar is active.
3. Open **WriterAgent Settings** from the LibreOffice menu and check the box to **enable the MCP Server** (defaults to port 8765).
4. **Enable HTTP:** For security, local HTTP connections are not started automatically. **Every time you start LibreOffice**, you must manually enable the HTTP server from the WriterAgent menu so that Hermes can connect.

## 2. Hermes Profile (Recommended)

The cleanest way to use WriterAgent is to create a dedicated "Office" profile in Hermes. This bundles the WriterAgent MCP server without cluttering your default environment.

Run this command in your terminal:
```bash
hermes profile create office --with-mcp writeragent=http://localhost:8765
```

When you want to work on documents, just run:
```bash
hermes --profile office
```

## 3. Manual MCP Configuration

If you prefer modifying your configuration directly, add the following snippet to your Hermes `config.yaml` under `mcp_servers`:

```yaml
mcp_servers:
  writeragent:
    url: "http://localhost:8765"
```

## 4. Example Prompts & Workflows

Once connected, you can ask Hermes to perform powerful tasks directly within LibreOffice:

- **Calc Analysis:**
  > "Open my `Q1_budget.ods`, analyze the expenses by category, and create a new summary chart in a new sheet."

- **Writer Editing:**
  > "Take the current Writer document and rewrite the final three paragraphs into a professional executive summary."

- **Research & Data Entry:**
  > "Research the top 5 AI models for 2024 using your web search tools, then insert the results as a formatted table into my active Calc spreadsheet."

## 5. Best Practices & Troubleshooting

- **Approving Tool Calls (HITL):** It is highly recommended to leave Human-in-the-Loop (HITL) approval *on* for actions that modify your documents. This ensures Hermes asks for permission before writing changes or saving files.
- **Port Conflicts:** If `8765` is in use, change the port in the WriterAgent settings, then update your Hermes configuration to match.
- **Reloading the MCP:** If you restart LibreOffice or change WriterAgent settings, you may need to reload the MCP tools in Hermes. Use the `/reload-mcp` command.
- **Document Targeting (modern way):** The MCP tools accept a `document_url` parameter **directly in the tool call arguments**. This is the recommended approach for multi-file or cross-document work and does not require the `X-Document-URL` HTTP header.

  Example tool call payload (conceptual):
  ```json
  {
    "name": "apply_document_content",
    "arguments": {
      "document_url": "file:///absolute/path/to/report.odt",
      "content": "...",
      "target": "end"
    }
  }
  ```

  - Use the `list_open_documents` tool first (MCP-only) to discover open documents and their exact `document_url` values (especially useful with multiple files open or when focus might be ambiguous).
  - The older `X-Document-URL` header is still supported for simple "active document" cases and backward compatibility, but prefer the argument form.
  - For delegation tools (`delegate_to_specialized_*_toolset`), you can also pass `document_url` so the inner agent works against the intended document.
  - See the full details and schemas in the WriterAgent project's `docs/mcp-protocol.md`.

## 6. Bonus: Agent Control Protocol (ACP) Mode

Want Hermes to be the full, autonomous backend for the LibreOffice UI itself? You can use WriterAgent as the primary chat interface for Hermes!

1. Open **WriterAgent Settings** from the LibreOffice menu.
2. Change your Model Selection to **Agent backends -> Hermes**.
3. Enable the **Agent Control Protocol** checkbox.

This allows Hermes to stream its thinking process and actions directly into the LibreOffice sidebar chat panel for the ultimate integrated experience!
