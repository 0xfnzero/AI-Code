# 1.6 Security Best Practices

> Build secure applications and protect against common vulnerabilities

## Overview

Security is not optional. This module covers essential security practices to protect your applications from common attacks and vulnerabilities.

## 1. Input Validation and Sanitization

### Comprehensive Validator

```javascript
class InputValidator {
  // Sanitize HTML to prevent XSS
  static sanitizeHTML(input) {
    const map = {
      '&': '&amp;',
      '<': '&lt;',
      '>': '&gt;',
      '"': '&quot;',
      "'": '&#x27;',
      '/': '&#x2F;'
    }
    return String(input).replace(/[&<>"'/]/g, char => map[char])
  }

  // Validate email
  static isValidEmail(email) {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    return regex.test(email)
  }

  // Validate strong password
  static isStrongPassword(password) {
    // At least 8 chars, including uppercase, lowercase, number, and special char
    const regex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/
    return regex.test(password)
  }

  // Validate username
  static isValidUsername(username) {
    // 3-20 chars, only letters, numbers, underscore
    const regex = /^[a-zA-Z0-9_]{3,20}$/
    return regex.test(username)
  }

  // Detect SQL injection attempts
  static containsSQLInjection(input) {
    const sqlPatterns = [
      /(\b(SELECT|INSERT|UPDATE|DELETE|DROP|CREATE|ALTER|EXEC|EXECUTE)\b)/i,
      /(--|\#|\/\*|\*\/)/,
      /(\bOR\b|\bAND\b).*=.*=/i
    ]

    return sqlPatterns.some(pattern => pattern.test(input))
  }

  // Comprehensive validation
  static validate(input, rules) {
    const errors = []

    if (rules.required && !input) {
      errors.push('This field is required')
    }

    if (input && rules.minLength && input.length < rules.minLength) {
      errors.push(`Minimum ${rules.minLength} characters required`)
    }

    if (input && rules.maxLength && input.length > rules.maxLength) {
      errors.push(`Maximum ${rules.maxLength} characters allowed`)
    }

    if (rules.email && !this.isValidEmail(input)) {
      errors.push('Invalid email format')
    }

    if (rules.strongPassword && !this.isStrongPassword(input)) {
      errors.push('Password must be at least 8 characters with uppercase, lowercase, number, and special character')
    }

    if (rules.username && !this.isValidUsername(input)) {
      errors.push('Username must be 3-20 characters, letters, numbers, and underscores only')
    }

    if (rules.noSQLInjection && this.containsSQLInjection(input)) {
      errors.push('Input contains invalid characters')
    }

    return {
      valid: errors.length === 0,
      errors
    }
  }
}

// Usage
const usernameValidation = InputValidator.validate(userInput.username, {
  required: true,
  minLength: 3,
  maxLength: 20,
  username: true,
  noSQLInjection: true
})

if (!usernameValidation.valid) {
  console.error('Validation errors:', usernameValidation.errors)
}

// Sanitize for display
const safeContent = InputValidator.sanitizeHTML(userInput.comment)
document.getElementById('comment').textContent = safeContent
```

## 2. XSS Prevention

### The Danger of innerHTML

```javascript
// ❌ DANGEROUS - XSS Vulnerability
function displayUserComment(comment) {
  document.getElementById('comment').innerHTML = comment
  // If comment = "<script>alert('XSS')</script>", it will execute!
}

// ✅ SAFE - Using textContent
function displayUserComment(comment) {
  document.getElementById('comment').textContent = comment
  // Script tags are treated as text, not executed
}

// ✅ SAFE - Sanitized HTML
function displayUserComment(comment) {
  const sanitized = InputValidator.sanitizeHTML(comment)
  document.getElementById('comment').innerHTML = sanitized
}
```

### Safe DOM Manipulation

```javascript
class SafeDOM {
  // Create element safely
  static createElement(tag, properties = {}, text = '') {
    const element = document.createElement(tag)

    // Set properties safely
    Object.entries(properties).forEach(([key, value]) => {
      if (key === 'className') {
        element.className = value
      } else if (key.startsWith('data-')) {
        element.setAttribute(key, value)
      } else {
        element[key] = value
      }
    })

    // Set text content (not HTML)
    if (text) {
      element.textContent = text
    }

    return element
  }

  // Create link safely
  static createLink(text, url) {
    const link = document.createElement('a')
    link.textContent = text
    link.href = url

    // Prevent target="_blank" vulnerabilities
    link.rel = 'noopener noreferrer'

    // Validate URL scheme
    if (!url.match(/^(https?:\/\/|\/)/)) {
      console.warn('Suspicious URL blocked:', url)
      return document.createTextNode(text)
    }

    return link
  }
}

// Usage
const userCard = SafeDOM.createElement('div', { className: 'user-card' })
const userName = SafeDOM.createElement('h3', {}, user.name)
const userLink = SafeDOM.createLink('Profile', user.profileUrl)

userCard.appendChild(userName)
userCard.appendChild(userLink)
```

## 3. Authentication Security

### Secure Password Handling

```javascript
// Backend (Node.js example)
const bcrypt = require('bcrypt')

async function registerUser(username, password) {
  // Validate password strength
  if (!InputValidator.isStrongPassword(password)) {
    throw new ValidationError('Password not strong enough')
  }

  // Hash password (DO NOT store plain text)
  const saltRounds = 10
  const passwordHash = await bcrypt.hash(password, saltRounds)

  // Store username and hash (NOT the password)
  await db.query(
    'INSERT INTO users (username, password_hash) VALUES (?, ?)',
    [username, passwordHash]
  )
}

async function loginUser(username, password) {
  // Get user from database
  const user = await db.query(
    'SELECT id, username, password_hash FROM users WHERE username = ?',
    [username]
  )

  if (!user) {
    // Generic message (don't reveal if user exists)
    throw new AuthError('Invalid credentials')
  }

  // Compare password with hash
  const isValid = await bcrypt.compare(password, user.password_hash)

  if (!isValid) {
    throw new AuthError('Invalid credentials')
  }

  // Create session/token
  return createAuthToken(user)
}
```

### JWT Token Security

```javascript
const jwt = require('jsonwebtoken')

// Create token
function createAuthToken(user) {
  const payload = {
    userId: user.id,
    username: user.username,
    role: user.role
    // DON'T include sensitive data (password, SSN, etc.)
  }

  // Sign with secret
  const token = jwt.sign(
    payload,
    process.env.JWT_SECRET, // Use environment variable
    {
      expiresIn: '15m', // Short-lived access token
      issuer: 'your-app-name',
      audience: 'your-app-name'
    }
  )

  return token
}

// Verify token middleware
function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization']
  const token = authHeader && authHeader.split(' ')[1] // Bearer TOKEN

  if (!token) {
    return res.status(401).json({ error: 'No token provided' })
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET)
    req.user = decoded
    next()
  } catch (error) {
    return res.status(403).json({ error: 'Invalid token' })
  }
}

// Usage in routes
app.get('/api/protected', authenticateToken, (req, res) => {
  res.json({ message: 'This is protected data', user: req.user })
})
```

## 4. CSRF Protection

### CSRF Token Implementation

```javascript
// Backend: Generate CSRF token
const crypto = require('crypto')

function generateCSRFToken() {
  return crypto.randomBytes(32).toString('hex')
}

// Store in session
app.get('/api/csrf-token', (req, res) => {
  const token = generateCSRFToken()
  req.session.csrfToken = token
  res.json({ csrfToken: token })
})

// Verify CSRF token
function verifyCSRFToken(req, res, next) {
  const token = req.headers['x-csrf-token']

  if (!token || token !== req.session.csrfToken) {
    return res.status(403).json({ error: 'Invalid CSRF token' })
  }

  next()
}

// Apply to state-changing routes
app.post('/api/transfer', verifyCSRFToken, (req, res) => {
  // Handle transfer
})

// Frontend: Include token in requests
async function makeSecureRequest(url, data) {
  // Get CSRF token
  const csrfResponse = await fetch('/api/csrf-token')
  const { csrfToken } = await csrfResponse.json()

  // Include in request
  const response = await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-CSRF-Token': csrfToken
    },
    body: JSON.stringify(data)
  })

  return response.json()
}
```

## 5. SQL Injection Prevention

### Use Parameterized Queries

```javascript
// ❌ DANGEROUS - SQL Injection Vulnerability
function getUser(username) {
  const sql = `SELECT * FROM users WHERE username = '${username}'`
  return db.query(sql)
  // Attack: username = "admin' OR '1'='1"
}

// ✅ SAFE - Parameterized Query
function getUser(username) {
  const sql = 'SELECT * FROM users WHERE username = ?'
  return db.query(sql, [username])
  // Parameters are escaped automatically
}

// ✅ SAFE - Named Parameters (better readability)
function getUserByEmailAndRole(email, role) {
  const sql = 'SELECT * FROM users WHERE email = :email AND role = :role'
  return db.query(sql, { email, role })
}
```

## 6. Security Headers

### Essential HTTP Headers

```javascript
// Backend: Set security headers
app.use((req, res, next) => {
  // Prevent clickjacking
  res.setHeader('X-Frame-Options', 'DENY')

  // Prevent MIME sniffing
  res.setHeader('X-Content-Type-Options', 'nosniff')

  // XSS Protection
  res.setHeader('X-XSS-Protection', '1; mode=block')

  // Content Security Policy
  res.setHeader('Content-Security-Policy',
    "default-src 'self'; " +
    "script-src 'self' 'unsafe-inline'; " +
    "style-src 'self' 'unsafe-inline'; " +
    "img-src 'self' data: https:;"
  )

  // Force HTTPS
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains')

  next()
})
```

## 7. Rate Limiting

### Prevent Brute Force Attacks

```javascript
const rateLimit = require('express-rate-limit')

// General API rate limit
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests, please try again later'
})

app.use('/api/', apiLimiter)

// Strict limit for authentication
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // Only 5 login attempts
  skipSuccessfulRequests: true,
  message: 'Too many login attempts, please try again later'
})

app.post('/api/login', authLimiter, loginHandler)
```

## Security Checklist

### Before Going to Production

```markdown
✅ Input Validation
- [ ] All user inputs are validated
- [ ] XSS prevention in place
- [ ] SQL injection prevention (parameterized queries)

✅ Authentication & Authorization
- [ ] Passwords are hashed (never stored plain)
- [ ] Strong password requirements enforced
- [ ] JWT tokens properly implemented
- [ ] Session management secure
- [ ] Authorization checks on all protected routes

✅ Data Protection
- [ ] Sensitive data encrypted
- [ ] HTTPS enforced
- [ ] Secure cookies (httpOnly, secure, sameSite)
- [ ] Environment variables for secrets

✅ Security Headers
- [ ] CSP configured
- [ ] HSTS enabled
- [ ] X-Frame-Options set
- [ ] X-Content-Type-Options set

✅ API Security
- [ ] Rate limiting implemented
- [ ] CSRF protection on state-changing operations
- [ ] API authentication required
- [ ] Input validation on all endpoints

✅ Error Handling
- [ ] No sensitive info in error messages
- [ ] Proper logging (without sensitive data)
- [ ] Generic error messages to users

✅ Dependencies
- [ ] All packages up to date
- [ ] No known vulnerabilities (npm audit)
- [ ] Minimal dependencies
```

## Best Practices Summary

### Do's ✅

1. **Validate all inputs** - Never trust user data
2. **Use parameterized queries** - Prevent SQL injection
3. **Hash passwords** - Use bcrypt or similar
4. **Implement rate limiting** - Prevent brute force
5. **Use HTTPS** - Always, no exceptions
6. **Set security headers** - Defense in depth
7. **Keep dependencies updated** - Patch vulnerabilities
8. **Follow principle of least privilege** - Minimal permissions

### Don'ts ❌

1. **Don't store passwords in plain text** - Ever
2. **Don't expose sensitive data** - In errors or responses
3. **Don't trust client-side validation** - Always validate on server
4. **Don't use innerHTML** - Unless absolutely necessary and sanitized
5. **Don't hardcode secrets** - Use environment variables
6. **Don't ignore security updates** - Update dependencies regularly

## Next Steps

Congratulations! You've completed Lesson 7. Apply these techniques to:

- Write better prompts
- Review and improve code quality
- Debug more efficiently
- Build secure, robust applications

👉 **Next Lesson**: [Lesson 8: Advanced Applications](../08-advanced-applications.md)

---

📚 **Back to**: [Lesson 7 Overview](../07-advanced-techniques.md)
