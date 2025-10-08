# 1.3 Debugging Techniques

> Master systematic debugging approaches with AI assistance

## Overview

Effective debugging is a critical skill. This module teaches you how to debug systematically with AI support, saving hours of troubleshooting time.

## 1. Systematic Debugging Process

### The Framework

```
Step 1: Reproduce → Can you consistently trigger the bug?
Step 2: Isolate → Where exactly does it break?
Step 3: Identify → What's the root cause?
Step 4: Fix → Implement the solution
Step 5: Verify → Confirm it's resolved
Step 6: Prevent → Add tests to prevent regression
```

### Example: Debugging Login Issues

```
You: Login functionality not working, don't know where the issue is

Claude: Let's troubleshoot systematically:

Step 1: Reproduce the Issue
Questions:
- What exact actions did you perform?
- What error messages appear (console, network, visual)?
- Does it happen every time or intermittently?
- Which browser/device?

Step 2: Check Frontend
1. Open browser DevTools Console
2. Check for JavaScript errors
3. Verify form data is collected correctly:
   console.log('Form data:', { username, password })

Step 3: Check Network
1. Open Network tab in DevTools
2. Find the login request
3. Check:
   - Request method (POST?)
   - Request payload (correct format?)
   - Response status (200, 401, 500?)
   - Response body (error message?)

Step 4: Check Backend
1. View server logs
2. Verify route is hit
3. Check database query
4. Validate authentication logic

Let's start with Step 1. Please provide:
- Exact error message
- Browser console output
- Network request details
```

## 2. Adding Debug Code

### Strategic Console Logging

**Before** (No debugging info):
```javascript
async function loginUser(credentials) {
  const response = await api.login(credentials)
  setUser(response.user)
}
```

**After** (Comprehensive logging):
```javascript
async function loginUser(credentials) {
  console.group('🔐 User Login Process')
  console.log('1. Credentials:', {
    ...credentials,
    password: '***' // Hide sensitive data
  })

  try {
    console.time('API Call Duration')
    const response = await api.login(credentials)
    console.timeEnd('API Call Duration')

    console.log('2. API Response:', response)

    if (response.user) {
      console.log('3. Setting user state:', response.user)
      setUser(response.user)
      console.log('✅ Login successful')
    } else {
      console.warn('⚠️ No user in response')
    }

  } catch (error) {
    console.error('❌ Login failed:', {
      message: error.message,
      stack: error.stack,
      response: error.response
    })
    throw error
  } finally {
    console.groupEnd()
  }
}
```

### Debug Utilities

```javascript
// Create a debug helper
const debug = {
  log: (label, data) => {
    if (process.env.NODE_ENV === 'development') {
      console.log(`[${new Date().toISOString()}] ${label}:`, data)
    }
  },

  error: (label, error) => {
    console.error(`[${new Date().toISOString()}] ERROR ${label}:`, {
      message: error.message,
      stack: error.stack,
      ...error
    })
  },

  table: (label, data) => {
    console.log(`\n${label}:`)
    console.table(data)
  },

  group: (label, fn) => {
    console.group(label)
    try {
      fn()
    } finally {
      console.groupEnd()
    }
  }
}

// Usage
debug.log('User data', userData)
debug.error('API call', error)
debug.table('Products', products)
```

## 3. Breakpoint Debugging

### Browser DevTools Debugging

**Method 1: DevTools UI**
```
1. Open DevTools (F12)
2. Go to Sources tab
3. Find your JavaScript file
4. Click line number to set breakpoint
5. Refresh page or trigger code
6. Execution pauses at breakpoint
```

**Method 2: Code Debugger Statement**
```javascript
function calculateTotal(items) {
  debugger; // Execution will pause here

  let total = 0
  for (const item of items) {
    debugger; // Pause on each iteration
    total += item.price * item.quantity
  }

  debugger; // Pause before return
  return total
}
```

**Method 3: Conditional Breakpoints**
```
Right-click line number → "Add conditional breakpoint"
Condition: userId === 123

// Or in code:
if (userId === 123) {
  debugger; // Only pause for specific user
}
```

### Debugging Controls

While paused at a breakpoint:

```
F8 (Resume)      - Continue execution
F10 (Step Over)  - Execute current line, don't enter functions
F11 (Step Into)  - Enter function calls
Shift+F11 (Out)  - Exit current function

Hover variables  - See values
Console tab      - Execute code in current context
Watch            - Monitor specific expressions
```

### Example: Debug Shopping Cart

```javascript
function addToCart(product, quantity) {
  debugger; // Pause 1: Check parameters

  // Verify product object
  console.log('Product:', product)
  console.log('Quantity:', quantity)

  const existingItem = cart.find(item => item.id === product.id)

  debugger; // Pause 2: Check if item exists

  if (existingItem) {
    existingItem.quantity += quantity
    debugger; // Pause 3: Verify quantity update
  } else {
    cart.push({ ...product, quantity })
    debugger; // Pause 4: Verify new item added
  }

  updateCartTotal()

  debugger; // Pause 5: Check final cart state
}
```

## 4. Common Debugging Scenarios

### Scenario 1: Async/Await Issues

```javascript
// Problem: Why doesn't this work?
async function loadData() {
  const data = await fetchData()
  processData(data) // data is undefined?
}

// Debug approach:
async function loadData() {
  console.log('1. Starting data load')

  try {
    console.log('2. Fetching data...')
    const data = await fetchData()
    console.log('3. Data received:', data)

    if (!data) {
      console.error('4. Data is null/undefined!')
      return
    }

    console.log('5. Processing data...')
    const result = processData(data)
    console.log('6. Processing complete:', result)

    return result
  } catch (error) {
    console.error('7. Error occurred:', error)
    throw error
  }
}
```

### Scenario 2: State Updates Not Working

```javascript
// Problem: State doesn't update in React
const [count, setCount] = useState(0)

function increment() {
  setCount(count + 1)
  console.log('Count:', count) // Still shows old value!
}

// Debug: Understand async state updates
function increment() {
  console.log('Before update:', count)

  setCount(prevCount => {
    const newCount = prevCount + 1
    console.log('Updating from', prevCount, 'to', newCount)
    return newCount
  })

  // State update is async, so count still has old value here
  console.log('Right after setState:', count)

  // To see new value, use useEffect
  useEffect(() => {
    console.log('After state update:', count)
  }, [count])
}
```

### Scenario 3: Event Handler Issues

```javascript
// Problem: onClick not firing
<button onClick={handleClick()}>Click me</button> // ❌ Calls immediately

// Debug and fix:
<button onClick={handleClick}>Click me</button> // ✅ Pass function reference

// Or with parameters:
<button onClick={() => handleClick(id)}>Click me</button> // ✅

// Debug event handlers:
function handleClick(event) {
  console.log('Click event:', {
    target: event.target,
    type: event.type,
    timestamp: event.timeStamp
  })

  // Your logic here
}
```

## 5. AI-Assisted Debugging

### Prompt Template

```
I'm getting this error: [error message]

Context:
- What I'm trying to do: [describe]
- What's happening: [describe]
- What I expected: [describe]

Code:
[paste relevant code]

Error details:
[paste full error stack trace]

What I've tried:
- [attempt 1]
- [attempt 2]

Please help me:
1. Identify the root cause
2. Explain why it's happening
3. Provide a fix
4. Suggest how to prevent similar issues
```

### Example: Debugging API Error

```
You: I'm getting this error when fetching user data:

Error: "Cannot read property 'name' of undefined"

Context:
- Trying to display user name after login
- User data should come from API
- It works sometimes, fails other times

Code:
function displayUser() {
  fetch('/api/user')
    .then(res => res.json())
    .then(data => {
      document.getElementById('username').textContent = data.user.name
    })
}

What I've tried:
- Added console.log(data) - sometimes data is empty object
- Checked API - it returns 200 status

Claude: Root cause: Race condition and missing error handling

Analysis:
1. API returns 200 even when user not authenticated
2. Empty object {} passed when no user logged in
3. Accessing data.user.name fails when data.user is undefined

Fix with proper error handling:

async function displayUser() {
  try {
    const response = await fetch('/api/user')

    // Check response status
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`)
    }

    const data = await response.json()

    // Validate data structure
    if (!data || !data.user) {
      console.warn('No user data received')
      showLoginPrompt()
      return
    }

    // Safe property access
    const username = data.user.name || 'Anonymous'
    document.getElementById('username').textContent = username

  } catch (error) {
    console.error('Failed to load user:', error)
    showErrorMessage('Could not load user data')
  }
}

Prevention strategies:
1. Always validate API responses
2. Use optional chaining: data?.user?.name
3. Add loading and error states
4. Implement proper authentication checks
```

## Best Practices

### Do's ✅

1. **Reproduce consistently** - Debug when you can reliably trigger the issue
2. **Isolate the problem** - Narrow down to smallest failing code
3. **Use console effectively** - Strategic logging over random console.logs
4. **Read error messages** - They usually tell you exactly what's wrong
5. **Check the basics first** - Typos, missing imports, wrong variables
6. **Use browser DevTools** - Network tab, console, debugger are powerful
7. **Test assumptions** - Verify what you think is true actually is
8. **Take breaks** - Fresh eyes often spot issues immediately

### Don'ts ❌

1. **Don't randomly change code** - Understand before fixing
2. **Don't ignore warnings** - They often indicate real problems
3. **Don't debug in production** - Use development environment
4. **Don't skip console output** - Read what the error says
5. **Don't forget to remove debuggers** - Clean up before committing

## Practice Exercise

Debug this code:

```javascript
function processOrders(orders) {
  const results = []

  for (let i = 0; i <= orders.length; i++) {
    const order = orders[i]
    const total = order.items.reduce((sum, item) =>
      sum + item.price * item.quantity
    )

    if (order.discount) {
      total = total - order.discount
    }

    results.push({
      id: order.id,
      total: total
    })
  }

  return results
}
```

Hint: There are at least 3 bugs!

## Next Steps

Learn how to apply design patterns effectively:

👉 **[1.4 Design Patterns](./04-design-patterns.md)** - Master common design patterns

---

📚 **Back to**: [Lesson 7 Overview](../07-advanced-techniques.md)
