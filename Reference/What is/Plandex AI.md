#ai #ide #dev

https://github.com/plandex-ai/plandex

Plandex (aka Plandex AI) is an **open-source, terminal-based AI coding engine and agent** engineered to tackle **large-scale, real‑world software projects**. It orchestrates planning and execution of complex multi-step coding tasks—across many files—while offering robust control over context, code diffs, and autonomy levels.

---

### ⚙️ **Key Features**

#### 🧠 Smart Context Management

- Handles up to **2M tokens** in-context and indexes **20M+ token** projects using tree-sitter for project maps.
- Loads only the necessary code slices at each step, optimizing both speed and cost.

#### 🧩 Diff‑Review Sandbox

- All AI-generated changes are staged in a sandbox until you approve them.
- Sandbox changes are version-controlled; you can rewind, branch, and compare edits.

#### 🤖 Configurable Autonomy & Automation

- Flexible autonomy: pick between “full auto mode” for end-to-end execution and granular control per step.
- Automatically executes build/test scripts and can debug on errors (including browser debugging on Chrome).

#### 🌐 Multi‑Model Integration

- Combines LLMs from OpenAI, Anthropic, Google, DeepSeek, Qwen, and more to optimize reliability, cost, and performance.
- You can configure custom model packs and tweak temperature/top‑p, with version control on all changes.

---

### 🏗️ Architecture & Workflow

- **CLI‑first workflow**: run `plandex` in your project to open an interactive agent with fuzzy autocompletion, project context management, and command execution.
- **Tree‑sitter indexing** for fast context loading and syntax validation .
- **Sandboxing & diffs**: changes accumulate until approved; you can branch and experiment .

