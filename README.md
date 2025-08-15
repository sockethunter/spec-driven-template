# Spec-Driven Development Template

A template for spec-driven development with automated Claude Code hooks for VSCode.

## 🚀 How It Works

This template enables automated development powered by Claude Code, triggered on file changes:

### **Spec-Driven Implementation**

- **Trigger**: Changes to `docs/Spec.md` (note: do **not** edit `docs/Tasks.md` manually!)
- **Hook**: `hooks/spec-driven-impl`
- **Function**: Claude reads the Markdown tasks and implements them automatically
- **Format**: Checklist items are marked `[x]` after implementation, with added notes

### **Bug-Driven Development**

- **Trigger**: Changes to `docs/Bugs.md`
- **Hook**: `hooks/spec-driven-bug`
- **Function**: Claude analyzes and fixes documented bugs

### **Automated Documentation**

#### KDoc for Kotlin (`.kt` files)

- **Hook**: `hooks/docgen/kdoc`
- **Function**: Automatically adds KDoc comments to undocumented Kotlin functions
- **Tool**: Uses Detekt to detect missing documentation
- **Model**: Haiku (fast, cost-effective)

#### JSDoc for JavaScript/TypeScript (`.js`, `.jsx`, `.ts`, `.tsx`)

- **Hook**: `hooks/docgen/jsdoc`
- **Function**: Adds JSDoc comments to JavaScript/TypeScript functions
- **Model**: Haiku (fast, cost-effective)

## ⚙️ Setup

### 1. Install VSCode Extension

```bash
code --install-extension emeraldwalk.RunOnSave
```

### 2. Install Claude CLI

```bash
npm install -g @anthropic/claude-cli
```

### 3. Use the Template

```bash
cp -r specdriven-template my-project/
cd my-project/
chmod +x hooks/*
chmod +x hooks/docgen/*
```

## 📁 Structure

```
.
├── .vscode/
│   ├── extensions.json     # RunOnSave extension config
│   └── settings.json       # Hook configuration
├── docs/
│   ├── Spec.md             # Specifications & tasks
│   ├── Bugs.md             # Bug reports
│   └── Tasks.md            # Auto-generated task list
├── hooks/
│   ├── spec-driven         # Spec interpreter
│   ├── spec-driven-impl    # Task implementation hook
│   ├── spec-driven-bug     # Bug fix hook
│   └── docgen/
│       ├── kdoc            # Kotlin documentation
│       └── jsdoc           # JS/TS documentation
└── README.md
```

## 🔧 Configuration

### Environment Variables

```bash
# For all hooks
export SETTING_MODEL="sonnet"           # Claude model
export SETTING_DEBOUNCE_SECONDS=8       # Debounce time
export SETTING_MAX_LINES=1200           # Max file size
export SETTING_TIMEOUT=300              # Timeout in seconds

# For documentation hooks

## Kotlin
export KDOC_MODEL="haiku"               # Claude model
export KDOC_MAX_TURNS=10                # Max turns
export KDOC_TIMEOUT=40                  # Shorter timeout

## JSDoc
export JSDOC_MODEL="haiku"              # Claude model
export JSDOC_MAX_TURNS=10               # Max turns
export JSDOC_TIMEOUT=40                 # Shorter timeout
```

### VSCode Integration

Hooks are triggered automatically via the **Run On Save** extension:

- File changes are detected and the corresponding hook runs
- Status is shown in the VSCode title bar (e.g., `KDoc running`, `Spec-driven ready`, etc.)
- Processes run asynchronously in the background

## 💡 Example Workflow

1. **Define tasks**: Write in `docs/Spec.md`:

   ```markdown
   ## Tasks

   - [ ] Implement user login
   - [ ] Create dashboard
   - [ ] Write tests
   ```

2. **Auto implementation**: Saving the file triggers Claude to implement the tasks
3. **Documentation**: Editing `.kt` or `.js` files triggers auto-generated documentation comments
4. **Bug fixes**: Write bugs in `docs/Bugs.md` → Claude analyzes and fixes them automatically

## ⚡ Features

- **Debouncing**: Prevents repeated execution during rapid file changes
- **Caching**: Skips unchanged files
- **PID Management**: Avoids concurrent executions
- **VSCode Integration**: Visual status in the title bar
- **Timeout Handling**: Automatically terminates hanging processes
- **Multi-language**: Supports Kotlin, JavaScript, TypeScript

## 🛠️ Development

### Adding New Hooks

1. Create your script in `hooks/`
2. Make it executable:

   ```bash
   chmod +x hooks/my-hook
   ```

3. Register it in `.vscode/settings.json`:

   ```json
   {
     "match": "\\.(extension)$",
     "isAsync": true,
     "cmd": "zsh -lc '\"${workspaceFolder}/hooks/my-hook\" \"${file}\" \"${workspaceFolder}\"'"
   }
   ```

### Hook Template

Use existing scripts as a base for new hook structures.

---

## 🧼 Reset Project State

If something goes wrong or you want to start fresh, you can reset the state of your `docs/` folder:

### Run the following reset command

```bash
rm docs/*
mkdir -p docs
echo -e "# 📝 Specification\n\n- [ ] Example task" > docs/Spec.md
echo -e "# 🐞 Known Bugs\n\n- [ ] Example bug description" > docs/Bugs.md
```

This will:

- Remove all files in the `docs/` folder
- Recreate `Spec.md` and `Bugs.md` with minimal valid outlines
- Allow Claude to resume automation with a clean slate
