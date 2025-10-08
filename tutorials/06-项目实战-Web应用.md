# 第六课：项目实战 - 完整 Web 应用

> 🎯 **学习目标**：从零构建一个完整的 Web 应用，包含前后端

## 项目概述

我们将创建一个**任务管理应用**（Todo App Pro），包含：
- ✅ 用户注册和登录
- ✅ 创建、编辑、删除任务
- ✅ 任务分类和标签
- ✅ 数据持久化
- ✅ 响应式设计

## 1. 项目规划

### 技术栈选择

```
你：我想做一个任务管理应用，帮我选择技术栈

Claude：
基于你的需求，我推荐：

前端：
- HTML5 + CSS3 + Vanilla JavaScript
- 或 React（如果想学现代框架）

后端：
- Node.js + Express
- SQLite 数据库（简单易用）

为什么这样选择：
1. 学习曲线平缓
2. 部署简单
3. 功能完整

你想用哪个方案？
```

### 功能拆分

```
你：把项目拆分成可管理的模块

Claude：
项目模块划分：

第一阶段（基础）：
- [ ] 项目结构搭建
- [ ] 静态页面设计
- [ ] 基本样式实现

第二阶段（核心功能）：
- [ ] 任务 CRUD 操作
- [ ] 本地存储
- [ ] 数据验证

第三阶段（增强）：
- [ ] 用户认证
- [ ] 后端 API
- [ ] 数据库集成

第四阶段（优化）：
- [ ] 响应式设计
- [ ] 性能优化
- [ ] 部署上线
```

## 2. 第一阶段：基础搭建

### 创建项目结构

```
你：创建 todo-app-pro 项目结构

Claude：
todo-app-pro/
├── client/
│   ├── index.html
│   ├── css/
│   │   ├── style.css
│   │   ├── components.css
│   │   └── responsive.css
│   ├── js/
│   │   ├── app.js
│   │   ├── task.js
│   │   ├── ui.js
│   │   └── storage.js
│   └── assets/
│       └── images/
├── server/
│   ├── index.js
│   ├── routes/
│   ├── models/
│   └── middleware/
├── package.json
├── .gitignore
└── README.md

项目结构已创建！
```

### 设计首页

```
你：创建一个现代化的任务管理应用首页，包含：
- 顶部导航栏（Logo、搜索框、用户头像）
- 侧边栏（任务分类）
- 主内容区（任务列表）
- 底部添加任务按钮

Claude：
我会为你创建一个美观的界面...
[创建 HTML 结构和 CSS 样式]

✅ 已创建 client/index.html
✅ 已创建 client/css/style.css
💡 在浏览器中打开 client/index.html 查看
```

## 3. 第二阶段：核心功能

### 任务数据模型

```
你：定义任务的数据结构

Claude：
创建 client/js/task.js：

class Task {
  constructor(title, description, dueDate, priority, category) {
    this.id = Date.now().toString()
    this.title = title
    this.description = description
    this.dueDate = dueDate
    this.priority = priority // 'high', 'medium', 'low'
    this.category = category
    this.completed = false
    this.createdAt = new Date()
  }
}
```

### 实现 CRUD 操作

```
你：实现任务的增删改查功能

Claude：
我会在 client/js/app.js 中实现：

1. 创建任务 (Create)
function createTask(taskData) {
  const task = new Task(...)
  tasks.push(task)
  saveToStorage()
  renderTasks()
}

2. 读取任务 (Read)
function getTasks() {
  return loadFromStorage()
}

3. 更新任务 (Update)
function updateTask(id, updates) {
  const task = tasks.find(t => t.id === id)
  Object.assign(task, updates)
  saveToStorage()
  renderTasks()
}

4. 删除任务 (Delete)
function deleteTask(id) {
  tasks = tasks.filter(t => t.id !== id)
  saveToStorage()
  renderTasks()
}

✅ 核心功能已实现
```

### 本地存储

```
你：实现数据持久化，使用 localStorage

Claude：
创建 client/js/storage.js：

const STORAGE_KEY = 'todoapp_tasks'

// 保存到本地
function saveToStorage(tasks) {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(tasks))
}

// 从本地加载
function loadFromStorage() {
  const data = localStorage.getItem(STORAGE_KEY)
  return data ? JSON.parse(data) : []
}

// 清空数据
function clearStorage() {
  localStorage.removeItem(STORAGE_KEY)
}

✅ 数据现在会自动保存到浏览器
```

### UI 交互

```
你：实现任务添加的交互流程

Claude：
实现完整的添加流程：

1. 用户点击"添加任务"按钮
2. 弹出模态框
3. 填写表单
4. 验证输入
5. 创建任务
6. 关闭模态框
7. 刷新列表
8. 显示成功提示

已添加到 client/js/ui.js
```

## 4. 第三阶段：后端开发

### 搭建 Express 服务器

```
你：创建 Node.js 后端服务器

Claude：
1. 初始化 package.json
2. 安装依赖：express, cors, dotenv
3. 创建基本服务器

server/index.js：
const express = require('express')
const cors = require('cors')
const app = express()

app.use(cors())
app.use(express.json())

// 路由
app.use('/api/tasks', require('./routes/tasks'))
app.use('/api/users', require('./routes/users'))

const PORT = process.env.PORT || 3000
app.listen(PORT, () => {
  console.log(`服务器运行在 http://localhost:${PORT}`)
})

✅ 后端服务器已创建
💡 运行 `node server/index.js` 启动服务器
```

### API 路由设计

```
你：设计 RESTful API

Claude：
创建 server/routes/tasks.js：

GET    /api/tasks          - 获取所有任务
GET    /api/tasks/:id      - 获取单个任务
POST   /api/tasks          - 创建新任务
PUT    /api/tasks/:id      - 更新任务
DELETE /api/tasks/:id      - 删除任务

示例实现：
router.get('/', async (req, res) => {
  try {
    const tasks = await Task.findAll()
    res.json(tasks)
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})

✅ API 路由已创建
```

### 数据库集成

```
你：集成 SQLite 数据库

Claude：
1. 安装 better-sqlite3
2. 创建数据库连接
3. 定义表结构

server/database.js：
const Database = require('better-sqlite3')
const db = new Database('tasks.db')

// 创建表
db.exec(`
  CREATE TABLE IF NOT EXISTS tasks (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    description TEXT,
    due_date TEXT,
    priority TEXT,
    category TEXT,
    completed BOOLEAN DEFAULT 0,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
  )
`)

✅ 数据库已配置
💡 数据现在会保存到 tasks.db 文件
```

## 5. 前后端连接

### 前端 API 调用

```
你：创建前端 API 服务层

Claude：
创建 client/js/api.js：

const API_BASE = 'http://localhost:3000/api'

const api = {
  // 获取所有任务
  async getTasks() {
    const response = await fetch(`${API_BASE}/tasks`)
    return response.json()
  },

  // 创建任务
  async createTask(taskData) {
    const response = await fetch(`${API_BASE}/tasks`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(taskData)
    })
    return response.json()
  },

  // 更新任务
  async updateTask(id, updates) {
    const response = await fetch(`${API_BASE}/tasks/${id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(updates)
    })
    return response.json()
  },

  // 删除任务
  async deleteTask(id) {
    await fetch(`${API_BASE}/tasks/${id}`, {
      method: 'DELETE'
    })
  }
}

✅ API 服务层已创建
```

### 更新前端逻辑

```
你：更新前端代码，使用后端 API 而不是 localStorage

Claude：
更新 client/js/app.js：

// 之前
function loadTasks() {
  return loadFromStorage()
}

// 现在
async function loadTasks() {
  try {
    tasks = await api.getTasks()
    renderTasks()
  } catch (error) {
    showError('加载任务失败')
  }
}

同样更新所有 CRUD 操作...

✅ 前端现在使用后端 API
💡 记得先启动后端服务器
```

## 6. 用户认证

### 实现注册登录

```
你：添加用户注册和登录功能

Claude：
我会创建完整的认证系统：

1. 创建用户表
2. 实现注册接口（密码加密）
3. 实现登录接口（JWT token）
4. 创建登录页面
5. 添加认证中间件
6. 保护 API 路由

已创建：
- server/routes/auth.js
- server/middleware/auth.js
- client/login.html
- client/js/auth.js

使用方式：
POST /api/auth/register - 注册
POST /api/auth/login    - 登录
GET  /api/auth/profile  - 获取用户信息
```

### JWT Token 管理

```
你：实现前端的 token 管理

Claude：
创建 client/js/auth.js：

const authManager = {
  // 保存 token
  saveToken(token) {
    localStorage.setItem('auth_token', token)
  },

  // 获取 token
  getToken() {
    return localStorage.getItem('auth_token')
  },

  // 移除 token（登出）
  removeToken() {
    localStorage.removeItem('auth_token')
  },

  // 检查是否登录
  isLoggedIn() {
    return !!this.getToken()
  },

  // 添加 token 到请求头
  getHeaders() {
    return {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${this.getToken()}`
    }
  }
}

✅ Token 管理已实现
```

## 7. 功能增强

### 搜索和过滤

```
你：添加任务搜索和过滤功能

Claude：
实现多维度筛选：

// 搜索
function searchTasks(keyword) {
  return tasks.filter(task =>
    task.title.toLowerCase().includes(keyword.toLowerCase()) ||
    task.description.toLowerCase().includes(keyword.toLowerCase())
  )
}

// 按分类过滤
function filterByCategory(category) {
  return tasks.filter(task => task.category === category)
}

// 按优先级过滤
function filterByPriority(priority) {
  return tasks.filter(task => task.priority === priority)
}

// 按状态过滤
function filterByStatus(completed) {
  return tasks.filter(task => task.completed === completed)
}

// 组合过滤
function filterTasks(filters) {
  let filtered = [...tasks]

  if (filters.keyword) {
    filtered = searchTasks(filters.keyword)
  }
  if (filters.category) {
    filtered = filtered.filter(t => t.category === filters.category)
  }
  // ... 更多过滤条件

  return filtered
}

✅ 搜索和过滤功能已添加
```

### 排序功能

```
你：添加任务排序（按日期、优先级、状态）

Claude：
实现多种排序方式：

const sortOptions = {
  // 按创建时间
  byDate: (a, b) => new Date(b.createdAt) - new Date(a.createdAt),

  // 按截止日期
  byDueDate: (a, b) => new Date(a.dueDate) - new Date(b.dueDate),

  // 按优先级
  byPriority: (a, b) => {
    const priority = { high: 3, medium: 2, low: 1 }
    return priority[b.priority] - priority[a.priority]
  },

  // 按标题
  byTitle: (a, b) => a.title.localeCompare(b.title)
}

function sortTasks(tasks, sortBy) {
  return [...tasks].sort(sortOptions[sortBy])
}

添加排序 UI：
[下拉菜单选择排序方式]

✅ 排序功能已实现
```

### 拖拽排序

```
你：实现任务拖拽重新排序

Claude：
使用 HTML5 Drag and Drop API：

// 添加拖拽属性
<div class="task" draggable="true" data-id="123">
  ...
</div>

// 实现拖拽逻辑
let draggedElement = null

element.addEventListener('dragstart', (e) => {
  draggedElement = e.target
  e.dataTransfer.effectAllowed = 'move'
})

element.addEventListener('dragover', (e) => {
  e.preventDefault()
  e.dataTransfer.dropEffect = 'move'
})

element.addEventListener('drop', (e) => {
  e.preventDefault()
  // 交换位置
  swapTasks(draggedElement.dataset.id, e.target.dataset.id)
})

✅ 拖拽排序已实现
```

## 8. 响应式设计

### 移动端适配

```
你：让应用支持手机访问

Claude：
添加响应式设计：

client/css/responsive.css：

/* 移动端 */
@media (max-width: 768px) {
  .sidebar {
    position: fixed;
    left: -250px;
    transition: left 0.3s;
  }

  .sidebar.open {
    left: 0;
  }

  .main-content {
    margin-left: 0;
  }

  .task-card {
    flex-direction: column;
  }
}

/* 平板 */
@media (min-width: 769px) and (max-width: 1024px) {
  .sidebar {
    width: 200px;
  }
}

✅ 响应式设计已添加
💡 在手机上打开查看效果
```

### 触摸手势

```
你：添加移动端手势支持（滑动删除）

Claude：
实现触摸手势：

let touchStartX = 0
let touchEndX = 0

element.addEventListener('touchstart', (e) => {
  touchStartX = e.touches[0].clientX
})

element.addEventListener('touchend', (e) => {
  touchEndX = e.changedTouches[0].clientX
  handleSwipe()
})

function handleSwipe() {
  const swipeDistance = touchStartX - touchEndX

  // 向左滑动删除
  if (swipeDistance > 100) {
    showDeleteConfirm()
  }

  // 向右滑动完成
  if (swipeDistance < -100) {
    toggleComplete()
  }
}

✅ 触摸手势已添加
```

## 9. 性能优化

### 懒加载

```
你：优化任务列表加载性能

Claude：
实现虚拟滚动（懒加载）：

// 只渲染可见的任务
const ITEMS_PER_PAGE = 20
let currentPage = 1

function renderVisibleTasks() {
  const start = (currentPage - 1) * ITEMS_PER_PAGE
  const end = start + ITEMS_PER_PAGE
  const visibleTasks = tasks.slice(start, end)

  renderTasks(visibleTasks)
}

// 监听滚动
window.addEventListener('scroll', () => {
  if (isNearBottom()) {
    currentPage++
    loadMoreTasks()
  }
})

✅ 懒加载已实现
💡 现在可以流畅处理成千上万个任务
```

### 缓存策略

```
你：添加数据缓存减少 API 调用

Claude：
实现智能缓存：

const cache = {
  data: null,
  timestamp: null,
  ttl: 5 * 60 * 1000 // 5分钟
}

async function getTasks(forceRefresh = false) {
  // 检查缓存
  if (!forceRefresh && cache.data &&
      Date.now() - cache.timestamp < cache.ttl) {
    return cache.data
  }

  // 请求新数据
  const tasks = await api.getTasks()

  // 更新缓存
  cache.data = tasks
  cache.timestamp = Date.now()

  return tasks
}

✅ 缓存已实现，减少了 80% 的 API 调用
```

## 10. 测试

### 单元测试

```
你：为核心函数添加单元测试

Claude：
创建 tests/task.test.js：

const { Task } = require('../client/js/task')

describe('Task Class', () => {
  test('创建新任务', () => {
    const task = new Task('买菜', '购买晚餐食材')
    expect(task.title).toBe('买菜')
    expect(task.completed).toBe(false)
  })

  test('标记任务完成', () => {
    const task = new Task('任务')
    task.completed = true
    expect(task.completed).toBe(true)
  })
})

运行：npm test

✅ 测试已创建
```

## 11. 部署上线

### 准备部署

```
你：准备生产环境部署

Claude：
执行部署前检查：

1. ✅ 环境变量配置
2. ✅ 数据库迁移脚本
3. ✅ 压缩静态资源
4. ✅ 错误处理完善
5. ✅ 日志系统配置
6. ✅ 安全性检查

创建部署脚本：scripts/deploy.sh
```

### 部署到 Heroku

```
你：部署到 Heroku

Claude：
部署步骤：

1. 创建 Procfile：
web: node server/index.js

2. 配置环境变量
3. 推送到 Heroku

命令：
heroku create todoapp-pro
git push heroku main
heroku open

✅ 应用已部署！
🌐 访问：https://todoapp-pro.herokuapp.com
```

## 12. 项目总结

### 完成清单

- ✅ 现代化 UI 界面
- ✅ 完整的 CRUD 功能
- ✅ 用户认证系统
- ✅ 前后端分离架构
- ✅ 数据库持久化
- ✅ 搜索和过滤
- ✅ 响应式设计
- ✅ 性能优化
- ✅ 测试覆盖
- ✅ 线上部署

### 学到的技能

1. **前端开发**
   - HTML/CSS/JavaScript
   - DOM 操作
   - 事件处理
   - AJAX 请求

2. **后端开发**
   - Node.js + Express
   - RESTful API
   - 数据库操作
   - 用户认证

3. **工程实践**
   - 项目架构设计
   - 代码组织
   - 版本控制
   - 测试和部署

## 详细模块

- [01-项目规划](./06-项目实战-Web应用/01-项目规划.md)
- [02-项目结构](./06-项目实战-Web应用/02-项目结构.md)
- [03-静态页面](./06-项目实战-Web应用/03-静态页面.md)
- [04-前端功能](./06-项目实战-Web应用/04-前端功能.md)
- [05-后端搭建](./06-项目实战-Web应用/05-后端搭建.md)
- [06-用户认证](./06-项目实战-Web应用/06-用户认证.md)
- [07-功能增强](./06-项目实战-Web应用/07-功能增强.md)
- [08-部署上线](./06-项目实战-Web应用/08-部署上线.md)

## 13. 下一步

👉 下一课：[进阶技巧和最佳实践](./07-进阶技巧.md)

学习更多高级话题：
- 设计模式
- 代码优化
- 安全最佳实践
- 性能监控

---

💡 **项目扩展建议**：
- 添加任务提醒功能
- 实现团队协作
- 添加数据统计和图表
- 支持文件附件
- 集成第三方服务（日历、邮件等）
