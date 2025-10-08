# 1.3 Static Page Design

> Create a beautiful, modern user interface with HTML and CSS

## Learning Objectives

By the end of this module, you will:
- Design a modern, professional UI layout
- Create reusable CSS components
- Implement responsive design for mobile devices
- Build an accessible and user-friendly interface

## Overview

In this module, we'll create the static user interface for our Todo App Pro. We'll focus on HTML structure and CSS styling before adding JavaScript functionality.

## 1. Designing the Homepage Layout

### Planning the UI Structure

**Your Conversation with Claude**:
```
You: Design a modern task management interface with:
- Top navigation bar (Logo, search, user menu)
- Sidebar for categories
- Main content area for tasks
- Add task button

Claude:
I'll create a clean, modern interface for you. The layout will be:

┌─────────────────────────────────────────────┐
│  [Logo] [Search Bar]        [User] [Logout] │ ← Header
├──────┬──────────────────────────────────────┤
│      │  📋 All Tasks                        │
│ ☰    │  ┌────────────────────────────────┐ │
│ Menu │  │ ✓ Complete project docs        │ │
│      │  │   Due: Dec 31  Priority: High  │ │
│ Work │  └────────────────────────────────┘ │
│ Home │                                      │ ← Main Content
│ Shop │  ┌────────────────────────────────┐ │
│      │  │ ○ Review pull requests         │ │
│      │  │   Due: Today   Priority: Med   │ │
│      │  └────────────────────────────────┘ │
│      │                                      │
│      │            [+ Add Task]              │
└──────┴──────────────────────────────────────┘
         ↑ Sidebar

Let me create this structure.
```

### Creating the HTML Structure

**client/index.html**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo App Pro - Task Management</title>
    <link rel="stylesheet" href="css/style.css">
    <link rel="stylesheet" href="css/components.css">
    <link rel="stylesheet" href="css/responsive.css">
</head>
<body>
    <!-- Header/Navigation -->
    <header class="header">
        <div class="header-left">
            <button class="menu-toggle" id="menuToggle">
                <span class="hamburger"></span>
            </button>
            <h1 class="logo">📝 Todo Pro</h1>
        </div>

        <div class="header-center">
            <div class="search-box">
                <input
                    type="text"
                    class="search-input"
                    id="searchInput"
                    placeholder="Search tasks...">
                <button class="search-btn">🔍</button>
            </div>
        </div>

        <div class="header-right">
            <div class="user-menu">
                <img src="assets/images/avatar.png" alt="User" class="user-avatar">
                <span class="user-name">John Doe</span>
                <button class="logout-btn" id="logoutBtn">Logout</button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <div class="container">
        <!-- Sidebar -->
        <aside class="sidebar" id="sidebar">
            <nav class="sidebar-nav">
                <h2 class="sidebar-title">Categories</h2>

                <ul class="category-list">
                    <li class="category-item active" data-category="all">
                        <span class="category-icon">📋</span>
                        <span class="category-name">All Tasks</span>
                        <span class="category-count">12</span>
                    </li>
                    <li class="category-item" data-category="work">
                        <span class="category-icon">💼</span>
                        <span class="category-name">Work</span>
                        <span class="category-count">5</span>
                    </li>
                    <li class="category-item" data-category="personal">
                        <span class="category-icon">🏠</span>
                        <span class="category-name">Personal</span>
                        <span class="category-count">4</span>
                    </li>
                    <li class="category-item" data-category="shopping">
                        <span class="category-icon">🛒</span>
                        <span class="category-name">Shopping</span>
                        <span class="category-count">3</span>
                    </li>
                </ul>

                <div class="sidebar-footer">
                    <button class="btn-add-category">+ New Category</button>
                </div>
            </nav>
        </aside>

        <!-- Main Content -->
        <main class="main-content">
            <!-- Filter Bar -->
            <div class="filter-bar">
                <div class="filter-group">
                    <button class="filter-btn active" data-filter="all">All</button>
                    <button class="filter-btn" data-filter="active">Active</button>
                    <button class="filter-btn" data-filter="completed">Completed</button>
                </div>

                <div class="sort-group">
                    <label for="sortSelect">Sort by:</label>
                    <select id="sortSelect" class="sort-select">
                        <option value="date">Date</option>
                        <option value="priority">Priority</option>
                        <option value="title">Title</option>
                    </select>
                </div>
            </div>

            <!-- Task List -->
            <div class="task-list" id="taskList">
                <!-- Tasks will be rendered here -->
                <!-- Example task card -->
                <div class="task-card">
                    <div class="task-header">
                        <input type="checkbox" class="task-checkbox" id="task1">
                        <label for="task1" class="task-title">Complete project documentation</label>
                    </div>

                    <p class="task-description">
                        Write comprehensive documentation for the API endpoints
                    </p>

                    <div class="task-meta">
                        <span class="task-category">Work</span>
                        <span class="task-priority priority-high">High</span>
                        <span class="task-date">📅 Dec 31, 2024</span>
                    </div>

                    <div class="task-actions">
                        <button class="btn-icon btn-edit" title="Edit">✏️</button>
                        <button class="btn-icon btn-delete" title="Delete">🗑️</button>
                    </div>
                </div>
            </div>

            <!-- Add Task Button -->
            <button class="fab" id="addTaskBtn" title="Add New Task">+</button>
        </main>
    </div>

    <!-- Modal for Adding/Editing Tasks -->
    <div class="modal" id="taskModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2 class="modal-title">Add New Task</h2>
                <button class="modal-close">&times;</button>
            </div>

            <form class="task-form" id="taskForm">
                <div class="form-group">
                    <label for="taskTitle">Title *</label>
                    <input
                        type="text"
                        id="taskTitle"
                        class="form-input"
                        placeholder="Enter task title"
                        required>
                </div>

                <div class="form-group">
                    <label for="taskDescription">Description</label>
                    <textarea
                        id="taskDescription"
                        class="form-textarea"
                        placeholder="Enter task description"
                        rows="4"></textarea>
                </div>

                <div class="form-row">
                    <div class="form-group">
                        <label for="taskCategory">Category</label>
                        <select id="taskCategory" class="form-select">
                            <option value="work">Work</option>
                            <option value="personal">Personal</option>
                            <option value="shopping">Shopping</option>
                        </select>
                    </div>

                    <div class="form-group">
                        <label for="taskPriority">Priority</label>
                        <select id="taskPriority" class="form-select">
                            <option value="low">Low</option>
                            <option value="medium">Medium</option>
                            <option value="high">High</option>
                        </select>
                    </div>
                </div>

                <div class="form-group">
                    <label for="taskDueDate">Due Date</label>
                    <input
                        type="date"
                        id="taskDueDate"
                        class="form-input">
                </div>

                <div class="form-actions">
                    <button type="button" class="btn btn-cancel">Cancel</button>
                    <button type="submit" class="btn btn-primary">Add Task</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Scripts -->
    <script src="js/storage.js"></script>
    <script src="js/task.js"></script>
    <script src="js/ui.js"></script>
    <script src="js/app.js"></script>
</body>
</html>
```

## 2. Styling the Interface

### Main Stylesheet

**client/css/style.css**:
```css
/* ==================== CSS Reset ==================== */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

/* ==================== CSS Variables ==================== */
:root {
    /* Colors */
    --primary-color: #3498db;
    --secondary-color: #2ecc71;
    --danger-color: #e74c3c;
    --warning-color: #f39c12;
    --info-color: #3498db;

    --dark-bg: #2c3e50;
    --light-bg: #ecf0f1;
    --white: #ffffff;
    --text-dark: #2c3e50;
    --text-light: #7f8c8d;
    --border-color: #ddd;

    /* Priority Colors */
    --priority-high: #e74c3c;
    --priority-medium: #f39c12;
    --priority-low: #3498db;

    /* Spacing */
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    --spacing-xl: 2rem;

    /* Borders */
    --border-radius: 8px;
    --border-radius-sm: 4px;

    /* Shadows */
    --shadow-sm: 0 1px 3px rgba(0,0,0,0.1);
    --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
    --shadow-lg: 0 10px 20px rgba(0,0,0,0.15);

    /* Transitions */
    --transition-fast: 0.2s ease;
    --transition-normal: 0.3s ease;
}

/* ==================== Base Styles ==================== */
body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto',
                 'Oxygen', 'Ubuntu', 'Cantarell', sans-serif;
    line-height: 1.6;
    color: var(--text-dark);
    background-color: #f5f7fa;
}

/* ==================== Header ==================== */
.header {
    background: var(--white);
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 var(--spacing-lg);
    box-shadow: var(--shadow-sm);
    position: sticky;
    top: 0;
    z-index: 100;
}

.header-left {
    display: flex;
    align-items: center;
    gap: var(--spacing-md);
}

.menu-toggle {
    background: none;
    border: none;
    cursor: pointer;
    padding: var(--spacing-sm);
    display: none; /* Show on mobile */
}

.logo {
    font-size: 1.5rem;
    font-weight: 600;
    color: var(--primary-color);
}

.header-center {
    flex: 1;
    max-width: 500px;
    margin: 0 var(--spacing-lg);
}

.search-box {
    display: flex;
    background: var(--light-bg);
    border-radius: var(--border-radius);
    overflow: hidden;
}

.search-input {
    flex: 1;
    border: none;
    background: transparent;
    padding: var(--spacing-sm) var(--spacing-md);
    font-size: 0.95rem;
    outline: none;
}

.search-btn {
    background: transparent;
    border: none;
    padding: var(--spacing-sm) var(--spacing-md);
    cursor: pointer;
    font-size: 1.2rem;
}

.header-right {
    display: flex;
    align-items: center;
}

.user-menu {
    display: flex;
    align-items: center;
    gap: var(--spacing-sm);
}

.user-avatar {
    width: 32px;
    height: 32px;
    border-radius: 50%;
    object-fit: cover;
}

.user-name {
    font-size: 0.9rem;
    font-weight: 500;
}

.logout-btn {
    background: var(--danger-color);
    color: white;
    border: none;
    padding: var(--spacing-sm) var(--spacing-md);
    border-radius: var(--border-radius-sm);
    cursor: pointer;
    font-size: 0.85rem;
    transition: var(--transition-fast);
}

.logout-btn:hover {
    background: #c0392b;
}

/* ==================== Layout ==================== */
.container {
    display: flex;
    height: calc(100vh - 60px);
}

/* ==================== Sidebar ==================== */
.sidebar {
    width: 260px;
    background: var(--white);
    border-right: 1px solid var(--border-color);
    padding: var(--spacing-lg);
    overflow-y: auto;
}

.sidebar-title {
    font-size: 0.85rem;
    text-transform: uppercase;
    color: var(--text-light);
    margin-bottom: var(--spacing-md);
    font-weight: 600;
    letter-spacing: 0.5px;
}

.category-list {
    list-style: none;
}

.category-item {
    display: flex;
    align-items: center;
    padding: var(--spacing-sm) var(--spacing-md);
    margin-bottom: var(--spacing-xs);
    border-radius: var(--border-radius-sm);
    cursor: pointer;
    transition: var(--transition-fast);
}

.category-item:hover {
    background: var(--light-bg);
}

.category-item.active {
    background: var(--primary-color);
    color: white;
}

.category-icon {
    font-size: 1.2rem;
    margin-right: var(--spacing-sm);
}

.category-name {
    flex: 1;
    font-size: 0.95rem;
}

.category-count {
    background: rgba(0,0,0,0.1);
    padding: 2px 8px;
    border-radius: 12px;
    font-size: 0.8rem;
}

.sidebar-footer {
    margin-top: var(--spacing-lg);
}

.btn-add-category {
    width: 100%;
    padding: var(--spacing-sm);
    background: transparent;
    border: 2px dashed var(--border-color);
    border-radius: var(--border-radius-sm);
    color: var(--text-light);
    cursor: pointer;
    transition: var(--transition-fast);
}

.btn-add-category:hover {
    border-color: var(--primary-color);
    color: var(--primary-color);
}

/* ==================== Main Content ==================== */
.main-content {
    flex: 1;
    padding: var(--spacing-lg);
    overflow-y: auto;
    position: relative;
}

.filter-bar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: var(--spacing-lg);
    background: var(--white);
    padding: var(--spacing-md);
    border-radius: var(--border-radius);
    box-shadow: var(--shadow-sm);
}

.filter-group {
    display: flex;
    gap: var(--spacing-sm);
}

.filter-btn {
    padding: var(--spacing-sm) var(--spacing-md);
    background: transparent;
    border: 1px solid var(--border-color);
    border-radius: var(--border-radius-sm);
    cursor: pointer;
    transition: var(--transition-fast);
    font-size: 0.9rem;
}

.filter-btn:hover {
    background: var(--light-bg);
}

.filter-btn.active {
    background: var(--primary-color);
    color: white;
    border-color: var(--primary-color);
}

.sort-group {
    display: flex;
    align-items: center;
    gap: var(--spacing-sm);
}

.sort-select {
    padding: var(--spacing-sm) var(--spacing-md);
    border: 1px solid var(--border-color);
    border-radius: var(--border-radius-sm);
    background: white;
    cursor: pointer;
    outline: none;
}

/* ==================== Floating Action Button ==================== */
.fab {
    position: fixed;
    bottom: 2rem;
    right: 2rem;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: var(--primary-color);
    color: white;
    border: none;
    font-size: 2rem;
    cursor: pointer;
    box-shadow: var(--shadow-lg);
    transition: var(--transition-normal);
    z-index: 50;
}

.fab:hover {
    transform: scale(1.1);
    box-shadow: 0 15px 30px rgba(52, 152, 219, 0.3);
}

.fab:active {
    transform: scale(0.95);
}
```

### Component Styles

**client/css/components.css**:
```css
/* ==================== Task Card Component ==================== */
.task-list {
    display: grid;
    gap: var(--spacing-md);
}

.task-card {
    background: var(--white);
    padding: var(--spacing-lg);
    border-radius: var(--border-radius);
    box-shadow: var(--shadow-sm);
    transition: var(--transition-fast);
    border-left: 4px solid transparent;
    position: relative;
}

.task-card:hover {
    box-shadow: var(--shadow-md);
    transform: translateY(-2px);
}

.task-card.priority-high {
    border-left-color: var(--priority-high);
}

.task-card.priority-medium {
    border-left-color: var(--priority-medium);
}

.task-card.priority-low {
    border-left-color: var(--priority-low);
}

.task-header {
    display: flex;
    align-items: center;
    gap: var(--spacing-sm);
    margin-bottom: var(--spacing-sm);
}

.task-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
}

.task-title {
    flex: 1;
    font-size: 1.1rem;
    font-weight: 600;
    cursor: pointer;
}

.task-card.completed .task-title {
    text-decoration: line-through;
    color: var(--text-light);
}

.task-description {
    color: var(--text-light);
    margin-bottom: var(--spacing-md);
    line-height: 1.5;
}

.task-meta {
    display: flex;
    gap: var(--spacing-sm);
    flex-wrap: wrap;
    margin-bottom: var(--spacing-md);
}

.task-category,
.task-priority,
.task-date {
    padding: var(--spacing-xs) var(--spacing-sm);
    border-radius: var(--border-radius-sm);
    font-size: 0.85rem;
    font-weight: 500;
}

.task-category {
    background: var(--light-bg);
    color: var(--text-dark);
}

.task-priority {
    color: white;
}

.priority-high {
    background: var(--priority-high);
}

.priority-medium {
    background: var(--priority-medium);
}

.priority-low {
    background: var(--priority-low);
}

.task-date {
    background: transparent;
    color: var(--text-light);
}

.task-actions {
    display: flex;
    gap: var(--spacing-sm);
    justify-content: flex-end;
}

.btn-icon {
    background: transparent;
    border: none;
    padding: var(--spacing-xs);
    cursor: pointer;
    font-size: 1.2rem;
    transition: var(--transition-fast);
    opacity: 0.6;
}

.btn-icon:hover {
    opacity: 1;
    transform: scale(1.2);
}

/* ==================== Modal Component ==================== */
.modal {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    z-index: 1000;
    align-items: center;
    justify-content: center;
}

.modal.active {
    display: flex;
}

.modal-content {
    background: var(--white);
    border-radius: var(--border-radius);
    width: 90%;
    max-width: 500px;
    max-height: 90vh;
    overflow-y: auto;
    box-shadow: var(--shadow-lg);
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        transform: translateY(-50px);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}

.modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: var(--spacing-lg);
    border-bottom: 1px solid var(--border-color);
}

.modal-title {
    font-size: 1.3rem;
    font-weight: 600;
}

.modal-close {
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    color: var(--text-light);
    transition: var(--transition-fast);
}

.modal-close:hover {
    color: var(--danger-color);
}

/* ==================== Form Component ==================== */
.task-form {
    padding: var(--spacing-lg);
}

.form-group {
    margin-bottom: var(--spacing-md);
}

.form-group label {
    display: block;
    margin-bottom: var(--spacing-xs);
    font-weight: 500;
    color: var(--text-dark);
    font-size: 0.9rem;
}

.form-input,
.form-textarea,
.form-select {
    width: 100%;
    padding: var(--spacing-sm) var(--spacing-md);
    border: 1px solid var(--border-color);
    border-radius: var(--border-radius-sm);
    font-family: inherit;
    font-size: 0.95rem;
    transition: var(--transition-fast);
    outline: none;
}

.form-input:focus,
.form-textarea:focus,
.form-select:focus {
    border-color: var(--primary-color);
    box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.1);
}

.form-textarea {
    resize: vertical;
    min-height: 100px;
}

.form-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: var(--spacing-md);
}

.form-actions {
    display: flex;
    gap: var(--spacing-sm);
    justify-content: flex-end;
    margin-top: var(--spacing-lg);
    padding-top: var(--spacing-lg);
    border-top: 1px solid var(--border-color);
}

/* ==================== Button Component ==================== */
.btn {
    padding: var(--spacing-sm) var(--spacing-lg);
    border: none;
    border-radius: var(--border-radius-sm);
    font-size: 0.95rem;
    font-weight: 500;
    cursor: pointer;
    transition: var(--transition-fast);
    font-family: inherit;
}

.btn-primary {
    background: var(--primary-color);
    color: white;
}

.btn-primary:hover {
    background: #2980b9;
}

.btn-cancel {
    background: transparent;
    color: var(--text-light);
    border: 1px solid var(--border-color);
}

.btn-cancel:hover {
    background: var(--light-bg);
}

/* ==================== Utility Classes ==================== */
.hidden {
    display: none !important;
}

.loading {
    text-align: center;
    padding: var(--spacing-xl);
    color: var(--text-light);
}
```

### Responsive Design

**client/css/responsive.css**:
```css
/* ==================== Tablet (768px and below) ==================== */
@media (max-width: 768px) {
    .header-center {
        max-width: 300px;
    }

    .user-name {
        display: none;
    }

    .sidebar {
        position: fixed;
        left: -260px;
        top: 60px;
        height: calc(100vh - 60px);
        z-index: 99;
        transition: left var(--transition-normal);
    }

    .sidebar.open {
        left: 0;
    }

    .menu-toggle {
        display: block;
    }

    .main-content {
        width: 100%;
    }

    .filter-bar {
        flex-direction: column;
        gap: var(--spacing-md);
    }

    .form-row {
        grid-template-columns: 1fr;
    }
}

/* ==================== Mobile (480px and below) ==================== */
@media (max-width: 480px) {
    .header {
        padding: 0 var(--spacing-sm);
    }

    .header-center {
        display: none; /* Hide search on small screens */
    }

    .logo {
        font-size: 1.2rem;
    }

    .main-content {
        padding: var(--spacing-sm);
    }

    .filter-bar {
        padding: var(--spacing-sm);
    }

    .filter-btn {
        padding: var(--spacing-xs) var(--spacing-sm);
        font-size: 0.85rem;
    }

    .task-card {
        padding: var(--spacing-md);
    }

    .fab {
        width: 50px;
        height: 50px;
        bottom: 1rem;
        right: 1rem;
        font-size: 1.5rem;
    }

    .modal-content {
        width: 95%;
    }

    .form-actions {
        flex-direction: column-reverse;
    }

    .btn {
        width: 100%;
    }
}
```

## 3. Testing the UI

### Viewing Your Design

**Your Conversation with Claude**:
```
You: How do I view the interface?

Claude:
Open client/index.html in your browser:

Method 1: Direct file opening
- Right-click index.html
- Open with browser

Method 2: Live Server (recommended)
- Install Live Server extension in VS Code
- Right-click index.html
- Select "Open with Live Server"

The page will open at http://localhost:5500

✅ You should see your task management interface!
```

## Key Takeaways

1. **Semantic HTML** - Use appropriate HTML5 elements
2. **CSS Variables** - Define reusable design tokens
3. **Component-Based CSS** - Organize styles by component
4. **Responsive Design** - Design mobile-first
5. **Accessibility** - Include labels, ARIA attributes

## Practice Exercise

**Task**: Customize the design
1. Change the color scheme (modify CSS variables)
2. Add a new category to the sidebar
3. Create an additional task card with different priority
4. Experiment with different layouts

## Next Steps

Now that you have a beautiful UI, move on to:

👉 **[1.4 Frontend Functionality](./04-frontend-logic.md)** - Add JavaScript interactivity

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)
