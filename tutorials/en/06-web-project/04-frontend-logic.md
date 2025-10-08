# 1.4 Frontend Functionality

> Bring your interface to life with JavaScript

## Learning Objectives

By the end of this module, you will:
- Implement task CRUD operations
- Work with local storage for data persistence
- Handle form validation
- Create dynamic UI interactions
- Manage application state

## Overview

In this module, we'll add JavaScript functionality to make our Todo App Pro fully interactive. We'll implement task management, local storage, and user interactions.

## 1. Task Data Model

### Creating the Task Class

**Your Conversation with Claude**:
```
You: Create a Task class to model our task data

Claude:
I'll create a Task class with all necessary properties and methods:
```

**client/js/task.js**:
```javascript
/**
 * Task Class
 * Represents a single task with all its properties
 */
class Task {
    constructor(title, description = '', dueDate = null, priority = 'medium', category = 'personal') {
        this.id = this.generateId();
        this.title = title;
        this.description = description;
        this.dueDate = dueDate;
        this.priority = priority; // 'low', 'medium', 'high'
        this.category = category;
        this.completed = false;
        this.createdAt = new Date().toISOString();
        this.updatedAt = new Date().toISOString();
    }

    /**
     * Generate unique ID for task
     */
    generateId() {
        return Date.now().toString(36) + Math.random().toString(36).substr(2);
    }

    /**
     * Toggle task completion status
     */
    toggleComplete() {
        this.completed = !this.completed;
        this.updatedAt = new Date().toISOString();
    }

    /**
     * Update task properties
     */
    update(updates) {
        Object.assign(this, updates);
        this.updatedAt = new Date().toISOString();
    }

    /**
     * Check if task is overdue
     */
    isOverdue() {
        if (!this.dueDate || this.completed) return false;
        return new Date(this.dueDate) < new Date();
    }

    /**
     * Get formatted due date
     */
    getFormattedDueDate() {
        if (!this.dueDate) return 'No due date';

        const date = new Date(this.dueDate);
        const today = new Date();
        const tomorrow = new Date(today);
        tomorrow.setDate(tomorrow.getDate() + 1);

        if (date.toDateString() === today.toDateString()) {
            return 'Today';
        } else if (date.toDateString() === tomorrow.toDateString()) {
            return 'Tomorrow';
        } else {
            return date.toLocaleDateString('en-US', {
                month: 'short',
                day: 'numeric',
                year: 'numeric'
            });
        }
    }

    /**
     * Convert to JSON for storage
     */
    toJSON() {
        return {
            id: this.id,
            title: this.title,
            description: this.description,
            dueDate: this.dueDate,
            priority: this.priority,
            category: this.category,
            completed: this.completed,
            createdAt: this.createdAt,
            updatedAt: this.updatedAt
        };
    }

    /**
     * Create Task from JSON data
     */
    static fromJSON(data) {
        const task = new Task(data.title);
        Object.assign(task, data);
        return task;
    }
}
```

## 2. Local Storage Management

### Storage Utility Functions

**Your Conversation with Claude**:
```
You: Implement local storage for persisting tasks

Claude:
I'll create a storage module to handle all localStorage operations:
```

**client/js/storage.js**:
```javascript
/**
 * Storage Manager
 * Handles all localStorage operations for the app
 */
const StorageManager = {
    STORAGE_KEY: 'todoapp_tasks',
    USER_KEY: 'todoapp_user',

    /**
     * Save tasks to localStorage
     */
    saveTasks(tasks) {
        try {
            const tasksJSON = tasks.map(task => task.toJSON());
            localStorage.setItem(this.STORAGE_KEY, JSON.stringify(tasksJSON));
            return true;
        } catch (error) {
            console.error('Error saving tasks:', error);
            return false;
        }
    },

    /**
     * Load tasks from localStorage
     */
    loadTasks() {
        try {
            const data = localStorage.getItem(this.STORAGE_KEY);
            if (!data) return [];

            const tasksData = JSON.parse(data);
            return tasksData.map(taskData => Task.fromJSON(taskData));
        } catch (error) {
            console.error('Error loading tasks:', error);
            return [];
        }
    },

    /**
     * Clear all tasks
     */
    clearTasks() {
        localStorage.removeItem(this.STORAGE_KEY);
    },

    /**
     * Save user data
     */
    saveUser(userData) {
        localStorage.setItem(this.USER_KEY, JSON.stringify(userData));
    },

    /**
     * Load user data
     */
    loadUser() {
        const data = localStorage.getItem(this.USER_KEY);
        return data ? JSON.parse(data) : null;
    },

    /**
     * Clear user data (logout)
     */
    clearUser() {
        localStorage.removeItem(this.USER_KEY);
    },

    /**
     * Export tasks as JSON file
     */
    exportTasks(tasks) {
        const dataStr = JSON.stringify(tasks, null, 2);
        const dataBlob = new Blob([dataStr], { type: 'application/json' });
        const url = URL.createObjectURL(dataBlob);
        const link = document.createElement('a');
        link.href = url;
        link.download = `tasks_${Date.now()}.json`;
        link.click();
        URL.revokeObjectURL(url);
    },

    /**
     * Import tasks from JSON file
     */
    async importTasks(file) {
        return new Promise((resolve, reject) => {
            const reader = new FileReader();
            reader.onload = (e) => {
                try {
                    const tasks = JSON.parse(e.target.result);
                    const taskObjects = tasks.map(data => Task.fromJSON(data));
                    resolve(taskObjects);
                } catch (error) {
                    reject(error);
                }
            };
            reader.onerror = reject;
            reader.readAsText(file);
        });
    }
};
```

## 3. UI Manager

### Creating Dynamic UI Updates

**Your Conversation with Claude**:
```
You: Create a UI manager to handle DOM manipulation

Claude:
I'll create a comprehensive UI manager:
```

**client/js/ui.js**:
```javascript
/**
 * UI Manager
 * Handles all DOM manipulation and UI updates
 */
const UIManager = {
    elements: {
        taskList: document.getElementById('taskList'),
        addTaskBtn: document.getElementById('addTaskBtn'),
        taskModal: document.getElementById('taskModal'),
        taskForm: document.getElementById('taskForm'),
        searchInput: document.getElementById('searchInput'),
        categoryItems: document.querySelectorAll('.category-item'),
        filterBtns: document.querySelectorAll('.filter-btn'),
        sortSelect: document.getElementById('sortSelect'),
        menuToggle: document.getElementById('menuToggle'),
        sidebar: document.getElementById('sidebar'),
        logoutBtn: document.getElementById('logoutBtn')
    },

    /**
     * Render all tasks
     */
    renderTasks(tasks) {
        if (tasks.length === 0) {
            this.showEmptyState();
            return;
        }

        this.elements.taskList.innerHTML = tasks.map(task =>
            this.createTaskCard(task)
        ).join('');

        // Add event listeners to task cards
        this.attachTaskEventListeners();
    },

    /**
     * Create HTML for a single task card
     */
    createTaskCard(task) {
        const overdueClass = task.isOverdue() ? 'overdue' : '';
        const completedClass = task.completed ? 'completed' : '';

        return `
            <div class="task-card priority-${task.priority} ${completedClass} ${overdueClass}"
                 data-task-id="${task.id}">
                <div class="task-header">
                    <input type="checkbox"
                           class="task-checkbox"
                           id="task-${task.id}"
                           ${task.completed ? 'checked' : ''}
                           onchange="App.toggleTaskComplete('${task.id}')">
                    <label for="task-${task.id}" class="task-title">
                        ${this.escapeHtml(task.title)}
                    </label>
                </div>

                ${task.description ? `
                    <p class="task-description">${this.escapeHtml(task.description)}</p>
                ` : ''}

                <div class="task-meta">
                    <span class="task-category">${task.category}</span>
                    <span class="task-priority priority-${task.priority}">
                        ${task.priority}
                    </span>
                    ${task.dueDate ? `
                        <span class="task-date">📅 ${task.getFormattedDueDate()}</span>
                    ` : ''}
                </div>

                <div class="task-actions">
                    <button class="btn-icon btn-edit"
                            onclick="App.editTask('${task.id}')"
                            title="Edit">
                        ✏️
                    </button>
                    <button class="btn-icon btn-delete"
                            onclick="App.deleteTask('${task.id}')"
                            title="Delete">
                        🗑️
                    </button>
                </div>
            </div>
        `;
    },

    /**
     * Show empty state when no tasks
     */
    showEmptyState() {
        this.elements.taskList.innerHTML = `
            <div class="empty-state">
                <div class="empty-icon">📝</div>
                <h3>No tasks yet</h3>
                <p>Click the + button to add your first task</p>
            </div>
        `;
    },

    /**
     * Open task modal for adding/editing
     */
    openTaskModal(task = null) {
        const modal = this.elements.taskModal;
        const form = this.elements.taskForm;

        // Set modal title
        const title = modal.querySelector('.modal-title');
        title.textContent = task ? 'Edit Task' : 'Add New Task';

        // Populate form if editing
        if (task) {
            form.elements.taskTitle.value = task.title;
            form.elements.taskDescription.value = task.description;
            form.elements.taskCategory.value = task.category;
            form.elements.taskPriority.value = task.priority;
            form.elements.taskDueDate.value = task.dueDate || '';
            form.dataset.taskId = task.id;
        } else {
            form.reset();
            delete form.dataset.taskId;
        }

        modal.classList.add('active');
    },

    /**
     * Close task modal
     */
    closeTaskModal() {
        this.elements.taskModal.classList.remove('active');
        this.elements.taskForm.reset();
    },

    /**
     * Show notification message
     */
    showNotification(message, type = 'success') {
        const notification = document.createElement('div');
        notification.className = `notification notification-${type}`;
        notification.textContent = message;

        document.body.appendChild(notification);

        // Animate in
        setTimeout(() => notification.classList.add('show'), 10);

        // Remove after 3 seconds
        setTimeout(() => {
            notification.classList.remove('show');
            setTimeout(() => notification.remove(), 300);
        }, 3000);
    },

    /**
     * Show confirmation dialog
     */
    showConfirm(message) {
        return confirm(message);
    },

    /**
     * Update category counts
     */
    updateCategoryCounts(tasks) {
        const counts = {
            all: tasks.length,
            work: tasks.filter(t => t.category === 'work').length,
            personal: tasks.filter(t => t.category === 'personal').length,
            shopping: tasks.filter(t => t.category === 'shopping').length
        };

        this.elements.categoryItems.forEach(item => {
            const category = item.dataset.category;
            const countEl = item.querySelector('.category-count');
            if (countEl && counts[category] !== undefined) {
                countEl.textContent = counts[category];
            }
        });
    },

    /**
     * Escape HTML to prevent XSS
     */
    escapeHtml(text) {
        const div = document.createElement('div');
        div.textContent = text;
        return div.innerHTML;
    },

    /**
     * Attach event listeners to task cards
     */
    attachTaskEventListeners() {
        // Drag and drop for reordering (optional enhancement)
        const taskCards = document.querySelectorAll('.task-card');
        taskCards.forEach(card => {
            card.draggable = true;
            card.addEventListener('dragstart', this.handleDragStart);
            card.addEventListener('dragover', this.handleDragOver);
            card.addEventListener('drop', this.handleDrop);
        });
    },

    handleDragStart(e) {
        e.dataTransfer.effectAllowed = 'move';
        e.dataTransfer.setData('text/html', e.target.dataset.taskId);
    },

    handleDragOver(e) {
        e.preventDefault();
        e.dataTransfer.dropEffect = 'move';
    },

    handleDrop(e) {
        e.preventDefault();
        // Implement reordering logic in App
    }
};

// Add notification styles to components.css
const style = document.createElement('style');
style.textContent = `
    .notification {
        position: fixed;
        top: 80px;
        right: 20px;
        padding: 1rem 1.5rem;
        border-radius: 8px;
        background: white;
        box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        transform: translateX(400px);
        transition: transform 0.3s ease;
        z-index: 1001;
    }

    .notification.show {
        transform: translateX(0);
    }

    .notification-success {
        border-left: 4px solid var(--secondary-color);
    }

    .notification-error {
        border-left: 4px solid var(--danger-color);
    }

    .notification-warning {
        border-left: 4px solid var(--warning-color);
    }

    .empty-state {
        text-align: center;
        padding: 4rem 2rem;
        color: var(--text-light);
    }

    .empty-icon {
        font-size: 4rem;
        margin-bottom: 1rem;
    }

    .empty-state h3 {
        font-size: 1.5rem;
        margin-bottom: 0.5rem;
        color: var(--text-dark);
    }
`;
document.head.appendChild(style);
```

## 4. Main Application Logic

### Creating the App Controller

**Your Conversation with Claude**:
```
You: Create the main app controller that ties everything together

Claude:
I'll create the main application logic:
```

**client/js/app.js**:
```javascript
/**
 * Main Application Controller
 */
const App = {
    tasks: [],
    currentFilter: 'all',
    currentCategory: 'all',
    currentSort: 'date',

    /**
     * Initialize the application
     */
    init() {
        console.log('Initializing Todo App Pro...');

        // Load tasks from storage
        this.loadTasks();

        // Setup event listeners
        this.setupEventListeners();

        // Render initial state
        this.renderTasks();

        console.log('App initialized successfully!');
    },

    /**
     * Load tasks from storage
     */
    loadTasks() {
        this.tasks = StorageManager.loadTasks();
        console.log(`Loaded ${this.tasks.length} tasks`);
    },

    /**
     * Save tasks to storage
     */
    saveTasks() {
        StorageManager.saveTasks(this.tasks);
    },

    /**
     * Setup all event listeners
     */
    setupEventListeners() {
        // Add task button
        UIManager.elements.addTaskBtn.addEventListener('click', () => {
            UIManager.openTaskModal();
        });

        // Modal close buttons
        const closeButtons = document.querySelectorAll('.modal-close, .btn-cancel');
        closeButtons.forEach(btn => {
            btn.addEventListener('click', () => UIManager.closeTaskModal());
        });

        // Task form submission
        UIManager.elements.taskForm.addEventListener('submit', (e) => {
            e.preventDefault();
            this.handleTaskFormSubmit();
        });

        // Search input
        UIManager.elements.searchInput.addEventListener('input', (e) => {
            this.handleSearch(e.target.value);
        });

        // Category filter
        UIManager.elements.categoryItems.forEach(item => {
            item.addEventListener('click', () => {
                this.handleCategoryChange(item.dataset.category);
                // Update active state
                UIManager.elements.categoryItems.forEach(i =>
                    i.classList.remove('active')
                );
                item.classList.add('active');
            });
        });

        // Status filter
        UIManager.elements.filterBtns.forEach(btn => {
            btn.addEventListener('click', () => {
                this.handleFilterChange(btn.dataset.filter);
                // Update active state
                UIManager.elements.filterBtns.forEach(b =>
                    b.classList.remove('active')
                );
                btn.classList.add('active');
            });
        });

        // Sort select
        UIManager.elements.sortSelect.addEventListener('change', (e) => {
            this.handleSortChange(e.target.value);
        });

        // Menu toggle (mobile)
        UIManager.elements.menuToggle.addEventListener('click', () => {
            UIManager.elements.sidebar.classList.toggle('open');
        });

        // Logout button
        UIManager.elements.logoutBtn.addEventListener('click', () => {
            this.handleLogout();
        });

        // Close modal on outside click
        UIManager.elements.taskModal.addEventListener('click', (e) => {
            if (e.target === UIManager.elements.taskModal) {
                UIManager.closeTaskModal();
            }
        });
    },

    /**
     * Handle task form submission
     */
    handleTaskFormSubmit() {
        const form = UIManager.elements.taskForm;
        const taskId = form.dataset.taskId;

        // Get form values
        const taskData = {
            title: form.elements.taskTitle.value.trim(),
            description: form.elements.taskDescription.value.trim(),
            category: form.elements.taskCategory.value,
            priority: form.elements.taskPriority.value,
            dueDate: form.elements.taskDueDate.value || null
        };

        // Validate
        if (!taskData.title) {
            UIManager.showNotification('Please enter a task title', 'error');
            return;
        }

        if (taskId) {
            // Update existing task
            this.updateTask(taskId, taskData);
        } else {
            // Create new task
            this.createTask(taskData);
        }

        UIManager.closeTaskModal();
    },

    /**
     * Create a new task
     */
    createTask(taskData) {
        const task = new Task(
            taskData.title,
            taskData.description,
            taskData.dueDate,
            taskData.priority,
            taskData.category
        );

        this.tasks.push(task);
        this.saveTasks();
        this.renderTasks();

        UIManager.showNotification('Task created successfully!', 'success');
    },

    /**
     * Update an existing task
     */
    updateTask(taskId, updates) {
        const task = this.tasks.find(t => t.id === taskId);
        if (task) {
            task.update(updates);
            this.saveTasks();
            this.renderTasks();
            UIManager.showNotification('Task updated successfully!', 'success');
        }
    },

    /**
     * Delete a task
     */
    deleteTask(taskId) {
        if (UIManager.showConfirm('Are you sure you want to delete this task?')) {
            this.tasks = this.tasks.filter(t => t.id !== taskId);
            this.saveTasks();
            this.renderTasks();
            UIManager.showNotification('Task deleted', 'success');
        }
    },

    /**
     * Toggle task completion
     */
    toggleTaskComplete(taskId) {
        const task = this.tasks.find(t => t.id === taskId);
        if (task) {
            task.toggleComplete();
            this.saveTasks();
            this.renderTasks();
        }
    },

    /**
     * Edit a task
     */
    editTask(taskId) {
        const task = this.tasks.find(t => t.id === taskId);
        if (task) {
            UIManager.openTaskModal(task);
        }
    },

    /**
     * Handle search
     */
    handleSearch(query) {
        this.searchQuery = query.toLowerCase();
        this.renderTasks();
    },

    /**
     * Handle category filter change
     */
    handleCategoryChange(category) {
        this.currentCategory = category;
        this.renderTasks();
    },

    /**
     * Handle status filter change
     */
    handleFilterChange(filter) {
        this.currentFilter = filter;
        this.renderTasks();
    },

    /**
     * Handle sort change
     */
    handleSortChange(sortBy) {
        this.currentSort = sortBy;
        this.renderTasks();
    },

    /**
     * Get filtered and sorted tasks
     */
    getFilteredTasks() {
        let filtered = [...this.tasks];

        // Apply category filter
        if (this.currentCategory !== 'all') {
            filtered = filtered.filter(t => t.category === this.currentCategory);
        }

        // Apply status filter
        if (this.currentFilter === 'active') {
            filtered = filtered.filter(t => !t.completed);
        } else if (this.currentFilter === 'completed') {
            filtered = filtered.filter(t => t.completed);
        }

        // Apply search
        if (this.searchQuery) {
            filtered = filtered.filter(t =>
                t.title.toLowerCase().includes(this.searchQuery) ||
                t.description.toLowerCase().includes(this.searchQuery)
            );
        }

        // Apply sort
        filtered.sort((a, b) => {
            switch (this.currentSort) {
                case 'date':
                    return new Date(b.createdAt) - new Date(a.createdAt);
                case 'priority':
                    const priorityValue = { high: 3, medium: 2, low: 1 };
                    return priorityValue[b.priority] - priorityValue[a.priority];
                case 'title':
                    return a.title.localeCompare(b.title);
                default:
                    return 0;
            }
        });

        return filtered;
    },

    /**
     * Render tasks with current filters
     */
    renderTasks() {
        const filtered = this.getFilteredTasks();
        UIManager.renderTasks(filtered);
        UIManager.updateCategoryCounts(this.tasks);
    },

    /**
     * Handle logout
     */
    handleLogout() {
        if (UIManager.showConfirm('Are you sure you want to logout?')) {
            StorageManager.clearUser();
            window.location.href = 'login.html';
        }
    }
};

// Initialize app when DOM is ready
document.addEventListener('DOMContentLoaded', () => {
    App.init();
});

// Make App globally accessible for inline event handlers
window.App = App;
```

## 5. Form Validation

### Adding Input Validation

**Your Conversation with Claude**:
```
You: Add better form validation

Claude:
I'll add comprehensive validation:
```

Add to **client/js/app.js**:
```javascript
/**
 * Validate task form
 */
validateTaskForm(formData) {
    const errors = [];

    // Title validation
    if (!formData.title || formData.title.trim().length === 0) {
        errors.push('Title is required');
    }
    if (formData.title.length > 100) {
        errors.push('Title must be less than 100 characters');
    }

    // Description validation
    if (formData.description.length > 500) {
        errors.push('Description must be less than 500 characters');
    }

    // Due date validation
    if (formData.dueDate) {
        const dueDate = new Date(formData.dueDate);
        const today = new Date();
        today.setHours(0, 0, 0, 0);

        if (dueDate < today) {
            errors.push('Due date cannot be in the past');
        }
    }

    return errors;
}
```

## Key Takeaways

1. **Class-Based Design** - Use classes for data models
2. **Separation of Concerns** - Keep storage, UI, and logic separate
3. **Local Storage** - Persist data in the browser
4. **Event Handling** - Properly manage user interactions
5. **Data Validation** - Always validate user input

## Practice Exercise

**Task**: Add new features
1. Add a "Clear Completed" button
2. Implement task duplication
3. Add keyboard shortcuts (e.g., Ctrl+N for new task)
4. Create a "Mark All Complete" function

## Next Steps

Now that you have a fully functional frontend, move on to:

👉 **[1.5 Backend Server Setup](./05-backend-setup.md)** - Build the backend API

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)
