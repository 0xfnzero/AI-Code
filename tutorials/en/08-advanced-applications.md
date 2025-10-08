# Lesson 8: Claude Code Advanced Applications

> 🎯 **Learning Objectives**: Master advanced AI collaboration techniques and build complex projects

## 1. Efficient Collaboration Models

### AI Pair Programming

**TDD Mode (Test-Driven Development)**:
```
You: I want to implement a shopping cart feature, let's use TDD approach

Claude: Okay, let's write tests first:

describe('ShoppingCart', () => {
  test('should add item to cart', () => {
    const cart = new ShoppingCart()
    cart.addItem({ id: 1, name: 'Book', price: 29.99 })
    expect(cart.items.length).toBe(1)
  })

  test('should calculate total price', () => {
    const cart = new ShoppingCart()
    cart.addItem({ id: 1, price: 10 })
    cart.addItem({ id: 2, price: 20 })
    expect(cart.getTotal()).toBe(30)
  })
})

Tests are written, shall I implement the functionality now?

You: Yes, implement the simplest code that passes the tests

Claude: [Implement ShoppingCart class]

You: Tests passed! Now refactor the code to make it more elegant

Claude: [Optimize implementation]
```

### Progressive Refactoring

```
You: I have a legacy project, want to refactor without affecting existing functionality, how?

Claude: Use progressive refactoring strategy:

Phase 1: Add tests
1. Write tests for existing functionality
2. Ensure test coverage > 80%
3. Establish test baseline

Phase 2: Identify issues
1. Analyze code smells
2. Identify duplicate code
3. Find overly complex parts

Phase 3: Small steps refactoring
1. Refactor one module at a time
2. Keep tests passing
3. Commit frequently

Phase 4: Progressive replacement
1. New features use new architecture
2. Gradually migrate old features
3. Strangler Pattern

Which module should we start with?
```

### Code Review Collaboration

```
You: Help me review this Pull Request, provide professional code review feedback

Claude: Code Review Report:

✅ Strengths:
1. Clear code structure, reasonable module division
2. Complete unit tests
3. Comprehensive error handling

⚠️ Needs Improvement:
1. Function too long (line 45-120), suggest splitting
2. Missing type annotations (if using TypeScript)
3. Some variable names not semantic enough (e.g., data, temp)

🔴 Issues:
1. Potential memory leak: addEventListener not cleaned up
2. SQL query not parameterized, injection risk
3. Synchronous blocking operation (line 78) should be async

📝 Suggestions:
1. Add JSDoc comments
2. Consider refactoring with design patterns (suggest: Strategy Pattern)
3. Performance: can add caching mechanism

Detailed modification suggestions:
[Specific code changes]
```

## 2. Complex Project Architecture

### Micro-Frontend Architecture

```
You: Design a micro-frontend architecture for multiple teams to develop different modules independently

Claude: Micro-frontend architecture solution:

Project structure:
monorepo/
├── packages/
│   ├── shell/          # Main container app
│   ├── module-auth/    # Authentication module
│   ├── module-dashboard/  # Dashboard
│   ├── module-settings/   # Settings
│   └── shared/         # Shared resources
├── lerna.json
└── package.json

Shell Application (container):
// shell/src/app.js
class MicroFrontendContainer {
  constructor() {
    this.modules = new Map()
  }

  // Register module
  register(name, moduleFactory) {
    this.modules.set(name, moduleFactory)
  }

  // Load module
  async load(name, container) {
    if (!this.modules.has(name)) {
      // Dynamic loading
      await this.loadModule(name)
    }

    const moduleFactory = this.modules.get(name)
    const module = await moduleFactory()

    module.mount(container)
    return module
  }

  // Dynamically load module
  async loadModule(name) {
    const script = document.createElement('script')
    script.src = `/modules/${name}/index.js`

    return new Promise((resolve, reject) => {
      script.onload = resolve
      script.onerror = reject
      document.head.appendChild(script)
    })
  }
}

Module Example (Dashboard):
// module-dashboard/src/index.js
export default {
  async mount(container) {
    // Render module
    container.innerHTML = '<div id="dashboard">...</div>'

    // Initialize
    await this.init()
  },

  async unmount(container) {
    // Cleanup resources
    container.innerHTML = ''
  },

  async init() {
    // Module initialization logic
  }
}

Module Communication (Event Bus):
// shared/eventBus.js
class EventBus {
  constructor() {
    this.events = {}
  }

  publish(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data))
    }
  }

  subscribe(event, callback) {
    if (!this.events[event]) {
      this.events[event] = []
    }
    this.events[event].push(callback)

    // Return unsubscribe function
    return () => {
      this.events[event] = this.events[event]
        .filter(cb => cb !== callback)
    }
  }
}

export const eventBus = new EventBus()

Usage:
// Module A publishes event
eventBus.publish('user:login', { userId: 123 })

// Module B subscribes to event
eventBus.subscribe('user:login', (data) => {
  console.log('User logged in:', data.userId)
  updateUI(data)
})

Deployment configuration:
- Each module deployed independently
- Use CDN to host modules
- Version management and rollback mechanism
```

### Plugin System Design

```
You: Design a plugin system to allow users to extend application functionality

Claude: Plugin system architecture:

// Core: Plugin Manager
class PluginManager {
  constructor() {
    this.plugins = new Map()
    this.hooks = new Map()
  }

  // Register plugin
  register(plugin) {
    // Validate plugin
    if (!this.validatePlugin(plugin)) {
      throw new Error('Invalid plugin')
    }

    // Save plugin
    this.plugins.set(plugin.name, plugin)

    // Register hooks
    if (plugin.hooks) {
      Object.entries(plugin.hooks).forEach(([hook, handler]) => {
        this.registerHook(hook, handler)
      })
    }

    // Call plugin initialization
    if (plugin.init) {
      plugin.init(this.getAPI())
    }

    console.log(`✅ Plugin registered: ${plugin.name}`)
  }

  // Register hook
  registerHook(hookName, handler) {
    if (!this.hooks.has(hookName)) {
      this.hooks.set(hookName, [])
    }
    this.hooks.get(hookName).push(handler)
  }

  // Trigger hook
  async trigger(hookName, data) {
    if (!this.hooks.has(hookName)) {
      return data
    }

    let result = data

    for (const handler of this.hooks.get(hookName)) {
      result = await handler(result, this.getAPI())
    }

    return result
  }

  // API provided to plugins
  getAPI() {
    return {
      // Data access
      getData: (key) => this.appData.get(key),
      setData: (key, value) => this.appData.set(key, value),

      // UI extension
      addMenuItem: (item) => this.menu.addItem(item),
      addPage: (route, component) => this.router.addRoute(route, component),

      // Notifications
      notify: (message, type) => this.notifications.show(message, type),

      // Utility functions
      http: this.httpClient,
      storage: this.storage
    }
  }

  // Validate plugin
  validatePlugin(plugin) {
    return plugin.name && plugin.version
  }

  // Unregister plugin
  unregister(pluginName) {
    const plugin = this.plugins.get(pluginName)

    if (plugin && plugin.destroy) {
      plugin.destroy()
    }

    this.plugins.delete(pluginName)

    // Remove hooks
    // ... cleanup logic
  }
}

// Plugin Example 1: Theme Switcher
const themePlugin = {
  name: 'theme-switcher',
  version: '1.0.0',

  init(api) {
    // Add menu item
    api.addMenuItem({
      label: 'Toggle Theme',
      onClick: () => this.toggleTheme(api)
    })

    // Load saved theme
    const savedTheme = api.getData('theme') || 'light'
    this.applyTheme(savedTheme)
  },

  toggleTheme(api) {
    const current = api.getData('theme') || 'light'
    const newTheme = current === 'light' ? 'dark' : 'light'

    this.applyTheme(newTheme)
    api.setData('theme', newTheme)
    api.notify(`Switched to ${newTheme} theme`, 'success')
  },

  applyTheme(theme) {
    document.body.classList.remove('theme-light', 'theme-dark')
    document.body.classList.add(`theme-${theme}`)
  }
}

// Plugin Example 2: Data Export
const exportPlugin = {
  name: 'data-exporter',
  version: '1.0.0',

  hooks: {
    // Process before data save
    'before:save': async (data, api) => {
      // Add metadata
      return {
        ...data,
        exportedAt: new Date().toISOString(),
        exportedBy: api.getData('currentUser')
      }
    }
  },

  init(api) {
    // Add export button
    api.addMenuItem({
      label: 'Export Data',
      onClick: () => this.exportData(api)
    })
  },

  async exportData(api) {
    const data = api.getData('appData')

    // Trigger hook to process data
    const processedData = await api.trigger('before:save', data)

    // Export as JSON
    const blob = new Blob([JSON.stringify(processedData, null, 2)], {
      type: 'application/json'
    })

    const url = URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.href = url
    link.download = `export-${Date.now()}.json`
    link.click()

    api.notify('Data exported successfully', 'success')
  }
}

// Use plugin system
const pluginManager = new PluginManager()

pluginManager.register(themePlugin)
pluginManager.register(exportPlugin)

// Third-party developers can also create plugins
const customPlugin = {
  name: 'my-custom-plugin',
  version: '1.0.0',

  init(api) {
    // Custom functionality
  }
}

pluginManager.register(customPlugin)
```

## 3. Automated Testing

### E2E Test Automation

```
You: Create complete E2E tests for my web application

Claude: Use Playwright to create E2E tests:

// tests/e2e/user-flow.spec.js
const { test, expect } = require('@playwright/test')

test.describe('Complete User Flow', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('http://localhost:3000')
  })

  test('User registration flow', async ({ page }) => {
    // 1. Click register button
    await page.click('text=Register')

    // 2. Fill in form
    await page.fill('[name="username"]', 'testuser')
    await page.fill('[name="email"]', 'test@example.com')
    await page.fill('[name="password"]', 'Test123!@#')

    // 3. Submit form
    await page.click('button[type="submit"]')

    // 4. Verify success message
    await expect(page.locator('.success-message')).toBeVisible()
    await expect(page.locator('.success-message'))
      .toContainText('Registration successful')

    // 5. Verify redirect to dashboard
    await expect(page).toHaveURL(/.*dashboard/)
  })

  test('User login flow', async ({ page }) => {
    // 1. Click login
    await page.click('text=Login')

    // 2. Fill credentials
    await page.fill('[name="username"]', 'testuser')
    await page.fill('[name="password"]', 'Test123!@#')

    // 3. Submit
    await page.click('button:has-text("Login")')

    // 4. Verify login success
    await expect(page.locator('.user-avatar')).toBeVisible()

    // 5. Verify username displayed
    await expect(page.locator('.username'))
      .toContainText('testuser')
  })

  test('Create task flow', async ({ page, context }) => {
    // Login first
    await loginAsUser(page, 'testuser', 'Test123!@#')

    // 1. Click add task
    await page.click('text=Add Task')

    // 2. Fill task info
    await page.fill('[name="title"]', 'Complete testing')
    await page.fill('[name="description"]', 'Write E2E tests')
    await page.selectOption('[name="priority"]', 'high')

    // 3. Upload attachment (if any)
    await page.setInputFiles('[name="attachment"]', './test-file.pdf')

    // 4. Submit
    await page.click('button:has-text("Create")')

    // 5. Verify task appears in list
    await expect(page.locator('.task-item:has-text("Complete testing")')).toBeVisible()

    // 6. Verify task details
    await page.click('.task-item:has-text("Complete testing")')
    await expect(page.locator('.task-detail .title'))
      .toContainText('Complete testing')
    await expect(page.locator('.task-detail .priority'))
      .toContainText('high')
  })

  test('Search functionality', async ({ page }) => {
    await loginAsUser(page, 'testuser', 'Test123!@#')

    // 1. Input search keyword
    await page.fill('[name="search"]', 'testing')

    // 2. Wait for search results
    await page.waitForSelector('.search-results')

    // 3. Verify results
    const results = page.locator('.search-result-item')
    await expect(results).toHaveCount(3)  // Assume 3 results

    // 4. Click search result
    await results.first().click()

    // 5. Verify navigation to correct page
    await expect(page).toHaveURL(/.*task/)
  })

  test('Responsive design', async ({ page }) => {
    // Test mobile
    await page.setViewportSize({ width: 375, height: 667 })

    // Verify mobile menu
    await expect(page.locator('.mobile-menu-button')).toBeVisible()
    await expect(page.locator('.desktop-sidebar')).not.toBeVisible()

    // Open mobile menu
    await page.click('.mobile-menu-button')
    await expect(page.locator('.mobile-menu')).toBeVisible()

    // Test tablet
    await page.setViewportSize({ width: 768, height: 1024 })
    // ... tablet layout tests

    // Test desktop
    await page.setViewportSize({ width: 1920, height: 1080 })
    // ... desktop layout tests
  })
})

// Helper functions
async function loginAsUser(page, username, password) {
  await page.goto('http://localhost:3000/login')
  await page.fill('[name="username"]', username)
  await page.fill('[name="password"]', password)
  await page.click('button[type="submit"]')
  await page.waitForURL('**/dashboard')
}

// Config file: playwright.config.js
module.exports = {
  testDir: './tests/e2e',
  timeout: 30000,
  use: {
    baseURL: 'http://localhost:3000',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
  projects: [
    { name: 'chromium', use: { browserName: 'chromium' } },
    { name: 'firefox', use: { browserName: 'firefox' } },
    { name: 'webkit', use: { browserName: 'webkit' } }
  ]
}

Run tests:
npm run test:e2e
```

### Visual Regression Testing

```
You: Add visual regression testing to detect UI changes

Claude: Use Playwright's screenshot comparison:

// tests/visual/visual-regression.spec.js
const { test, expect } = require('@playwright/test')

test.describe('Visual Regression Tests', () => {
  test('Homepage visual check', async ({ page }) => {
    await page.goto('/')

    // Wait for page to fully load
    await page.waitForLoadState('networkidle')

    // Screenshot comparison
    await expect(page).toHaveScreenshot('homepage.png', {
      fullPage: true,
      maxDiffPixels: 100  // Allowed pixel difference
    })
  })

  test('Button style check', async ({ page }) => {
    await page.goto('/components')

    // Capture specific element
    const button = page.locator('.primary-button')
    await expect(button).toHaveScreenshot('primary-button.png')

    // Hover state
    await button.hover()
    await expect(button).toHaveScreenshot('primary-button-hover.png')

    // Active state
    await button.focus()
    await expect(button).toHaveScreenshot('primary-button-active.png')
  })

  test('Form layout check', async ({ page }) => {
    await page.goto('/form')

    // Screenshots at different viewport sizes
    const viewports = [
      { width: 375, height: 667, name: 'mobile' },
      { width: 768, height: 1024, name: 'tablet' },
      { width: 1920, height: 1080, name: 'desktop' }
    ]

    for (const viewport of viewports) {
      await page.setViewportSize(viewport)
      await expect(page).toHaveScreenshot(`form-${viewport.name}.png`)
    }
  })

  test('Dark mode check', async ({ page }) => {
    await page.goto('/')

    // Light mode
    await expect(page).toHaveScreenshot('homepage-light.png')

    // Switch to dark mode
    await page.click('[data-theme-toggle]')
    await page.waitForTimeout(500)  // Wait for theme transition

    // Dark mode
    await expect(page).toHaveScreenshot('homepage-dark.png')
  })
})

// Update baseline screenshots
// npm run test:visual -- --update-snapshots
```

## 4. CI/CD Integration

### GitHub Actions Configuration

```
You: Configure CI/CD process for automated testing and deployment

Claude: Create GitHub Actions workflow:

# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  # Code quality check
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run ESLint
        run: npm run lint

      - name: Run Prettier check
        run: npm run format:check

  # Unit tests
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit -- --coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/coverage-final.json

  # E2E tests
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npm run test:e2e

      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: playwright-report
          path: playwright-report/

  # Build
  build:
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: dist/

  # Deploy to Staging
  deploy-staging:
    needs: [build, e2e]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/develop'
    environment:
      name: staging
      url: https://staging.example.com
    steps:
      - name: Download build
        uses: actions/download-artifact@v3
        with:
          name: build
          path: dist/

      - name: Deploy to Staging
        run: |
          # Deploy to staging server
          echo "Deploying to staging..."
          # scp -r dist/* user@staging.example.com:/var/www/html

  # Deploy to Production
  deploy-production:
    needs: [build, e2e]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://example.com
    steps:
      - name: Download build
        uses: actions/download-artifact@v3
        with:
          name: build
          path: dist/

      - name: Deploy to Production
        run: |
          echo "Deploying to production..."
          # Actual deployment commands

      - name: Notify Slack
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}

  # Performance testing
  performance:
    needs: [deploy-staging]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/develop'
    steps:
      - uses: actions/checkout@v3

      - name: Run Lighthouse CI
        run: |
          npm install -g @lhci/cli
          lhci autorun --config=lighthouserc.json

      - name: Upload results
        uses: actions/upload-artifact@v3
        with:
          name: lighthouse-results
          path: .lighthouseci/

# Lighthouse config: lighthouserc.json
{
  "ci": {
    "collect": {
      "url": ["https://staging.example.com"],
      "numberOfRuns": 3
    },
    "assert": {
      "assertions": {
        "categories:performance": ["error", {"minScore": 0.9}],
        "categories:accessibility": ["error", {"minScore": 0.9}],
        "categories:best-practices": ["error", {"minScore": 0.9}],
        "categories:seo": ["error", {"minScore": 0.9}]
      }
    },
    "upload": {
      "target": "temporary-public-storage"
    }
  }
}
```

## 5. Multi-Language Project Development

### Python + FastAPI Backend

```
You: Rewrite backend API using Python FastAPI

Claude: Create FastAPI backend:

# backend/main.py
from fastapi import FastAPI, HTTPException, Depends
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import List, Optional
import uvicorn

app = FastAPI(title="Task API", version="1.0.0")

# CORS configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Data models
class Task(BaseModel):
    id: Optional[int] = None
    title: str
    description: Optional[str] = None
    completed: bool = False
    priority: str = "medium"

class TaskCreate(BaseModel):
    title: str
    description: Optional[str] = None
    priority: str = "medium"

class TaskUpdate(BaseModel):
    title: Optional[str] = None
    description: Optional[str] = None
    completed: Optional[bool] = None
    priority: Optional[str] = None

# Database (in-memory example)
tasks_db = []
next_id = 1

# Routes
@app.get("/")
async def root():
    return {"message": "Task API", "version": "1.0.0"}

@app.get("/api/tasks", response_model=List[Task])
async def get_tasks():
    """Get all tasks"""
    return tasks_db

@app.get("/api/tasks/{task_id}", response_model=Task)
async def get_task(task_id: int):
    """Get single task"""
    task = next((t for t in tasks_db if t['id'] == task_id), None)
    if not task:
        raise HTTPException(status_code=404, detail="Task not found")
    return task

@app.post("/api/tasks", response_model=Task, status_code=201)
async def create_task(task: TaskCreate):
    """Create new task"""
    global next_id
    new_task = {
        "id": next_id,
        **task.dict()
    }
    tasks_db.append(new_task)
    next_id += 1
    return new_task

@app.put("/api/tasks/{task_id}", response_model=Task)
async def update_task(task_id: int, task: TaskUpdate):
    """Update task"""
    existing_task = next((t for t in tasks_db if t['id'] == task_id), None)
    if not existing_task:
        raise HTTPException(status_code=404, detail="Task not found")

    # Update fields
    update_data = task.dict(exclude_unset=True)
    for key, value in update_data.items():
        existing_task[key] = value

    return existing_task

@app.delete("/api/tasks/{task_id}", status_code=204)
async def delete_task(task_id: int):
    """Delete task"""
    global tasks_db
    tasks_db = [t for t in tasks_db if t['id'] != task_id]
    return None

# Health check
@app.get("/health")
async def health_check():
    return {"status": "healthy"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)

# requirements.txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
pydantic==2.5.0

# Install and run
# pip install -r requirements.txt
# python main.py
```

## 6. Summary and Outlook

### Skills You've Mastered

✅ **Basic Skills**
- Claude Code usage
- Effective AI communication
- Basic file operations

✅ **Development Skills**
- Frontend development (HTML/CSS/JavaScript)
- Backend development (Node.js/Python)
- Database operations
- API design

✅ **Engineering Practices**
- Code review and refactoring
- Testing (unit tests, E2E tests)
- CI/CD automation
- Performance optimization

✅ **Advanced Techniques**
- Design pattern application
- Project architecture design
- Plugin systems
- Multi-language development

### Continued Learning Directions

🚀 **Deep Learning**
- Cloud-native development (Docker, Kubernetes)
- Microservices architecture
- GraphQL
- Machine learning integration

🎯 **Practical Projects**
- Open source contributions
- Personal portfolio website
- SaaS product development
- Mobile app development

📚 **Recommended Resources**
- MDN Web Docs (Web development)
- freeCodeCamp (Free programming courses)
- GitHub (Learn from open source)
- Stack Overflow (Problem solving)

---

💡 **Final Advice**:
- Stay curious, keep learning
- Build more projects, practice is the best teacher
- Participate in communities, share your experiences
- Use Claude Code well, but understand the code principles
- Write elegant code, not just working code

🎉 **Congratulations on completing all courses! Now, start creating your own projects!**
