# AI Code Assistant

A minimal, Cursor-style AI assistant built as a VS Code extension. It has six pieces:

- **Chat** — a sidebar panel that talks about your currently open file.
- **Inline completions** — AI-powered ghost text as you type (like Tab in Cursor).
- **Agent Edit** — give a plain-English instruction and the AI proposes edits across
  your currently *open* files, which you review and apply one by one.
- **Generate App** — describe any kind of app (3D viewer, image tool, video tool, web app...)
  and the AI picks an appropriate tech stack and scaffolds a minimal, runnable starter project.
- **Generate Game** — describe a game and pick 2D or 3D; generates an actually-playable
  starter built on hand-verified Phaser/Three.js engines, with the AI customizing only the
  theme (colors, title, story text), not the gameplay logic itself.
- **Live Preview** — a local hot-reload dev server. Serves any generated (or hand-written)
  folder with an `index.html`, auto-refreshing your browser whenever a file changes, with
  an on-screen FPS/console-log overlay for the generated games.

It calls the Anthropic API directly. There's no separate backend server — the
extension itself makes the API requests.

## Setup

1. **Install dependencies**
   ```bash
   npm install
   ```

2. **Compile**
   ```bash
   npm run compile
   ```
   (or `npm run watch` to recompile on save while you work on it)

3. **Run it**
   - Open this folder in VS Code.
   - Press `F5` (or Run → Start Debugging). This launches a second
     "Extension Development Host" window with the extension loaded.

4. **Add your API key**
   - In the Extension Development Host window, open Settings (`Cmd+,` / `Ctrl+,`).
   - Search for "AI Assistant".
   - Paste your Anthropic API key into **AI Assistant: Api Key**
     (get one at console.anthropic.com).
   - Optionally change the model (defaults to `claude-sonnet-4-6`).

## Using it

- **Chat**: click the sparkle icon in the activity bar (left sidebar), type a question,
  press Enter. The currently open file is automatically included as context.
- **Explain Selection**: select code in the editor → right-click → "AI Assistant: Explain
  Selection" → sends it straight to chat.
- **Inline completions**: just start typing in any file; ghost text suggestions appear
  after a short pause. Press `Tab` to accept (standard VS Code inline-suggestion behavior).
  Toggle on/off via the `aiAssistant.inlineCompletionsEnabled` setting.
- **Agent Edit**: open the file(s) you want changed, then run "AI Assistant: Agent Edit"
  from the Command Palette (`Cmd/Ctrl+Shift+P`) or press `Cmd/Ctrl+K Cmd/Ctrl+I`. Describe
  the change in plain English. The AI proposes new versions of the relevant open files;
  you're asked to Apply or Skip each one individually before anything is written.
- **Generate App**: run "AI Assistant: Generate App From Description" from the Command
  Palette. Describe what you want (any domain), pick a destination folder name, and the AI
  decides the best stack and writes a minimal runnable project into that folder.
- **Generate Game**: run "AI Assistant: Generate Game (2D/3D Starter)" from the Command
  Palette. Describe the game, choose 2D (Phaser) or 3D (Three.js), pick a folder name —
  you get a real, playable starter. Open `index.html` in a browser to play it.
- **Live Preview**: run "AI Assistant: Live Preview (Hot Reload)" from the Command Palette.
  It finds folders with an `index.html` (workspace root or one level of subfolders), serves
  the one you pick on a local port, and opens it in your browser. Any file you save in that
  folder auto-reloads the page — no manual refresh needed. Run "AI Assistant: Stop Live
  Preview" to shut the server down. Generated games also get an FPS counter and on-screen
  log feed — press the backtick key (`` ` ``) in the game to toggle it.

## How it's simplified vs. real Cursor

This is a teaching-scale version, not a production tool:

- No codebase-wide indexing/embeddings — the agent only sees files you currently have
  open, not your whole project.
- No diff streaming — completions and agent edits wait for a full response rather than
  streaming token-by-token.
- No undo-aware multi-file transaction — each file edit is applied (or skipped)
  independently via VS Code's standard edit/undo stack.
- Single model provider (Anthropic) instead of the multi-model routing real Cursor does.

These are reasonable next steps if you want to extend it further — codebase indexing
with embeddings would be the highest-impact one.

## Packaging (optional)

To produce a `.vsix` file you can install permanently (instead of just running via F5):

```bash
npm install -g @vscode/vsce
vsce package
```

This creates `ai-code-assistant-0.1.0.vsix`, installable via the Extensions view's
"Install from VSIX..." command.
