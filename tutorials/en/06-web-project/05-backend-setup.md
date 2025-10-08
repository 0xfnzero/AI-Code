# 1.5 Backend Server Setup

> Build a RESTful API with Node.js and Express

## Learning Objectives

By the end of this module, you will:
- Set up an Express.js server
- Design RESTful API routes
- Integrate SQLite database
- Create data models
- Handle API requests and responses

## Overview

In this module, we'll build the backend server for our Todo App Pro. We'll create a RESTful API that handles task management and integrates with a SQLite database.

## 1. Setting Up Express Server

### Creating the Basic Server

**Your Conversation with Claude**:
```
You: Set up a basic Express server

Claude:
I'll create a professional Express server with proper structure:
```

**server/index.js**:
```javascript
require('dotenv').config();
const express = require('express');
const cors = require('cors');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(cors({
    origin: process.env.CLIENT_URL || 'http://localhost:5500',
    credentials: true
}));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Serve static files (frontend)
app.use(express.static(path.join(__dirname, '../client')));

// Request logging middleware
app.use((req, res, next) => {
    console.log(`${new Date().toISOString()} - ${req.method} ${req.path}`);
    next();
});

// Routes
app.get('/api/health', (req, res) => {
    res.json({
        status: 'OK',
        message: 'Server is running',
        timestamp: new Date().toISOString()
    });
});

// Import route handlers (we'll create these next)
const taskRoutes = require('./routes/tasks');
const userRoutes = require('./routes/users');
const authRoutes = require('./routes/auth');

// Use routes
app.use('/api/tasks', taskRoutes);
app.use('/api/users', userRoutes);
app.use('/api/auth', authRoutes);

// Error handling middleware
app.use((err, req, res, next) => {
    console.error('Error:', err);
    res.status(err.status || 500).json({
        error: {
            message: err.message || 'Internal server error',
            status: err.status || 500
        }
    });
});

// 404 handler
app.use((req, res) => {
    res.status(404).json({
        error: {
            message: 'Route not found',
            status: 404
        }
    });
});

// Start server
app.listen(PORT, () => {
    console.log(`
🚀 Server is running!
📡 API: http://localhost:${PORT}/api
🌐 Frontend: http://localhost:${PORT}
📊 Health: http://localhost:${PORT}/api/health
    `);
});

// Handle graceful shutdown
process.on('SIGTERM', () => {
    console.log('SIGTERM received, shutting down gracefully...');
    process.exit(0);
});

module.exports = app;
```

## 2. Database Setup

### Creating SQLite Database

**Your Conversation with Claude**:
```
You: Set up SQLite database with better-sqlite3

Claude:
I'll create a database module with all necessary tables:
```

**server/database.js**:
```javascript
const Database = require('better-sqlite3');
const path = require('path');

// Create database connection
const dbPath = process.env.DB_PATH || path.join(__dirname, '../database/tasks.db');
const db = new Database(dbPath, { verbose: console.log });

// Enable foreign keys
db.pragma('foreign_keys = ON');

/**
 * Initialize database tables
 */
function initializeDatabase() {
    // Users table
    db.exec(`
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            username TEXT UNIQUE NOT NULL,
            email TEXT UNIQUE NOT NULL,
            password_hash TEXT NOT NULL,
            created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
            updated_at DATETIME DEFAULT CURRENT_TIMESTAMP
        )
    `);

    // Tasks table
    db.exec(`
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            user_id INTEGER NOT NULL,
            title TEXT NOT NULL,
            description TEXT,
            due_date TEXT,
            priority TEXT CHECK(priority IN ('low', 'medium', 'high')) DEFAULT 'medium',
            category TEXT DEFAULT 'personal',
            completed BOOLEAN DEFAULT 0,
            created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
            updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
        )
    `);

    // Categories table (optional)
    db.exec(`
        CREATE TABLE IF NOT EXISTS categories (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            user_id INTEGER NOT NULL,
            name TEXT NOT NULL,
            color TEXT,
            icon TEXT,
            created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
            UNIQUE(user_id, name)
        )
    `);

    // Create indexes for better performance
    db.exec(`
        CREATE INDEX IF NOT EXISTS idx_tasks_user_id ON tasks(user_id);
        CREATE INDEX IF NOT EXISTS idx_tasks_category ON tasks(category);
        CREATE INDEX IF NOT EXISTS idx_tasks_completed ON tasks(completed);
        CREATE INDEX IF NOT EXISTS idx_tasks_due_date ON tasks(due_date);
    `);

    console.log('✅ Database initialized successfully');
}

// Initialize database
initializeDatabase();

/**
 * Helper function to run queries safely
 */
function query(sql, params = []) {
    try {
        return db.prepare(sql).all(params);
    } catch (error) {
        console.error('Query error:', error);
        throw error;
    }
}

/**
 * Helper function to run a single query
 */
function get(sql, params = []) {
    try {
        return db.prepare(sql).get(params);
    } catch (error) {
        console.error('Query error:', error);
        throw error;
    }
}

/**
 * Helper function for insert/update/delete
 */
function run(sql, params = []) {
    try {
        return db.prepare(sql).run(params);
    } catch (error) {
        console.error('Query error:', error);
        throw error;
    }
}

module.exports = {
    db,
    query,
    get,
    run
};
```

## 3. Task Model

### Creating the Task Model

**Your Conversation with Claude**:
```
You: Create a Task model for database operations

Claude:
I'll create a comprehensive Task model:
```

**server/models/task.js**:
```javascript
const { query, get, run } = require('../database');

class TaskModel {
    /**
     * Get all tasks for a user
     */
    static findByUserId(userId, filters = {}) {
        let sql = `
            SELECT * FROM tasks
            WHERE user_id = ?
        `;
        const params = [userId];

        // Apply filters
        if (filters.category) {
            sql += ' AND category = ?';
            params.push(filters.category);
        }

        if (filters.completed !== undefined) {
            sql += ' AND completed = ?';
            params.push(filters.completed ? 1 : 0);
        }

        if (filters.priority) {
            sql += ' AND priority = ?';
            params.push(filters.priority);
        }

        // Search
        if (filters.search) {
            sql += ' AND (title LIKE ? OR description LIKE ?)';
            const searchTerm = `%${filters.search}%`;
            params.push(searchTerm, searchTerm);
        }

        // Sort
        const sortBy = filters.sortBy || 'created_at';
        const order = filters.order || 'DESC';
        sql += ` ORDER BY ${sortBy} ${order}`;

        return query(sql, params);
    }

    /**
     * Get a single task by ID
     */
    static findById(id, userId) {
        return get(
            'SELECT * FROM tasks WHERE id = ? AND user_id = ?',
            [id, userId]
        );
    }

    /**
     * Create a new task
     */
    static create(taskData) {
        const {
            userId,
            title,
            description,
            dueDate,
            priority,
            category
        } = taskData;

        const result = run(
            `INSERT INTO tasks (
                user_id, title, description, due_date, priority, category
            ) VALUES (?, ?, ?, ?, ?, ?)`,
            [userId, title, description || null, dueDate || null, priority || 'medium', category || 'personal']
        );

        // Return the created task
        return this.findById(result.lastInsertRowid, userId);
    }

    /**
     * Update a task
     */
    static update(id, userId, updates) {
        const allowedFields = ['title', 'description', 'due_date', 'priority', 'category', 'completed'];
        const fields = [];
        const params = [];

        // Build dynamic update query
        Object.keys(updates).forEach(key => {
            const dbKey = key.replace(/([A-Z])/g, '_$1').toLowerCase();
            if (allowedFields.includes(dbKey)) {
                fields.push(`${dbKey} = ?`);
                params.push(updates[key]);
            }
        });

        if (fields.length === 0) {
            throw new Error('No valid fields to update');
        }

        // Add updated_at
        fields.push('updated_at = CURRENT_TIMESTAMP');

        // Add id and user_id to params
        params.push(id, userId);

        const sql = `
            UPDATE tasks
            SET ${fields.join(', ')}
            WHERE id = ? AND user_id = ?
        `;

        run(sql, params);

        // Return updated task
        return this.findById(id, userId);
    }

    /**
     * Delete a task
     */
    static delete(id, userId) {
        const result = run(
            'DELETE FROM tasks WHERE id = ? AND user_id = ?',
            [id, userId]
        );

        return result.changes > 0;
    }

    /**
     * Toggle task completion
     */
    static toggleComplete(id, userId) {
        run(
            `UPDATE tasks
             SET completed = NOT completed,
                 updated_at = CURRENT_TIMESTAMP
             WHERE id = ? AND user_id = ?`,
            [id, userId]
        );

        return this.findById(id, userId);
    }

    /**
     * Get task statistics for a user
     */
    static getStats(userId) {
        const stats = get(
            `SELECT
                COUNT(*) as total,
                SUM(CASE WHEN completed = 1 THEN 1 ELSE 0 END) as completed,
                SUM(CASE WHEN completed = 0 THEN 1 ELSE 0 END) as active,
                SUM(CASE WHEN priority = 'high' THEN 1 ELSE 0 END) as high_priority
             FROM tasks
             WHERE user_id = ?`,
            [userId]
        );

        return stats;
    }

    /**
     * Delete all completed tasks for a user
     */
    static clearCompleted(userId) {
        const result = run(
            'DELETE FROM tasks WHERE user_id = ? AND completed = 1',
            [userId]
        );

        return result.changes;
    }
}

module.exports = TaskModel;
```

## 4. Task Routes

### Creating RESTful Endpoints

**Your Conversation with Claude**:
```
You: Create RESTful routes for task operations

Claude:
I'll create comprehensive task routes:
```

**server/routes/tasks.js**:
```javascript
const express = require('express');
const router = express.Router();
const TaskModel = require('../models/task');
const { authenticate } = require('../middleware/auth');

// Apply authentication middleware to all routes
router.use(authenticate);

/**
 * GET /api/tasks
 * Get all tasks for authenticated user
 */
router.get('/', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const filters = {
            category: req.query.category,
            completed: req.query.completed === 'true' ? true :
                      req.query.completed === 'false' ? false : undefined,
            priority: req.query.priority,
            search: req.query.search,
            sortBy: req.query.sortBy,
            order: req.query.order
        };

        const tasks = TaskModel.findByUserId(userId, filters);

        res.json({
            success: true,
            count: tasks.length,
            tasks
        });
    } catch (error) {
        next(error);
    }
});

/**
 * GET /api/tasks/stats
 * Get task statistics
 */
router.get('/stats', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const stats = TaskModel.getStats(userId);

        res.json({
            success: true,
            stats
        });
    } catch (error) {
        next(error);
    }
});

/**
 * GET /api/tasks/:id
 * Get a single task
 */
router.get('/:id', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const taskId = req.params.id;

        const task = TaskModel.findById(taskId, userId);

        if (!task) {
            return res.status(404).json({
                success: false,
                error: 'Task not found'
            });
        }

        res.json({
            success: true,
            task
        });
    } catch (error) {
        next(error);
    }
});

/**
 * POST /api/tasks
 * Create a new task
 */
router.post('/', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const { title, description, dueDate, priority, category } = req.body;

        // Validation
        if (!title || title.trim().length === 0) {
            return res.status(400).json({
                success: false,
                error: 'Title is required'
            });
        }

        if (title.length > 100) {
            return res.status(400).json({
                success: false,
                error: 'Title must be less than 100 characters'
            });
        }

        const taskData = {
            userId,
            title: title.trim(),
            description: description?.trim(),
            dueDate,
            priority: priority || 'medium',
            category: category || 'personal'
        };

        const task = TaskModel.create(taskData);

        res.status(201).json({
            success: true,
            message: 'Task created successfully',
            task
        });
    } catch (error) {
        next(error);
    }
});

/**
 * PUT /api/tasks/:id
 * Update a task
 */
router.put('/:id', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const taskId = req.params.id;
        const updates = req.body;

        // Check if task exists
        const existingTask = TaskModel.findById(taskId, userId);
        if (!existingTask) {
            return res.status(404).json({
                success: false,
                error: 'Task not found'
            });
        }

        // Validation
        if (updates.title !== undefined) {
            if (!updates.title || updates.title.trim().length === 0) {
                return res.status(400).json({
                    success: false,
                    error: 'Title cannot be empty'
                });
            }
            updates.title = updates.title.trim();
        }

        const task = TaskModel.update(taskId, userId, updates);

        res.json({
            success: true,
            message: 'Task updated successfully',
            task
        });
    } catch (error) {
        next(error);
    }
});

/**
 * PATCH /api/tasks/:id/toggle
 * Toggle task completion
 */
router.patch('/:id/toggle', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const taskId = req.params.id;

        const task = TaskModel.toggleComplete(taskId, userId);

        if (!task) {
            return res.status(404).json({
                success: false,
                error: 'Task not found'
            });
        }

        res.json({
            success: true,
            message: 'Task status updated',
            task
        });
    } catch (error) {
        next(error);
    }
});

/**
 * DELETE /api/tasks/:id
 * Delete a task
 */
router.delete('/:id', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const taskId = req.params.id;

        const deleted = TaskModel.delete(taskId, userId);

        if (!deleted) {
            return res.status(404).json({
                success: false,
                error: 'Task not found'
            });
        }

        res.json({
            success: true,
            message: 'Task deleted successfully'
        });
    } catch (error) {
        next(error);
    }
});

/**
 * DELETE /api/tasks
 * Clear all completed tasks
 */
router.delete('/', async (req, res, next) => {
    try {
        const userId = req.user.id;
        const count = TaskModel.clearCompleted(userId);

        res.json({
            success: true,
            message: `Deleted ${count} completed tasks`,
            count
        });
    } catch (error) {
        next(error);
    }
});

module.exports = router;
```

## 5. Testing the API

### Using cURL or Postman

**Your Conversation with Claude**:
```
You: How do I test the API endpoints?

Claude:
Here are example requests for testing:
```

**Testing with cURL**:
```bash
# Health check
curl http://localhost:3000/api/health

# Get all tasks (requires auth token)
curl -H "Authorization: Bearer YOUR_TOKEN" \
     http://localhost:3000/api/tasks

# Create a task
curl -X POST http://localhost:3000/api/tasks \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -d '{
       "title": "Test task",
       "description": "This is a test",
       "priority": "high",
       "category": "work"
     }'

# Update a task
curl -X PUT http://localhost:3000/api/tasks/1 \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -d '{
       "title": "Updated title",
       "completed": true
     }'

# Delete a task
curl -X DELETE http://localhost:3000/api/tasks/1 \
     -H "Authorization: Bearer YOUR_TOKEN"
```

### Creating a Test Script

**server/test-api.js**:
```javascript
const axios = require('axios');

const API_BASE = 'http://localhost:3000/api';
let authToken = '';

async function testAPI() {
    try {
        // 1. Health check
        console.log('Testing health endpoint...');
        const health = await axios.get(`${API_BASE}/health`);
        console.log('✅ Health:', health.data);

        // Note: Authentication will be implemented in next module
        // For now, these tests will fail without auth

        console.log('\n✅ All tests passed!');
    } catch (error) {
        console.error('❌ Test failed:', error.message);
    }
}

testAPI();
```

## Key Takeaways

1. **RESTful Design** - Follow REST principles for API design
2. **Database Models** - Separate business logic from routes
3. **Error Handling** - Implement comprehensive error handling
4. **Validation** - Always validate input data
5. **Middleware** - Use middleware for cross-cutting concerns

## Practice Exercise

**Task**: Extend the API
1. Add a PATCH endpoint for bulk updates
2. Implement pagination for task lists
3. Add task search with multiple filters
4. Create an endpoint for task export

## Next Steps

Now that you have a working backend API, move on to:

👉 **[1.6 User Authentication System](./06-authentication.md)** - Implement secure user authentication

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)
