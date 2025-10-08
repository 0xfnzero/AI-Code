# Lesson 2: Installation and Configuration

> 🎯 **Learning Objectives**: Complete the installation of Claude Code and successfully run your first conversation

## Pre-lesson Preparation

Before starting, please ensure:
- ✅ You've read Lesson 1 and understand what Claude Code is
- ✅ Your computer can connect to the internet
- ✅ You have about 30 minutes available

## 1. Find and Open the Terminal

### Windows Users

**Method 1: Search and Open**
1. Press the `Win` key
2. Type `cmd` or `PowerShell`
3. Click to open

**Method 2: Shortcut**
- Press `Win + R`
- Type `cmd`
- Press Enter

### Mac Users

**Method 1: Search and Open**
1. Press `Command + Space`
2. Type `Terminal`
3. Press Enter

**Method 2: Open from Applications**
- Open the "Applications" folder
- Go to "Utilities"
- Double-click "Terminal"

### Linux Users

- Shortcut: `Ctrl + Alt + T`
- Or find Terminal from the applications menu

### What Does the Terminal Look Like?

After opening, you'll see a black or white window like this:

```
user@computer:~$
```

Or:

```
C:\Users\YourName>
```

**Don't be intimidated!** This is where you "talk" with your computer.

## 2. Install Node.js

### Why Do You Need Node.js?

Node.js is the runtime environment for Claude Code, like how a phone needs an operating system.

### Check if Already Installed

In the terminal, type the following command (press Enter after typing):

```bash
node --version
```

**If you see a version number like `v18.0.0`** → Skip installation, go directly to Step 3
**If you see an error message** → Continue with the installation steps below

### Windows Installation

1. **Download the Installer**
   - Visit: https://nodejs.org/
   - Click the "LTS" version (recommended version)
   - Download and run the installer

2. **Installation Steps**
   - Double-click the downloaded file
   - Keep clicking "Next"
   - Keep the default options
   - Wait for installation to complete

3. **Verify Installation**
   ```bash
   node --version
   npm --version
   ```

   You should see two version numbers

### Mac Installation

**Method 1: Use Official Installer (Recommended for Beginners)**

1. Visit https://nodejs.org/
2. Download the LTS version
3. Double-click the installer
4. Follow the prompts to complete installation

**Method 2: Use Homebrew (Recommended for Technical Users)**

```bash
# If you don't have Homebrew, install it first
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Node.js
brew install node

# Verify installation
node --version
```

### Ubuntu/Debian Linux Installation

```bash
# Add Node.js repository
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo bash -

# Install Node.js
sudo apt-get install -y nodejs

# Verify installation
node --version
npm --version
```

## 3. Install Claude Code

### One Command Does It All

In the terminal, type:

```bash
npm install -g @anthropic-ai/claude-code
```

**Explanation**:
- `npm`: Node.js package management tool
- `install`: Install command
- `-g`: Global installation (can be used anywhere)
- `@anthropic-ai/claude-code`: Claude Code's package name

### Wait for Installation

You'll see output like this:

```
⸨░░░░░░░░░░░░░░░░░░⸩ ⠧ reify:@anthropic-ai/claude-code: ...
```

**Don't close the window, wait for the progress bar to complete!** Usually takes 1-3 minutes.

### Verify Installation

After installation completes, type:

```bash
claude --version
```

If you see a version number, installation was successful! 🎉

### Common Installation Issues

**Problem 1: Permission Error**
```
Error: EACCES: permission denied
```

**Solution** (Mac/Linux):
```bash
sudo npm install -g @anthropic-ai/claude-code
# Will prompt for password
```

**Problem 2: Network Timeout**
```
Error: network timeout
```

**Solution**:
- Check network connection
- Try several times
- Or use a Chinese mirror:
```bash
npm config set registry https://registry.npmmirror.com
npm install -g @anthropic-ai/claude-code
```

## 4. Obtain API Key

### What is an API Key?

An API key is like your "ID card" that lets Claude Code know it's you using it.

### How to Obtain?

API keys are typically provided by the service provider, with a format like:

```
cr_xxxxxxxxxxxxxxxxxxxxx
```

> ⚠️ **Note**: API keys are private, don't share them with others!

### Configure API Key and Service Address

You need to configure two pieces of information:
1. **API Key** (`ANTHROPIC_AUTH_TOKEN`)
2. **API Address** (`ANTHROPIC_BASE_URL`)

## 5. Configure Environment Variables

### What are Environment Variables?

Environment variables are like "notes" you set for your computer, letting Claude Code know your API information.

### Temporary Configuration (Valid for Current Session)

#### Windows (CMD)

```cmd
set ANTHROPIC_AUTH_TOKEN=your-api-key
set ANTHROPIC_BASE_URL=your-api-address
```

#### Windows (PowerShell)

```powershell
$env:ANTHROPIC_AUTH_TOKEN="your-api-key"
$env:ANTHROPIC_BASE_URL="your-api-address"
```

#### Mac/Linux

```bash
export ANTHROPIC_AUTH_TOKEN="your-api-key"
export ANTHROPIC_BASE_URL="your-api-address"
```

### Permanent Configuration (Recommended)

#### Mac/Linux

1. **Open Configuration File**

```bash
# If using bash
nano ~/.bashrc

# If using zsh (Mac default)
nano ~/.zshrc
```

2. **Add the Following Content**

```bash
export ANTHROPIC_AUTH_TOKEN="your-api-key"
export ANTHROPIC_BASE_URL="your-api-address"
```

3. **Save and Exit**
   - Press `Ctrl + O` to save
   - Press `Enter` to confirm
   - Press `Ctrl + X` to exit

4. **Apply Configuration**

```bash
source ~/.bashrc  # or source ~/.zshrc
```

#### Windows

1. **Open Environment Variables Settings**
   - Right-click "This PC" → "Properties"
   - Click "Advanced system settings"
   - Click "Environment Variables"

2. **Add User Variables**
   - Click "New"
   - Variable name: `ANTHROPIC_AUTH_TOKEN`
   - Variable value: your API key
   - Click "New" again
   - Variable name: `ANTHROPIC_BASE_URL`
   - Variable value: your API address

3. **Confirm and Restart Terminal**

## 6. First Run

### Create Test Project

```bash
# Create a test folder
mkdir claude-test
cd claude-test
```

**Explanation**:
- `mkdir`: Create folder (make directory)
- `claude-test`: Folder name
- `cd`: Enter folder (change directory)

### Start Claude Code

```bash
claude
```

### First Run Configuration

When running for the first time, Claude Code will ask you a few questions:

**1. Choose Theme**
```
? Choose your preferred theme:
  ❯ Light
    Dark
    Auto
```
Use arrow keys to select, press `Enter` to confirm

**2. Security Notice**
```
⚠️  Claude Code can read/write files and execute commands
Do you agree? (y/N)
```
Type `y` then press `Enter`

**3. Terminal Configuration**
```
Use default Terminal configuration? (Y/n)
```
Press `Enter` (use default)

**4. Trust Working Directory**
```
Do you trust the current directory? (Y/n)
```
Press `Enter` (yes, trust)

### Success!

Now you should see:

```
╭─────────────────────────────────────╮
│ Claude Code is ready                │
│ Your AI programming assistant is    │
│ waiting for your commands           │
╰─────────────────────────────────────╯

You: ▌
```

## 7. Test Conversation

### First Conversation

Type:

```
Hello, please introduce yourself
```

Press `Enter`, and Claude Code will respond!

### Simple Test

```
You: Help me create a simple HTML file that displays "Hello World"
```

Claude Code will:
1. Create an `index.html` file
2. Write basic HTML code
3. Tell you the file has been created

### Verify Results

```bash
# View created files
ls

# View file content (Mac/Linux)
cat index.html

# View file content (Windows)
type index.html
```

## 8. Common Commands Quick Reference

### Terminal Basic Commands

| Command | Purpose | Example |
|------|------|------|
| `ls` (Mac/Linux) | List files | `ls` |
| `dir` (Windows) | List files | `dir` |
| `cd` | Change directory | `cd my-project` |
| `cd ..` | Return to parent directory | `cd ..` |
| `pwd` | Show current path | `pwd` |
| `mkdir` | Create folder | `mkdir test` |

### Claude Code Commands

| Command | Purpose |
|------|------|
| `claude` | Start Claude Code |
| `claude --version` | Check version |
| `claude --help` | View help |
| `/exit` | Exit Claude Code |
| `/clear` | Clear conversation history |

## 9. Configuration File Location

### Where are Configuration Files Saved?

- **Mac/Linux**: `~/.config/claude-code/`
- **Windows**: `C:\Users\your-username\.config\claude-code\`

### If You Need to Reset Configuration

```bash
# Mac/Linux
rm -rf ~/.config/claude-code

# Windows (PowerShell)
Remove-Item -Recurse -Force $env:USERPROFILE\.config\claude-code
```

## 10. Troubleshooting

### Claude Code Won't Start

**Checklist**:
1. ✅ Node.js is correctly installed (`node --version`)
2. ✅ Claude Code is correctly installed (`claude --version`)
3. ✅ Environment variables are correctly set
4. ✅ Network connection is normal

### API Error Prompt

```
Error: Invalid API key
```

**Solution**:
1. Check if API key is correct
2. Reset environment variables
3. Restart terminal

### Command Not Found

```
'claude' is not recognized as an internal or external command
```

**Solution**:
1. Reinstall Claude Code
2. Restart terminal
3. Check PATH environment variable

## 11. Next Steps

🎉 Congratulations on completing installation and configuration!

Now you have:
- ✅ Installed Node.js
- ✅ Installed Claude Code
- ✅ Configured API key
- ✅ Successfully run your first conversation

**Ready to get into practice?**

👉 Next Lesson: [First Conversation - Create Your First Project](./03-first-conversation.md)

There, we will:
1. Learn how to communicate effectively with Claude Code
2. Create a complete small project
3. Understand Claude Code's workflow
4. Master basic interaction techniques

---

💡 **Tips**:
- Save your API key in a secure place
- If you encounter problems, first check the "Troubleshooting" section of this lesson
- Don't rush, take your time, you can try each command multiple times
