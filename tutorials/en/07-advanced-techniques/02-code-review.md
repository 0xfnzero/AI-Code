# 1.2 Code Review

> Learn how to conduct effective code reviews with AI assistance

## Overview

Code review is essential for maintaining code quality, catching bugs, and improving team knowledge. This module shows you how to leverage AI for thorough, professional code reviews.

## 1. Requesting Code Reviews

### Basic Review Request

**Template**:
```
Please review this code and point out issues and improvement suggestions:

[paste code]

Focus on:
- Code quality (readability, maintainability)
- Performance issues
- Security vulnerabilities
- Best practices
- Potential bugs
```

### Example: API Function Review

```javascript
You: Review this code and point out issues:

function getUserData(id) {
  fetch('https://api.example.com/users/' + id)
    .then(res => res.json())
    .then(data => {
      console.log(data)
      document.getElementById('user-name').innerHTML = data.name
    })
}

Claude: I found the following issues:

🔴 Critical Issues:
1. No error handling - Network failures will break the app
2. Using innerHTML - XSS vulnerability risk
3. Not checking response status - Treats all responses as success

🟡 Important Issues:
4. Hardcoded DOM ID - Low reusability
5. No return value - Cannot be tested easily
6. Using string concatenation for URL - Modern template literals preferred

Improved version:

async function getUserData(id) {
  try {
    const response = await fetch(`https://api.example.com/users/${id}`)

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`)
    }

    const data = await response.json()

    // Safely update DOM
    const userNameEl = document.getElementById('user-name')
    if (userNameEl) {
      userNameEl.textContent = data.name  // Use textContent, not innerHTML
    }

    return data
  } catch (error) {
    console.error('Failed to fetch user data:', error)
    throw error
  }
}
```

## 2. Performance Optimization Analysis

### Request Performance Review

**Prompt Template**:
```
This code has performance issues, help me optimize:

[paste code]

Current metrics:
- Data size: [amount]
- Execution time: [duration]
- Target: [goal]

Please provide:
1. Performance bottleneck analysis
2. Optimization solutions (multiple options)
3. Expected improvement metrics
4. Implementation code
```

### Example: Array Filtering Optimization

```javascript
You: This code is slow with large datasets, please optimize:

function filterProducts(products, keyword) {
  let result = []
  for (let i = 0; i < products.length; i++) {
    for (let j = 0; j < products[i].tags.length; j++) {
      if (products[i].tags[j].includes(keyword)) {
        result.push(products[i])
        break
      }
    }
  }
  return result
}

Current: ~450ms for 10,000 products
Target: < 200ms

Claude: Problem Analysis:
1. Nested loops - O(n*m) complexity
2. Repeated array lookups - products[i] accessed multiple times
3. Case-sensitive search - Might miss matches

Optimization Solution 1 (Clearest):
function filterProducts(products, keyword) {
  return products.filter(product =>
    product.tags.some(tag => tag.includes(keyword))
  )
}

Optimization Solution 2 (Fastest):
function filterProducts(products, keyword) {
  const result = []
  const lowerKeyword = keyword.toLowerCase()

  for (const product of products) {
    if (product.tags.some(tag =>
      tag.toLowerCase().includes(lowerKeyword)
    )) {
      result.push(product)
    }
  }

  return result
}

Performance Results:
- Solution 1: ~180ms (60% faster, more readable)
- Solution 2: ~150ms (70% faster, case-insensitive)

Recommendation: Use Solution 2 for best performance
```

## 3. Code Refactoring Suggestions

### Request Refactoring Help

**Prompt**:
```
This component is too long ([X] lines), help me refactor:

[paste code]

Goals:
- Improve maintainability
- Increase reusability
- Follow single responsibility principle
- Make testing easier
```

### Example: Component Refactoring

```javascript
You: This component is 300 lines, help me refactor

// UserDashboard.js - 300 lines
function UserDashboard() {
  // Lots of state, logic, and JSX mixed together
  // [abbreviated for example]
}

Claude: After analyzing your component structure, I suggest splitting into:

Current: UserDashboard.js (300 lines)

Refactored structure:
components/
├── UserDashboard.js        (50 lines - main container)
├── UserProfile.js          (60 lines - personal info display)
├── UserStats.js            (40 lines - statistics charts)
├── ActivityFeed.js         (80 lines - activity timeline)
└── SettingsPanel.js        (70 lines - user settings)

Benefits:
✅ Each component has single responsibility
✅ Easy to test independently
✅ High reusability across app
✅ Easier to maintain and debug
✅ Better code organization

Shall I implement this refactoring?
```

## 4. Security Review

### Request Security Audit

**Prompt Template**:
```
Please conduct a security review of this code:

[paste code]

Check for:
✅ SQL injection
✅ XSS vulnerabilities
✅ CSRF protection
✅ Authentication/Authorization
✅ Data validation
✅ Sensitive data exposure
```

### Example: Authentication Security Review

```javascript
You: Review the security of this login function:

app.post('/api/login', (req, res) => {
  const { username, password } = req.body
  const sql = `SELECT * FROM users WHERE username='${username}' AND password='${password}'`

  db.query(sql, (err, results) => {
    if (results.length > 0) {
      res.json({ success: true, user: results[0] })
    } else {
      res.json({ success: false })
    }
  })
})

Claude: 🔴 CRITICAL SECURITY ISSUES FOUND:

1. SQL Injection Vulnerability
   - Direct string concatenation in SQL query
   - Attacker can use: username = "admin' OR '1'='1" to bypass authentication
   - Severity: CRITICAL

2. Plain Text Password Storage
   - Comparing passwords directly without hashing
   - Database compromise exposes all passwords
   - Severity: CRITICAL

3. Information Leakage
   - Returning entire user object including sensitive data
   - Password hash might be included in response
   - Severity: HIGH

4. No Rate Limiting
   - Vulnerable to brute force attacks
   - Severity: HIGH

5. Weak Error Handling
   - No distinction between "wrong password" and "user not found"
   - But generic response is actually good for security
   - Severity: LOW

Secure Implementation:

const bcrypt = require('bcrypt')
const jwt = require('jsonwebtoken')
const rateLimit = require('express-rate-limit')

// Rate limiting
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5 // 5 attempts
})

app.post('/api/login', loginLimiter, async (req, res) => {
  try {
    const { username, password } = req.body

    // Input validation
    if (!username || !password) {
      return res.status(400).json({
        success: false,
        message: 'Username and password required'
      })
    }

    // Parameterized query (prevents SQL injection)
    const results = await db.query(
      'SELECT id, username, password_hash, role FROM users WHERE username = ?',
      [username]
    )

    if (results.length === 0) {
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      })
    }

    const user = results[0]

    // Compare hashed password
    const isValid = await bcrypt.compare(password, user.password_hash)

    if (!isValid) {
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      })
    }

    // Create JWT token (don't include sensitive data)
    const token = jwt.sign(
      { userId: user.id, username: user.username, role: user.role },
      process.env.JWT_SECRET,
      { expiresIn: '1h' }
    )

    // Return only necessary data
    res.json({
      success: true,
      token,
      user: {
        id: user.id,
        username: user.username,
        role: user.role
      }
    })

  } catch (error) {
    console.error('Login error:', error)
    res.status(500).json({
      success: false,
      message: 'Server error'
    })
  }
})
```

## Best Practices for Code Review

### Effective Review Requests

1. **Provide Context**
   ```
   This is part of a payment processing system.
   Security is critical. This function handles...
   ```

2. **State Your Concerns**
   ```
   I'm worried about:
   - Performance with large datasets
   - Edge cases with null values
   - Memory leaks
   ```

3. **Ask Specific Questions**
   ```
   Questions:
   - Is this the right pattern for this use case?
   - Are there better alternatives?
   - What are the potential issues?
   ```

### Review Checklist

When requesting reviews, ask AI to check:

```markdown
✅ Correctness
- Logic errors
- Edge cases
- Type safety

✅ Performance
- Time complexity
- Space complexity
- Unnecessary computations

✅ Security
- Input validation
- SQL injection
- XSS vulnerabilities
- Authentication/Authorization

✅ Maintainability
- Code readability
- Documentation
- Naming conventions
- Function length

✅ Best Practices
- Design patterns
- DRY principle
- SOLID principles
- Framework conventions
```

## Practice Exercise

### Exercise: Review This Code

Request a review of this code and identify at least 5 issues:

```javascript
function processOrder(order) {
  var total = 0
  for (var i = 0; i < order.items.length; i++) {
    total = total + order.items[i].price * order.items[i].quantity
  }

  if (order.discount) {
    total = total - order.discount
  }

  document.getElementById('total').innerHTML = '$' + total

  fetch('/api/orders', {
    method: 'POST',
    body: JSON.stringify(order)
  })
}
```

## Next Steps

Learn systematic debugging techniques:

👉 **[1.3 Debugging](./03-debugging.md)** - Master debugging with AI assistance

---

📚 **Back to**: [Lesson 7 Overview](../07-advanced-techniques.md)
