# 1.7 Feature Enhancement

> Add advanced features and optimizations to your application

## Learning Objectives

By the end of this module, you will:
- Implement search and filtering functionality
- Add sorting capabilities
- Create drag-and-drop task reordering
- Optimize application performance
- Add responsive touch gestures

## Overview

In this module, we'll enhance our Todo App Pro with advanced features that improve user experience and performance.

## 1. Search and Filtering

### Implementing Advanced Search

**Your Conversation with Claude**:
```
You: Add comprehensive search and filtering

Claude:
I'll create a powerful search and filter system:
```

**client/js/search.js**:
```javascript
/**
 * Search and Filter Manager
 */
const SearchFilter = {
    /**
     * Search tasks by keyword
     */
    searchTasks(tasks, keyword) {
        if (!keyword || keyword.trim() === '') {
            return tasks;
        }

        const lowerKeyword = keyword.toLowerCase();

        return tasks.filter(task => {
            const titleMatch = task.title.toLowerCase().includes(lowerKeyword);
            const descMatch = task.description.toLowerCase().includes(lowerKeyword);
            const categoryMatch = task.category.toLowerCase().includes(lowerKeyword);

            return titleMatch || descMatch || categoryMatch;
        });
    },

    /**
     * Filter tasks by multiple criteria
     */
    filterTasks(tasks, filters) {
        let filtered = [...tasks];

        // Filter by category
        if (filters.category && filters.category !== 'all') {
            filtered = filtered.filter(t => t.category === filters.category);
        }

        // Filter by status
        if (filters.status === 'active') {
            filtered = filtered.filter(t => !t.completed);
        } else if (filters.status === 'completed') {
            filtered = filtered.filter(t => t.completed);
        }

        // Filter by priority
        if (filters.priority) {
            filtered = filtered.filter(t => t.priority === filters.priority);
        }

        // Filter by due date
        if (filters.dueDate) {
            const today = new Date();
            today.setHours(0, 0, 0, 0);

            switch (filters.dueDate) {
                case 'today':
                    filtered = filtered.filter(t => {
                        if (!t.dueDate) return false;
                        const due = new Date(t.dueDate);
                        due.setHours(0, 0, 0, 0);
                        return due.getTime() === today.getTime();
                    });
                    break;

                case 'week':
                    const weekFromNow = new Date(today);
                    weekFromNow.setDate(weekFromNow.getDate() + 7);
                    filtered = filtered.filter(t => {
                        if (!t.dueDate) return false;
                        const due = new Date(t.dueDate);
                        return due >= today && due <= weekFromNow;
                    });
                    break;

                case 'overdue':
                    filtered = filtered.filter(t => {
                        if (!t.dueDate || t.completed) return false;
                        return new Date(t.dueDate) < today;
                    });
                    break;
            }
        }

        // Filter by tags (if implemented)
        if (filters.tags && filters.tags.length > 0) {
            filtered = filtered.filter(t =>
                filters.tags.some(tag => t.tags?.includes(tag))
            );
        }

        return filtered;
    },

    /**
     * Combine search and filters
     */
    applyAll(tasks, searchKeyword, filters) {
        let result = this.searchTasks(tasks, searchKeyword);
        result = this.filterTasks(result, filters);
        return result;
    }
};
```

### Enhanced Search UI

Add to **client/index.html**:
```html
<!-- Advanced Filter Panel -->
<div class="filter-panel" id="filterPanel">
    <div class="filter-section">
        <h3>Priority</h3>
        <div class="filter-options">
            <label>
                <input type="checkbox" value="high" class="priority-filter">
                High
            </label>
            <label>
                <input type="checkbox" value="medium" class="priority-filter">
                Medium
            </label>
            <label>
                <input type="checkbox" value="low" class="priority-filter">
                Low
            </label>
        </div>
    </div>

    <div class="filter-section">
        <h3>Due Date</h3>
        <select class="filter-select" id="dueDateFilter">
            <option value="">All</option>
            <option value="today">Today</option>
            <option value="week">This Week</option>
            <option value="overdue">Overdue</option>
        </select>
    </div>

    <div class="filter-actions">
        <button class="btn btn-cancel" id="clearFilters">Clear All</button>
        <button class="btn btn-primary" id="applyFilters">Apply</button>
    </div>
</div>
```

## 2. Sorting Functionality

### Implementing Multiple Sort Options

**Your Conversation with Claude**:
```
You: Add flexible sorting capabilities

Claude:
I'll create a comprehensive sorting system:
```

**client/js/sort.js**:
```javascript
/**
 * Task Sorting Manager
 */
const TaskSorter = {
    /**
     * Sort functions
     */
    sortFunctions: {
        // By creation date (newest first)
        dateDesc: (a, b) => new Date(b.createdAt) - new Date(a.createdAt),

        // By creation date (oldest first)
        dateAsc: (a, b) => new Date(a.createdAt) - new Date(b.createdAt),

        // By due date (earliest first)
        dueDateAsc: (a, b) => {
            if (!a.dueDate && !b.dueDate) return 0;
            if (!a.dueDate) return 1;
            if (!b.dueDate) return -1;
            return new Date(a.dueDate) - new Date(b.dueDate);
        },

        // By due date (latest first)
        dueDateDesc: (a, b) => {
            if (!a.dueDate && !b.dueDate) return 0;
            if (!a.dueDate) return -1;
            if (!b.dueDate) return 1;
            return new Date(b.dueDate) - new Date(a.dueDate);
        },

        // By priority (high to low)
        priorityDesc: (a, b) => {
            const priorityValue = { high: 3, medium: 2, low: 1 };
            return priorityValue[b.priority] - priorityValue[a.priority];
        },

        // By priority (low to high)
        priorityAsc: (a, b) => {
            const priorityValue = { high: 3, medium: 2, low: 1 };
            return priorityValue[a.priority] - priorityValue[b.priority];
        },

        // By title (A-Z)
        titleAsc: (a, b) => a.title.localeCompare(b.title),

        // By title (Z-A)
        titleDesc: (a, b) => b.title.localeCompare(a.title),

        // By status (incomplete first)
        statusAsc: (a, b) => {
            if (a.completed === b.completed) return 0;
            return a.completed ? 1 : -1;
        },

        // By status (complete first)
        statusDesc: (a, b) => {
            if (a.completed === b.completed) return 0;
            return a.completed ? -1 : 1;
        }
    },

    /**
     * Sort tasks by specified criteria
     */
    sort(tasks, sortBy, order = 'asc') {
        const sortKey = `${sortBy}${order === 'desc' ? 'Desc' : 'Asc'}`;
        const sortFn = this.sortFunctions[sortKey];

        if (!sortFn) {
            console.warn(`Unknown sort option: ${sortKey}`);
            return tasks;
        }

        return [...tasks].sort(sortFn);
    },

    /**
     * Multi-level sorting
     */
    multiSort(tasks, sortCriteria) {
        return [...tasks].sort((a, b) => {
            for (const criteria of sortCriteria) {
                const { field, order } = criteria;
                const sortKey = `${field}${order === 'desc' ? 'Desc' : 'Asc'}`;
                const sortFn = this.sortFunctions[sortKey];

                if (sortFn) {
                    const result = sortFn(a, b);
                    if (result !== 0) return result;
                }
            }
            return 0;
        });
    }
};
```

### Smart Sorting UI

Add sorting controls:
```javascript
// Add to app.js
const sortControl = {
    currentSort: 'dateDesc',

    init() {
        const sortSelect = document.getElementById('sortSelect');
        sortSelect.addEventListener('change', (e) => {
            this.currentSort = e.target.value;
            App.renderTasks();
        });
    },

    getSortedTasks(tasks) {
        const [field, order] = this.currentSort.match(/([a-z]+)(Asc|Desc)/i).slice(1);
        return TaskSorter.sort(tasks, field.toLowerCase(), order.toLowerCase());
    }
};
```

## 3. Drag-and-Drop Reordering

### Implementing Drag-and-Drop

**Your Conversation with Claude**:
```
You: Add drag-and-drop to reorder tasks

Claude:
I'll implement HTML5 drag-and-drop functionality:
```

**client/js/dragdrop.js**:
```javascript
/**
 * Drag and Drop Manager
 */
const DragDropManager = {
    draggedElement: null,
    draggedTaskId: null,

    /**
     * Initialize drag and drop
     */
    init() {
        this.attachEventListeners();
    },

    /**
     * Attach event listeners to task cards
     */
    attachEventListeners() {
        const taskCards = document.querySelectorAll('.task-card');

        taskCards.forEach(card => {
            card.draggable = true;

            card.addEventListener('dragstart', this.handleDragStart.bind(this));
            card.addEventListener('dragend', this.handleDragEnd.bind(this));
            card.addEventListener('dragover', this.handleDragOver.bind(this));
            card.addEventListener('drop', this.handleDrop.bind(this));
            card.addEventListener('dragenter', this.handleDragEnter.bind(this));
            card.addEventListener('dragleave', this.handleDragLeave.bind(this));
        });
    },

    /**
     * Handle drag start
     */
    handleDragStart(e) {
        this.draggedElement = e.currentTarget;
        this.draggedTaskId = e.currentTarget.dataset.taskId;

        e.currentTarget.classList.add('dragging');
        e.dataTransfer.effectAllowed = 'move';
        e.dataTransfer.setData('text/html', e.currentTarget.innerHTML);
    },

    /**
     * Handle drag end
     */
    handleDragEnd(e) {
        e.currentTarget.classList.remove('dragging');

        // Remove all drag-over classes
        document.querySelectorAll('.task-card').forEach(card => {
            card.classList.remove('drag-over');
        });

        this.draggedElement = null;
        this.draggedTaskId = null;
    },

    /**
     * Handle drag over
     */
    handleDragOver(e) {
        e.preventDefault();
        e.dataTransfer.dropEffect = 'move';
        return false;
    },

    /**
     * Handle drag enter
     */
    handleDragEnter(e) {
        if (e.currentTarget !== this.draggedElement) {
            e.currentTarget.classList.add('drag-over');
        }
    },

    /**
     * Handle drag leave
     */
    handleDragLeave(e) {
        e.currentTarget.classList.remove('drag-over');
    },

    /**
     * Handle drop
     */
    handleDrop(e) {
        e.preventDefault();
        e.stopPropagation();

        const dropTarget = e.currentTarget;
        const dropTaskId = dropTarget.dataset.taskId;

        if (this.draggedTaskId !== dropTaskId) {
            // Reorder tasks
            App.reorderTasks(this.draggedTaskId, dropTaskId);
        }

        dropTarget.classList.remove('drag-over');
        return false;
    }
};

// Add CSS for drag states
const dragStyles = `
    .task-card.dragging {
        opacity: 0.5;
        cursor: grabbing;
    }

    .task-card.drag-over {
        border-top: 3px solid var(--primary-color);
        margin-top: -3px;
    }

    .task-card {
        cursor: grab;
        transition: opacity 0.2s ease;
    }
`;

const style = document.createElement('style');
style.textContent = dragStyles;
document.head.appendChild(style);
```

### Task Reordering Logic

Add to **client/js/app.js**:
```javascript
/**
 * Reorder tasks
 */
reorderTasks(draggedId, targetId) {
    const draggedIndex = this.tasks.findIndex(t => t.id === draggedId);
    const targetIndex = this.tasks.findIndex(t => t.id === targetId);

    if (draggedIndex === -1 || targetIndex === -1) return;

    // Remove dragged task
    const [draggedTask] = this.tasks.splice(draggedIndex, 1);

    // Insert at new position
    this.tasks.splice(targetIndex, 0, draggedTask);

    // Save and render
    this.saveTasks();
    this.renderTasks();
}
```

## 4. Performance Optimization

### Implementing Virtual Scrolling

**Your Conversation with Claude**:
```
You: Optimize for large task lists with virtual scrolling

Claude:
I'll implement virtual scrolling for better performance:
```

**client/js/virtual-scroll.js**:
```javascript
/**
 * Virtual Scroll Manager
 * Renders only visible items for better performance
 */
class VirtualScroll {
    constructor(container, itemHeight, renderItem) {
        this.container = container;
        this.itemHeight = itemHeight;
        this.renderItem = renderItem;
        this.items = [];
        this.visibleStart = 0;
        this.visibleEnd = 0;

        this.init();
    }

    init() {
        this.container.style.position = 'relative';
        this.container.addEventListener('scroll', () => this.onScroll());
        window.addEventListener('resize', () => this.update());
    }

    setItems(items) {
        this.items = items;
        this.update();
    }

    update() {
        const containerHeight = this.container.clientHeight;
        const scrollTop = this.container.scrollTop;

        // Calculate visible range
        this.visibleStart = Math.floor(scrollTop / this.itemHeight);
        this.visibleEnd = Math.ceil((scrollTop + containerHeight) / this.itemHeight);

        // Add buffer
        const buffer = 5;
        this.visibleStart = Math.max(0, this.visibleStart - buffer);
        this.visibleEnd = Math.min(this.items.length, this.visibleEnd + buffer);

        this.render();
    }

    render() {
        const totalHeight = this.items.length * this.itemHeight;
        const offsetY = this.visibleStart * this.itemHeight;

        // Create viewport
        this.container.innerHTML = `
            <div style="height: ${totalHeight}px; position: relative;">
                <div style="transform: translateY(${offsetY}px);">
                    ${this.items
                        .slice(this.visibleStart, this.visibleEnd)
                        .map(item => this.renderItem(item))
                        .join('')}
                </div>
            </div>
        `;
    }

    onScroll() {
        requestAnimationFrame(() => this.update());
    }
}

// Usage example
const taskVirtualScroll = new VirtualScroll(
    document.getElementById('taskList'),
    100, // estimated height of each task card
    (task) => UIManager.createTaskCard(task)
);
```

### Debouncing Search

Add to **client/js/utils.js**:
```javascript
/**
 * Utility Functions
 */
const Utils = {
    /**
     * Debounce function calls
     */
    debounce(func, wait) {
        let timeout;
        return function executedFunction(...args) {
            const later = () => {
                clearTimeout(timeout);
                func(...args);
            };
            clearTimeout(timeout);
            timeout = setTimeout(later, wait);
        };
    },

    /**
     * Throttle function calls
     */
    throttle(func, limit) {
        let inThrottle;
        return function(...args) {
            if (!inThrottle) {
                func.apply(this, args);
                inThrottle = true;
                setTimeout(() => inThrottle = false, limit);
            }
        };
    },

    /**
     * Lazy load images
     */
    lazyLoadImages() {
        const images = document.querySelectorAll('img[data-src]');
        const imageObserver = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    const img = entry.target;
                    img.src = img.dataset.src;
                    img.removeAttribute('data-src');
                    imageObserver.unobserve(img);
                }
            });
        });

        images.forEach(img => imageObserver.observe(img));
    }
};

// Apply debouncing to search
const debouncedSearch = Utils.debounce((query) => {
    App.handleSearch(query);
}, 300);

searchInput.addEventListener('input', (e) => {
    debouncedSearch(e.target.value);
});
```

## 5. Mobile Touch Gestures

### Implementing Swipe Actions

**Your Conversation with Claude**:
```
You: Add swipe gestures for mobile

Claude:
I'll implement touch gestures for mobile devices:
```

**client/js/touch.js**:
```javascript
/**
 * Touch Gesture Manager
 */
const TouchManager = {
    touchStartX: 0,
    touchEndX: 0,
    touchStartY: 0,
    minSwipeDistance: 50,

    /**
     * Initialize touch gestures
     */
    init() {
        const taskCards = document.querySelectorAll('.task-card');
        taskCards.forEach(card => {
            this.addSwipeGesture(card);
        });
    },

    /**
     * Add swipe gesture to element
     */
    addSwipeGesture(element) {
        element.addEventListener('touchstart', (e) => {
            this.touchStartX = e.changedTouches[0].screenX;
            this.touchStartY = e.changedTouches[0].screenY;
        }, { passive: true });

        element.addEventListener('touchend', (e) => {
            this.touchEndX = e.changedTouches[0].screenX;
            const touchEndY = e.changedTouches[0].screenY;

            // Check if vertical scroll
            if (Math.abs(touchEndY - this.touchStartY) > 30) {
                return; // It's a scroll, not a swipe
            }

            this.handleSwipe(element);
        }, { passive: true });
    },

    /**
     * Handle swipe gesture
     */
    handleSwipe(element) {
        const swipeDistance = this.touchStartX - this.touchEndX;
        const taskId = element.dataset.taskId;

        // Swipe left - Delete
        if (swipeDistance > this.minSwipeDistance) {
            this.showSwipeActions(element, 'delete');
        }

        // Swipe right - Complete
        if (swipeDistance < -this.minSwipeDistance) {
            this.showSwipeActions(element, 'complete');
        }
    },

    /**
     * Show swipe actions
     */
    showSwipeActions(element, action) {
        const taskId = element.dataset.taskId;

        if (action === 'delete') {
            element.style.transform = 'translateX(-100px)';
            element.style.backgroundColor = '#fee';

            setTimeout(() => {
                if (confirm('Delete this task?')) {
                    App.deleteTask(taskId);
                } else {
                    element.style.transform = '';
                    element.style.backgroundColor = '';
                }
            }, 200);
        } else if (action === 'complete') {
            element.style.transform = 'translateX(100px)';
            element.style.backgroundColor = '#efe';

            setTimeout(() => {
                App.toggleTaskComplete(taskId);
                element.style.transform = '';
                element.style.backgroundColor = '';
            }, 200);
        }
    }
};
```

## 6. Additional Enhancements

### Task Statistics Dashboard

**client/js/stats.js**:
```javascript
/**
 * Statistics Manager
 */
const StatsManager = {
    /**
     * Calculate task statistics
     */
    getStats(tasks) {
        const total = tasks.length;
        const completed = tasks.filter(t => t.completed).length;
        const active = total - completed;
        const overdue = tasks.filter(t => {
            if (!t.dueDate || t.completed) return false;
            return new Date(t.dueDate) < new Date();
        }).length;

        const byPriority = {
            high: tasks.filter(t => t.priority === 'high').length,
            medium: tasks.filter(t => t.priority === 'medium').length,
            low: tasks.filter(t => t.priority === 'low').length
        };

        const byCategory = {};
        tasks.forEach(t => {
            byCategory[t.category] = (byCategory[t.category] || 0) + 1;
        });

        return {
            total,
            completed,
            active,
            overdue,
            completionRate: total > 0 ? (completed / total * 100).toFixed(1) : 0,
            byPriority,
            byCategory
        };
    },

    /**
     * Render statistics
     */
    renderStats(tasks) {
        const stats = this.getStats(tasks);

        return `
            <div class="stats-grid">
                <div class="stat-card">
                    <div class="stat-value">${stats.total}</div>
                    <div class="stat-label">Total Tasks</div>
                </div>
                <div class="stat-card">
                    <div class="stat-value">${stats.completed}</div>
                    <div class="stat-label">Completed</div>
                </div>
                <div class="stat-card">
                    <div class="stat-value">${stats.active}</div>
                    <div class="stat-label">Active</div>
                </div>
                <div class="stat-card stat-danger">
                    <div class="stat-value">${stats.overdue}</div>
                    <div class="stat-label">Overdue</div>
                </div>
            </div>
            <div class="stat-progress">
                <div class="progress-bar">
                    <div class="progress-fill" style="width: ${stats.completionRate}%"></div>
                </div>
                <div class="progress-text">${stats.completionRate}% Complete</div>
            </div>
        `;
    }
};
```

## Key Takeaways

1. **Advanced Search** - Implement comprehensive search and filtering
2. **Flexible Sorting** - Support multiple sort criteria
3. **Drag-and-Drop** - Add intuitive reordering
4. **Performance** - Optimize for large datasets
5. **Mobile UX** - Enhance mobile experience with gestures

## Practice Exercise

**Task**: Add more enhancements
1. Implement task tags/labels
2. Add task templates
3. Create keyboard shortcuts
4. Implement bulk operations
5. Add data export/import

## Next Steps

Now that your app is feature-rich, move on to:

👉 **[1.8 Deployment](./08-deployment.md)** - Deploy your application to production

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)
