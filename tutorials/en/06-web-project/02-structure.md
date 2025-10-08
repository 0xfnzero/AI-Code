# 1.2 Project Structure Setup

> Set up a professional project structure and development environment

## Learning Objectives

By the end of this module, you will:
- Create a well-organized project directory structure
- Initialize version control with Git
- Set up the development environment
- Install and configure necessary dependencies

## Overview

A well-organized project structure is crucial for maintainability and scalability. In this module, we'll set up the foundation for our Todo App Pro project.

## 1. Creating the Project Directory

### Basic Project Setup

**Your Conversation with Claude**:
```
You: Create the directory structure for todo-app-pro

Claude:
I'll create a professional project structure for you:

todo-app-pro/
├── client/                    # Frontend code
│   ├── index.html
│   ├── login.html
│   ├── register.html
│   ├── css/
│   │   ├── style.css
│   │   ├── components.css
│   │   └── responsive.css
│   ├── js/
│   │   ├── app.js
│   │   ├── task.js
│   │   ├── ui.js
│   │   ├── api.js
│   │   ├── auth.js
│   │   └── storage.js
│   └── assets/
│       └── images/
├── server/                   # Backend code
│   ├── index.js
│   ├── routes/
│   ├── models/
│   └── middleware/
├── package.json
├── .gitignore
└── README.md

Let me create this structure for you.
```

### Creating Directories with Claude

**Step-by-Step Creation**:
```
You: Create all the directories for this project

Claude: I'll create the complete directory structure:

# Create main project directory
mkdir todo-app-pro
cd todo-app-pro

# Create client directories
mkdir -p client/css
mkdir -p client/js
mkdir -p client/assets/images

# Create server directories
mkdir -p server/routes
mkdir -p server/models
mkdir -p server/middleware

# Create test directory
mkdir tests

Project structure created successfully!
```

## 2. Version Control Setup

### Initializing Git Repository

**Your Conversation with Claude**:
```
You: Set up Git for this project

Claude:
I'll help you initialize Git and create a proper .gitignore file:

1. Initialize Git repository:
   git init

2. Create .gitignore file with these entries:
```

**.gitignore**:
```
# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Environment variables
.env
.env.local
.env.production

# Database
*.db
*.sqlite
*.sqlite3

# OS files
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# Build files
dist/
build/
*.log

# Test coverage
coverage/
.nyc_output/
```

**Initial Commit**:
```
You: Make the first commit

Claude:
git add .
git commit -m "Initial project structure setup"

✅ First commit created!
💡 Your project is now under version control
```

### Connecting to Remote Repository

```
You: Connect this to GitHub

Claude:
1. Create a new repository on GitHub (don't initialize with README)

2. Add remote and push:
   git remote add origin https://github.com/yourusername/todo-app-pro.git
   git branch -M main
   git push -u origin main

✅ Project pushed to GitHub!
```

## 3. Package Configuration

### Creating package.json

**Your Conversation with Claude**:
```
You: Set up package.json for the project

Claude:
I'll create a comprehensive package.json file:
```

**package.json**:
```json
{
  "name": "todo-app-pro",
  "version": "1.0.0",
  "description": "A professional task management application",
  "main": "server/index.js",
  "scripts": {
    "start": "node server/index.js",
    "dev": "nodemon server/index.js",
    "test": "jest",
    "client": "cd client && live-server",
    "server": "nodemon server/index.js",
    "both": "concurrently \"npm run client\" \"npm run server\""
  },
  "keywords": ["todo", "task-manager", "web-app"],
  "author": "Your Name",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "dotenv": "^16.3.1",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.2",
    "better-sqlite3": "^9.0.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.1",
    "jest": "^29.7.0",
    "concurrently": "^8.2.1"
  }
}
```

### Installing Dependencies

**Your Conversation with Claude**:
```
You: Install all the necessary packages

Claude:
I'll guide you through installing dependencies:

1. Backend dependencies:
npm install express cors dotenv bcryptjs jsonwebtoken better-sqlite3

Why each package:
- express: Web framework for Node.js
- cors: Enable cross-origin requests
- dotenv: Manage environment variables
- bcryptjs: Password hashing
- jsonwebtoken: JWT authentication
- better-sqlite3: SQLite database driver

2. Development dependencies:
npm install --save-dev nodemon jest concurrently

Why each package:
- nodemon: Auto-restart server on changes
- jest: Testing framework
- concurrently: Run multiple commands simultaneously

✅ All dependencies installed!
```

## 4. Environment Configuration

### Creating Environment Variables

**Your Conversation with Claude**:
```
You: Set up environment variables

Claude:
Create a .env file in the root directory:
```

**.env**:
```
# Server Configuration
PORT=3000
NODE_ENV=development

# Database
DB_PATH=./database/tasks.db

# JWT Configuration
JWT_SECRET=your-secret-key-change-this-in-production
JWT_EXPIRE=7d

# Frontend URL (for CORS)
CLIENT_URL=http://localhost:8080
```

**.env.example** (for team sharing):
```
# Server Configuration
PORT=3000
NODE_ENV=development

# Database
DB_PATH=./database/tasks.db

# JWT Configuration
JWT_SECRET=change-this-secret-key
JWT_EXPIRE=7d

# Frontend URL (for CORS)
CLIENT_URL=http://localhost:8080
```

**Loading Environment Variables**:
```javascript
// server/index.js (beginning)
require('dotenv').config();

const PORT = process.env.PORT || 3000;
const JWT_SECRET = process.env.JWT_SECRET;
```

## 5. Creating Base Files

### Setting Up HTML Template

**Your Conversation with Claude**:
```
You: Create a basic HTML template for the main page

Claude:
I'll create a clean HTML5 template:
```

**client/index.html**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Professional task management application">
    <title>Todo App Pro - Task Management</title>

    <!-- Stylesheets -->
    <link rel="stylesheet" href="css/style.css">
    <link rel="stylesheet" href="css/components.css">
    <link rel="stylesheet" href="css/responsive.css">
</head>
<body>
    <div id="app">
        <!-- App content will be injected here -->
        <div class="loading">Loading...</div>
    </div>

    <!-- Scripts -->
    <script src="js/storage.js"></script>
    <script src="js/task.js"></script>
    <script src="js/api.js"></script>
    <script src="js/auth.js"></script>
    <script src="js/ui.js"></script>
    <script src="js/app.js"></script>
</body>
</html>
```

### Creating Base CSS

**client/css/style.css** (starter):
```css
/* CSS Reset */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

/* CSS Variables */
:root {
    --primary-color: #3498db;
    --secondary-color: #2ecc71;
    --danger-color: #e74c3c;
    --warning-color: #f39c12;
    --dark-color: #2c3e50;
    --light-color: #ecf0f1;
    --text-color: #333;
    --border-color: #ddd;

    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 2rem;

    --border-radius: 4px;
    --box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

/* Base Styles */
body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    line-height: 1.6;
    color: var(--text-color);
    background-color: #f5f5f5;
}

/* Utility Classes */
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 var(--spacing-md);
}

.btn {
    padding: var(--spacing-sm) var(--spacing-md);
    border: none;
    border-radius: var(--border-radius);
    cursor: pointer;
    font-size: 1rem;
    transition: all 0.3s ease;
}

.btn-primary {
    background-color: var(--primary-color);
    color: white;
}

.btn-primary:hover {
    background-color: #2980b9;
}
```

### Creating Base JavaScript Files

**client/js/app.js** (starter):
```javascript
// Main application logic
const TodoApp = {
    init() {
        console.log('Todo App Pro initialized');
        this.loadTasks();
        this.setupEventListeners();
    },

    loadTasks() {
        // Will implement in next module
        console.log('Loading tasks...');
    },

    setupEventListeners() {
        // Will implement in next module
        console.log('Setting up event listeners...');
    }
};

// Initialize app when DOM is ready
document.addEventListener('DOMContentLoaded', () => {
    TodoApp.init();
});
```

**server/index.js** (starter):
```javascript
require('dotenv').config();
const express = require('express');
const cors = require('cors');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(cors());
app.use(express.json());
app.use(express.static('client'));

// Basic route
app.get('/api/health', (req, res) => {
    res.json({ status: 'OK', message: 'Server is running' });
});

// Start server
app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

## 6. Setting Up Development Environment

### VS Code Configuration

**Your Conversation with Claude**:
```
You: Configure VS Code for this project

Claude:
Create a .vscode/settings.json file:
```

**.vscode/settings.json**:
```json
{
    "editor.formatOnSave": true,
    "editor.tabSize": 2,
    "files.exclude": {
        "node_modules": true,
        "*.db": true
    },
    "emmet.includeLanguages": {
        "javascript": "javascriptreact"
    },
    "liveServer.settings.port": 8080
}
```

**.vscode/extensions.json** (recommended extensions):
```json
{
    "recommendations": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "ritwickdey.liveserver",
        "ms-vscode.vscode-node-debug2"
    ]
}
```

### Testing the Setup

**Your Conversation with Claude**:
```
You: How do I test that everything is set up correctly?

Claude:
Follow these steps to verify your setup:

1. Test server:
   npm run dev

   Expected output:
   Server running on http://localhost:3000

2. Test health endpoint:
   curl http://localhost:3000/api/health

   Expected response:
   {"status":"OK","message":"Server is running"}

3. Test frontend:
   - Open another terminal
   - cd client
   - npx live-server

   Expected: Browser opens at http://localhost:8080

4. Verify Git:
   git status

   Expected: Clean working directory

✅ If all tests pass, your setup is complete!
```

## 7. Creating README Documentation

**README.md**:
```markdown
# Todo App Pro

A professional task management application built with vanilla JavaScript and Node.js.

## Features

- User authentication (register/login)
- Create, edit, delete tasks
- Task categorization and prioritization
- Search and filter functionality
- Responsive design
- Data persistence with SQLite

## Tech Stack

**Frontend:**
- HTML5
- CSS3
- Vanilla JavaScript

**Backend:**
- Node.js
- Express.js
- SQLite

## Getting Started

### Prerequisites
- Node.js (v14 or higher)
- npm or yarn

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/todo-app-pro.git
cd todo-app-pro
```

2. Install dependencies:
```bash
npm install
```

3. Set up environment variables:
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. Start the development server:
```bash
npm run dev
```

5. Open your browser to `http://localhost:3000`

## Project Structure

```
todo-app-pro/
├── client/          # Frontend code
├── server/          # Backend code
├── tests/           # Test files
└── package.json     # Dependencies
```

## Development

- `npm run dev` - Start development server
- `npm test` - Run tests
- `npm run both` - Run both frontend and backend concurrently

## License

MIT
```

## 8. Verification Checklist

Before moving to the next module, verify:

```
✅ Project directory structure created
✅ Git repository initialized
✅ .gitignore configured
✅ package.json created
✅ Dependencies installed
✅ Environment variables set up
✅ Base HTML/CSS/JS files created
✅ Server runs without errors
✅ Frontend is accessible
✅ README.md documented
```

## Key Takeaways

1. **Organized Structure** - A well-planned directory structure saves time later
2. **Version Control** - Set up Git from the start
3. **Environment Config** - Use .env for sensitive data
4. **Documentation** - Write README as you build
5. **Testing** - Verify each setup step works

## Practice Exercise

**Task**: Set up a similar structure for a different project
1. Create project directory for a "Blog Platform"
2. Initialize Git repository
3. Set up package.json with appropriate dependencies
4. Create base files (HTML, CSS, JS)
5. Document in README

## Next Steps

Now that your project structure is ready, move on to:

👉 **[1.3 Static Page Design](./03-frontend-ui.md)** - Build the user interface

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)
