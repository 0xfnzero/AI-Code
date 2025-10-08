# Lesson 12: Claude Code Best Practices

> 🎯 **Learning Objectives**: Master Anthropic's official best practices and build efficient agentic programming workflows

> 📖 **Original Article**: [Claude Code: Best practices for agentic coding](https://www.anthropic.com/engineering/claude-code-best-practices)

## 📚 Course Overview

This lesson is based on Anthropic's officially published best practices guide, summarizing proven general patterns and workflows applicable to various codebases, languages, and environments. These recommendations come from practical experience of both Anthropic's internal teams and external engineers.

## 🎯 Core Philosophy

Claude Code is a **low-level, non-opinionated** agentic coding tool:
- Provides nearly raw access to the model
- Doesn't impose specific workflows
- Flexible, customizable, scriptable, and safe
- Suitable for building your own best practices

## 📖 Best Practices Guide

### I. Customize Your Setup

#### 1.1 Create CLAUDE.md File

`CLAUDE.md` is a special file that Claude automatically loads at the start of conversations, making it an ideal place to document project information.

**Suitable content to record:**
- Common bash commands
- Core files and utility functions
- Code style guidelines
- Testing instructions
- Repository conventions (branch naming, merge vs. rebase, etc.)
- Development environment setup (pyenv, compilers, etc.)
- Project-specific unusual behaviors or warnings
- Other information you want Claude to remember

**Example CLAUDE.md:**
```markdown
# Bash commands
- npm run build: Build the project
- npm run typecheck: Run the typechecker

# Code style
- Use ES modules (import/export) syntax, not CommonJS (require)
- Destructure imports when possible (eg. import { foo } from 'bar')

# Workflow
- Be sure to typecheck when you're done making a series of code changes
- Prefer running single tests, not the whole test suite, for performance
```

**CLAUDE.md locations:**

1. **Repository root** (most common)
   - Name it `CLAUDE.md` and commit to git (recommended)
   - Or name it `CLAUDE.local.md` and add to `.gitignore`

2. **Parent directories**
   - Suitable for monorepos
   - If running in `root/foo`, both `root/CLAUDE.md` and `root/foo/CLAUDE.md` will load

3. **Subdirectories**
   - Claude loads as needed when processing subdirectory files

4. **Home folder**
   - `~/.claude/CLAUDE.md`
   - Applies to all claude sessions

**Quick generation:**
```bash
# In project directory
/init
```

Claude will automatically generate a `CLAUDE.md` based on your project structure.

---

#### 1.2 Tune Your CLAUDE.md File

`CLAUDE.md` becomes part of Claude's prompt and should be continuously optimized like frequently used prompts.

**Optimization suggestions:**

1. **Dynamically add content**
   ```
   You: # All API calls must include error handling and retry logic

   Claude: Added to CLAUDE.md, I'll follow this rule in all future API-related code.
   ```

2. **Regular optimization**
   - Process CLAUDE.md with prompt optimizer
   - Use "IMPORTANT" or "YOU MUST" for emphasis
   - Remove ineffective content, keep concise

3. **Team collaboration**
   - Use `#` to record commands, files, style guides while coding
   - Submit CLAUDE.md changes together
   - Benefits team members

**Common mistakes:**
- ❌ Add lots of content without iterative validation
- ❌ Never optimize and refine
- ✅ Continuously test and improve most effective content

---

#### 1.3 Manage Claude's Allowed Tools List

By default, Claude Code requests permission for operations that might modify the system (file writes, bash commands, MCP tools, etc.).

**Four ways to manage allowed tools:**

1. **Select "Always allow" in session**
   - Authorize directly when prompted

2. **Use /permissions command**
   ```
   /permissions add Edit
   /permissions add Bash(git commit:*)
   /permissions add mcp__puppeteer__puppeteer_navigate
   ```

3. **Manually edit configuration file**
   - `.claude/settings.json` (recommended to commit to source control)
   - `~/.claude.json` (personal configuration)

4. **Use CLI flags**
   ```bash
   claude --allowedTools Edit,Bash
   ```

**Recommended allowed list:**
- `Edit` - File editing (easily reversible)
- `Bash(git commit:*)` - git commits
- `Bash(npm run:*)` - npm scripts

---

#### 1.4 If Using GitHub, Install gh CLI

Claude can use `gh` CLI to interact with GitHub:
- Create issues
- Open PRs
- Read comments
- Manage repositories

**Installation:**
```bash
# macOS
brew install gh

# Authenticate
gh auth login
```

Without `gh`, Claude can still use GitHub API or MCP server.

---

### II. Give Claude More Tools

#### 2.1 Use bash Tools with Claude

Claude Code inherits your bash environment and can access all tools.

**How to let Claude know your tools:**

1. **Tell Claude tool name and example usage**
   ```
   You: We use deploy-staging command to deploy to staging environment
       Usage: deploy-staging <branch-name>
   ```

2. **Let Claude check --help**
   ```
   You: Run deploy-staging --help to learn how to use it
   ```

3. **Document in CLAUDE.md**
   ```markdown
   # Custom Tools
   - deploy-staging: Deploy to staging environment
     Usage: deploy-staging <branch-name>
   ```

---

#### 2.2 Use MCP with Claude

Claude Code is both an MCP server and client.

**Three connection methods:**

1. **Project configuration**
   - Available when running in project directory

2. **Global configuration**
   - Available for all projects

3. **.mcp.json file**
   - Team members get out-of-box functionality

**Example .mcp.json configuration:**
```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    },
    "sentry": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sentry"],
      "env": {
        "SENTRY_AUTH_TOKEN": "${SENTRY_AUTH_TOKEN}"
      }
    }
  }
}
```

**Debug MCP:**
```bash
claude --mcp-debug
```

---

#### 2.3 Use Custom Slash Commands

Store repetitive workflows as slash commands.

**Create slash commands:**

1. Create Markdown files in `.claude/commands/`
2. Use `$ARGUMENTS` to pass parameters

**Example: Fix GitHub Issue**

Create `.claude/commands/fix-github-issue.md`:
```markdown
Please analyze and fix the GitHub issue: $ARGUMENTS.

Follow these steps:
1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR

Remember to use the GitHub CLI (`gh`) for all GitHub-related tasks.
```

**Usage:**
```
/project:fix-github-issue 1234
```

**Personal commands:**
- Place in `~/.claude/commands/` folder
- Use in all sessions

---

### III. Try Common Workflows

#### 3.1 Explore, Plan, Code, Commit

General workflow applicable to many problems.

**Steps:**

1. **Exploration phase**
   ```
   You: Read files that handle logging, but don't write code yet
   ```
   - Fully utilize agent to investigate problem
   - Preserve context

2. **Planning phase**
   ```
   You: think hard and develop a plan to solve this problem
   ```
   - Use "think" to trigger extended thinking mode
   - Thinking levels: think < think hard < think harder < ultrathink

3. **Coding phase**
   ```
   You: Implement this solution
   ```
   - Let Claude validate solution reasonableness

4. **Commit phase**
   ```
   You: Commit code and create PR, update README documenting changes
   ```

**Key points:**
- Steps 1-2 are critical
- Otherwise Claude jumps directly to coding
- Deep thinking problems need research and planning first

---

#### 3.2 Write Tests, Commit; Code, Iterate, Commit

Test-Driven Development (TDD) is more powerful in agentic programming.

**TDD workflow:**

1. **Write tests**
   ```
   You: Write tests based on expected input/output. We're doing TDD, don't write mock implementation.
   ```

2. **Verify tests fail**
   ```
   You: Run tests and confirm failure. Don't write implementation code.
   ```

3. **Commit tests**
   ```
   You: Commit when satisfied with tests
   ```

4. **Write implementation**
   ```
   You: Write code that passes tests, don't modify tests
   ```

5. **Iterate and optimize**
   ```
   You: Continue iterating until all tests pass
   ```

6. **Verify and commit**
   ```
   You: Use independent agent to verify implementation doesn't overfit tests
   ```

**Advantages:**
- Claude performs best with clear goals
- Visual mocks and test cases provide clear goals
- Continuously modify, evaluate, refine until success

---

#### 3.3 Code, Screenshot Results, Iterate

Provide visual goals for Claude.

**Visual feedback workflow:**

1. **Provide screenshot tools**
   - Puppeteer MCP server
   - iOS Simulator MCP server
   - Manual copy/paste screenshots

2. **Provide visual mocks**
   ```
   You: [Paste design mockup]
       Implement this design
   ```

3. **Iterate and optimize**
   ```
   You: Screenshot current result and compare with mock, continue optimizing
   ```

4. **Commit when satisfied**
   ```
   You: Commit code
   ```

**Results:**
- First version may be good
- 2-3 iterations usually better
- Most effective with visible output tools

---

#### 3.4 Safe YOLO Mode

Skip all permission checks and let Claude complete tasks at once.

**Usage:**
```bash
claude --dangerously-skip-permissions
```

**Applicable scenarios:**
- Fix lint errors
- Generate boilerplate code
- Batch renaming

**Safety recommendations:**
- ⚠️ Risk of data loss, system damage
- ⚠️ May be vulnerable to prompt injection attacks
- ✅ Recommend using in containers without network access
- ✅ Refer to Docker Dev Containers

---

#### 3.5 Codebase Q&A

When new members onboard, use Claude Code to learn and explore the codebase.

**Example questions:**

1. **How it works**
   ```
   You: How does logging work?
   ```

2. **Operation guide**
   ```
   You: How to add a new API endpoint?
   ```

3. **Code explanation**
   ```
   You: What does the async move { ... } on line 134 of foo.rs do?
   ```

4. **Edge cases**
   ```
   You: What edge cases does CustomerOnboardingFlowImpl handle?
   ```

5. **Design decisions**
   ```
   You: Why call foo() instead of bar() on line 333?
   ```

6. **Cross-language comparison**
   ```
   You: What's the Java equivalent of line 334 in baz.py?
   ```

**Application at Anthropic:**
- Core onboarding process
- Significantly speeds up ramp-up
- Reduces burden on other engineers
- No special prompts needed, just ask directly

---

#### 3.6 Use Claude to Operate git

Many Anthropic engineers have Claude handle over 90% of git operations.

**Git operation examples:**

1. **Search git history**
   ```
   You: Check git history, what changes were included in v1.2.3?
   You: Who is responsible for this feature?
   You: Why was this API designed this way?
   ```

2. **Write commit messages**
   ```
   You: Generate commit message
   ```
   - Claude automatically reviews changes and history
   - Generates messages with all relevant context

3. **Complex git operations**
   ```
   You: Revert changes to foo.js
   You: Resolve rebase conflicts
   You: Compare and cherry-pick this patch
   ```

---

#### 3.7 Use Claude to Operate GitHub

Claude Code can manage GitHub interactions.

**GitHub operation examples:**

1. **Create PR**
   ```
   You: pr
   ```
   - Claude understands "pr" shorthand
   - Generates commit message based on diff and context

2. **Fix code review comments**
   ```
   You: Fix comments on PR
   ```
   - One-click resolution of simple comments
   - Pushes back to PR branch when done

3. **Fix build failures**
   ```
   You: Fix build failures or linter warnings
   ```

4. **Issue management**
   ```
   You: Triage and organize all open issues
   ```
   - Loop through all open GitHub issues

**Advantages:**
- No need to remember `gh` command-line syntax
- Automate daily tasks

---

#### 3.8 Use Claude to Handle Jupyter Notebooks

Anthropic researchers and data scientists use Claude Code to read and write Jupyter notebooks.

**Workflow:**

1. **Open side-by-side in VS Code**
   - Claude Code terminal
   - `.ipynb` file

2. **Claude capabilities**
   - Interpret output (including images)
   - Quickly explore and interact with data
   - Optimize notebook aesthetics

3. **Optimization suggestions**
   ```
   You: Optimize this data visualization to make it more beautiful
   ```
   - Explicitly request "beautiful"
   - Remind Claude to optimize human perception

---

### IV. Optimize Your Workflow

#### 4.1 Be Specific with Instructions

Claude Code success rate significantly improves with more specific instructions.

**Comparison example:**

❌ **Vague instructions**
```
You: Implement a calendar widget
```

✅ **Specific instructions**
```
You: Reference the existing widget implementation on homepage to understand the pattern, especially how code and interface separate.
    HotDogWidget.php is a good example.
    Then, implement a new calendar widget following this pattern, supporting month selection and year pagination.
    Only use libraries already in the codebase, implement from scratch.
```

**Principles:**
- Claude can infer intent but can't read minds
- Specific instructions better align expectations
- Clear instructions reduce subsequent corrections

---

#### 4.2 Give Claude Images

Claude excels at handling images and diagrams in multiple ways.

**Ways to provide images:**

1. **Paste screenshots**
   - macOS: `Cmd+Ctrl+Shift+4` screenshot to clipboard
   - Paste: `Ctrl+V` (note not `Cmd+V`)
   - Ineffective when remote

2. **Drag images**
   - Drag directly to input box

3. **Provide file path**
   ```
   You: Read designs/mockup.png and implement this interface
   ```

**Application scenarios:**
- Use design mockups as reference for UI development
- Analyze and debug visualization charts
- Even without adding images, explicitly mention results need to be beautiful

---

#### 4.3 Explicitly Mention Files to Process

Use Tab completion to quickly reference any file or folder in repository.

**Tab completion example:**
```
You: Optimize src/comp[TAB]
    → src/components/

You: Refactor src/components/Head[TAB]
    → src/components/Header.tsx
```

**Benefits:**
- Helps Claude find correct resources
- Avoid path errors
- Provide precise context

---

#### 4.4 Give Claude URLs

Paste specific URLs in prompts, Claude will automatically fetch and read.

**URL usage example:**
```
You: https://react.dev/learn/thinking-in-react
    Refactor our components based on this article
```

**Domain permission management:**
```
/permissions add WebFetch(docs.foo.com:*)
```
Avoid repeated permission requests for same domain.

---

#### 4.5 Correct Promptly

Proactive collaboration and guidance usually works better with Claude.

**Four correction tools:**

1. **Let Claude develop plan first**
   ```
   You: Develop plan, don't code until confirmed
   ```

2. **Press Escape to interrupt**
   - Interrupt at any stage (thinking, calling tools, editing files)
   - Preserve context
   - Facilitate redirection or supplemental instructions

3. **Double press Escape to edit history**
   - Go back to history
   - Edit previous prompt
   - Explore different directions
   - Can edit repeatedly

4. **Let Claude undo changes**
   ```
   You: Undo recent changes
   ```
   - Often combined with point 2
   - Try different approaches

**Principle:**
- Claude Code occasionally solves perfectly in one shot
- But using correction tools usually gets better results faster

---

#### 4.6 Use /clear to Keep Context Focused

In long sessions, context window may fill with irrelevant content, affecting performance.

**Use cases:**
- After completing each task
- When context becomes messy
- Starting new independent task

**Command:**
```
/clear
```

**Effects:**
- Reset context window
- Keep attention focused
- Improve performance

---

#### 4.7 Use Checklists and Scratchpads for Complex Workflows

For large tasks with multiple steps or requiring exhaustive solutions, recommend using Markdown checklists.

**Applicable scenarios:**
- Code migration
- Fix numerous lint errors
- Run complex build scripts

**Workflow example: Fix lint issues**

1. **Create checklist**
   ```
   You: Run lint command, write all errors (with filenames and line numbers) to lint-fixes.md
   ```

2. **Process item by item**
   ```
   You: Start from top of checklist, fix one by one. Check off each after fixing and verifying, then handle next.
   ```

**Checklist format:**
```markdown
# Lint Fixes Checklist

- [ ] src/app.js:10 - Missing semicolon
- [ ] src/utils.js:23 - Unused variable
- [ ] src/api.js:45 - Inconsistent quotes
...
```

---

#### 4.8 Pass Data to Claude

Multiple ways to provide data to Claude.

**Data passing methods:**

1. **Direct copy-paste** (most common)
   ```
   You: [Paste logs]
       Analyze these errors
   ```

2. **Pipe in**
   ```bash
   cat foo.txt | claude
   cat error.log | claude -P "Analyze these logs"
   ```
   - Suitable for logs, CSV, large data

3. **Let Claude pull**
   ```
   You: Use curl to pull API data and analyze
   You: Read /var/log/app.log
   ```

4. **Read files or URLs**
   ```
   You: Read data.csv and generate report
   You: Fetch data from https://api.example.com/data
   ```

**Combined use:**
Most sessions combine multiple methods. For example:
- First pipe in log file
- Then let Claude use tools to pull more context

---

### V. Use Headless Mode to Automate Infrastructure

Claude Code provides headless mode, suitable for non-interactive scenarios.

**Enable headless mode:**
```bash
# Basic usage
claude -p "Your prompt"

# Streaming JSON output
claude -p "Your prompt" --output-format stream-json
```

**Notes:**
- Headless mode doesn't persist between sessions
- Must manually trigger each time

---

#### 5.1 Use Claude to Handle Issue Triage

Headless mode can drive automation triggered by GitHub events.

**Example: Auto-label new issues**

```yaml
# .github/workflows/triage-issues.yml
name: Triage Issues
on:
  issues:
    types: [opened]

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Triage with Claude
        run: |
          claude -p "Analyze issue #${{ github.event.issue.number }} and add appropriate labels"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

#### 5.2 Use Claude as Linter

Claude Code can perform subjective code review.

**Problems it can find:**
- Spelling errors
- Outdated comments
- Misleading function or variable names
- Inconsistent code style
- Non-compliance with best practices

**Example: pre-commit hook**

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Get staged files
FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.js$')

if [ -n "$FILES" ]; then
    echo "Running Claude linter..."
    claude -p "Review code quality of these files: $FILES" --output-format stream-json
fi
```

---

### VI. Multiple Claude Collaboration Boosts Efficiency

One of the most powerful uses is running multiple Claude instances in parallel.

#### 6.1 One Claude Writes Code, Another Claude Reviews

Like multi-engineer collaboration, separating contexts is sometimes better.

**Workflow:**

1. **Claude A: Write code**
   ```
   Claude A: Implement user authentication feature
   ```

2. **Claude B: Review code**
   ```
   You: /clear  # Or start new terminal
   Claude B: Review authentication code written by Claude A
   ```

3. **Claude C: Modify based on feedback**
   ```
   You: /clear  # Or start new terminal
   Claude C: Read code and review feedback, modify based on suggestions
   ```

**Other patterns:**
- One writes tests, another writes implementation
- Let Claude instances communicate through scratchpads
- Specify who writes and who reads

**Advantages:**
- Division of labor often works better than single Claude
- Each focuses on specific task

---

#### 6.2 Multiple Repository Checkouts

Many Anthropic engineers' approach.

**Setup steps:**

1. **Create multiple checkouts**
   ```bash
   cd ~/projects
   git clone repo.git project-1
   git clone repo.git project-2
   git clone repo.git project-3
   git clone repo.git project-4
   ```

2. **Open in different terminal tabs**
   ```bash
   # Tab 1
   cd project-1 && claude

   # Tab 2
   cd project-2 && claude

   # Tab 3
   cd project-3 && claude

   # Tab 4
   cd project-4 && claude
   ```

3. **Assign different tasks**
   - Claude 1: Refactor authentication
   - Claude 2: Add new feature
   - Claude 3: Fix bugs
   - Claude 4: Write documentation

4. **Check progress in rotation**
   - Approve/reject permission requests
   - Provide feedback
   - Coordinate tasks

---

#### 6.3 Use git worktree

Suitable for multiple independent tasks, lightweight alternative to multiple checkouts.

**What is git worktree?**
- Check out multiple branches of same repository to different directories
- Each worktree has independent working directory and files
- History and reflog shared

**Operation steps:**

1. **Create worktree**
   ```bash
   # Create worktree for feature-a branch
   git worktree add ../project-feature-a feature-a

   # Create worktree for feature-b branch
   git worktree add ../project-feature-b feature-b

   # Create worktree for feature-c branch
   git worktree add ../project-feature-c feature-c
   ```

2. **Start Claude in each worktree**
   ```bash
   # Terminal 1
   cd ../project-feature-a && claude

   # Terminal 2
   cd ../project-feature-b && claude

   # Terminal 3
   cd ../project-feature-c && claude
   ```

3. **Clean up after completion**
   ```bash
   git worktree remove ../project-feature-a
   git worktree remove ../project-feature-b
   git worktree remove ../project-feature-c
   ```

**Best practices:**
- Consistent naming conventions
- Keep one terminal tab per worktree
- Mac users use iTerm2 to set notifications when attention needed
- Different IDE windows for different worktrees
- Clean up promptly after completion

**Example scenario:**
- Claude A: Refactor authentication system
- Claude B: Build data visualization components
- Claude C: Fix performance issues
- Each progresses independently without interference

---

#### 6.4 Use Headless Mode with Custom Scripts

`claude -p` (headless mode) can programmatically integrate Claude Code into larger workflows.

**Two main modes:**

#### A. Batch Processing

Suitable for large-scale migration or analysis.

**Example: Batch migrate files**

1. **Generate task list**
   ```
   You: Generate list of files needing migration from React to Vue
   ```

2. **Write batch script**
   ```bash
   #!/bin/bash
   # migrate.sh

   # Read file list
   while read file; do
     echo "Migrating $file..."

     # Call Claude to process single file
     result=$(claude -p "Migrate $file from React to Vue. When done, return OK if succeeded, or FAIL if failed." \
                     --allowedTools Edit,Bash \
                     --json)

     # Check result
     if echo "$result" | grep -q "OK"; then
       echo "✅ $file migrated successfully"
     else
       echo "❌ $file migration failed"
     fi
   done < files-to-migrate.txt
   ```

3. **Multiple runs for optimization**
   - First run: Test prompt
   - Iterate optimize prompt
   - Final large-scale run

**Other batch processing scenarios:**
- Analyze hundreds of log files
- Process thousands of CSVs
- Batch update documentation
- Large-scale code refactoring

---

#### B. Pipeline Integration

Integrate Claude into existing data/processing pipelines.

**Basic usage:**
```bash
claude -p "<your prompt>" --json | your_command
```

**Example 1: Log analysis pipeline**
```bash
#!/bin/bash
# log-analysis-pipeline.sh

# Step 1: Collect logs
cat /var/log/app/*.log | \

# Step 2: Claude analyzes
claude -p "Analyze these logs, find error patterns and frequency" --json | \

# Step 3: Format results
jq '.analysis' | \

# Step 4: Send notification
mail -s "Daily Log Analysis" team@example.com
```

**Example 2: Code quality pipeline**
```bash
#!/bin/bash
# quality-pipeline.sh

# Get recently committed files
git diff --name-only HEAD~1 | \

# Review with Claude
claude -p "Review code quality of these files" --json | \

# Extract issues
jq -r '.issues[] | "\(.file):\(.line) - \(.message)"' | \

# Create GitHub issues
while read issue; do
  gh issue create --title "Code Quality: $issue" --body "Found by automated review"
done
```

**Debugging suggestions:**

```bash
# Use --verbose during development
claude -p "test" --verbose

# Turn off verbose in production
claude -p "test" --json > output.json
```

---

## 🎯 Practical Tips Summary

### Configuration Priority

1. ✅ Create and optimize CLAUDE.md
2. ✅ Configure allowed tools list
3. ✅ Install gh CLI
4. ✅ Set up custom commands

### Workflow Selection

| Scenario | Recommended Workflow |
|-----|----------|
| New feature development | Explore→Plan→Code→Commit |
| Bug fix | Write tests→Code→Iterate |
| UI development | Screenshot→Code→Iterate |
| Codebase learning | Codebase Q&A |
| Batch tasks | Headless mode batch processing |

### Efficiency Improvement Points

1. **Clear instructions** - More specific the better
2. **Provide visuals** - Images and screenshots
3. **Correct promptly** - Esc / double Esc / undo
4. **Keep focused** - /clear to clean context
5. **Multiple instance collaboration** - Division of labor more efficient

### Safety Recommendations

1. ⚠️ Use `--dangerously-skip-permissions` cautiously
2. ✅ Run high-risk operations in containers
3. ✅ Regularly review allowed tools list
4. ✅ Commit CLAUDE.md and .mcp.json to git

---

## 🚀 Advanced Learning

### Related Courses
- 📖 [Lesson 9: Prompt Optimization](./09-prompt-optimization.md) - Improve interaction efficiency
- 📖 [Lesson 10: AI Agent System](./10-ai-agents.md) - Build expert team
- 📖 [Lesson 11: 34 Practical Tips](./11-practical-tips.md) - Comprehensive tips collection

### Official Resources
- 📚 [Claude Code Official Documentation](https://claude.ai/code)
- 📚 [MCP Documentation](https://modelcontextprotocol.io)
- 📚 [GitHub CLI Documentation](https://cli.github.com)

### Community Resources
- 💬 [Claude Code GitHub Discussions](https://github.com/anthropics/claude-code/discussions)
- 💬 [Discord Community](https://discord.gg/claude-code)

---

## 💡 Final Advice

These best practices are not set in stone or universally applicable, please treat them as a starting point. We encourage you to:

1. **Experiment boldly** - Explore different approaches
2. **Continuously optimize** - Adjust workflows based on practice
3. **Share experience** - Communicate with team and community
4. **Stay flexible** - Find what works best for you

Remember: Claude Code is a flexible, customizable tool, best practices will continuously evolve with your experience.

---

📚 [Back to Tutorial Home](../README.md)
