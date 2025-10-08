# Lesson 4: Basic Commands and Operations

> 🎯 **Learning Objectives**: Master Claude Code's built-in commands and efficient operation techniques

## Pre-lesson Preparation

- ✅ Have completed the first three lessons
- ✅ Can proficiently start and use Claude Code
- ✅ Prepare a test project for practice

## 1. Claude Code Built-in Commands

### Command Overview

In Claude Code conversations, commands starting with `/` are special commands that directly execute system functions.

### Basic Command List

| Command | Purpose | Use Case |
|------|------|----------|
| `/exit` | Exit Claude Code | After completing work |
| `/clear` | Clear conversation history | Starting new task |
| `/help` | Show help information | When forgetting commands |
| `/model` | View current model | Confirm AI version |
| `/cost` | View usage cost | Understand consumption |
| `/undo` | Undo last operation | When operation is wrong |

### Practice Demonstration

```
You: /help
Claude: [Shows all available commands and instructions]

You: /clear
Claude: Conversation history cleared, starting new conversation

You: /exit
[Exits Claude Code]
```

## 2. File Operation Commands

### View Files

```
You: Show the content of index.html

You: Read the style.css file

You: Look at README.md
```

### Create Files

```
You: Create a configuration file named config.js

You: Create a new about.html page

You: Generate a .gitignore file
```

### Edit Files

```
You: Modify line 10 of index.html, change the title to "Welcome"

You: Add a new class .button in style.css

You: Delete console.log statements in script.js
```

### Delete Files

```
You: Delete test.html

You: Remove the unnecessary backup.js file
```

## 3. Project Management Tips

### Initialize Project Structure

```
You: Help me create a standard website project structure

Claude will create:
my-project/
├── index.html
├── css/
│   └── style.css
├── js/
│   └── script.js
├── images/
└── README.md
```

### View Project Structure

```
You: Show the current project's file structure

You: List all HTML files

You: What JavaScript files are there?
```

### Organize Files

```
You: Move all CSS files to the styles folder

You: Create a components folder to store components

You: Rename main.js to app.js
```

## 4. Code Search and Navigation

### Search Code

```
You: Find all code containing "button"

You: Search where the getUserData function is used

You: Find all TODO comments
```

### Locate Code

```
You: Find where the login function is defined

You: Show which file the User class is in

You: Locate the API_URL constant
```

## 5. Batch Operations

### Batch Modifications

```
You: Change var to let in all files

You: Replace all console.log with custom logging function

You: Update copyright information in all files to 2024
```

### Batch Additions

```
You: Add JSDoc comments to all JavaScript functions

You: Add meta tags to each HTML file

You: Add comment descriptions to all CSS classes
```

## 6. Git Operations (Version Control)

### Initialize Git

```
You: Initialize git repository

Claude: I'll run the git init command...
```

### Check Status

```
You: Check git status

You: Which files have been modified?

You: Show current branch
```

### Commit Code

```
You: Commit all changes with the message "Add homepage functionality"

Claude: I will execute:
git add .
git commit -m "Add homepage functionality"
```

### View History

```
You: Show the last 5 commit records

You: View what changed in the last commit
```

## 7. Debugging Techniques

### Check Syntax Errors

```
You: Check if the code has syntax errors

You: Is there a problem with this code?
```

### Explain Error Messages

```
You: What does this error mean?
TypeError: Cannot read property 'name' of undefined

Claude: This error means you're trying to access the 'name' property of an undefined object...
```

### Debugging Suggestions

```
You: How to debug this login function?

Claude: Suggest debugging with the following steps:
1. Check if form data is correctly captured
2. Verify if API request is sent successfully
3. Check response data format
...
```

## 8. Code Optimization

### Request Code Review

```
You: Help me review this code, see what can be improved

Claude: I notice the following points:
1. Can use more concise syntax
2. Suggest adding error handling
3. Variable naming can be clearer
...
```

### Performance Optimization

```
You: This code runs very slowly, can you optimize it?

Claude: I found several performance issues:
1. Duplicate calculations in loops
2. Can use caching to reduce calculations
...
```

### Code Refactoring

```
You: Split this function into smaller functions

You: Rewrite this code using more modern JavaScript syntax

You: Simplify this if-else logic
```

## 9. Documentation Generation

### Generate README

```
You: Generate a README file for this project

Claude will include:
- Project introduction
- Installation instructions
- Usage methods
- File structure
```

### Generate Comments

```
You: Add detailed comments to this function

You: Generate JSDoc documentation for this class

You: Explain this complex algorithm
```

### Generate API Documentation

```
You: Generate API documentation based on these interfaces

You: List all available functions and their parameters
```

## 10. Practical Workflows

### Workflow 1: Add New Feature

```
1. You: I want to add user registration functionality, including form validation
2. Claude: Create form HTML + validation JavaScript
3. You: Form styles should be more beautiful
4. Claude: Beautify CSS styles
5. You: Add success notification after submission
6. Claude: Add success notification feature
7. You: Test if there are any issues
8. Claude: Run tests and report results
```

### Workflow 2: Fix Bug

```
1. You: Clicking the submit button has no response
2. Claude: Let me check... Found that the event listener is not properly bound
3. You: Can you fix it?
4. Claude: Fixed, please test
5. You: It works now, thanks
```

### Workflow 3: Project Refactoring

```
1. You: The project code is too messy, help me organize it
2. Claude:
   - Analyze current structure
   - Suggest new file organization
   - Ask for your confirmation
3. You: Go with this plan
4. Claude:
   - Create new file structure
   - Move and refactor code
   - Update all references
5. Claude: Refactoring complete, please test if functionality is normal
```

## 11. Shortcut Operation Tips

### Continuous Operations

```
You:
1. Create a dashboard.html page
2. Add navigation bar
3. Add statistics cards
4. Apply blue theme

Claude: Okay, I'll complete these tasks in order...
```

### Conditional Operations

```
You: If config.js exists, update it; otherwise create a new one

Claude: I checked, the file doesn't exist, I'll create a new one...
```

### Reference Previous Content

```
You: Create a login page

[Claude creates login page]

You: Create a registration page using the same style

Claude: Okay, I'll use the same design style as the login page...
```

## 12. Advanced Tips

### Use Context

```
You: Remember this design specification:
- Primary color: #3498db
- Font: Roboto
- Border radius: 8px

Claude: Okay, I've remembered these design specifications

You: Create a button component using this specification

Claude: [Creates button using specified design specifications]
```

### Multi-file Collaboration

```
You: Create a user management module that needs:
- user.html display page
- user.js business logic
- user.css styles
- They should work together

Claude: I'll create these three files and ensure they're properly linked...
```

### Templates and Scaffolding

```
You: Create a React component template that I can reuse later

Claude: [Creates reusable component template]

You: Create a Header component based on this template

Claude: [Generates new component using template]
```

## 13. Avoid Common Mistakes

### Mistake 1: Operating Too Quickly

❌ Poor Practice:
```
You: Create index.html
You: Add navigation bar
You: Add footer
You: Modify color
[Continue making requests without waiting for Claude to complete]
```

✅ Good Practice:
```
You: Create index.html
[Wait for Claude to complete]
You: Add navigation bar
[Wait for completion and check]
You: Add footer
...
```

### Mistake 2: Not Checking Results

❌ Poor Practice:
```
Claude: File created
You: [Immediately make next request]
```

✅ Good Practice:
```
Claude: File created
You: Look at the content of index.html
[Check and confirm]
You: Good, continue...
```

### Mistake 3: Vague Description

❌ Poor Practice:
```
You: Modify the style
```

✅ Good Practice:
```
You: Change the button's background color to blue and font size to 16px
```

## 14. Efficient Collaboration Modes

### Teaching Mode

```
You: Can you teach me how this code works? Explain step by step

Claude: Of course, let me explain in detail:
Line 1: Define function...
Line 2: Check parameters...
...
```

### Exploration Mode

```
You: What are the ways to implement image lazy loading?

Claude: There are several common methods:
1. Intersection Observer API
2. scroll event listening
3. Third-party libraries like lazysizes
Each method's pros and cons are...
```

### Decision Support

```
You: Should I use Vue or React for this project?

Claude: Let me analyze your needs...
Based on [project characteristics], I suggest...
Because...
```

## 15. Keyboard Shortcuts (Terminal)

### Common Shortcuts

| Shortcut | Purpose | System |
|--------|------|------|
| `Ctrl + C` | Interrupt current command | All |
| `Ctrl + D` | Exit/EOF | All |
| `Ctrl + L` | Clear screen | All |
| `↑` / `↓` | Browse command history | All |
| `Tab` | Auto-complete | All |
| `Ctrl + A` | Cursor to beginning of line | All |
| `Ctrl + E` | Cursor to end of line | All |

## 16. Hands-on Practice

### Practice 1: File Management

Tasks:
1. Create project structure (3 HTML, 2 CSS, 1 JS)
2. View all created files
3. Rename one file
4. Delete a test file

### Practice 2: Code Modification

Tasks:
1. Create a simple HTML page
2. Ask Claude to display content
3. Modify specific line content
4. Batch replace a word

### Practice 3: Project Development

Tasks:
1. Initialize a to-do list project
2. Gradually add features (add, delete, mark complete)
3. Beautify styles
4. Generate project documentation

### Practice 4: Debug and Fix

Tasks:
1. Intentionally write code with errors
2. Have Claude find the problem
3. Request fix suggestions
4. Apply fix and verify

## 17. Command Quick Reference

### Project Initialization
```bash
claude                    # Start
cd my-project            # Enter project
/clear                   # Clear history
```

### File Operations
```
Create [filename]
Show [filename]
Modify [filename]'s [content]
Delete [filename]
```

### Code Operations
```
Search [keyword]
Find [function name]
Optimize [code section]
Refactor [module name]
```

### Git Operations
```
Initialize git
Check status
Commit changes
View history
```

## 18. Next Steps

🎉 Congratulations on mastering basic commands and operations!

Now you have learned:
- ✅ All of Claude Code's built-in commands
- ✅ Efficient file operation methods
- ✅ Project management techniques
- ✅ Debugging and optimization processes

**Ready to enter advanced learning?**

👉 Next Lesson: [Advanced File Operations](./05-advanced-files.md)

There, we will learn:
1. Complex file operations
2. Cross-file refactoring
3. Project template management
4. Automated workflows

---

💡 **Tips**:
- Write down commonly used commands in a notebook
- Practice keyboard shortcuts more to improve efficiency
- Establish your own workflow templates
- Record problems encountered and solutions
