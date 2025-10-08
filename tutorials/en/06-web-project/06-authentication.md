# 1.6 User Authentication System

> Implement secure user registration, login, and session management

## Learning Objectives

By the end of this module, you will:
- Implement user registration and login
- Hash passwords securely with bcrypt
- Generate and validate JWT tokens
- Create authentication middleware
- Protect API routes
- Build login/register pages

## Overview

In this module, we'll add a complete authentication system to our Todo App Pro, allowing users to register, log in, and securely manage their tasks.

## 1. User Model

### Creating the User Model

**Your Conversation with Claude**:
```
You: Create a User model for authentication

Claude:
I'll create a User model with password hashing and validation:
```

**server/models/user.js**:
```javascript
const bcrypt = require('bcryptjs');
const { query, get, run } = require('../database');

class UserModel {
    /**
     * Find user by email
     */
    static findByEmail(email) {
        return get('SELECT * FROM users WHERE email = ?', [email]);
    }

    /**
     * Find user by username
     */
    static findByUsername(username) {
        return get('SELECT * FROM users WHERE username = ?', [username]);
    }

    /**
     * Find user by ID
     */
    static findById(id) {
        return get('SELECT * FROM users WHERE id = ?', [id]);
    }

    /**
     * Create a new user
     */
    static async create(userData) {
        const { username, email, password } = userData;

        // Validate input
        const errors = this.validate(userData);
        if (errors.length > 0) {
            throw new Error(errors.join(', '));
        }

        // Check if user already exists
        if (this.findByEmail(email)) {
            throw new Error('Email already registered');
        }

        if (this.findByUsername(username)) {
            throw new Error('Username already taken');
        }

        // Hash password
        const passwordHash = await bcrypt.hash(password, 10);

        // Create user
        const result = run(
            `INSERT INTO users (username, email, password_hash)
             VALUES (?, ?, ?)`,
            [username, email, passwordHash]
        );

        // Return user without password
        const user = this.findById(result.lastInsertRowid);
        return this.sanitize(user);
    }

    /**
     * Verify user password
     */
    static async verifyPassword(email, password) {
        const user = this.findByEmail(email);

        if (!user) {
            return null;
        }

        const isValid = await bcrypt.compare(password, user.password_hash);

        if (!isValid) {
            return null;
        }

        return this.sanitize(user);
    }

    /**
     * Update user
     */
    static async update(id, updates) {
        const allowedFields = ['username', 'email'];
        const fields = [];
        const params = [];

        Object.keys(updates).forEach(key => {
            if (allowedFields.includes(key)) {
                fields.push(`${key} = ?`);
                params.push(updates[key]);
            }
        });

        if (fields.length === 0) {
            throw new Error('No valid fields to update');
        }

        // Add updated_at
        fields.push('updated_at = CURRENT_TIMESTAMP');
        params.push(id);

        run(
            `UPDATE users SET ${fields.join(', ')} WHERE id = ?`,
            params
        );

        const user = this.findById(id);
        return this.sanitize(user);
    }

    /**
     * Change password
     */
    static async changePassword(id, oldPassword, newPassword) {
        const user = this.findById(id);

        if (!user) {
            throw new Error('User not found');
        }

        // Verify old password
        const isValid = await bcrypt.compare(oldPassword, user.password_hash);
        if (!isValid) {
            throw new Error('Current password is incorrect');
        }

        // Hash new password
        const passwordHash = await bcrypt.hash(newPassword, 10);

        // Update password
        run(
            'UPDATE users SET password_hash = ?, updated_at = CURRENT_TIMESTAMP WHERE id = ?',
            [passwordHash, id]
        );

        return true;
    }

    /**
     * Delete user
     */
    static delete(id) {
        const result = run('DELETE FROM users WHERE id = ?', [id]);
        return result.changes > 0;
    }

    /**
     * Validate user data
     */
    static validate(userData) {
        const errors = [];
        const { username, email, password } = userData;

        // Username validation
        if (!username || username.trim().length < 3) {
            errors.push('Username must be at least 3 characters');
        }
        if (username && username.length > 50) {
            errors.push('Username must be less than 50 characters');
        }
        if (username && !/^[a-zA-Z0-9_]+$/.test(username)) {
            errors.push('Username can only contain letters, numbers, and underscores');
        }

        // Email validation
        if (!email || !this.isValidEmail(email)) {
            errors.push('Valid email is required');
        }

        // Password validation
        if (!password || password.length < 6) {
            errors.push('Password must be at least 6 characters');
        }
        if (password && password.length > 100) {
            errors.push('Password must be less than 100 characters');
        }

        return errors;
    }

    /**
     * Email validation
     */
    static isValidEmail(email) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return emailRegex.test(email);
    }

    /**
     * Remove sensitive data
     */
    static sanitize(user) {
        if (!user) return null;

        const { password_hash, ...sanitized } = user;
        return sanitized;
    }
}

module.exports = UserModel;
```

## 2. JWT Token Management

### Creating Token Utilities

**Your Conversation with Claude**:
```
You: Create utilities for JWT token generation and validation

Claude:
I'll create a comprehensive token management module:
```

**server/utils/token.js**:
```javascript
const jwt = require('jsonwebtoken');

const JWT_SECRET = process.env.JWT_SECRET || 'your-secret-key-change-in-production';
const JWT_EXPIRE = process.env.JWT_EXPIRE || '7d';

/**
 * Generate JWT token
 */
function generateToken(user) {
    const payload = {
        id: user.id,
        username: user.username,
        email: user.email
    };

    return jwt.sign(payload, JWT_SECRET, {
        expiresIn: JWT_EXPIRE
    });
}

/**
 * Verify JWT token
 */
function verifyToken(token) {
    try {
        return jwt.verify(token, JWT_SECRET);
    } catch (error) {
        return null;
    }
}

/**
 * Decode token without verification (for debugging)
 */
function decodeToken(token) {
    return jwt.decode(token);
}

/**
 * Generate refresh token (longer expiration)
 */
function generateRefreshToken(user) {
    const payload = {
        id: user.id,
        type: 'refresh'
    };

    return jwt.sign(payload, JWT_SECRET, {
        expiresIn: '30d'
    });
}

module.exports = {
    generateToken,
    verifyToken,
    decodeToken,
    generateRefreshToken
};
```

## 3. Authentication Middleware

### Creating Auth Middleware

**Your Conversation with Claude**:
```
You: Create middleware to protect routes

Claude:
I'll create authentication middleware:
```

**server/middleware/auth.js**:
```javascript
const { verifyToken } = require('../utils/token');
const UserModel = require('../models/user');

/**
 * Authentication middleware
 * Verifies JWT token and attaches user to request
 */
function authenticate(req, res, next) {
    try {
        // Get token from header
        const authHeader = req.headers.authorization;

        if (!authHeader || !authHeader.startsWith('Bearer ')) {
            return res.status(401).json({
                success: false,
                error: 'No token provided'
            });
        }

        // Extract token
        const token = authHeader.substring(7);

        // Verify token
        const decoded = verifyToken(token);

        if (!decoded) {
            return res.status(401).json({
                success: false,
                error: 'Invalid or expired token'
            });
        }

        // Check if user still exists
        const user = UserModel.findById(decoded.id);

        if (!user) {
            return res.status(401).json({
                success: false,
                error: 'User not found'
            });
        }

        // Attach user to request
        req.user = UserModel.sanitize(user);
        next();
    } catch (error) {
        res.status(401).json({
            success: false,
            error: 'Authentication failed'
        });
    }
}

/**
 * Optional authentication
 * Attaches user if token is valid, but doesn't fail if not
 */
function optionalAuth(req, res, next) {
    try {
        const authHeader = req.headers.authorization;

        if (authHeader && authHeader.startsWith('Bearer ')) {
            const token = authHeader.substring(7);
            const decoded = verifyToken(token);

            if (decoded) {
                const user = UserModel.findById(decoded.id);
                if (user) {
                    req.user = UserModel.sanitize(user);
                }
            }
        }
    } catch (error) {
        // Silently fail for optional auth
    }

    next();
}

module.exports = {
    authenticate,
    optionalAuth
};
```

## 4. Authentication Routes

### Creating Auth Endpoints

**Your Conversation with Claude**:
```
You: Create routes for registration and login

Claude:
I'll create comprehensive auth routes:
```

**server/routes/auth.js**:
```javascript
const express = require('express');
const router = express.Router();
const UserModel = require('../models/user');
const { generateToken } = require('../utils/token');
const { authenticate } = require('../middleware/auth');

/**
 * POST /api/auth/register
 * Register a new user
 */
router.post('/register', async (req, res, next) => {
    try {
        const { username, email, password } = req.body;

        // Create user
        const user = await UserModel.create({
            username,
            email,
            password
        });

        // Generate token
        const token = generateToken(user);

        res.status(201).json({
            success: true,
            message: 'User registered successfully',
            user,
            token
        });
    } catch (error) {
        res.status(400).json({
            success: false,
            error: error.message
        });
    }
});

/**
 * POST /api/auth/login
 * Login user
 */
router.post('/login', async (req, res, next) => {
    try {
        const { email, password } = req.body;

        // Validate input
        if (!email || !password) {
            return res.status(400).json({
                success: false,
                error: 'Email and password are required'
            });
        }

        // Verify credentials
        const user = await UserModel.verifyPassword(email, password);

        if (!user) {
            return res.status(401).json({
                success: false,
                error: 'Invalid email or password'
            });
        }

        // Generate token
        const token = generateToken(user);

        res.json({
            success: true,
            message: 'Login successful',
            user,
            token
        });
    } catch (error) {
        next(error);
    }
});

/**
 * GET /api/auth/profile
 * Get current user profile
 */
router.get('/profile', authenticate, (req, res) => {
    res.json({
        success: true,
        user: req.user
    });
});

/**
 * PUT /api/auth/profile
 * Update user profile
 */
router.put('/profile', authenticate, async (req, res, next) => {
    try {
        const userId = req.user.id;
        const updates = req.body;

        const user = await UserModel.update(userId, updates);

        res.json({
            success: true,
            message: 'Profile updated successfully',
            user
        });
    } catch (error) {
        res.status(400).json({
            success: false,
            error: error.message
        });
    }
});

/**
 * POST /api/auth/change-password
 * Change user password
 */
router.post('/change-password', authenticate, async (req, res, next) => {
    try {
        const userId = req.user.id;
        const { oldPassword, newPassword } = req.body;

        if (!oldPassword || !newPassword) {
            return res.status(400).json({
                success: false,
                error: 'Old and new passwords are required'
            });
        }

        await UserModel.changePassword(userId, oldPassword, newPassword);

        res.json({
            success: true,
            message: 'Password changed successfully'
        });
    } catch (error) {
        res.status(400).json({
            success: false,
            error: error.message
        });
    }
});

/**
 * POST /api/auth/logout
 * Logout user (client-side token removal)
 */
router.post('/logout', authenticate, (req, res) => {
    // With JWT, logout is handled client-side by removing the token
    // This endpoint is mainly for logging/tracking purposes
    res.json({
        success: true,
        message: 'Logout successful'
    });
});

module.exports = router;
```

## 5. Frontend Authentication

### Creating Auth Manager

**Your Conversation with Claude**:
```
You: Create frontend authentication utilities

Claude:
I'll create a comprehensive auth manager for the frontend:
```

**client/js/auth.js**:
```javascript
/**
 * Authentication Manager
 * Handles user authentication on the client side
 */
const AuthManager = {
    TOKEN_KEY: 'todoapp_token',
    USER_KEY: 'todoapp_user',

    /**
     * Register a new user
     */
    async register(username, email, password) {
        try {
            const response = await fetch('/api/auth/register', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ username, email, password })
            });

            const data = await response.json();

            if (!response.ok) {
                throw new Error(data.error || 'Registration failed');
            }

            // Save token and user
            this.saveToken(data.token);
            this.saveUser(data.user);

            return data;
        } catch (error) {
            throw error;
        }
    },

    /**
     * Login user
     */
    async login(email, password) {
        try {
            const response = await fetch('/api/auth/login', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ email, password })
            });

            const data = await response.json();

            if (!response.ok) {
                throw new Error(data.error || 'Login failed');
            }

            // Save token and user
            this.saveToken(data.token);
            this.saveUser(data.user);

            return data;
        } catch (error) {
            throw error;
        }
    },

    /**
     * Logout user
     */
    logout() {
        localStorage.removeItem(this.TOKEN_KEY);
        localStorage.removeItem(this.USER_KEY);
        window.location.href = '/login.html';
    },

    /**
     * Get current user
     */
    getUser() {
        const userData = localStorage.getItem(this.USER_KEY);
        return userData ? JSON.parse(userData) : null;
    },

    /**
     * Save user data
     */
    saveUser(user) {
        localStorage.setItem(this.USER_KEY, JSON.stringify(user));
    },

    /**
     * Get authentication token
     */
    getToken() {
        return localStorage.getItem(this.TOKEN_KEY);
    },

    /**
     * Save authentication token
     */
    saveToken(token) {
        localStorage.setItem(this.TOKEN_KEY, token);
    },

    /**
     * Check if user is logged in
     */
    isAuthenticated() {
        return !!this.getToken();
    },

    /**
     * Get auth headers for API requests
     */
    getAuthHeaders() {
        const token = this.getToken();
        return {
            'Content-Type': 'application/json',
            'Authorization': token ? `Bearer ${token}` : ''
        };
    },

    /**
     * Verify token is still valid
     */
    async verifyToken() {
        try {
            const response = await fetch('/api/auth/profile', {
                headers: this.getAuthHeaders()
            });

            if (!response.ok) {
                this.logout();
                return false;
            }

            return true;
        } catch (error) {
            return false;
        }
    },

    /**
     * Require authentication (redirect if not logged in)
     */
    requireAuth() {
        if (!this.isAuthenticated()) {
            window.location.href = '/login.html';
            return false;
        }
        return true;
    }
};
```

### Creating Login Page

**client/login.html**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login - Todo App Pro</title>
    <link rel="stylesheet" href="css/style.css">
    <style>
        .auth-container {
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }

        .auth-card {
            background: white;
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            width: 90%;
            max-width: 400px;
        }

        .auth-header {
            text-align: center;
            margin-bottom: 2rem;
        }

        .auth-header h1 {
            color: var(--primary-color);
            margin-bottom: 0.5rem;
        }

        .error-message {
            background: #fee;
            color: #c33;
            padding: 0.75rem;
            border-radius: 4px;
            margin-bottom: 1rem;
            display: none;
        }

        .error-message.show {
            display: block;
        }

        .auth-footer {
            text-align: center;
            margin-top: 1.5rem;
            color: var(--text-light);
        }

        .auth-footer a {
            color: var(--primary-color);
            text-decoration: none;
        }
    </style>
</head>
<body>
    <div class="auth-container">
        <div class="auth-card">
            <div class="auth-header">
                <h1>📝 Todo App Pro</h1>
                <p>Welcome back! Please login to your account.</p>
            </div>

            <div class="error-message" id="errorMessage"></div>

            <form id="loginForm">
                <div class="form-group">
                    <label for="email">Email</label>
                    <input
                        type="email"
                        id="email"
                        class="form-input"
                        placeholder="your@email.com"
                        required>
                </div>

                <div class="form-group">
                    <label for="password">Password</label>
                    <input
                        type="password"
                        id="password"
                        class="form-input"
                        placeholder="Enter your password"
                        required>
                </div>

                <button type="submit" class="btn btn-primary" style="width: 100%;">
                    Login
                </button>
            </form>

            <div class="auth-footer">
                Don't have an account?
                <a href="register.html">Sign up</a>
            </div>
        </div>
    </div>

    <script src="js/auth.js"></script>
    <script>
        const form = document.getElementById('loginForm');
        const errorMessage = document.getElementById('errorMessage');

        form.addEventListener('submit', async (e) => {
            e.preventDefault();

            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;

            try {
                errorMessage.classList.remove('show');
                await AuthManager.login(email, password);
                window.location.href = '/';
            } catch (error) {
                errorMessage.textContent = error.message;
                errorMessage.classList.add('show');
            }
        });
    </script>
</body>
</html>
```

### Creating Register Page

**client/register.html**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Register - Todo App Pro</title>
    <link rel="stylesheet" href="css/style.css">
    <style>
        /* Use same styles as login.html */
        .auth-container {
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }

        .auth-card {
            background: white;
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            width: 90%;
            max-width: 400px;
        }

        .auth-header {
            text-align: center;
            margin-bottom: 2rem;
        }

        .auth-header h1 {
            color: var(--primary-color);
            margin-bottom: 0.5rem;
        }

        .error-message {
            background: #fee;
            color: #c33;
            padding: 0.75rem;
            border-radius: 4px;
            margin-bottom: 1rem;
            display: none;
        }

        .error-message.show {
            display: block;
        }

        .auth-footer {
            text-align: center;
            margin-top: 1.5rem;
            color: var(--text-light);
        }

        .auth-footer a {
            color: var(--primary-color);
            text-decoration: none;
        }
    </style>
</head>
<body>
    <div class="auth-container">
        <div class="auth-card">
            <div class="auth-header">
                <h1>📝 Todo App Pro</h1>
                <p>Create your account to get started.</p>
            </div>

            <div class="error-message" id="errorMessage"></div>

            <form id="registerForm">
                <div class="form-group">
                    <label for="username">Username</label>
                    <input
                        type="text"
                        id="username"
                        class="form-input"
                        placeholder="Choose a username"
                        required>
                </div>

                <div class="form-group">
                    <label for="email">Email</label>
                    <input
                        type="email"
                        id="email"
                        class="form-input"
                        placeholder="your@email.com"
                        required>
                </div>

                <div class="form-group">
                    <label for="password">Password</label>
                    <input
                        type="password"
                        id="password"
                        class="form-input"
                        placeholder="Create a password (min 6 characters)"
                        required>
                </div>

                <button type="submit" class="btn btn-primary" style="width: 100%;">
                    Sign Up
                </button>
            </form>

            <div class="auth-footer">
                Already have an account?
                <a href="login.html">Login</a>
            </div>
        </div>
    </div>

    <script src="js/auth.js"></script>
    <script>
        const form = document.getElementById('registerForm');
        const errorMessage = document.getElementById('errorMessage');

        form.addEventListener('submit', async (e) => {
            e.preventDefault();

            const username = document.getElementById('username').value;
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;

            try {
                errorMessage.classList.remove('show');
                await AuthManager.register(username, email, password);
                window.location.href = '/';
            } catch (error) {
                errorMessage.textContent = error.message;
                errorMessage.classList.add('show');
            }
        });
    </script>
</body>
</html>
```

### Protecting the Main App

Add to **client/js/app.js** at the beginning:
```javascript
// Require authentication
if (!AuthManager.requireAuth()) {
    throw new Error('Authentication required');
}

// Update API client to use auth headers
const api = {
    async getTasks() {
        const response = await fetch('/api/tasks', {
            headers: AuthManager.getAuthHeaders()
        });
        return response.json();
    },

    async createTask(taskData) {
        const response = await fetch('/api/tasks', {
            method: 'POST',
            headers: AuthManager.getAuthHeaders(),
            body: JSON.stringify(taskData)
        });
        return response.json();
    },

    // ... update all other API methods similarly
};
```

## Key Takeaways

1. **Password Security** - Always hash passwords with bcrypt
2. **JWT Tokens** - Use tokens for stateless authentication
3. **Middleware** - Protect routes with authentication middleware
4. **Client Storage** - Store tokens securely in localStorage
5. **Validation** - Validate all user input on both client and server

## Practice Exercise

**Task**: Enhance authentication
1. Add "Remember Me" functionality
2. Implement password reset via email
3. Add OAuth login (Google, GitHub)
4. Implement two-factor authentication (2FA)

## Next Steps

Now that you have secure authentication, move on to:

👉 **[1.7 Feature Enhancement](./07-enhancements.md)** - Add advanced features

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)
