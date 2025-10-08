# Lesson 14: Complete Claude Code Usage Guide (Reference Manual)

> 🎯 **Learning Objective**: Systematically master all features and best practices of Claude Code as a daily development reference manual
>
> 🔥 **January 2025 Update**: Includes the latest CLI commands, configuration methods, MCP integration details, and production environment practices. This is your Claude Code encyclopedia!

## 📚 Guide Usage Instructions

### 🎯 Target Audience

- **Beginner Developers**: Learn Claude Code systematically from scratch
- **Intermediate Developers**: Quickly find specific features and best practices
- **Team Leaders**: Establish standardized development processes for teams
- **Professional Developers**: Deep dive into advanced features and optimization techniques

### 📖 How to Use This Guide

1. **As Learning Material**: Read chapters in sequence
2. **As Reference Manual**: Quickly look up specific topics when needed
3. **As Team Standards**: Establish team development standards
4. **As Troubleshooting**: Find solutions when encountering problems

### 🔖 Quick Navigation

- [Installation & Configuration](#i-installation--configuration) - Basic environment setup
- [CLI Commands](#ii-cli-commands-detailed) - Command-line tool usage
- [Configuration Management](#iii-configuration-file-management) - Project configuration optimization
- [MCP Server Integration](#iv-mcp-server-integration) - Extended functionality integration
- [Prompt Engineering](#v-prompt-engineering) - Efficient interaction techniques
- [File Operations](#vi-file-operations) - Code management techniques
- [Git Workflow](#vii-git-workflow) - Version control integration
- [Project Management](#viii-project-management) - Multi-project collaboration
- [Performance Optimization](#ix-performance-optimization) - Improve response speed
- [Security Best Practices](#x-security-best-practices) - Secure development guide
- [Troubleshooting](#xi-troubleshooting) - Common problem solutions
- [Production Practices](#xii-production-environment-practices) - Enterprise-level applications

---

## I. Installation & Configuration

### 1.1 System Requirements

**Minimum Requirements:**
- Node.js 18.0.0 or higher
- npm 9.0.0 or higher
- 5GB available disk space
- Stable internet connection

**Recommended Configuration:**
- Node.js 20.x LTS (Long-term Support)
- npm 10.x
- 10GB+ available disk space
- High-speed internet connection (for API calls)

**Supported Operating Systems:**
- macOS 12.0 (Monterey) and above
- Windows 10/11 (64-bit)
- Linux (Ubuntu 20.04+, Debian 11+, Fedora 36+)

### 1.2 Installation Methods

#### Method 1: NPM Global Installation (Recommended)

```bash
# Install latest version
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version

# View help
claude --help
```

#### Method 2: NPX Temporary Run

```bash
# Run latest version without installing
npx @anthropic-ai/claude-code

# Specify version
npx @anthropic-ai/claude-code@1.2.3
```

#### Method 3: Install from Source (Developers)

```bash
# Clone repository
git clone https://github.com/anthropics/claude-code.git
cd claude-code

# Install dependencies
npm install

# Build
npm run build

# Link globally
npm link
```

### 1.3 API Key Configuration

#### Obtain API Key

1. Visit [Anthropic Console](https://console.anthropic.com/)
2. Register/Login
3. Go to API Keys page
4. Create new API Key
5. **Save key immediately** (shown only once)

#### Configuration Methods

**Method 1: Interactive Configuration (Recommended for Beginners)**

```bash
# Start configuration wizard
claude auth login

# Enter API key as prompted
# Key is securely saved to ~/.claude/config.json
```

**Method 2: Environment Variables (Recommended for Teams)**

```bash
# macOS/Linux - Add to ~/.bashrc or ~/.zshrc
export ANTHROPIC_API_KEY="sk-ant-api03-..."

# Windows PowerShell - Add to $PROFILE
$env:ANTHROPIC_API_KEY="sk-ant-api03-..."

# Windows CMD - Temporary setting
set ANTHROPIC_API_KEY=sk-ant-api03-...
```

**Method 3: Configuration File (Recommended for Projects)**

Create `.env` file (don't commit to Git):

```bash
ANTHROPIC_API_KEY=sk-ant-api03-...
```

Add to `.gitignore`:

```
.env
.env.local
```

### 1.4 Initial Configuration

```bash
# Initialize configuration
claude init

# Set default model
claude config set model claude-sonnet-4

# Set default working directory
claude config set workdir ~/Projects

# View all configurations
claude config list
```

### 1.5 Verify Installation

```bash
# Check version
claude --version

# Check API connection
claude auth status

# Run test conversation
claude chat "Hello, Claude!"

# View available models
claude models list
```

---

## II. CLI Commands Detailed

### 2.1 Basic Commands

#### claude chat

**Purpose**: Start interactive conversation session

```bash
# Basic usage
claude chat

# Start from specific directory
claude chat --dir /path/to/project

# Use specific model
claude chat --model claude-opus-4

# Set context window size
claude chat --context 100000

# Load project configuration
claude chat --config .claude.json
```

**Common Options:**

| Option | Description | Example |
|--------|-------------|---------|
| `--dir, -d` | Working directory | `-d ~/project` |
| `--model, -m` | Model to use | `-m claude-opus-4` |
| `--context` | Context window | `--context 200000` |
| `--config, -c` | Config file | `-c .claude.json` |
| `--verbose, -v` | Verbose output | `-v` |
| `--debug` | Debug mode | `--debug` |

#### claude auth

**Purpose**: Manage API keys and authentication

```bash
# Login (interactive)
claude auth login

# Logout
claude auth logout

# Check authentication status
claude auth status

# Update API key
claude auth set-key sk-ant-api03-...

# Show current key (partially masked)
claude auth show-key
```

#### claude config

**Purpose**: Manage configuration settings

```bash
# View all configurations
claude config list

# Set configuration item
claude config set <key> <value>

# Get configuration item
claude config get <key>

# Delete configuration item
claude config delete <key>

# Reset all configurations
claude config reset
```

**Common Configuration Items:**

```bash
# Default model
claude config set model claude-sonnet-4

# Working directory
claude config set workdir ~/Projects

# Editor
claude config set editor vscode

# Auto-save history
claude config set saveHistory true

# Max tokens
claude config set maxTokens 4096
```

### 2.2 MCP Commands

#### claude mcp add

**Purpose**: Add MCP server

```bash
# Basic syntax
claude mcp add <name> [options] -- <command> [args...]

# User-level server (globally available)
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents

# Project-level server (team shared)
claude mcp add project-tools -s project -- npx -y @your-team/tools

# With environment variables
claude mcp add github -s user -e GITHUB_TOKEN=ghp_xxx -- npx -y @modelcontextprotocol/server-github

# Local server (current directory)
claude mcp add local-tool -- node ./mcp-server.js
```

**Option Description:**

| Option | Description | Values |
|--------|-------------|---------|
| `-s, --scope` | Scope | `local`, `user`, `project` |
| `-e, --env` | Environment variable | `KEY=value` |
| `--` | Command separator | Required |

#### claude mcp list

```bash
# View all MCP servers
claude mcp list

# View specific scope
claude mcp list --scope user
claude mcp list --scope project

# Detailed information
claude mcp list --verbose
```

#### claude mcp remove

```bash
# Remove server
claude mcp remove <name>

# Force remove (no confirmation)
claude mcp remove <name> --force

# Remove server from specific scope
claude mcp remove <name> --scope user
```

#### claude mcp test

```bash
# Test MCP server connection
claude mcp test <name>

# Test all servers
claude mcp test --all

# Detailed test report
claude mcp test <name> --verbose
```

### 2.3 Project Commands

#### claude project init

```bash
# Initialize project in current directory
claude project init

# Specify project type
claude project init --type web-app
claude project init --type cli-tool
claude project init --type library

# Use template
claude project init --template react-typescript
claude project init --template node-express
```

#### claude project info

```bash
# View project information
claude project info

# JSON format output
claude project info --json

# Include statistics
claude project info --stats
```

### 2.4 Tool Commands

#### claude analyze

```bash
# Analyze codebase
claude analyze

# Analyze specific file
claude analyze src/index.ts

# Generate analysis report
claude analyze --report analysis-report.md

# Check specific issues
claude analyze --check security
claude analyze --check performance
claude analyze --check best-practices
```

#### claude refactor

```bash
# Interactive refactoring
claude refactor

# Refactor specific file
claude refactor src/legacy-code.js

# Apply specific refactoring pattern
claude refactor --pattern extract-method
claude refactor --pattern rename-variable
```

#### claude test

```bash
# Generate tests
claude test generate

# Generate tests for specific file
claude test generate src/utils.ts

# Run tests
claude test run

# Analyze test coverage
claude test coverage
```

### 2.5 Debug Commands

#### claude debug

```bash
# Enable debug mode
claude --debug chat

# View logs
claude debug logs

# View logs in real-time
claude debug logs --follow

# Clear logs
claude debug logs --clear
```

#### claude doctor

```bash
# Diagnose installation issues
claude doctor

# Fix common issues
claude doctor --fix

# Detailed diagnostic report
claude doctor --verbose
```

---

## III. Configuration File Management

### 3.1 Global Configuration File

**Location:** `~/.claude/config.json`

**Purpose:** User-level global settings

```json
{
  "apiKey": "sk-ant-api03-...",
  "model": "claude-sonnet-4",
  "maxTokens": 4096,
  "temperature": 1.0,
  "editor": "vscode",
  "saveHistory": true,
  "historyDir": "~/.claude/history",
  "theme": "dark"
}
```

### 3.2 Project Configuration File

**Location:** Project root `.claude.json`

**Purpose:** Project-specific configuration (team shared)

```json
{
  "name": "my-project",
  "description": "Project description",
  "version": "1.0.0",
  "model": "claude-sonnet-4",
  "context": {
    "include": [
      "src/**/*.ts",
      "docs/**/*.md",
      "package.json"
    ],
    "exclude": [
      "node_modules/**",
      "dist/**",
      "*.test.ts"
    ]
  },
  "rules": [
    "Use TypeScript strict mode",
    "Follow ESLint rules",
    "All functions must have documentation comments"
  ],
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."]
    }
  }
}
```

### 3.3 CLAUDE.md File

**Location:** Project root `CLAUDE.md`

**Purpose:** Project context and development standards

```markdown
# Project Name

## Project Overview
Brief description of project purpose and functionality

## Tech Stack
- Frontend: React 18 + TypeScript
- Backend: Node.js + Express
- Database: PostgreSQL
- Deployment: Docker + Kubernetes

## Development Standards

### Code Style
- Use Prettier for code formatting
- Follow ESLint rules
- Use semantic naming

### Commit Standards
- Use Conventional Commits
- Format: `<type>(<scope>): <subject>`
- Types: feat, fix, docs, style, refactor, test, chore

### Testing Requirements
- Unit test coverage > 80%
- All APIs must have integration tests
- Critical paths must have E2E tests

## Project Structure
```
src/
├── components/     # React components
├── services/       # Business logic
├── utils/          # Utility functions
├── types/          # TypeScript types
└── tests/          # Test files
```

## Environment Variables
- `DATABASE_URL`: Database connection string
- `API_KEY`: Third-party API key
- `PORT`: Server port (default 3000)

## Common Commands
- `npm run dev`: Start development server
- `npm run build`: Build production version
- `npm test`: Run tests
- `npm run lint`: Code check

## Notes
- Don't commit `.env` files
- All API keys must be configured via environment variables
- Must run tests and lint before committing
```

### 3.4 Environment-Specific Configuration

#### Development `.claude.dev.json`

```json
{
  "model": "claude-sonnet-4",
  "context": {
    "include": ["src/**", "tests/**"]
  },
  "debug": true,
  "verbose": true
}
```

#### Production `.claude.prod.json`

```json
{
  "model": "claude-opus-4",
  "maxTokens": 8192,
  "context": {
    "include": ["src/**"],
    "exclude": ["**/*.test.*", "**/__tests__/**"]
  },
  "debug": false
}
```

#### Switch Configuration

```bash
# Use development config
claude chat --config .claude.dev.json

# Use production config
claude chat --config .claude.prod.json

# Control via environment variable
export CLAUDE_CONFIG=.claude.dev.json
claude chat
```

---

## IV. MCP Server Integration

### 4.1 MCP Architecture Overview

**MCP (Model Context Protocol)** is an open-source protocol launched by Anthropic that allows AI assistants to connect to external tools and data sources.

**Architecture Diagram:**

```
┌────────────────────────┐
│    Claude Code CLI     │
│    (MCP Client)        │
└───────────┬────────────┘
            │ MCP Protocol
            │
    ┌───────┴────────┐
    │                │
┌───┴────┐      ┌────┴────┐
│ Server │      │ Server  │
│   A    │      │    B    │
└───┬────┘      └────┬────┘
    │                │
┌───┴────┐      ┌────┴────┐
│  Tool  │      │   API   │
│ Files  │      │ Database│
└────────┘      └─────────┘
```

### 4.2 Configuration File Details

**Locations:**
- User-level: `~/.claude.json`
- Project-level: `.mcp.json`

**Complete Configuration Example:**

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "~/Documents",
        "~/Projects"
      ],
      "env": {
        "NODE_ENV": "production"
      }
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "postgres": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

### 4.3 Core MCP Servers

#### 1. Filesystem Server

**Features**: Read/write files, directory operations

```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Projects
```

**Usage Example:**
```
You: Read ~/Projects/app/src/index.ts and add type annotations

Claude: [Using filesystem MCP]
File read, adding type annotations...
```

#### 2. GitHub Server

**Features**: Manage issues, PRs, repositories

```bash
claude mcp add github -s user -e GITHUB_TOKEN=ghp_xxx -- npx -y @modelcontextprotocol/server-github
```

**Usage Example:**
```
You: Create a PR to merge feature branch to main

Claude: [Using github MCP]
Created PR #123: "Add new feature"
Reviewers: @reviewer
Labels: feature, ready-for-review
```

#### 3. Database Server

**Features**: Query, analyze databases

```bash
claude mcp add postgres -s user -e DATABASE_URL=postgresql://... -- npx -y @modelcontextprotocol/server-postgres
```

**Usage Example:**
```
You: Query user registration trends for the last 7 days

Claude: [Using postgres MCP]
SELECT DATE(created_at), COUNT(*)
FROM users
WHERE created_at >= NOW() - INTERVAL '7 days'
GROUP BY DATE(created_at);

[Shows chart and analysis]
```

#### 4. Web Search Server

**Features**: Search for latest information

```bash
claude mcp add search -s user -e BRAVE_API_KEY=xxx -- npx -y @modelcontextprotocol/server-brave-search
```

#### 5. Puppeteer Server

**Features**: Browser automation, screenshots

```bash
claude mcp add puppeteer -s user -- npx -y @modelcontextprotocol/server-puppeteer
```

### 4.4 Custom MCP Server

**Create Custom Server:**

```javascript
// my-mcp-server.js
import { Server } from '@modelcontextprotocol/sdk';

const server = new Server({
  name: 'my-custom-server',
  version: '1.0.0',
});

// Define tools
server.setRequestHandler('tools/list', async () => {
  return {
    tools: [{
      name: 'process_text',
      description: 'Process text: uppercase, lowercase, reverse',
      inputSchema: {
        type: 'object',
        properties: {
          text: { type: 'string' },
          operation: {
            type: 'string',
            enum: ['uppercase', 'lowercase', 'reverse']
          }
        },
        required: ['text', 'operation']
      }
    }]
  };
});

// Implement tool logic
server.setRequestHandler('tools/call', async (request) => {
  const { name, arguments: args } = request.params;

  if (name === 'process_text') {
    const { text, operation } = args;
    let result;

    switch (operation) {
      case 'uppercase':
        result = text.toUpperCase();
        break;
      case 'lowercase':
        result = text.toLowerCase();
        break;
      case 'reverse':
        result = text.split('').reverse().join('');
        break;
    }

    return {
      content: [{ type: 'text', text: result }]
    };
  }
});

server.start();
```

**Add Custom Server:**

```bash
# Install dependencies
npm install @modelcontextprotocol/sdk

# Add server
claude mcp add my-server -- node /path/to/my-mcp-server.js
```

---

## V. Prompt Engineering

### 5.1 Basic Prompt Principles

#### 1. Be Specific

```
❌ Bad Example:
"Help me improve this code"

✅ Good Example:
"Refactor the login function in src/utils/auth.ts with requirements:
1. Add input validation
2. Improve error handling
3. Add JSDoc comments
4. Replace Promise with async/await"
```

#### 2. Provide Context

```
❌ Bad Example:
"Add a button"

✅ Good Example:
"Add a 'Forgot Password' button to the LoginPage component (src/pages/Login.tsx):
- Position: Below login button
- Style: Follow existing Tailwind CSS design system
- Click action: Navigate to /forgot-password route
- Add corresponding unit tests"
```

#### 3. Step-by-Step Instructions

```
❌ Bad Example:
"Create a complete user management system"

✅ Good Example:
"Create user management system following these steps:

Step 1: Data Model
- Design User entity (id, email, name, role, created_at)
- Create TypeScript interfaces

Step 2: API Routes
- POST /api/users - Create user
- GET /api/users - List users
- GET /api/users/:id - Get user details
- PUT /api/users/:id - Update user
- DELETE /api/users/:id - Delete user

Step 3: Frontend Components
- UserList - User list
- UserForm - User form
- UserDetail - User details

Step 4: Testing
- API integration tests
- Component unit tests"
```

### 5.2 Advanced Prompt Techniques

#### 1. Role Setting

```
You are a senior TypeScript backend engineer specializing in:
- Type safety
- Performance optimization
- Security best practices
- RESTful API design

Please help me design a high-performance user authentication API...
```

#### 2. Chain of Thought

```
Before implementing this feature, please:

1. Analyze requirements and edge cases
2. Design data structures and algorithms
3. Consider performance and security issues
4. Propose implementation plan
5. Write code

Then execute step by step.
```

#### 3. Constraints

```
Implement shopping cart functionality with requirements:

Must satisfy:
- Thread safety (multi-user concurrent)
- Data consistency
- Performance (< 100ms response)

Cannot use:
- Global variables
- Synchronous I/O
- Deprecated APIs

Recommended to use:
- Redis cache
- Database transactions
- Asynchronous operations
```

#### 4. Example References

```
Reference the implementation style of src/services/OrderService.ts:
- Use dependency injection
- Error handling patterns
- Logging methods

Create a similar PaymentService...
```

### 5.3 Prompt Template Library

#### Feature Development Template

```
Implement [Feature Name]

Requirements:
[Detailed requirement description]

Tech Stack:
- [Tech 1]
- [Tech 2]

File Location:
[File path]

Requirements:
1. [Requirement 1]
2. [Requirement 2]

Acceptance Criteria:
- [ ] [Criterion 1]
- [ ] [Criterion 2]

Reference Implementation:
[Related code files]
```

#### Code Review Template

```
Please review the following code:

File: [File path]

Focus Areas:
1. Code quality
2. Performance issues
3. Security vulnerabilities
4. Best practices
5. Test coverage

Provide:
- List of issues
- Improvement suggestions
- Refactored code
```

#### Bug Fix Template

```
Fix Bug

Problem Description:
[Detailed problem description]

Reproduction Steps:
1. [Step 1]
2. [Step 2]

Expected Behavior:
[How it should work]

Actual Behavior:
[What actually happens]

Related Files:
[Potentially related files]

Error Logs:
```
[Paste error message]
```

Please:
1. Analyze root cause
2. Provide fix solution
3. Implement fix
4. Add tests to prevent regression
```

#### Performance Optimization Template

```
Optimize Performance

Target:
[Specific performance goal, e.g., response time < 100ms]

Current Performance:
[Current metrics]

Bottleneck Analysis:
[Known performance bottlenecks]

Optimization Scope:
[Code/modules to optimize]

Requirements:
1. Analyze performance bottlenecks
2. Provide optimization plan
3. Implement optimization
4. Test and verify

Constraints:
- Don't change existing API
- Maintain code readability
- Add performance tests
```

---

## VI. File Operations

### 6.1 Reading Files

```
# Basic read
Read src/index.ts

# Read multiple files
Read the following files:
- src/index.ts
- src/utils.ts
- src/types.ts

# Read and analyze
Read src/api/users.ts and analyze:
1. API endpoints
2. Data validation
3. Error handling
4. Potential security issues
```

### 6.2 Writing Files

```
# Create new file
Create src/services/EmailService.ts implementing email sending functionality

# Modify existing file
Modify src/config/database.ts to add Redis configuration

# Batch create
Create the following files:
1. src/models/User.ts - User model
2. src/models/Post.ts - Post model
3. src/models/Comment.ts - Comment model
```

### 6.3 Search and Replace

```
# Global search
Search for all uses of console.log in the project

# Search and replace
Replace all var declarations with const/let

# Regex search
Search for all unused import statements (regex: ^import .* from .* that is never used)

# Rename
Rename function getUserById to fetchUserById (all references)
```

### 6.4 Code Refactoring

```
# Extract function
From src/components/UserProfile.tsx's render method, extract the following logic as separate functions:
- User avatar rendering
- User info display
- Action button group

# Extract component
Extract statistics card from src/pages/Dashboard.tsx as StatsCard component

# Refactor class
Refactor src/services/PaymentService.ts to:
1. Extract interface IPaymentService
2. Separate payment gateway logic as PaymentGateway
3. Use dependency injection
```

### 6.5 Batch Operations

```
# Batch rename
Rename all component files in src/components/ directory:
- From kebab-case.tsx to PascalCase.tsx
- Update all import paths

# Batch add
Add to all files in src/api/ directory:
1. Error handling middleware
2. Request logging
3. Response time statistics

# Batch format
Format all TypeScript files in src/ directory using Prettier
```

---

## VII. Git Workflow

### 7.1 Basic Git Operations

```
# View status
Show current git status

# View diff
Show unstaged changes

# Stage changes
Stage all changes

# Commit
Create commit with message: "feat: add user authentication"

# Push
Push to remote repository
```

### 7.2 Branch Management

```
# Create branch
Create and switch to new branch feature/user-profile

# Merge branch
Merge feature/user-profile into main

# Resolve conflicts
Help me resolve merge conflicts:
1. Analyze conflicts
2. Provide solution
3. Keep correct code

# Delete branch
Delete merged feature/user-profile branch
```

### 7.3 Commit Standards

#### Conventional Commits

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation update
- `style`: Code format (doesn't affect code execution)
- `refactor`: Refactoring
- `perf`: Performance optimization
- `test`: Testing
- `chore`: Build/toolchain

**Example:**

```bash
feat(auth): add JWT token refresh mechanism

- Implement refresh token rotation
- Add token expiration check
- Update API documentation

Closes #123
```

### 7.4 Pull Request Flow

```
Create Pull Request including:

Title:
[Concise description, e.g., "Add user authentication system"]

Description:
## Overview of Changes
[Describe main changes]

## Change Type
- [ ] New feature
- [ ] Bug fix
- [ ] Refactoring
- [ ] Documentation

## Testing
- [ ] Unit tests
- [ ] Integration tests
- [ ] Manual testing

## Checklist
- [ ] Code passes lint
- [ ] All tests pass
- [ ] Documentation updated
- [ ] No breaking changes

## Screenshots
[If there are UI changes]

## Related Issues
Closes #123
```

### 7.5 Code Review

```
Review Pull Request #123

Focus Areas:
1. Code Quality
   - Naming conventions
   - Code complexity
   - Duplicate code

2. Functional Correctness
   - Logic correctness
   - Edge cases
   - Error handling

3. Performance
   - Algorithm efficiency
   - Database queries
   - Cache usage

4. Security
   - Input validation
   - Authentication/Authorization
   - Sensitive data

5. Testing
   - Test coverage
   - Test quality
   - Edge case testing

Provide:
- Specific improvement suggestions
- Code examples
- Approve/Request changes
```

---

## VIII. Project Management

### 8.1 Project Initialization

```
Initialize new project:

Project Type: Web Application
Tech Stack:
- Frontend: React 18 + TypeScript + Vite
- Backend: Node.js + Express + TypeScript
- Database: PostgreSQL
- Styling: Tailwind CSS

Create:
1. Project structure
2. Configuration files
3. README.md
4. .gitignore
5. Basic dependencies
6. Development scripts
```

### 8.2 Project Structure

**Recommended Structure:**

```
project-root/
├── .claude.json              # Claude Code configuration
├── CLAUDE.md                 # Project context
├── .mcp.json                 # MCP server configuration
├── .env.example              # Environment variables template
├── .gitignore
├── package.json
├── tsconfig.json
├── README.md
├── docs/                     # Documentation
│   ├── API.md
│   ├── ARCHITECTURE.md
│   └── CONTRIBUTING.md
├── src/
│   ├── components/           # Components
│   ├── services/             # Business logic
│   ├── utils/                # Utility functions
│   ├── types/                # TypeScript types
│   ├── hooks/                # React Hooks
│   ├── config/               # Configuration
│   └── tests/                # Tests
└── scripts/                  # Scripts
```

### 8.3 Documentation Management

#### API Documentation

```
Generate API documentation:

Analyze all routes in src/api/ directory
Create docs/API.md including:

For each endpoint:
- HTTP method and path
- Request parameters
- Request body example
- Response example
- Error codes
- Usage example (curl)

Format: OpenAPI 3.0 style
```

#### Architecture Documentation

```
Generate architecture documentation:

Analyze project structure
Create docs/ARCHITECTURE.md including:

1. System overview
2. Architecture diagram
3. Tech stack
4. Data flow
5. Core modules
6. Design decisions
7. Performance considerations
8. Security measures
```

### 8.4 Dependency Management

```
# Analyze dependencies
Analyze package.json, check:
1. Outdated dependencies
2. Security vulnerabilities
3. Unused dependencies
4. Duplicate dependencies

# Update dependencies
Safely update all dependencies, avoid breaking changes

# Add dependency
Add axios and configure interceptors

# Remove dependency
Remove lodash and replace with native JavaScript
```

### 8.5 Script Management

**package.json scripts:**

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "lint": "eslint . --ext ts,tsx",
    "lint:fix": "eslint . --ext ts,tsx --fix",
    "format": "prettier --write \"src/**/*.{ts,tsx}\"",
    "type-check": "tsc --noEmit",
    "prepare": "husky install"
  }
}
```

---

## IX. Performance Optimization

### 9.1 Code Optimization

```
Optimize Performance

File: src/components/LargeList.tsx

Problems:
- Lag when rendering 10,000 items
- Re-renders entire list on every state update

Requirements:
1. Implement virtual scrolling
2. Optimize with React.memo
3. Implement incremental loading
4. Add performance monitoring

Goals:
- Initial render < 100ms
- Smooth scrolling (60fps)
- Memory usage < 100MB
```

### 9.2 Build Optimization

```
Optimize Build Performance

Current Issues:
- Build time > 5 minutes
- Bundle size > 5MB
- First screen load > 3s

Optimization Items:
1. Code splitting
2. Tree shaking
3. Compression optimization
4. CDN resources
5. Lazy loading

Config file: vite.config.ts
```

### 9.3 Database Optimization

```
Optimize Database Query

Problem Query:
```sql
SELECT * FROM users
WHERE created_at > '2024-01-01'
ORDER BY created_at DESC
```

Issues:
- Full table scan
- Query time > 5s
- Returns unnecessary columns

Requirements:
1. Add indexes
2. Optimize query
3. Implement pagination
4. Caching strategy

Goal: < 100ms
```

### 9.4 API Optimization

```
Optimize API Performance

Endpoint: GET /api/users

Current Issues:
- Response time > 2s
- N+1 query problem
- No caching

Optimization Plan:
1. Database query optimization
2. Add Redis cache
3. Response compression
4. Implement GraphQL (optional)

Goals:
- Response time < 200ms
- Support 1000 concurrent requests/s
```

### 9.5 Performance Monitoring

```
Add Performance Monitoring

Requirements:
1. Frontend Performance Monitoring
   - FCP, LCP, CLS
   - Route switching time
   - API request time

2. Backend Performance Monitoring
   - Request response time
   - Database query time
   - Memory and CPU usage

3. Alert Mechanism
   - Response time exceeds threshold
   - Error rate exceeds 1%
   - Abnormal resource usage

Tools:
- Frontend: Web Vitals
- Backend: Prometheus + Grafana
```

---

## X. Security Best Practices

### 10.1 Input Validation

```
Add Input Validation

File: src/api/users.ts

Endpoint: POST /api/users

Validation Rules:
1. email
   - Required
   - Valid email format
   - Max length 255

2. password
   - Required
   - Min length 8
   - Contains uppercase, lowercase, numbers, special characters

3. name
   - Required
   - Min length 2
   - Max length 50
   - Can only contain letters, spaces, hyphens

Use:
- Zod for validation
- Return detailed error messages
- Prevent SQL injection
```

### 10.2 Authentication & Authorization

```
Implement JWT Authentication

Requirements:
1. User Registration/Login
   - Password encryption (bcrypt)
   - JWT token generation
   - Refresh token mechanism

2. Authentication Middleware
   - Token verification
   - Permission checking
   - Rate limiting

3. Authorization
   - Role-based (RBAC)
   - Resource permission checking

4. Security Measures
   - Token expiration policy
   - CSRF protection
   - XSS protection
```

### 10.3 Data Security

```
Protect Sensitive Data

Requirements:
1. Encryption
   - Passwords: bcrypt
   - Sensitive fields: AES-256
   - Transmission: HTTPS/TLS

2. Data Masking
   - Sensitive info in logs
   - Passwords in API responses
   - Error messages

3. Access Control
   - Principle of least privilege
   - Audit logs
   - Data backups

Implementation Location:
- src/utils/encryption.ts
- src/middleware/sanitize.ts
```

### 10.4 API Security

```
Harden API Security

Measures:
1. Authentication
   - JWT/OAuth 2.0
   - API Key

2. Authorization
   - Resource-level permissions
   - Rate limiting

3. Input Validation
   - Parameter validation
   - Injection prevention

4. Output Encoding
   - XSS protection
   - JSON encoding

5. CORS
   - Whitelist configuration
   - Preflight requests

6. Request Limiting
   - Rate limiting
   - Payload size limits
   - Timeout settings

Implementation Files:
- src/middleware/security.ts
- src/config/cors.ts
```

### 10.5 Dependency Security

```
Check Dependency Security

Tasks:
1. Scan Vulnerabilities
   npm audit
   npm audit fix

2. Update Dependencies
   - Security patches
   - Verify updates

3. Supply Chain Security
   - Verify package signatures
   - Use lock files
   - Review new dependencies

4. Automation
   - Dependabot
   - Snyk
   - GitHub Security Alerts

Generate Report: security-report.md
```

---

## XI. Troubleshooting

### 11.1 Common Errors

#### Error 1: API Connection Failed

**Symptom:**
```
Error: Could not connect to Anthropic API
```

**Causes:**
- Invalid or expired API key
- Network connection issues
- API service disruption

**Solutions:**

```bash
# 1. Check API key
claude auth status

# 2. Test network connection
curl https://api.anthropic.com/v1/messages

# 3. Update API key
claude auth login

# 4. Check proxy settings
echo $HTTPS_PROXY
```

#### Error 2: MCP Server Won't Start

**Symptom:**
```
Error: MCP server 'filesystem' failed to start
```

**Causes:**
- Server configuration error
- Dependencies not installed
- Permission issues

**Solutions:**

```bash
# 1. Test server
claude mcp test filesystem

# 2. View logs
claude debug logs --filter mcp

# 3. Reinstall
claude mcp remove filesystem
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents

# 4. Check permissions
ls -la ~/.claude.json
```

#### Error 3: Context Window Overflow

**Symptom:**
```
Error: Context window exceeded
```

**Causes:**
- Too many files included
- Files too large
- Conversation history too long

**Solutions:**

```bash
# 1. Clear history
/clear

# 2. Limit included files
Edit .claude.json:
{
  "context": {
    "include": ["src/**/*.ts"],
    "exclude": ["**/*.test.ts", "dist/**"]
  }
}

# 3. Use larger model
claude chat --model claude-opus-4

# 4. Process in batches
Split large task into smaller tasks
```

### 11.2 Performance Issues

#### Issue 1: Slow Response

**Symptoms:**
- Reply delay > 30s
- Frequent timeouts

**Diagnosis:**

```bash
# 1. Check network
ping api.anthropic.com

# 2. View detailed logs
claude --debug chat

# 3. Check context size
claude project info --stats

# 4. Monitor resources
top
```

**Optimization:**

```bash
# 1. Reduce context
Limit number of included files

# 2. Use faster model
claude chat --model claude-sonnet-4

# 3. Clear cache
rm -rf ~/.claude/cache/*

# 4. Configure proxy (if needed)
export HTTPS_PROXY=http://proxy:port
```

#### Issue 2: High Memory Usage

**Symptoms:**
- Memory usage > 2GB
- System slowdown

**Solutions:**

```bash
# 1. Clear history
claude history clear

# 2. Limit cache size
claude config set cacheSize 100MB

# 3. Restart Claude
killall claude
claude chat

# 4. Optimize configuration
Reduce maxTokens
Reduce included files
```

### 11.3 Configuration Issues

#### Issue 1: Configuration Not Taking Effect

**Check Steps:**

```bash
# 1. Verify config file syntax
cat .claude.json | jq .

# 2. Check configuration priority
# Project config > User config > Default config

# 3. View effective configuration
claude config list

# 4. Test configuration
claude chat --config .claude.json --verbose
```

#### Issue 2: Environment Variable Conflicts

```bash
# 1. View all environment variables
env | grep CLAUDE
env | grep ANTHROPIC

# 2. Clear conflicting variables
unset CLAUDE_API_KEY
unset ANTHROPIC_API_KEY

# 3. Reset
export ANTHROPIC_API_KEY=sk-ant-api03-...

# 4. Verify
echo $ANTHROPIC_API_KEY
claude auth status
```

### 11.4 Debugging Techniques

#### 1. Enable Debug Mode

```bash
# Verbose output
claude --verbose chat

# Debug mode
claude --debug chat

# Enable both
claude --verbose --debug chat
```

#### 2. View Logs

```bash
# View logs in real-time
tail -f ~/.claude/logs/claude.log

# View error logs
grep ERROR ~/.claude/logs/claude.log

# View recent logs
tail -n 100 ~/.claude/logs/claude.log
```

#### 3. Test Connection

```bash
# Test API
claude auth status

# Test MCP
claude mcp test --all

# Diagnose system
claude doctor
```

#### 4. Reset Configuration

```bash
# Backup current configuration
cp ~/.claude.json ~/.claude.json.backup

# Reset configuration
claude config reset

# Reconfigure
claude init
```

---

## XII. Production Environment Practices

### 12.1 CI/CD Integration

#### GitHub Actions

**.github/workflows/claude-review.yml:**

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  code-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Configure Claude
        run: |
          echo "${{ secrets.ANTHROPIC_API_KEY }}" | claude auth login --stdin

      - name: Run Code Review
        run: |
          claude analyze --format json > review.json

      - name: Comment PR
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const review = JSON.parse(fs.readFileSync('review.json'));

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## Claude Code Review\n\n${review.summary}`
            });
```

#### GitLab CI

**.gitlab-ci.yml:**

```yaml
stages:
  - review
  - test
  - deploy

claude-review:
  stage: review
  image: node:20
  before_script:
    - npm install -g @anthropic-ai/claude-code
    - echo "$ANTHROPIC_API_KEY" | claude auth login --stdin
  script:
    - claude analyze --report review.md
  artifacts:
    reports:
      codequality: review.md
  only:
    - merge_requests
```

### 12.2 Docker Integration

**Dockerfile:**

```dockerfile
FROM node:20-slim

# Install Claude Code
RUN npm install -g @anthropic-ai/claude-code

# Set working directory
WORKDIR /workspace

# Configure Claude
ARG ANTHROPIC_API_KEY
ENV ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY

# Copy project configuration
COPY .claude.json /root/.claude/config.json
COPY CLAUDE.md .

# Entry point
ENTRYPOINT ["claude"]
CMD ["chat"]
```

**docker-compose.yml:**

```yaml
version: '3.8'

services:
  claude-code:
    build: .
    volumes:
      - ./:/workspace
      - ~/.claude:/root/.claude
    environment:
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    stdin_open: true
    tty: true
```

**Usage:**

```bash
# Build image
docker build -t claude-code .

# Run
docker run -it --rm \
  -v $(pwd):/workspace \
  -e ANTHROPIC_API_KEY \
  claude-code

# Use docker-compose
docker-compose run claude-code
```

### 12.3 Team Collaboration

#### Team Configuration

**Project root: .claude.json**

```json
{
  "name": "team-project",
  "version": "1.0.0",
  "model": "claude-sonnet-4",
  "context": {
    "include": [
      "src/**/*.{ts,tsx}",
      "docs/**/*.md",
      "package.json",
      "tsconfig.json"
    ],
    "exclude": [
      "node_modules/**",
      "dist/**",
      "**/*.test.ts"
    ]
  },
  "rules": [
    "Use TypeScript strict mode",
    "Follow Airbnb code standards",
    "All functions must have JSDoc comments",
    "Test coverage must be > 80%",
    "Commit messages follow Conventional Commits"
  ],
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

#### Environment Variable Management

**.env.example:**

```bash
# API Keys
ANTHROPIC_API_KEY=sk-ant-api03-...
GITHUB_TOKEN=ghp_...

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/db

# Redis
REDIS_URL=redis://localhost:6379

# Application
NODE_ENV=development
PORT=3000
```

**Team Documentation: CLAUDE.md**

```markdown
# Team Development Guide

## Project Overview
[Project description]

## Development Process

### 1. Environment Setup
```bash
# Install dependencies
npm install

# Configure environment variables
cp .env.example .env.local
# Edit .env.local and fill in your credentials

# Start development server
npm run dev
```

### 2. Development Standards

#### Branch Naming
- feature/feature-name
- fix/bug-description
- refactor/refactor-description

#### Commit Standards
Follow Conventional Commits:
- feat: New feature
- fix: Bug fix
- docs: Documentation update
- refactor: Refactoring

#### Code Review
- All code must be reviewed
- At least 1 approval required
- CI checks must pass

### 3. Using Claude Code

#### Start
```bash
claude chat
```

#### Common Commands
- Implement feature: Detailed requirement description
- Fix bug: Provide error info and reproduction steps
- Code review: Request review of specific files
- Generate tests: Generate tests for existing code

### 4. Best Practices

#### Prompt Techniques
1. Provide sufficient context
2. Clear specific requirements
3. Reference existing code style
4. Request tests and documentation

#### File Operations
1. Clearly specify file paths
2. Describe reason for changes
3. Request consistency

#### Git Integration
1. Use semantic commit messages
2. Each PR focuses on single feature
3. Update documentation promptly
```

### 12.4 Monitoring and Logging

#### Application Monitoring

**Monitoring Configuration: monitoring.ts**

```typescript
import { createLogger } from 'winston';
import { PrometheusExporter } from '@opentelemetry/exporter-prometheus';

// Logging configuration
export const logger = createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({
      filename: 'error.log',
      level: 'error'
    }),
    new winston.transports.File({
      filename: 'combined.log'
    }),
  ],
});

// Metrics export
export const metricsExporter = new PrometheusExporter({
  port: 9464,
});

// Claude Code usage statistics
export function trackClaudeUsage(operation: string, duration: number) {
  logger.info('Claude operation', {
    operation,
    duration,
    timestamp: new Date().toISOString(),
  });

  // Record to Prometheus
  claudeOperationDuration.record(duration, { operation });
}
```

#### Error Tracking

**Integrate Sentry:**

```typescript
import * as Sentry from '@sentry/node';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 1.0,
});

// Capture Claude Code errors
process.on('uncaughtException', (error) => {
  Sentry.captureException(error);
  logger.error('Uncaught exception', { error });
});
```

### 12.5 Performance Optimization

#### Caching Strategy

```typescript
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

// Cache Claude responses
export async function getCachedResponse(
  prompt: string
): Promise<string | null> {
  const cacheKey = `claude:${hash(prompt)}`;
  return await redis.get(cacheKey);
}

export async function cacheResponse(
  prompt: string,
  response: string,
  ttl: number = 3600
): Promise<void> {
  const cacheKey = `claude:${hash(prompt)}`;
  await redis.setex(cacheKey, ttl, response);
}
```

#### Request Queue

```typescript
import Queue from 'bull';

const claudeQueue = new Queue('claude-requests', {
  redis: process.env.REDIS_URL
});

// Process queue tasks
claudeQueue.process(async (job) => {
  const { prompt, options } = job.data;

  // Call Claude Code
  const response = await executeClaudeRequest(prompt, options);

  return response;
});

// Add task to queue
export async function queueClaudeRequest(
  prompt: string,
  options: any
): Promise<Job> {
  return await claudeQueue.add({
    prompt,
    options
  }, {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 2000
    }
  });
}
```

---

## XIII. Appendix

### 13.1 Shortcuts and Commands

#### Interactive Session Shortcuts

| Shortcut | Function |
|----------|----------|
| `Ctrl+C` | Interrupt current operation |
| `Ctrl+D` | Exit session |
| `↑` / `↓` | Navigate command history |
| `Tab` | Auto-complete |
| `Ctrl+L` | Clear screen |

#### Built-in Commands

| Command | Description |
|---------|-------------|
| `/help` | Show help |
| `/clear` | Clear conversation history |
| `/history` | View history |
| `/save` | Save current session |
| `/load` | Load saved session |
| `/context` | View current context |
| `/mcp` | MCP server status |
| `/config` | View configuration |
| `/exit` | Exit |

### 13.2 Model Selection Guide

| Model | Use Case | Features | Cost |
|-------|----------|----------|------|
| Claude Opus 4 | Complex tasks | Strongest capability | High |
| Claude Sonnet 4 | Daily development | Balanced performance | Medium |
| Claude Haiku 3 | Simple tasks | Fast response | Low |

**Selection Recommendations:**

- **Opus**: Architecture design, complex refactoring, performance optimization
- **Sonnet**: Feature development, code review, bug fixes
- **Haiku**: Simple modifications, formatting, documentation generation

### 13.3 Resource Links

**Official Resources:**
- [Claude Code Official Docs](https://docs.anthropic.com/claude/docs/claude-code)
- [MCP Protocol Specification](https://github.com/anthropics/model-context-protocol)
- [Anthropic API Docs](https://docs.anthropic.com/claude/reference)

**Community Resources:**
- [GitHub Issues](https://github.com/anthropics/claude-code/issues)
- [Discord Community](https://discord.gg/anthropic)
- [Forum Discussion](https://forum.anthropic.com)

**Learning Resources:**
- [Prompt Engineering Guide](https://docs.anthropic.com/claude/docs/prompt-engineering)
- [Best Practices Collection](https://docs.anthropic.com/claude/docs/best-practices)
- [Example Projects](https://github.com/anthropics/claude-code-examples)

### 13.4 Glossary

| Term | Description |
|------|-------------|
| **MCP** | Model Context Protocol |
| **Token** | Text unit, approximately 0.75 English words |
| **Context Window** | Maximum tokens the model can process at once |
| **Prompt** | Instructions sent to AI |
| **Temperature** | Temperature parameter, controls output randomness (0-1) |
| **System Prompt** | System prompt that defines AI behavior |
| **Few-shot** | Few-shot learning, providing examples to improve output |
| **Fine-tuning** | Fine-tuning, training model with specific data |

### 13.5 FAQ

**Q: Is Claude Code free?**
A: The Claude Code CLI tool itself is free, but requires an Anthropic API key. API calls are billed based on usage.

**Q: How to check API usage?**
A: Log in to [Anthropic Console](https://console.anthropic.com/) to view detailed usage statistics.

**Q: Does it support offline use?**
A: No. Claude Code requires an internet connection to call the Anthropic API.

**Q: Is my data secure?**
A: Data sent to the API is not used for model training. See [Privacy Policy](https://www.anthropic.com/privacy).

**Q: How to upgrade to the latest version?**
A: Run `npm update -g @anthropic-ai/claude-code`

**Q: Which programming languages are supported?**
A: Supports all mainstream programming languages, including TypeScript, Python, Java, Go, Rust, etc.

**Q: Can it be used within a company?**
A: Yes. Consider checking [Enterprise Solutions](https://www.anthropic.com/claude/enterprise).

**Q: Where do MCP servers run?**
A: MCP servers run locally and communicate with Claude Code through inter-process communication.

---

## 📚 Summary

This guide covers:

✅ **Basic Features**
- Installation and configuration
- CLI commands
- File operations

✅ **Advanced Features**
- MCP integration
- Prompt engineering
- Git workflow

✅ **Production Practices**
- CI/CD integration
- Performance optimization
- Security best practices

✅ **Troubleshooting**
- Common errors
- Debugging techniques
- Performance optimization

### 🎯 Next Steps

1. **Beginners**: Start with [Lesson 1](./01-basics.md) for systematic learning
2. **Intermediate**: Check out [Lesson 9: Prompt Optimization](./09-prompt-optimization.md)
3. **Advanced**: Learn [Lesson 10: AI Agent System](./10-ai-agents.md)

### 💡 Continuous Learning

- Follow official updates
- Participate in community discussions
- Share usage experiences
- Contribute best practices

---

**This guide is continuously updated**. Follow the [GitHub Repository](https://github.com/0xfnzero/AI-Code-Tutorials) for the latest content.

📚 [Back to Tutorial Index](../README.md)
