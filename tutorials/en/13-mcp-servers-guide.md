# Lesson 13: Complete MCP Server Guide - From Beginner to Expert

> 🎯 **Learning Objective**: Master MCP server addition, configuration and usage to boost Claude Code functionality 10x

> 🔥 **January 2025 Update**: Includes the latest MCP configuration methods, common error solutions, and 10 tested practical MCP server recommendations. Solves 90%+ configuration issues!

## 📚 Course Overview

This course will guide you step-by-step on how to correctly add and configure MCP (Model Context Protocol) servers, transforming your Claude Code from a simple AI assistant into a powerful development partner.

⚡ **Quick Tip**: If you just want to quickly add an MCP server, jump directly to the ["30-Second Quick Start"](#30-second-quick-start) section.

## 🎯 What is MCP?

### MCP Core Concepts

MCP (Model Context Protocol) is an open-source communication standard launched by Anthropic. It's like a "Swiss Army knife" for AI assistants.

**MCP allows Claude Code to:**

- 📁 **Directly access and manipulate the local file system**
- 🌐 **Connect to various APIs and network services**
- 🗄️ **Query and manipulate databases**
- 🛠️ **Integrate various development tools**
- 🔧 **Automate daily tasks**

### MCP Architecture

```
┌─────────────────┐
│  Claude Code    │
│  (MCP Client)   │
└────────┬────────┘
         │
    MCP Protocol
         │
┌────────┴────────┐
│  MCP Server     │
├─────────────────┤
│ • File System   │
│ • Database      │
│ • API Services  │
│ • Dev Tools     │
└─────────────────┘
```

---

## ⚡ 30-Second Quick Start

If you're in a hurry, this is the fastest way to add:

```bash
# Add file system access (most common)
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Desktop

# Verify success
claude mcp list
```

**It's that simple!** But if you encounter errors, please continue reading the detailed guide.

---

## 📖 Detailed Adding Steps

### Method 1: Command Line Addition (Recommended for Beginners)

Claude Code provides simple command-line tools to add MCP servers.

#### Basic Syntax

```bash
claude mcp add <name> <command> [arguments...]
```

#### Real Examples

**1. Add Local File System Access**

```bash
claude mcp add my-filesystem -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**2. Example with Environment Variables**

```bash
claude mcp add api-server -e API_KEY=your-key-here -- /path/to/server
```

**3. Specify Scope**

```bash
# User level (global)
claude mcp add github -s user -- npx -y @modelcontextprotocol/server-github

# Project level (team shared)
claude mcp add project-tools -s project -- npx -y @your-team/mcp-tools
```

#### Command Line Parameter Description

| Parameter | Description | Example |
|-----------|-------------|---------|
| `-s, --scope` | Scope (local/user/project) | `-s user` |
| `-e, --env` | Environment variables | `-e API_KEY=xxx` |
| `--` | Separator, followed by command | `-- npx -y ...` |

---

### Method 2: Direct Configuration File Editing (Recommended for Advanced Users)

Many developers find the CLI wizard too tedious, especially when you have to restart if you make a mistake. Directly editing the configuration file is more efficient.

#### Step 1: Find Configuration File Location

**Configuration file path:**
- **macOS/Linux**: `~/.claude.json`
- **Windows**: `%USERPROFILE%\.claude.json`

#### Step 2: Edit Configuration File

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/username/Documents"],
      "env": {}
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "your-github-token"
      }
    },
    "database": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

#### Step 3: Restart Claude Code

```bash
# Restart for configuration to take effect
# Or simply close and reopen Claude Code
```

**Configuration File Structure Explanation:**

```json
{
  "mcpServers": {
    "<server-name>": {
      "type": "stdio",           // Communication type (usually stdio)
      "command": "<command>",    // Execution command (e.g., npx, node)
      "args": ["<arg1>", "<arg2>"], // Command arguments
      "env": {                   // Environment variables
        "KEY": "value"
      }
    }
  }
}
```

---

### Method 3: Project-Level Configuration (Recommended for Team Collaboration)

If you want team members to use the same MCP configuration.

#### Add Project-Level MCP Server

```bash
# Add project-level MCP server
claude mcp add shared-tools -s project -- npx -y @your-team/mcp-tools
```

This creates a `.mcp.json` file in the project root directory:

```json
{
  "mcpServers": {
    "shared-tools": {
      "command": "npx",
      "args": ["-y", "@your-team/mcp-tools"],
      "env": {}
    }
  }
}
```

#### Team Collaboration Best Practices

**1. Commit `.mcp.json` to git**

```bash
git add .mcp.json
git commit -m "Add team MCP configuration"
git push
```

**2. Document in README**

```markdown
## MCP Server Configuration

This project uses the following MCP servers:
- shared-tools: Team shared development tools
- project-db: Project database access

Installation: Project configuration loads automatically, no manual setup required.
```

**3. Handling Sensitive Information**

```json
{
  "mcpServers": {
    "api-service": {
      "command": "npx",
      "args": ["-y", "@company/api-server"],
      "env": {
        "API_KEY": "${API_KEY}"  // Use environment variables
      }
    }
  }
}
```

Then in `.env.local` (don't commit to git):

```bash
API_KEY=your-actual-api-key
```

---

## 🎚️ MCP Server Scope Details

Understanding scope is crucial for avoiding "server not found" errors.

### 1. Local Scope (Default)

**Characteristics:**
- Only available in current directory
- Configuration stored in `projects` section of `~/.claude.json`
- **Suitable for**: Personal project-specific tools

**Configuration Example:**

```json
{
  "projects": {
    "/path/to/my-project": {
      "mcpServers": {
        "local-tool": {
          "command": "npx",
          "args": ["-y", "@my/local-tool"]
        }
      }
    }
  }
}
```

**Usage Scenario:**
```
You: [in project A directory]
    Use local-tool to process files  ✅ Available

You: [in project B directory]
    Use local-tool to process files  ❌ Not available
```

---

### 2. User Scope (Global)

**Characteristics:**
- Available in all projects
- Add using `-s user` flag
- **Suitable for**: Common tools like file system, database clients

**Add Command:**

```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**Configuration Example:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "~/Documents"]
    }
  }
}
```

**Usage Scenario:**
```
You: [in any project]
    Read ~/Documents/file.txt  ✅ Available everywhere
```

---

### 3. Project Scope (Team Shared)

**Characteristics:**
- Shared via `.mcp.json` file
- Add using `-s project` flag
- **Suitable for**: Team-shared project-specific tools

**Add Command:**

```bash
claude mcp add team-tools -s project -- npx -y @team/tools
```

**Configuration File:** `.mcp.json`

```json
{
  "mcpServers": {
    "team-tools": {
      "command": "npx",
      "args": ["-y", "@team/tools"]
    }
  }
}
```

**Usage Scenario:**
```
Team Member A: git clone project  ✅ Automatically available
Team Member B: git clone project  ✅ Automatically available
Team Member C: git clone project  ✅ Automatically available
```

---

### Scope Selection Guide

| Scope | Config File | Use Case | Team Sharing |
|-------|------------|----------|--------------|
| Local | `~/.claude.json` (projects) | Personal temporary tools | ❌ |
| User | `~/.claude.json` | Personal common tools | ❌ |
| Project | `.mcp.json` | Project team tools | ✅ |

**Decision Tree:**

```
Need team sharing?
├─ Yes → Project scope
└─ No → Use in all projects?
         ├─ Yes → User scope
         └─ No → Local scope
```

---

## 🔧 10 Most Practical MCP Server Recommendations

Based on community feedback and practical experience, this is the most worthwhile MCP server list to install.

### 1. File System Access ⭐⭐⭐⭐⭐

**Purpose**: Let Claude directly read/write files, modify code

```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Projects ~/Desktop
```

**Features:**
- Read file contents
- Write/modify files
- Create/delete directories
- Search files

**Usage Example:**
```
You: Read ~/Projects/myapp/src/index.js and add error handling

Claude: [Using filesystem MCP]
File read, adding try-catch error handling...
```

---

### 2. GitHub Integration ⭐⭐⭐⭐⭐

**Purpose**: Manage issues, PRs, code reviews

```bash
# Requires GitHub Personal Access Token
claude mcp add github -s user -e GITHUB_TOKEN=ghp_your_token_here -- npx -y @modelcontextprotocol/server-github
```

**Features:**
- Create and manage issues
- Create and review pull requests
- View repository information
- Manage GitHub Actions

**Usage Example:**
```
You: Create an issue to report the login page bug

Claude: [Using github MCP]
Created issue #123: "Login page bug"
Labels: bug, ui
Assigned to: @you
```

---

### 3. Web Browser Control ⭐⭐⭐⭐

**Purpose**: Automate web operations, scraping, testing

```bash
claude mcp add puppeteer -s user -- npx -y @modelcontextprotocol/server-puppeteer
```

**Features:**
- Automate browser operations
- Web screenshots
- Data scraping
- E2E testing

**Usage Example:**
```
You: Visit example.com and take a screenshot

Claude: [Using puppeteer MCP]
Visited page and generated screenshot: screenshot.png
```

---

### 4. Database Connection (PostgreSQL) ⭐⭐⭐⭐

**Purpose**: Directly query and manipulate databases

```bash
claude mcp add postgres -s user -e DATABASE_URL=postgresql://user:pass@localhost/mydb -- npx -y @modelcontextprotocol/server-postgres
```

**Features:**
- Execute SQL queries
- View table structures
- Export data
- Data analysis

**Usage Example:**
```
You: Query the 10 most recently registered users from the users table

Claude: [Using postgres MCP]
SELECT * FROM users ORDER BY created_at DESC LIMIT 10;

Results:
1. john@example.com (2025-01-15)
2. jane@example.com (2025-01-14)
...
```

---

### 5. Fetch Tool (API Calls) ⭐⭐⭐⭐

**Purpose**: Call various REST APIs

```bash
claude mcp add fetch -s user -- npx -y @kazuph/mcp-fetch
```

**Features:**
- HTTP requests (GET, POST, PUT, DELETE)
- Handle JSON/XML responses
- Set request headers and authentication
- Error handling

**Usage Example:**
```
You: Call the weather API to get Beijing weather

Claude: [Using fetch MCP]
GET https://api.weather.com/v1/weather?city=Beijing

Response: Sunny, temperature 15°C, humidity 45%
```

---

### 6. Search Engine ⭐⭐⭐⭐

**Purpose**: Search for latest information

```bash
# Requires Brave Search API Key
claude mcp add search -s user -e BRAVE_API_KEY=your_key -- npx -y @modelcontextprotocol/server-brave-search
```

**Features:**
- Web search
- News search
- Image search
- Real-time information

**Usage Example:**
```
You: Search for "React 19 new features"

Claude: [Using search MCP]
Found the following results:
1. React 19 Official Release - Official Blog
2. React 19 Upgrade Guide - Medium
3. React 19 Performance Comparison - Dev.to
```

---

### 7. Slack Integration ⭐⭐⭐

**Purpose**: Send messages, manage channels

```bash
claude mcp add slack -s user -e SLACK_TOKEN=xoxb-your-token -- npx -y @modelcontextprotocol/server-slack
```

**Features:**
- Send messages to channels
- Create/manage channels
- Upload files
- Get message history

**Usage Example:**
```
You: Send a deployment complete message to the #dev-team channel

Claude: [Using slack MCP]
Sent to #dev-team:
"✅ Production deployment completed successfully!"
```

---

### 8. Time Management ⭐⭐⭐

**Purpose**: Time zone conversion, date calculations

```bash
claude mcp add time -s user -- npx -y @modelcontextprotocol/server-time
```

**Features:**
- Time zone conversion
- Date formatting
- Time calculations
- Business day calculations

**Usage Example:**
```
You: What time is 3 PM Beijing time tomorrow in New York?

Claude: [Using time MCP]
Beijing time: 2025-01-16 15:00 CST
New York time: 2025-01-16 02:00 EST
```

---

### 9. Memory Storage ⭐⭐⭐⭐

**Purpose**: Save information across conversations

```bash
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory
```

**Features:**
- Store key-value pairs
- Save data across sessions
- Temporary caching
- Configuration management

**Usage Example:**
```
You: Remember my API key is abc123

Claude: [Using memory MCP]
Saved: API_KEY = abc123

[Next conversation]
You: What is my API key?

Claude: [Reading from memory MCP]
Your API key is abc123
```

---

### 10. Sequential Thinking ⭐⭐⭐⭐⭐

**Purpose**: Think through complex problems step by step

```bash
claude mcp add thinking -s user -- npx -y @modelcontextprotocol/server-sequential-thinking
```

**Features:**
- Step-by-step thinking for complex problems
- Show reasoning process
- Improve answer quality
- Debug logic

**Usage Example:**
```
You: Design a highly available microservices architecture

Claude: [Using thinking MCP]
Let me think through this problem step by step...

Step 1: Identify core requirements
- High availability (99.9%+ uptime)
- Scalability
- Fault tolerance

Step 2: Choose architecture patterns
- Microservices vs monolith
- Synchronous vs asynchronous communication

Step 3: Design solution
[Detailed design...]
```

---

## 🐛 Common Errors and Solutions

### Error 1: Tool Name Validation Failed

**Error Message:**
```
API Error 400: "tools.11.custom.name: String should match pattern '^[a-zA-Z0-9_-]{1,64}'"
```

**Cause:**
- Server name contains illegal characters
- Name too long (exceeds 64 characters)
- Uses spaces or special characters

**Solution:**

```bash
# ❌ Wrong examples
claude mcp add "my server" -- ...        # Contains space
claude mcp add my@server -- ...          # Contains @ symbol
claude mcp add very-long-server-name-that-exceeds-the-maximum-allowed-length -- ...  # Too long

# ✅ Correct examples
claude mcp add my-server -- ...          # Use hyphen
claude mcp add my_server -- ...          # Use underscore
claude mcp add myserver -- ...           # Pure letters
```

**Naming Rules:**
- Only use letters (a-z, A-Z)
- Numbers (0-9)
- Hyphens (-)
- Underscores (_)
- Length not exceeding 64 characters

---

### Error 2: MCP Server Not Found

**Error Message:**
```
MCP server 'my-server' not found
```

**Cause Analysis and Solutions:**

**1. Check Scope Settings**

```bash
# View all MCP servers
claude mcp list

# Confirm server is in correct scope
# Local: Only available in specific directory
# User: Globally available
# Project: Project-level available
```

**2. Confirm in Correct Directory**

```bash
# For Local scope servers
pwd  # Check current directory
cd /path/to/your/project  # Switch to project directory
claude mcp list  # Check again
```

**3. Restart Claude Code**

```bash
# Exit and restart
# Or use command
claude --restart
```

**4. Manually Verify Configuration File**

```bash
# macOS/Linux
cat ~/.claude.json

# Windows
type %USERPROFILE%\.claude.json

# Check project configuration
cat .mcp.json
```

---

### Error 3: Protocol Version Error

**Error Message:**
```json
{
  "error": {
    "protocolVersion": "Required"
  }
}
```

**Cause:**
This is a known bug in Claude Code where the MCP server doesn't return the correct protocol version.

**Solutions:**

**Solution 1: Use Wrapper Script**

Create `mcp-wrapper.sh`:

```bash
#!/bin/bash
# mcp-wrapper.sh

# Run actual MCP server and inject protocol version
npx -y @modelcontextprotocol/server-filesystem "$@" | \
  jq 'if .jsonrpc then . + {protocolVersion: "2024-11-05"} else . end'
```

Make it executable:

```bash
chmod +x mcp-wrapper.sh
```

Use wrapper script:

```bash
claude mcp add filesystem -- ./mcp-wrapper.sh ~/Documents
```

**Solution 2: Update Claude Code**

```bash
# Update to latest version
npm update -g @anthropic-ai/claude-code

# Or use npx to force latest version
npx @anthropic-ai/claude-code@latest
```

**Solution 3: Wait for Fix**

This issue has been reported on GitHub, follow:
- https://github.com/anthropics/claude-code/issues/xxx

---

### Error 4: Windows Path Issues

**Error Message:**
```
Error: Cannot find module 'C:UsersusernameDocuments'
```

**Cause:**
Windows paths use backslashes (\), which need to be escaped or use forward slashes in configuration.

**Solutions:**

```bash
# ❌ Wrong: Using backslash
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem C:\Users\username\Documents

# ✅ Method 1: Use forward slash
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem C:/Users/username/Documents

# ✅ Method 2: Use double backslash
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem C:\\Users\\username\\Documents

# ✅ Method 3: Use environment variables
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem %USERPROFILE%/Documents
```

**In configuration file:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "C:/Users/username/Documents"  // Use forward slash
      ]
    }
  }
}
```

---

### Error 5: Permission Issues

**Error Message:**
```
Error: EACCES: permission denied
```

**Solutions:**

**macOS/Linux:**

```bash
# Method 1: Use user directory (recommended)
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents

# Method 2: Modify file permissions
chmod 755 /path/to/directory

# Method 3: Use sudo (not recommended)
sudo claude mcp add ...
```

**Windows:**

```powershell
# Method 1: Run PowerShell/CMD as administrator
# Right-click PowerShell/CMD -> "Run as administrator"

# Method 2: Modify folder permissions
# Right-click folder -> Properties -> Security -> Edit -> Add full control permissions
```

**Best Practices:**
- Install MCP servers in user directory
- Avoid operations requiring root/administrator privileges
- Use environment variables to manage sensitive paths

---

## 🔍 Debugging Techniques

When encountering issues, these debugging methods can help you quickly locate problems.

### 1. Enable Debug Mode

```bash
# Add --mcp-debug flag when starting
claude --mcp-debug

# Will show detailed MCP communication logs
```

**Output Example:**
```
[MCP Debug] Connecting to server: filesystem
[MCP Debug] Request: {"jsonrpc":"2.0","id":1,"method":"tools/list"}
[MCP Debug] Response: {"jsonrpc":"2.0","id":1,"result":{"tools":[...]}}
```

---

### 2. View MCP Status

```bash
# Input in Claude Code
/mcp

# Shows all MCP server statuses
```

**Output Example:**
```
MCP Servers Status:
✅ filesystem (user) - Connected
✅ github (user) - Connected
❌ database (local) - Disconnected
⚠️ api-service (project) - Not configured
```

---

### 3. View Log Files

**macOS/Linux:**

```bash
# View logs in real-time
tail -f ~/Library/Logs/Claude/mcp*.log

# View recent errors
grep "ERROR" ~/Library/Logs/Claude/mcp*.log | tail -20

# View specific server logs
grep "filesystem" ~/Library/Logs/Claude/mcp*.log
```

**Windows:**

```powershell
# View logs
type "%APPDATA%\Claude\logs\mcp*.log"

# View recent logs
Get-Content "$env:APPDATA\Claude\logs\mcp*.log" -Tail 50

# Search for errors
Select-String -Path "$env:APPDATA\Claude\logs\mcp*.log" -Pattern "ERROR"
```

---

### 4. Manually Test Server

```bash
# Run server command directly to see if there's output
npx -y @modelcontextprotocol/server-filesystem ~/Documents

# Should see JSON-RPC format output
```

**Normal Output Example:**
```json
{
  "jsonrpc": "2.0",
  "method": "server/ready",
  "params": {
    "protocolVersion": "2024-11-05"
  }
}
```

---

### 5. Verify Configuration File Syntax

```bash
# Use jq to verify JSON syntax
cat ~/.claude.json | jq .

# If there's syntax error, jq will report error
```

**Common Syntax Errors:**
```json
{
  "mcpServers": {
    "test": {
      "command": "npx",
      "args": ["test"],  // ❌ Extra comma at end
    }
  }
}
```

**Correct Example:**
```json
{
  "mcpServers": {
    "test": {
      "command": "npx",
      "args": ["test"]  // ✅ No comma
    }
  }
}
```

---

### 6. Run with Verbose Mode

```bash
# Enable verbose output
claude --verbose

# Combine with MCP debugging
claude --verbose --mcp-debug
```

---

## 🇨🇳 Special Notes for Chinese Users

### 1. Chinese Path Issues

**Problem:** Paths containing Chinese characters may cause errors.

**Avoid Using Chinese Paths:**

```bash
# ❌ Avoid
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/文档

# ✅ Recommended: Use English paths
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents

# ✅ Or use full path
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem /Users/username/Documents
```

**If You Must Use Chinese Paths:**

```bash
# Wrap with quotes
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem "~/文档"

# Or use URL encoding
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/\%E6\%96\%87\%E6\%A1\%A3
```

---

### 2. Proxy Configuration

**Problem:** Accessing npm in mainland China may be slow or fail.

**Configure Proxy:**

```bash
# HTTP proxy
npm config set proxy http://your-proxy:port
npm config set https-proxy http://your-proxy:port

# SOCKS proxy
npm config set proxy socks5://127.0.0.1:1080
npm config set https-proxy socks5://127.0.0.1:1080

# Then add MCP server
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**Remove Proxy:**

```bash
npm config delete proxy
npm config delete https-proxy
```

---

### 3. Domestic Mirror Sources

**Use Taobao npm mirror for faster downloads:**

**Temporary Use:**

```bash
claude mcp add fs -- npx -y --registry=https://registry.npmmirror.com @modelcontextprotocol/server-filesystem ~/Documents
```

**Permanent Setting:**

```bash
# Set Taobao mirror
npm config set registry https://registry.npmmirror.com

# Verify setting
npm config get registry

# Then add MCP server normally
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**Restore Official Source:**

```bash
npm config set registry https://registry.npmjs.org
```

---

### 4. Character Encoding Issues

**Problem:** Garbled characters may appear in Windows Chinese environment.

**Solutions:**

```powershell
# Windows PowerShell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# Windows CMD
chcp 65001
```

**Use UTF-8 in Configuration File:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "D:/Projects"],
      "env": {
        "LANG": "zh_CN.UTF-8"
      }
    }
  }
}
```

---

## 💡 Best Practice Recommendations

### 1. Add as Needed

**Don't add too many MCP servers at once**, it will affect performance.

```bash
# ❌ Not recommended: Add everything at once
claude mcp add server1 -- ...
claude mcp add server2 -- ...
claude mcp add server3 -- ...
# ... added 20 servers

# ✅ Recommended: Only add commonly used ones
claude mcp add filesystem -s user -- ...
claude mcp add github -s user -- ...
# Enough is good
```

**Recommendations:**
- Beginners: 2-3 common servers
- Intermediate: 5-7 common servers
- Advanced: Add dynamically as needed

---

### 2. Regular Cleanup

**Remove unused servers:**

```bash
# View all servers
claude mcp list

# Remove unused servers
claude mcp remove old-server

# Batch cleanup
for server in old1 old2 old3; do
  claude mcp remove $server
done
```

**Cleanup Strategy:**
- Review installed servers monthly
- Remove servers unused for 3 months
- Keep core servers (like filesystem)

---

### 3. Security First

**Only add trusted MCP servers**, especially those requiring network access.

**Security Checklist:**

- ✅ Official servers (@modelcontextprotocol/*)
- ✅ Servers from well-known companies/organizations
- ✅ GitHub projects with many stars and active maintenance
- ⚠️ Servers requiring sensitive permissions (file system, network)
- ❌ Servers from unknown sources

**Review Code:**

```bash
# View server source code
npm view @modelcontextprotocol/server-filesystem repository
# Visit GitHub to view code

# Check dependencies
npm view @modelcontextprotocol/server-filesystem dependencies
```

---

### 4. Backup Configuration

**Regularly backup `~/.claude.json` file:**

```bash
# macOS/Linux
cp ~/.claude.json ~/.claude.json.backup

# Or use date backup
cp ~/.claude.json ~/.claude.json.$(date +%Y%m%d)

# Windows
copy %USERPROFILE%\.claude.json %USERPROFILE%\.claude.json.backup
```

**Use Version Control:**

```bash
# Create config repository
mkdir ~/claude-config
cd ~/claude-config
git init

# Link config file
ln -s ~/.claude.json claude.json

# Commit changes
git add claude.json
git commit -m "Update MCP configuration"
```

---

### 5. Team Collaboration

**Use project scope to share common configurations.**

**Team Configuration Example:**

`.mcp.json`:

```json
{
  "mcpServers": {
    "team-db": {
      "command": "npx",
      "args": ["-y", "@company/db-mcp"],
      "env": {
        "DB_HOST": "${DB_HOST}",
        "DB_USER": "${DB_USER}"
      }
    },
    "team-api": {
      "command": "npx",
      "args": ["-y", "@company/api-mcp"]
    }
  }
}
```

`.env.example`:

```bash
# Team members copy as .env.local and fill in
DB_HOST=localhost
DB_USER=your_username
DB_PASSWORD=your_password
```

`README.md`:

```markdown
## MCP Configuration

1. Copy `.env.example` as `.env.local`
2. Fill in your database credentials
3. Run `claude` to use team MCP servers
```

---

## 🚀 Advanced Techniques

### 1. Create Custom MCP Server

If existing MCP servers don't meet your needs, you can create your own.

**Basic Server Example:**

`my-mcp-server.js`:

```javascript
import { Server } from '@modelcontextprotocol/sdk';

// Create server
const server = new Server({
  name: 'my-custom-server',
  version: '1.0.0',
});

// Define tool list
server.setRequestHandler('tools/list', async () => {
  return {
    tools: [{
      name: 'my_custom_tool',
      description: 'Custom tool: Process text',
      inputSchema: {
        type: 'object',
        properties: {
          text: {
            type: 'string',
            description: 'Text to process'
          },
          operation: {
            type: 'string',
            enum: ['uppercase', 'lowercase', 'reverse'],
            description: 'Operation type'
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

  if (name === 'my_custom_tool') {
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
      default:
        throw new Error(`Unknown operation: ${operation}`);
    }

    return {
      content: [{
        type: 'text',
        text: result
      }]
    };
  }

  throw new Error(`Unknown tool: ${name}`);
});

// Start server
server.start();
```

**Add Custom Server:**

```bash
# Install dependencies
npm install @modelcontextprotocol/sdk

# Add to Claude Code
claude mcp add my-server -- node /path/to/my-mcp-server.js
```

**Use Custom Server:**

```
You: Use my_custom_tool to convert "Hello World" to uppercase

Claude: [Using my-server MCP]
HELLO WORLD
```

---

### 2. Batch Configuration Script

Create a script to configure all common MCP servers at once.

`setup-mcp.sh`:

```bash
#!/bin/bash

echo "🚀 Starting Claude Code MCP server configuration..."
echo ""

# 1. File System
echo "📁 Adding file system access..."
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Projects
echo "✅ File system configuration complete"
echo ""

# 2. GitHub
echo "🐙 Configuring GitHub integration..."
read -p "Please enter GitHub Personal Access Token: " github_token
claude mcp add github -s user -e GITHUB_TOKEN=$github_token -- npx -y @modelcontextprotocol/server-github
echo "✅ GitHub configuration complete"
echo ""

# 3. Puppeteer
echo "🌐 Adding browser control..."
claude mcp add puppeteer -s user -- npx -y @modelcontextprotocol/server-puppeteer
echo "✅ Puppeteer configuration complete"
echo ""

# 4. Database (optional)
read -p "Configure database access? (y/n) " configure_db
if [ "$configure_db" = "y" ]; then
  read -p "Please enter database URL: " db_url
  claude mcp add postgres -s user -e DATABASE_URL=$db_url -- npx -y @modelcontextprotocol/server-postgres
  echo "✅ Database configuration complete"
fi
echo ""

# 5. Other servers
echo "🔧 Adding other common tools..."
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory
claude mcp add time -s user -- npx -y @modelcontextprotocol/server-time
echo "✅ Other tools configuration complete"
echo ""

# Complete
echo "🎉 All MCP servers configured!"
echo ""
echo "View installed servers:"
claude mcp list
```

**Use Script:**

```bash
# Add execution permission
chmod +x setup-mcp.sh

# Run script
./setup-mcp.sh
```

---

### 3. Environment-Specific Configuration

Configure different MCP servers for different environments (development, testing, production).

**Directory Structure:**

```
~/claude-configs/
├── development.json
├── testing.json
└── production.json
```

**development.json:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "~/dev"]
    },
    "database": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://localhost/dev_db"
      }
    }
  }
}
```

**Switch Script:**

```bash
#!/bin/bash
# switch-env.sh

ENV=$1

if [ -z "$ENV" ]; then
  echo "Usage: ./switch-env.sh [development|testing|production]"
  exit 1
fi

cp ~/claude-configs/$ENV.json ~/.claude.json
echo "Switched to $ENV environment"
claude mcp list
```

**Usage:**

```bash
./switch-env.sh development  # Switch to development environment
./switch-env.sh production   # Switch to production environment
```

---

## 📚 Summary

Through this course, you should have mastered:

### ✅ Core Skills

1. **Understanding MCP Concepts**
   - What is MCP
   - How MCP works
   - MCP scopes

2. **Adding MCP Servers**
   - Command line addition
   - Configuration file editing
   - Project-level configuration

3. **Using Practical Servers**
   - 10 most commonly used MCP servers
   - Purpose and examples of each server

4. **Solving Common Issues**
   - 5 major categories of common errors
   - Debugging techniques
   - Special handling for Chinese environments

5. **Best Practices**
   - Security configuration
   - Performance optimization
   - Team collaboration

### 🎯 Next Steps

- 📖 [Lesson 9: Prompt Optimization Techniques](./09-prompt-optimization.md) - Boost interaction efficiency
- 📖 [Lesson 10: AI Agent System](./10-ai-agents.md) - Build expert teams
- 📖 [Lesson 12: Best Practices](./12-best-practices.md) - Official Anthropic recommendations

### 💡 Practice Recommendations

1. **Start with Basics**
   - First add filesystem server
   - Get familiar with basic operations
   - Gradually add other servers

2. **Solve Real Problems**
   - Use MCP servers to solve daily tasks
   - Record usage experience
   - Optimize configuration

3. **Continuous Learning**
   - Follow MCP community
   - Try new servers
   - Share usage experience

---

After correctly configuring MCP servers, your Claude Code will transform from a simple AI assistant into a powerful development partner, and development efficiency will significantly improve!

📚 [Back to Tutorial Index](../README.md)
