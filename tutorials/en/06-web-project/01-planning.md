# 1.1 Project Planning

> Master the art of planning a complete web application from scratch

## Learning Objectives

By the end of this module, you will:
- Understand how to analyze project requirements
- Learn to select the appropriate tech stack
- Break down features into manageable modules
- Create a realistic development timeline

## Overview

Successful web applications start with solid planning. In this module, we'll plan a **Task Management Application (Todo App Pro)** that includes user authentication, CRUD operations, and data persistence.

## 1. Requirements Analysis

### Defining Project Goals

**Your Conversation with Claude**:
```
You: I want to build a task management application. Help me define the requirements.

Claude:
Let's start by understanding what you need. A task management app should include:

Core Features:
✅ User registration and login
✅ Create, edit, delete tasks
✅ Task categorization and tagging
✅ Mark tasks as complete/incomplete
✅ Set due dates and priorities

Additional Features:
✅ Search and filter tasks
✅ Sort by date, priority, or status
✅ Responsive design for mobile devices
✅ Data persistence (database)

Do you want to add any specific features to this list?
```

### User Stories

Define clear user stories to guide development:

```
As a user, I want to:
- Register an account so I can save my tasks
- Log in to access my personal task list
- Create new tasks with title, description, due date, and priority
- Edit existing tasks to update details
- Delete tasks I no longer need
- Mark tasks as complete
- Categorize tasks (work, personal, shopping, etc.)
- Search for specific tasks
- Filter tasks by category, priority, or status
- Access the app from my phone
```

## 2. Tech Stack Selection

### Choosing the Right Tools

**Your Conversation with Claude**:
```
You: Help me choose the technology stack for this project.

Claude:
Based on your requirements, I recommend:

Frontend:
- HTML5 + CSS3 + Vanilla JavaScript
  OR
- React (if you want to learn modern frameworks)

Why?
- Easy to learn and understand
- No complex build processes (vanilla JS)
- Full control over the code

Backend:
- Node.js + Express
- SQLite database

Why?
- JavaScript on both frontend and backend
- Express is simple and powerful
- SQLite requires no setup, perfect for learning

This stack gives you:
1. Gentle learning curve
2. Easy deployment
3. Complete functionality
4. Industry-relevant skills

Which approach do you prefer?
```

### Our Tech Stack Decision

For this tutorial, we'll use:

**Frontend**:
- HTML5 for structure
- CSS3 for styling (with responsive design)
- Vanilla JavaScript for functionality

**Backend**:
- Node.js runtime environment
- Express.js web framework
- SQLite database for data persistence
- JWT for authentication

**Development Tools**:
- Git for version control
- VS Code as code editor
- Postman for API testing

## 3. Feature Breakdown

### Modular Development Approach

**Your Conversation with Claude**:
```
You: Break this project down into manageable modules.

Claude:
Here's a structured breakdown:

Phase 1: Foundation (2-3 hours)
- [ ] Set up project directory structure
- [ ] Initialize Git repository
- [ ] Create basic HTML structure
- [ ] Add CSS styling framework

Phase 2: Core Frontend (4-5 hours)
- [ ] Design static pages (HTML/CSS)
- [ ] Implement task CRUD operations
- [ ] Add local storage for data
- [ ] Create UI interactions

Phase 3: Backend Integration (4-5 hours)
- [ ] Set up Express server
- [ ] Design RESTful API routes
- [ ] Integrate SQLite database
- [ ] Create data models

Phase 4: Authentication (2-3 hours)
- [ ] Implement user registration
- [ ] Add login functionality
- [ ] Set up JWT token management
- [ ] Protect API routes

Phase 5: Enhancement (2-3 hours)
- [ ] Add search and filter features
- [ ] Implement sorting functionality
- [ ] Optimize performance
- [ ] Add responsive design

Phase 6: Deployment (1-2 hours)
- [ ] Prepare for production
- [ ] Deploy to hosting platform
- [ ] Configure environment variables
- [ ] Test live application

Total estimated time: 15-20 hours
```

## 4. Development Timeline

### Creating a Realistic Schedule

```markdown
Week 1: Foundation & Frontend
├── Day 1-2: Project setup and static UI
├── Day 3-4: Frontend functionality
└── Day 5: Testing and refinement

Week 2: Backend & Integration
├── Day 1-2: Backend server and API
├── Day 3-4: User authentication
└── Day 5: Frontend-backend integration

Week 3: Enhancement & Deployment
├── Day 1-2: Additional features
├── Day 3-4: Testing and bug fixes
└── Day 5: Deployment and documentation
```

### Daily Goals Template

```
Day [X]: [Focus Area]
Morning (2-3 hours):
- [ ] Task 1
- [ ] Task 2

Afternoon (2-3 hours):
- [ ] Task 3
- [ ] Task 4

End-of-day checkpoint:
- What worked well?
- What challenges did I face?
- What do I need to research tomorrow?
```

## 5. Project Architecture

### High-Level System Design

```
┌─────────────────────────────────────────┐
│          Client (Browser)               │
│  ┌─────────────────────────────────┐   │
│  │  HTML + CSS + JavaScript        │   │
│  │  - UI Components                │   │
│  │  - Event Handlers               │   │
│  │  - API Client                   │   │
│  └─────────────────────────────────┘   │
└──────────────┬──────────────────────────┘
               │ HTTP Requests
               │ (REST API)
┌──────────────▼──────────────────────────┐
│          Server (Node.js)               │
│  ┌─────────────────────────────────┐   │
│  │  Express.js                     │   │
│  │  - Route Handlers               │   │
│  │  - Middleware (Auth, CORS)      │   │
│  │  - Business Logic               │   │
│  └─────────────────────────────────┘   │
└──────────────┬──────────────────────────┘
               │ Database Queries
┌──────────────▼──────────────────────────┐
│       Database (SQLite)                 │
│  ┌─────────────────────────────────┐   │
│  │  Tables:                        │   │
│  │  - users                        │   │
│  │  - tasks                        │   │
│  │  - categories                   │   │
│  └─────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

### Data Flow

```
1. User Action (Frontend)
   ↓
2. JavaScript Event Handler
   ↓
3. API Request to Backend
   ↓
4. Express Route Handler
   ↓
5. Database Operation
   ↓
6. Response to Frontend
   ↓
7. Update UI
```

## 6. File Structure Planning

### Project Directory Layout

```
todo-app-pro/
├── client/                    # Frontend code
│   ├── index.html            # Main page
│   ├── login.html            # Login page
│   ├── register.html         # Registration page
│   ├── css/
│   │   ├── style.css         # Main styles
│   │   ├── components.css    # Component styles
│   │   └── responsive.css    # Mobile styles
│   ├── js/
│   │   ├── app.js           # Main application logic
│   │   ├── task.js          # Task class and methods
│   │   ├── ui.js            # UI manipulation
│   │   ├── api.js           # API client
│   │   ├── auth.js          # Authentication logic
│   │   └── storage.js       # Local storage utilities
│   └── assets/
│       └── images/          # Icons and images
├── server/                   # Backend code
│   ├── index.js             # Server entry point
│   ├── routes/
│   │   ├── tasks.js         # Task routes
│   │   ├── users.js         # User routes
│   │   └── auth.js          # Auth routes
│   ├── models/
│   │   ├── task.js          # Task model
│   │   └── user.js          # User model
│   ├── middleware/
│   │   ├── auth.js          # Auth middleware
│   │   └── errorHandler.js  # Error handling
│   └── database.js          # Database setup
├── tests/                    # Test files
│   ├── task.test.js
│   └── api.test.js
├── .env                      # Environment variables
├── .gitignore               # Git ignore rules
├── package.json             # Dependencies
└── README.md                # Documentation
```

## 7. API Design

### RESTful Endpoints

**Task Management**:
```
GET    /api/tasks              Get all tasks
GET    /api/tasks/:id          Get single task
POST   /api/tasks              Create new task
PUT    /api/tasks/:id          Update task
DELETE /api/tasks/:id          Delete task
```

**User Management**:
```
POST   /api/auth/register      Register new user
POST   /api/auth/login         Login user
GET    /api/auth/profile       Get user profile
POST   /api/auth/logout        Logout user
```

**Filter and Search**:
```
GET    /api/tasks?category=work        Filter by category
GET    /api/tasks?priority=high        Filter by priority
GET    /api/tasks?search=keyword       Search tasks
GET    /api/tasks?completed=true       Filter by status
```

### Request/Response Examples

**Create Task Request**:
```json
POST /api/tasks
Content-Type: application/json
Authorization: Bearer <token>

{
  "title": "Complete project documentation",
  "description": "Write comprehensive docs for the API",
  "dueDate": "2024-12-31",
  "priority": "high",
  "category": "work"
}
```

**Create Task Response**:
```json
{
  "success": true,
  "task": {
    "id": "1234",
    "title": "Complete project documentation",
    "description": "Write comprehensive docs for the API",
    "dueDate": "2024-12-31",
    "priority": "high",
    "category": "work",
    "completed": false,
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

## 8. Database Schema

### Tables and Relationships

**Users Table**:
```sql
CREATE TABLE users (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  username TEXT UNIQUE NOT NULL,
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

**Tasks Table**:
```sql
CREATE TABLE tasks (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id INTEGER NOT NULL,
  title TEXT NOT NULL,
  description TEXT,
  due_date TEXT,
  priority TEXT CHECK(priority IN ('low', 'medium', 'high')),
  category TEXT,
  completed BOOLEAN DEFAULT 0,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Categories Table** (Optional):
```sql
CREATE TABLE categories (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id INTEGER NOT NULL,
  name TEXT NOT NULL,
  color TEXT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## Key Takeaways

1. **Start with Clear Requirements** - Define what you're building before writing code
2. **Choose Appropriate Tools** - Select a tech stack that matches your skill level and project needs
3. **Break Down the Project** - Divide work into manageable phases
4. **Plan the Architecture** - Design your system structure upfront
5. **Create a Timeline** - Set realistic goals and milestones

## Practice Exercise

Before moving to the next module, complete this planning exercise:

**Task**: Plan a similar web application for a different domain
- Choose a project idea (e.g., blog, expense tracker, recipe manager)
- Define core features
- Select a tech stack
- Break down into development phases
- Sketch the file structure

## Next Steps

Now that you have a solid plan, move on to:

👉 **[1.2 Project Structure Setup](./02-structure.md)** - Set up the project directory and development environment

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)
