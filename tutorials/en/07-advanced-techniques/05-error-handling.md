# 1.5 Error Handling Best Practices

> Build robust applications with comprehensive error handling

## Overview

Proper error handling is the difference between a fragile app and a production-ready system. This module covers best practices for catching, handling, and recovering from errors.

## 1. Unified Error Handling System

### Custom Error Classes

```javascript
// Base application error
class AppError extends Error {
  constructor(message, statusCode, code) {
    super(message)
    this.statusCode = statusCode
    this.code = code
    this.isOperational = true
    Error.captureStackTrace(this, this.constructor)
  }
}

// Specific error types
class ValidationError extends AppError {
  constructor(message, field) {
    super(message, 400, 'VALIDATION_ERROR')
    this.field = field
  }
}

class AuthError extends AppError {
  constructor(message) {
    super(message, 401, 'AUTH_ERROR')
  }
}

class NotFoundError extends AppError {
  constructor(resource) {
    super(`${resource} not found`, 404, 'NOT_FOUND')
    this.resource = resource
  }
}

class DatabaseError extends AppError {
  constructor(message) {
    super(message, 500, 'DATABASE_ERROR')
  }
}
```

### Global Error Handler

```javascript
class ErrorHandler {
  static handle(error) {
    if (error.isOperational) {
      return this.handleOperationalError(error)
    }
    return this.handleProgrammerError(error)
  }

  static handleOperationalError(error) {
    // User-friendly error message
    const message = this.getUserMessage(error)
    this.showNotification(message, 'error')

    // Log for debugging
    console.error('[Operational Error]', {
      code: error.code,
      message: error.message,
      statusCode: error.statusCode,
      stack: error.stack
    })

    // Report to monitoring service
    this.reportError(error)
  }

  static handleProgrammerError(error) {
    // Critical error - needs developer attention
    console.error('[Programmer Error - CRITICAL]', error)

    // Show generic error message
    this.showNotification(
      'Something went wrong. Please refresh and try again.',
      'error'
    )

    // Always report programmer errors
    this.reportError(error, 'critical')

    // In development, you might want to throw
    if (process.env.NODE_ENV === 'development') {
      throw error
    }
  }

  static getUserMessage(error) {
    const messages = {
      'VALIDATION_ERROR': `Invalid input: ${error.message}`,
      'AUTH_ERROR': 'Authentication failed. Please log in again.',
      'NOT_FOUND': 'The requested resource was not found.',
      'NETWORK_ERROR': 'Network connection failed. Please check your connection.',
      'DATABASE_ERROR': 'A server error occurred. Please try again later.'
    }

    return messages[error.code] || 'An unexpected error occurred.'
  }

  static showNotification(message, type) {
    // Implement your notification system
    console.log(`[${type.toUpperCase()}]`, message)
    // Example: toast.error(message)
  }

  static reportError(error, severity = 'error') {
    // Send to error monitoring service (Sentry, LogRocket, etc.)
    // Example: Sentry.captureException(error)
    console.log(`[REPORT ${severity}]`, error.message)
  }
}

// Global error capture
window.addEventListener('error', (event) => {
  ErrorHandler.handle(event.error)
})

window.addEventListener('unhandledrejection', (event) => {
  ErrorHandler.handle(event.reason)
})
```

### Usage Example

```javascript
async function login(username, password) {
  try {
    // Validation
    if (!username) {
      throw new ValidationError('Username is required', 'username')
    }

    if (!password || password.length < 8) {
      throw new ValidationError(
        'Password must be at least 8 characters',
        'password'
      )
    }

    // API call
    const response = await api.login(username, password)

    if (!response.ok) {
      if (response.status === 401) {
        throw new AuthError('Invalid username or password')
      }
      throw new Error('Login failed')
    }

    return response.data

  } catch (error) {
    ErrorHandler.handle(error)
    throw error // Re-throw if caller needs to handle
  }
}
```

## 2. API Request Error Handling

### Robust API Client

```javascript
class APIClient {
  constructor(baseURL, options = {}) {
    this.baseURL = baseURL
    this.timeout = options.timeout || 10000
    this.retryCount = options.retryCount || 3
    this.retryDelay = options.retryDelay || 1000
  }

  async request(endpoint, options = {}) {
    const url = `${this.baseURL}${endpoint}`
    let lastError

    for (let attempt = 0; attempt < this.retryCount; attempt++) {
      try {
        const response = await Promise.race([
          fetch(url, {
            ...options,
            headers: {
              'Content-Type': 'application/json',
              ...this.getAuthHeaders(),
              ...options.headers
            }
          }),
          this.timeoutPromise(this.timeout)
        ])

        if (!response.ok) {
          throw await this.handleHTTPError(response)
        }

        return await response.json()

      } catch (error) {
        lastError = error

        console.log(`Attempt ${attempt + 1}/${this.retryCount} failed:`, error.message)

        if (!this.shouldRetry(error, attempt)) {
          break
        }

        await this.delay(this.retryDelay * (attempt + 1))
      }
    }

    throw lastError
  }

  timeoutPromise(ms) {
    return new Promise((_, reject) =>
      setTimeout(() => reject(new Error('Request timeout')), ms)
    )
  }

  delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms))
  }

  shouldRetry(error, attempt) {
    if (attempt >= this.retryCount - 1) return false

    // Retry on timeout or network errors
    if (error.message === 'Request timeout' || error.message === 'Failed to fetch') {
      return true
    }

    // Retry on 5xx errors
    if (error.statusCode >= 500) {
      return true
    }

    return false
  }

  async handleHTTPError(response) {
    let errorData
    try {
      errorData = await response.json()
    } catch {
      errorData = { message: response.statusText }
    }

    const error = new Error(errorData.message || 'Request failed')
    error.statusCode = response.status
    error.response = errorData
    return error
  }

  getAuthHeaders() {
    const token = localStorage.getItem('auth_token')
    return token ? { 'Authorization': `Bearer ${token}` } : {}
  }

  // Convenience methods
  get(endpoint, options) {
    return this.request(endpoint, { ...options, method: 'GET' })
  }

  post(endpoint, data, options) {
    return this.request(endpoint, {
      ...options,
      method: 'POST',
      body: JSON.stringify(data)
    })
  }

  put(endpoint, data, options) {
    return this.request(endpoint, {
      ...options,
      method: 'PUT',
      body: JSON.stringify(data)
    })
  }

  delete(endpoint, options) {
    return this.request(endpoint, { ...options, method: 'DELETE' })
  }
}

// Usage
const api = new APIClient('https://api.example.com', {
  timeout: 5000,
  retryCount: 3,
  retryDelay: 1000
})

try {
  const users = await api.get('/users')
  const newUser = await api.post('/users', { name: 'John' })
} catch (error) {
  console.error('API request failed:', error)
}
```

## 3. Try-Catch Best Practices

### What to Catch

```javascript
// ✅ DO: Catch specific operations that can fail
async function loadUserData(userId) {
  try {
    const response = await fetch(`/api/users/${userId}`)
    const data = await response.json()
    return data
  } catch (error) {
    console.error('Failed to load user data:', error)
    return null // Provide fallback
  }
}

// ❌ DON'T: Wrap everything in try-catch
try {
  const x = 1
  const y = 2
  const z = x + y // This won't fail
  console.log(z)
} catch (error) {
  // Unnecessary
}
```

### Specific Error Handling

```javascript
async function processPayment(amount) {
  try {
    await api.post('/payments', { amount })
    return { success: true }

  } catch (error) {
    // Handle specific errors differently
    if (error.statusCode === 401) {
      return { success: false, error: 'Please log in again' }
    }

    if (error.statusCode === 402) {
      return { success: false, error: 'Insufficient funds' }
    }

    if (error.message === 'Request timeout') {
      return { success: false, error: 'Payment processing timeout. Please check your connection.' }
    }

    // Generic fallback
    return { success: false, error: 'Payment failed. Please try again.' }
  }
}
```

## 4. Error Recovery Strategies

### Graceful Degradation

```javascript
async function loadDashboard() {
  const dashboard = {
    user: null,
    stats: null,
    notifications: null
  }

  // Try to load each section, but don't fail entire dashboard
  try {
    dashboard.user = await api.get('/user')
  } catch (error) {
    console.error('Failed to load user:', error)
    dashboard.user = { name: 'Guest' } // Fallback
  }

  try {
    dashboard.stats = await api.get('/stats')
  } catch (error) {
    console.error('Failed to load stats:', error)
    dashboard.stats = [] // Fallback to empty
  }

  try {
    dashboard.notifications = await api.get('/notifications')
  } catch (error) {
    console.error('Failed to load notifications:', error)
    dashboard.notifications = [] // Fallback to empty
  }

  return dashboard
}
```

### Retry with Exponential Backoff

```javascript
async function fetchWithRetry(url, maxAttempts = 3) {
  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    try {
      return await fetch(url)
    } catch (error) {
      const isLastAttempt = attempt === maxAttempts - 1
      if (isLastAttempt) throw error

      // Exponential backoff: 1s, 2s, 4s
      const delay = Math.pow(2, attempt) * 1000
      console.log(`Retrying in ${delay}ms...`)
      await new Promise(resolve => setTimeout(resolve, delay))
    }
  }
}
```

## Best Practices Summary

### Do's ✅

1. **Use custom error classes** - Makes error handling more precise
2. **Provide user-friendly messages** - Don't show technical details to users
3. **Log errors properly** - Include context for debugging
4. **Implement retry logic** - For transient failures
5. **Have fallback values** - Graceful degradation
6. **Report errors** - Use monitoring services
7. **Test error scenarios** - Don't just test happy path

### Don'ts ❌

1. **Don't swallow errors silently** - Always log or handle
2. **Don't expose sensitive info** - In error messages
3. **Don't retry forever** - Set limits
4. **Don't crash the app** - Recover gracefully
5. **Don't use generic messages** - Be specific about what went wrong

## Next Steps

Learn about security best practices:

👉 **[1.6 Security Best Practices](./06-security.md)** - Build secure applications

---

📚 **Back to**: [Lesson 7 Overview](../07-advanced-techniques.md)
