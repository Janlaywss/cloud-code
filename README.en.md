# Cloud-code Restored Source

English | [简体中文](./README.md)

## Source Study Docs
Try it here: https://cloud-code-study.vercel.app/
- https://github.com/Janlaywss/cloud-code-study

## Usage Guide
- This repository already contains the restored source code under `claude-code-source/`.
- For local build and run instructions, see `claude-code-source/README.md`.
  - The original setup was tested on a MacBook Pro with Apple silicon, so the Bun runtime there is configured for Apple chips.
  - If you are using a different CPU architecture, adjust the installation yourself or let AI help you with it.
- The project root also contains an `ai-cloud-xxxx.tgz` archive, which is the original npm package for version `2.1.88`.

## Restoration Method Used in This Repository
```bash
# 1. Download the package from npm
npm pack @anthropic-ai/claude-code --registry https://registry.npmjs.org

# 2. Extract it
tar xzf anthropic-ai-claude-code-2.1.88.tgz

# 3. Parse cli.js.map and write sourcesContent back to the original paths
node -e "
const fs = require('fs');
const path = require('path');
const map = JSON.parse(fs.readFileSync('package/cli.js.map', 'utf8'));
const outDir = './claude-code-source';
for (let i = 0; i < map.sources.length; i++) {
  const content = map.sourcesContent[i];
  if (!content) continue;
  let relPath = map.sources[i];
  while (relPath.startsWith('../')) relPath = relPath.slice(3);
  const outPath = path.join(outDir, relPath);
  fs.mkdirSync(path.dirname(outPath), { recursive: true });
  fs.writeFileSync(outPath, content);
}
"
```

The source map contains **4,756** source files with complete `sourcesContent`, which makes it possible to restore every original TypeScript/TSX file losslessly.

## Directory Structure

```text
.
├── src/                  # Core source code (1,902 files)
│   ├── main.tsx          # Application entry
│   ├── Tool.ts           # Tool base class
│   ├── Task.ts           # Task management
│   ├── QueryEngine.ts    # Query engine
│   ├── commands.ts       # Command registration
│   ├── tools.ts          # Tool registration
│   ├── assistant/        # Conversation history management
│   ├── bootstrap/        # Startup initialization
│   ├── bridge/           # Bridge layer (31 files)
│   ├── buddy/            # Sub-agent system (6)
│   ├── cli/              # CLI argument parsing and entrypoints (19)
│   ├── commands/         # Slash command implementations (207)
│   ├── components/       # Terminal UI components built with Ink (389)
│   ├── constants/        # Shared constants (21)
│   ├── context/          # Context management (9)
│   ├── coordinator/      # Agent coordinator (1)
│   ├── entrypoints/      # Various entrypoints (8)
│   ├── hooks/            # Lifecycle hooks (104)
│   ├── ink/              # Custom Ink terminal rendering engine (96)
│   ├── keybindings/      # Shortcut key management (14)
│   ├── memdir/           # Memory directory system (8)
│   ├── migrations/       # Data migrations (11)
│   ├── moreright/        # Permission system (1)
│   ├── native-ts/        # Native TS tooling (4)
│   ├── outputStyles/     # Output formatting (1)
│   ├── plugins/          # Plugin system (2)
│   ├── query/            # Query handling (4)
│   ├── remote/           # Remote execution (4)
│   ├── schemas/          # Data schema definitions (1)
│   ├── screens/          # Screen views (3)
│   ├── server/           # Server mode (3)
│   ├── services/         # Core services (130)
│   ├── skills/           # Skill system (20)
│   ├── state/            # State management (6)
│   ├── tasks/            # Task execution (12)
│   ├── tools/            # Tool implementations (184)
│   ├── types/            # TypeScript type definitions (11)
│   ├── upstreamproxy/    # Upstream proxy support (2)
│   ├── utils/            # Utility functions (564)
│   ├── vim/              # Vim mode (5)
│   └── voice/            # Voice input (1)
├── vendor/               # Internal vendor code (4 files)
│   ├── modifiers-napi-src/   # Native key modifier module
│   ├── url-handler-src/      # URL handling
│   ├── audio-capture-src/    # Audio capture
│   └── image-processor-src/  # Image processing
└── node_modules/         # Bundled third-party dependencies (2,850 files)
```

## Core Module Overview

| Module | File Count | Description |
|------|--------|------|
| `utils/` | 564 | Utility functions for file I/O, Git operations, permission checks, diff handling, and more |
| `components/` | 389 | Terminal UI components built with Ink, the CLI equivalent of React |
| `commands/` | 207 | Slash command implementations such as `/commit` and `/review` |
| `tools/` | 184 | Agent tools including Read, Write, Edit, Bash, Glob, Grep, and more |
| `services/` | 130 | Core services such as API clients, auth, config, and session management |
| `hooks/` | 104 | Lifecycle hooks for interception and permission control around tool execution |
| `ink/` | 96 | A custom Ink rendering engine with layout, focus management, and render optimizations |
| `bridge/` | 31 | The bridge layer between IDE extensions and the CLI |
| `skills/` | 20 | Skill loading and execution system |
| `cli/` | 19 | CLI argument parsing and startup flow |
| `keybindings/` | 14 | Keyboard shortcut bindings and customization |
| `tasks/` | 12 | Background jobs and scheduled task management |

## Stats

| Metric | Value |
|------|------|
| Total source files | 4,756 |
| Core source (`src/ + vendor/`) | 1,906 files |
| Third-party dependencies (`node_modules/`) | 2,850 files |
| Source map size | 57 MB |
| Package version | 2.1.88 |
