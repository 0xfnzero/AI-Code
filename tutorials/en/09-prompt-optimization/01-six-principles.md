# 1.1 Six Golden Principles

> Master the foundational principles of effective prompt writing

## Overview

These six principles form the foundation of excellent prompt writing. Master them to achieve 10x efficiency improvement.

## Principle 1: Specific and Clear

### The Problem
Vague prompts lead to generic, unhelpful responses and multiple clarification rounds.

### The Solution
Be precise about what you want, how you want it, and what constraints apply.

### Formula
```
[Action] + [Object] + [Specific Requirements] + [Constraints] + [Expected Results]
```

### Example

**❌ Vague**:
```
Make a website
```

**✅ Specific**:
```
Create an e-commerce product display page with:
- Card layout, 3 products per row (desktop), 2 (tablet), 1 (mobile)
- Each card: 16:9 image, title, price, 5-star rating, "Add to Cart" button
- Hover effect: slight lift with shadow
- Click behavior: navigate to product detail page
- Color scheme: #3498db (primary), white background
- Tech stack: HTML5, CSS3 (Flexbox/Grid), vanilla JavaScript
- Browser support: Modern browsers (Chrome, Firefox, Safari, Edge)
```

## Principle 2: Structured

### The Power of Structure
Well-structured prompts help AI quickly understand priorities and relationships.

### Template
```markdown
## Background
[Current situation and problem]

## Objective
[What you want to achieve]

## Requirements
1. [Functional requirements]
2. [Technical requirements]
3. [Performance requirements]

## Constraints
- [Limitations and restrictions]

## Expected Outcome
[What success looks like]
```

### Example
```markdown
## Background
Our login system uses sessions, but we need to support mobile apps and multiple device logins.

## Objective
Implement JWT token-based authentication

## Requirements
1. Dual token system (access + refresh)
2. Access token: 15 min lifetime
3. Refresh token: 7 days lifetime
4. Secure token storage and transmission
5. Token blacklist for logout

## Constraints
- Cannot break existing web app
- Must support gradual migration
- Use existing Express.js backend
- PostgreSQL database

## Expected Outcome
Secure, scalable authentication system supporting web and mobile clients
```

## Principle 3: Context-Rich

### Why Context Matters
Context helps AI make informed decisions about trade-offs, priorities, and appropriate solutions.

### Context Checklist
```markdown
✅ Project Context
- Type and scale
- Current stage (MVP/beta/production)
- User demographics

✅ Technical Context
- Tech stack and versions
- Architecture patterns
- Existing constraints

✅ Team Context
- Size and skill level
- Timeline and resources
- Development practices

✅ Business Context
- Critical requirements
- Performance targets
- Budget considerations
```

## Principle 4: Example-Driven

### The Power of Examples
One good example eliminates ambiguity better than paragraphs of description.

### Types of Examples

**1. Code Examples**
```
Current implementation:
[paste problematic code]

Issues:
- No error handling
- Performance problems

Expected style:
- async/await
- Comprehensive error handling
- Clear variable names
```

**2. UI References**
```
Create a modal similar to Stripe's checkout:
- Centered overlay
- Smooth fade-in animation
- Click outside to close
- Reference: https://stripe.com/checkout
```

**3. Data Examples**
```
Transform this input:
{"users": [{"id": 1, "name": "John"}]}

To this output:
{"1": {"id": 1, "name": "John"}}
```

## Principle 5: Iterative

### The Approach
Complete complex tasks in phases rather than all at once.

### Iteration Flow
```
Phase 1: MVP (Core functionality)
  ↓ Validate
Phase 2: Basic Features
  ↓ Validate
Phase 3: Advanced Features
  ↓ Validate
Phase 4: Optimization
  ↓ Validate
Phase 5: Polish
```

### Example
```
Phase 1: Create basic login form (username, password, submit)
[wait for completion and validate]

Phase 2: Add form validation (client-side)
[wait and validate]

Phase 3: Add "Remember Me" functionality
[wait and validate]

Phase 4: Add OAuth (Google, GitHub)
[wait and validate]

Phase 5: Polish UI and animations
```

## Principle 6: Feedback-Driven

### Types of Feedback

**Positive** (Reinforce good direction):
```
✅ Perfect! This is exactly what I need
✅ Great approach, continue with this pattern
✅ Excellent solution, very elegant
```

**Constructive** (Guide improvements):
```
⚠️ Good direction, but can we make it more concise?
⚠️ Works well, but performance might be an issue with large datasets
⚠️ Correct implementation, but doesn't match our coding standards
```

**Corrective** (Change direction):
```
❌ Not what I meant, let me clarify...
❌ This is too complex, I need a simpler solution
❌ Wrong approach, should use [alternative] instead
```

## Quick Reference

### The Six Principles
1. **Specific** - Be precise and detailed
2. **Structured** - Organize information clearly
3. **Context-Rich** - Provide background and constraints
4. **Example-Driven** - Show, don't just tell
5. **Iterative** - Build in phases
6. **Feedback-Driven** - Guide and refine

### Application Checklist
- [ ] Is my request specific enough?
- [ ] Have I structured the information?
- [ ] Did I provide necessary context?
- [ ] Can I show an example?
- [ ] Should I break this into steps?
- [ ] Am I giving clear feedback?

## Next Steps

Learn advanced prompting techniques:

👉 **[1.2 Advanced Techniques](./02-advanced-techniques.md)**

---

📚 **Back to**: [Lesson 9 Overview](../09-prompt-optimization.md)
