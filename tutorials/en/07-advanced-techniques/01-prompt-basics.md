# 1.1 Prompt Basics

> Master the fundamentals of effective prompt writing for better AI collaboration

## Overview

Good prompts are the foundation of successful AI collaboration. This module covers essential techniques for writing clear, effective prompts that get you the results you need.

## 1. Precise Requirement Description

### The Problem with Vague Prompts

**❌ Vague Prompt**:
```
Make a website
```

**Issues**:
- AI doesn't know what type of website
- No design or functionality requirements
- Results in 10-15 rounds of clarification
- Wastes time and effort

### The Power of Precision

**✅ Precise Prompt**:
```
Create an e-commerce product display page with requirements:
- Card layout, 3 products per row
- Each product card includes: image (16:9), title, price, rating
- Card slightly lifts with shadow on mouse hover
- Clicking card jumps to detail page
- Blue and white color scheme
- Responsive design, 1 product per row on mobile
```

**Benefits**:
- Clear expectations
- Fewer iterations (1-3 rounds)
- Better results
- Saves 80% of communication time

### The Precision Formula

```
[What] + [How] + [Constraints] + [Expected Outcome]
```

**Example**:
```
[What] Create a user authentication system
[How] Using JWT tokens and bcrypt password hashing
[Constraints] Must work with existing Express.js backend, support refresh tokens
[Expected Outcome] Secure login/logout with 15-min access tokens and 7-day refresh tokens
```

## 2. Step-by-Step Guidance

### When to Use Step-by-Step

Use this approach when:
- The task is complex and has multiple phases
- You want to review each phase before proceeding
- You're learning and want to understand each step
- The task might change direction based on results

### Prompt Template

```
Step 1: [Specific task]
Step 2: [Specific task]
Step 3: [Specific task]

Wait for my confirmation after each step before continuing
```

### Practical Example

**Refactoring a Login Module**:
```
Help me refactor this login module in three steps:

Step 1: Extract form validation logic into separate functions
- Create validateEmail() function
- Create validatePassword() function
- Add error message handling

Step 2: Improve error handling mechanism
- Add try-catch blocks
- Implement user-friendly error messages
- Add logging for debugging

Step 3: Add unit tests
- Test validation functions
- Test error handling
- Achieve 80%+ coverage

Please complete Step 1 first and pause for my review.
```

### Benefits

- ✅ Control over the process
- ✅ Opportunity to course-correct
- ✅ Better understanding of each phase
- ✅ Reduced risk of major rework

## 3. Provide Context

### Why Context Matters

AI makes better decisions when it understands:
- Your project type and scale
- Your technical constraints
- Your team's skill level
- Your users' needs
- Your performance requirements

### Context Checklist

```markdown
✅ Project Information
- Type (web app, mobile app, API, etc.)
- Scale (users, data, requests)
- Stage (MVP, beta, production)

✅ Technical Context
- Tech stack and versions
- Existing architecture
- Dependencies and constraints

✅ Team Context
- Team size and experience
- Development timeline
- Budget constraints

✅ User Context
- User demographics
- Usage patterns
- Devices and network conditions

✅ Business Context
- Critical requirements
- Nice-to-have features
- Dealbreakers
```

### Example: Performance Optimization

**Without Context**:
```
Help me optimize this function
```

**With Rich Context**:
```
Background: I'm developing an online education platform
Tech stack: React + Node.js + MongoDB
Current issue: Course list loads slowly after user login
Already tried: Added loading animation, but still slow

Performance data:
- Current load time: 3-5 seconds
- Number of courses: 500+ per user
- API response time: 2 seconds
- Frontend rendering: 1-2 seconds

Target: Load time < 1 second

Please help me analyze performance bottlenecks and provide optimization solutions.
```

**What the context tells AI**:
- It's a production issue, not theoretical
- The problem is split between API and rendering
- There's a specific performance target
- You've already tried UI solutions

## 4. Use Examples and References

### Why Examples Work

- They eliminate ambiguity
- They show your expectations clearly
- They provide a concrete target
- They save explanation time

### Types of Examples

#### 1. Visual References

```
Help me create a navigation bar similar to GitHub's top navigation:
- Left side: Logo and search box
- Right side: User avatar and dropdown menu
- Fixed at top when scrolling
- Subtle blur background effect

Reference: https://github.com (describe desired effect)
```

#### 2. Code Examples

```
I want to improve this code:

Current code:
```javascript
function getUserData(id) {
  fetch('https://api.example.com/users/' + id)
    .then(res => res.json())
    .then(data => {
      console.log(data)
      document.getElementById('user-name').innerHTML = data.name
    })
}
```

Issues I see:
- No error handling
- Using innerHTML (security risk)
- No loading state

Please refactor following modern best practices.
```

#### 3. Data Structure Examples

```
Transform this data structure:

Input:
```json
{
  "users": [
    {"id": 1, "name": "John", "posts": [1, 2, 3]},
    {"id": 2, "name": "Jane", "posts": [4, 5]}
  ],
  "posts": [
    {"id": 1, "title": "Hello", "userId": 1},
    {"id": 2, "title": "World", "userId": 1}
  ]
}
```

Output (normalized):
```json
{
  "users": {
    "1": {"id": 1, "name": "John", "postIds": [1, 2, 3]},
    "2": {"id": 2, "name": "Jane", "postIds": [4, 5]}
  },
  "posts": {
    "1": {"id": 1, "title": "Hello", "userId": 1},
    "2": {"id": 2, "title": "World", "userId": 1}
  }
}
```

Please write the transformation function.
```

## Best Practices Summary

### Do's ✅

1. **Be Specific** - State exactly what you want
2. **Provide Context** - Share relevant background information
3. **Use Examples** - Show, don't just tell
4. **Break Down Complex Tasks** - Use step-by-step approach
5. **Set Clear Expectations** - Define success criteria

### Don'ts ❌

1. **Don't Be Vague** - "Make it better" doesn't help
2. **Don't Assume Context** - AI doesn't know your project
3. **Don't Skip Examples** - They save time in the long run
4. **Don't Combine Too Much** - Break complex tasks down
5. **Don't Forget Constraints** - State limitations upfront

## Practice Exercises

### Exercise 1: Improve These Prompts

Rewrite these vague prompts to be more effective:

1. "Create a form"
2. "Make this faster"
3. "Add validation"
4. "Fix the bug"

### Exercise 2: Add Context

Take this prompt and add appropriate context:
```
Help me build a user dashboard
```

### Exercise 3: Create a Step-by-Step Plan

Break down this task into steps:
```
Implement a comment system for blog posts
```

## Next Steps

Now that you understand prompt basics, move on to:

👉 **[1.2 Code Review](./02-code-review.md)** - Learn how to request effective code reviews

---

📚 **Back to**: [Lesson 7 Overview](../07-advanced-techniques.md)
