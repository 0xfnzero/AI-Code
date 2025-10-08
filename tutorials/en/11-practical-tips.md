# Lesson 11: 34 Practical Tips for Claude Code

> 🎯 **Learning Objectives**: Master 34 practical tips to dramatically improve your Claude Code efficiency

## 📚 Course Overview

This lesson compiles 34 practical tips for Claude Code, covering six major areas: command-line operations, image processing, integrations, configuration, custom commands, and workflow optimization to help you become a Claude Code expert.

## 📖 Tips by Category

### I. Command Line (CLI) Tips (7 Tips)

#### 1. Treat it as a CLI Tool
Fundamentally understand that Claude Code is a command-line tool with all basic CLI features.

**Practical Application:**
```bash
# Run directly in terminal
claude "Help me create a React component"

# Run in project directory
cd my-project
claude "Analyze project structure and suggest improvements"
```

#### 2. Pass Command Arguments
Use the `-P` parameter to run in command-line mode.

**Examples:**
```bash
# Use -P parameter to pass prompts
claude -P "Refactor src/utils.js file"

# Combine multiple parameters
claude -P "Add unit tests" --files src/app.js
```

#### 3. Use Headless Mode
Use the `-P` parameter to run in headless mode, suitable for automation scenarios.

**Automation Script Example:**
```bash
#!/bin/bash
# Automated code review script
claude -P "Review recent code changes" > review.md
```

#### 4. Connect with Other Tools
Connect other command-line tools (bash/CLI tools) to your workflow.

**Practical Examples:**
```bash
# Combine with git
git diff | claude -P "Analyze these code changes"

# Combine with npm
npm test 2>&1 | claude -P "Analyze test failure reasons"

# Combine with grep
grep -r "TODO" . | claude -P "Organize all TODOs"
```

#### 5. Use Pipe Input
Pass data to Claude Code through pipes (|).

**Pipe Operation Examples:**
```bash
# Analyze logs
cat error.log | claude -P "Find error patterns and solutions"

# Process command output
ls -la | claude -P "Analyze if directory structure is reasonable"

# Multi-level pipes
cat package.json | jq '.dependencies' | claude -P "Check dependency versions"
```

#### 6. Run Multiple Instances
Run multiple Claude Code instances simultaneously for different tasks.

**Multi-Instance Application:**
```bash
# Terminal 1: Handle frontend
cd frontend && claude

# Terminal 2: Handle backend
cd backend && claude

# Terminal 3: Run tests
claude -P "Continuously monitor test results"
```

#### 7. Let it Start Itself
Instruct Claude Code to start a new instance to handle tasks.

**Example Conversation:**
```
You: This task is complex, please start a new Claude Code instance to specifically handle database migration

Claude: Sure, I'll start a new instance...
[New instance starts and focuses on database task]
```

---

### II. Image Processing Tips (6 Tips)

#### 8. Drag and Drop Images
Drag image files directly into the terminal to use them.

**Steps:**
1. Prepare image files (design mockups, screenshots, etc.)
2. Drag directly into Claude Code terminal
3. Describe your needs

**Use Cases:**
- Generate code from UI mockups
- Analyze interface layouts
- Copy existing interface designs

#### 9. macOS Screenshot Paste
Use shortcut `Shift+Command+Control+4` to copy screenshots directly to clipboard.

**macOS Shortcuts:**
- `Shift+Command+Control+4`: Screenshot to clipboard (select area)
- `Shift+Command+Control+3`: Full screen screenshot to clipboard

#### 10. Use Control+V to Paste
Use `Control+V` (not `Command+V`) to paste images into terminal.

**Notes:**
- macOS terminal uses `Control+V` not `Command+V`
- Ensure image is in clipboard
- Supports various image formats

#### 11. Generate Code from Design Mockups
Paste design mockup images and let Claude Code build the interface.

**Practical Example:**
```
You: [Paste Figma design mockup screenshot]
    Generate React component code based on this mockup

Claude:
I see this is a login form design containing:
- Email input field
- Password input field
- Remember me checkbox
- Login button

I'll create the corresponding React component...
[Generates code]
```

#### 12. Build Visual Feedback Loop
Take screenshots of your application's current state and feed it back to Claude Code for iterative improvements.

**Iteration Process:**
```
1. Run application
2. Take screenshot of current state
3. Paste to Claude Code
4. Describe needed improvements
5. Claude Code modifies code
6. Repeat steps 1-5
```

**Conversation Example:**
```
You: [Paste current interface screenshot]
    Buttons are too small, text spacing insufficient, needs modernization

Claude:
I see the current interface, I'll make these adjustments:
1. Increase button size to 48px height
2. Increase line height to 1.6
3. Use modern shadow effects
[Modifies code]
```

#### 13. Automate Screenshot Generation
Use Puppeteer MCP service to automate application screenshot generation process.

**Automation Configuration:**
```javascript
// .claude/puppeteer-automation.js
module.exports = {
  autoScreenshot: true,
  interval: 5000, // Screenshot every 5 seconds
  outputDir: './screenshots'
};
```

---

### III. Integration & External Data Tips (5 Tips)

#### 14. Act as MCP Server/Client
Claude Code can act as both an MCP server and client connecting to other services.

**Understanding MCP Architecture:**
```
Claude Code (Client) ←→ MCP Server (Database/API/File System)
Claude Code (Server) ←→ Other Tools (Clients)
```

**Configuration Example:**
```json
// claude_desktop_config.json
{
  "mcpServers": {
    "custom-server": {
      "command": "node",
      "args": ["./my-mcp-server.js"]
    }
  }
}
```

#### 15. Connect to Databases
Use Postgres MCP server to connect Claude Code to your database.

**Configure Database Connection:**
```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

**Usage Example:**
```
You: Query the 10 most recently registered users from the users table

Claude:
[Connects to database via MCP]
SELECT * FROM users ORDER BY created_at DESC LIMIT 10;
[Returns query results]
```

#### 16. Get Latest API Documentation
Use MCP servers provided by companies like Cloudflare to get real-time updated documentation.

**Configure API Documentation Service:**
```json
{
  "mcpServers": {
    "cloudflare-docs": {
      "command": "npx",
      "args": ["@cloudflare/mcp-server-cloudflare"]
    }
  }
}
```

#### 17. Fetch Link Content
Paste a URL directly, and Claude Code will scrape the webpage content as context.

**URL Scraping Example:**
```
You: https://react.dev/learn/thinking-in-react
    Refactor our components based on this article's methodology

Claude:
[Fetches and analyzes article content]
I've read React's official "Thinking in React" article.
Let me refactor the components following the article's approach...
```

#### 18. Get External Knowledge
Use URL fetching to obtain external world knowledge (like game rules) and apply it to code.

**Practical Case:**
```
You: https://en.wikipedia.org/wiki/Chess
    Help me implement chess move validation logic

Claude:
[Fetches chess rules]
Based on chess rules, I'll implement move validation for each piece:
1. Pawn movement rules...
2. Rook movement rules...
[Generates code]
```

---

### IV. claude.md Configuration File Tips (7 Tips)

#### 19. Understand Its Core Purpose
`claude.md` is a system prompt file loaded with every request.

**Purpose of claude.md:**
- Define project context and conventions
- Set code style preferences
- Provide project-specific knowledge
- Automatically loaded into every conversation

#### 20. Use /init to Auto-Generate
Run `/init` command in project directory to auto-generate `claude.md` based on project structure.

**Steps:**
```bash
cd my-project
claude

# In Claude Code, execute
/init
```

**Auto-generated content includes:**
- Project structure analysis
- Tech stack identification
- Code style detection
- Common conventions

#### 21. Use # for Dynamic Updates
In conversations, use `#` symbol to add instructions directly to `claude.md`.

**Dynamic Update Example:**
```
You: # All API calls must include error handling and retry logic

Claude:
Added to claude.md, I'll follow this rule in all future
API-related code.
```

#### 22. Set Global Configuration
Create `claude.md` in user home directory `~/.claude/` to apply to all projects.

**Global Configuration Example:**
```markdown
# ~/.claude/claude.md

# Personal Coding Preferences
- Use TypeScript strict mode
- Use functional style for components
- Prefer async/await over Promise.then()
- All functions must have JSDoc comments

# Code Style
- Use 2-space indentation
- Prefer single quotes
- Add trailing semicolons
```

#### 23. Use Subdirectory Configuration
Add `claude.md` in subdirectories to apply to specific modules.

**Directory Structure Example:**
```
my-project/
├── claude.md              # Project global config
├── frontend/
│   └── claude.md          # Frontend-specific config
└── backend/
    └── claude.md          # Backend-specific config
```

**Frontend claude.md:**
```markdown
# Frontend Module

## Tech Stack
- React 18
- Tailwind CSS
- React Router v6

## Component Conventions
- All components use TypeScript
- Define props as interfaces
- Use React.FC type
```

#### 24. Regular Reload and Optimization
Regularly optimize and refine your `claude.md` file to maintain specificity and efficiency.

**Optimization Suggestions:**
- Review monthly
- Remove outdated rules
- Add new best practices
- Keep it concise and clear

#### 25. Use Prompt Optimization Tools
Use Anthropic's prompt optimization tools to improve `claude.md` content.

**Optimization Process:**
```
You: /optimize-prompt
    [Paste current claude.md content]

Claude:
I'll improve your claude.md using prompt optimization best practices:
1. Make instructions more specific
2. Add examples
3. Improve structure
[Provides optimized version]
```

---

### V. Custom Slash Command Tips (6 Tips)

#### 26. Define in Specified Folder
Create files in `.claude/slash_commands` folder to define custom slash commands.

**Directory Structure:**
```
my-project/
└── .claude/
    └── slash_commands/
        ├── solve_issue.md
        ├── refactor.md
        ├── lint.md
        └── review_pr.md
```

#### 27. Create Command to Solve GitHub Issues
Create a `/solve_github_issue` command.

**Create File:** `.claude/slash_commands/solve_issue.md`
```markdown
# Solve GitHub Issue

## Instructions
Analyze GitHub issue #{issue_number} and provide solution:

1. Read issue description and discussion
2. Analyze root cause
3. Propose solution
4. Implement code fix
5. Write tests
6. Prepare PR description

## Parameters
- {issue_number}: GitHub issue number
```

**Usage:**
```
/solve_issue 123
```

#### 28. Create Refactor Command
Create a `/refactor` command.

**Create File:** `.claude/slash_commands/refactor.md`
```markdown
# Refactor Code

## Instructions
Refactor {file_path}:

1. Analyze current code structure
2. Identify code smells
3. Suggest refactoring approaches
4. Implement refactoring
5. Ensure tests pass
6. Update documentation

## Refactoring Principles
- Maintain functionality
- Improve readability
- Reduce duplication
- Follow SOLID principles

## Parameters
- {file_path}: File path to refactor
```

#### 29. Create Lint Command
Create a `/lint` command.

**Create File:** `.claude/slash_commands/lint.md`
```markdown
# Code Lint

## Instructions
Perform code check on {target}:

1. Run ESLint/Prettier
2. Check type errors
3. Identify potential issues
4. Auto-fix fixable issues
5. Report unfixable issues

## Check Items
- Code style
- Type safety
- Best practices
- Performance issues

## Parameters
- {target}: File path or directory (default: .)
```

#### 30. Create PR Review Command
Create a `/review_pr` command.

**Create File:** `.claude/slash_commands/review_pr.md`
```markdown
# Review Pull Request

## Instructions
Review Pull Request #{pr_number}:

1. Read PR description and changes
2. Analyze code quality
3. Check test coverage
4. Identify potential issues
5. Provide improvement suggestions
6. Generate review comments

## Review Focus
- Code quality
- Security
- Performance impact
- Backward compatibility
- Documentation completeness

## Parameters
- {pr_number}: Pull Request number
```

#### 31. Pass Arguments to Commands
Your custom slash commands are prompt templates that can receive command-line arguments.

**Parameter Syntax:**
```
/command_name arg1 arg2 arg3
```

**Examples:**
```
/refactor src/components/Header.tsx
/solve_issue 456
/review_pr 789
/lint src/
```

---

### VI. UI & Workflow Tips (3 Tips)

#### 32. Use Tab Completion
Use Tab key to auto-complete file and directory names for more precise context.

**Tab Completion Example:**
```
You: Please optimize src/co[TAB]
    → src/components/

You: Please optimize src/components/He[TAB]
    → src/components/Header.tsx
```

**Benefits:**
- Avoid path errors
- Provide precise context
- Speed up input

#### 33. Press Esc to Interrupt Decisively
When Claude Code's output deviates from expectations, immediately press `Esc` to interrupt.

**Use Cases:**
- Generated code going wrong direction
- Misunderstood your intent
- Started generating unwanted content

**Operation Flow:**
```
1. Notice output deviating from expectations
2. Immediately press Esc
3. Re-clarify requirements
4. Continue in correct direction
```

#### 34. Use undo to Revert
After interrupting, you can ask it to `undo` the last operation.

**Undo Example:**
```
You: [Press Esc to interrupt]
    undo, that modification was wrong

Claude:
Undid the last modification, file restored to previous state.
Please tell me the correct modification direction.
```

**Undo Commands:**
- `undo` - Undo last modification
- `undo all` - Undo all modifications in this conversation
- `undo src/app.js` - Undo modifications to specific file

---

## 🎯 Tip Application Scenarios

### Scenario 1: Rapid Prototyping
Combining tips: **11, 12, 32, 33**
```
1. Screenshot design mockup (Tip 11)
2. Generate initial code
3. Build visual feedback loop (Tip 12)
4. Use Tab to precisely specify files (Tip 32)
5. Interrupt wrong directions timely (Tip 33)
```

### Scenario 2: Code Quality Improvement
Combining tips: **27, 28, 29, 30**
```
1. Use /lint to check code (Tip 29)
2. Use /refactor to refactor (Tip 28)
3. Create PR and use /review_pr (Tip 30)
4. Link GitHub issue for resolution (Tip 27)
```

### Scenario 3: Automated Workflow
Combining tips: **3, 4, 5, 6, 13**
```
1. Write automation scripts (Tip 3)
2. Connect with other tools (Tip 4)
3. Use pipes to process data (Tip 5)
4. Run multiple instances in parallel (Tip 6)
5. Automated screenshot verification (Tip 13)
```

### Scenario 4: Project Customization
Combining tips: **19, 20, 21, 22, 23**
```
1. Run /init to generate project config (Tip 20)
2. Set global preferences (Tip 22)
3. Add specific config for submodules (Tip 23)
4. Dynamically update rules (Tip 21)
5. Regularly optimize config (Tip 24)
```

## 💡 Best Practices

### Tip Combination Strategy

**Beginner Path:**
1. Master basics: 1, 8, 9, 10, 32, 33, 34
2. Learn configuration: 19, 20, 21
3. Explore advanced: 4, 5, 11, 12

**Intermediate Developer Path:**
1. Proficient in all basics
2. Configuration optimization: 22, 23, 24, 25
3. Custom commands: 26-31
4. Tool integration: 14-18

**Advanced Developer Path:**
1. Master all tips
2. Build automated workflows: 3-7
3. Deep integration: 14-18
4. Team standards: 22, 23, 26-31

### Efficiency Improvement Comparison

| Skill Level | Tips Mastered | Efficiency Gain |
|------------|---------------|-----------------|
| Beginner | 10-15 | 2-3x |
| Intermediate | 20-25 | 5-7x |
| Advanced | 30-34 | 10x+ |

## 🚀 Next Steps

### Deep Learning
- 📖 [Lesson 9: Prompt Optimization](./09-prompt-optimization.md) - Improve interaction efficiency
- 📖 [Lesson 10: AI Agent System](./10-ai-agents.md) - Build expert team

### Practical Exercises
1. **Exercise 1**: Set up your first `claude.md`
2. **Exercise 2**: Create 3 custom slash commands
3. **Exercise 3**: Generate complete interface from design mockup
4. **Exercise 4**: Build automated code review process
5. **Exercise 5**: Configure MCP to connect database

### Advanced Challenges
1. Build complete automated workflow (combining 10+ tips)
2. Create standardized configuration templates for team
3. Develop custom MCP server
4. Implement visual-driven development process

---

📚 [Back to Tutorial Home](../README.md)
