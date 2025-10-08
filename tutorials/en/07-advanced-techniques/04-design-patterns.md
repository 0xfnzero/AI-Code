# 1.4 Design Patterns

> Apply proven design patterns to build maintainable, scalable code

## Overview

Design patterns are reusable solutions to common programming problems. This module covers essential patterns and when to use them.

## 1. Singleton Pattern

### When to Use
- Global configuration management
- Database connections
- Logging services
- Cache managers

### Implementation

```javascript
class Config {
  static instance = null

  constructor() {
    if (Config.instance) {
      return Config.instance
    }

    this.settings = {
      apiUrl: 'https://api.example.com',
      theme: 'light',
      language: 'en-US'
    }

    Config.instance = this
  }

  get(key) {
    return this.settings[key]
  }

  set(key, value) {
    this.settings[key] = value
  }
}

// Usage
const config1 = new Config()
const config2 = new Config()
console.log(config1 === config2) // true - same instance
```

### Simpler Alternative (ES6 Module)

```javascript
// config.js
const settings = {
  apiUrl: 'https://api.example.com',
  theme: 'light'
}

export default {
  get(key) {
    return settings[key]
  },

  set(key, value) {
    settings[key] = value
  }
}

// Usage
import config from './config.js'
config.set('theme', 'dark')
```

## 2. Observer Pattern (Pub/Sub)

### When to Use
- Event systems
- State management
- Real-time notifications
- Component communication

### Implementation

```javascript
class EventEmitter {
  constructor() {
    this.events = {}
  }

  // Subscribe to event
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = []
    }
    this.events[event].push(callback)

    // Return unsubscribe function
    return () => this.off(event, callback)
  }

  // Unsubscribe
  off(event, callback) {
    if (!this.events[event]) return

    this.events[event] = this.events[event].filter(cb => cb !== callback)
  }

  // Trigger event
  emit(event, data) {
    if (!this.events[event]) return

    this.events[event].forEach(callback => callback(data))
  }

  // Subscribe once
  once(event, callback) {
    const onceCallback = (data) => {
      callback(data)
      this.off(event, onceCallback)
    }
    this.on(event, onceCallback)
  }
}

// Usage
const emitter = new EventEmitter()

// Subscribe to user login event
const unsubscribe = emitter.on('user:login', (user) => {
  console.log('User logged in:', user.name)
  updateUI(user)
})

// Another subscriber
emitter.on('user:login', (user) => {
  logActivity('login', user.id)
})

// Trigger event
emitter.emit('user:login', { id: 123, name: 'John' })

// Unsubscribe when needed
unsubscribe()
```

## 3. Factory Pattern

### When to Use
- Creating objects with complex initialization
- Deciding which class to instantiate at runtime
- Hiding creation logic

### Implementation

```javascript
// Base class
class FormField {
  constructor(config) {
    this.name = config.name
    this.label = config.label
    this.required = config.required || false
    this.value = config.value || ''
  }

  validate() {
    if (this.required && !this.value) {
      return `${this.label} is required`
    }
    return null
  }
}

// Specific field types
class TextField extends FormField {
  render() {
    return `
      <div class="form-field">
        <label>${this.label}</label>
        <input type="text" name="${this.name}"
               value="${this.value}"
               ${this.required ? 'required' : ''}>
      </div>
    `
  }
}

class SelectField extends FormField {
  constructor(config) {
    super(config)
    this.options = config.options || []
  }

  render() {
    const options = this.options
      .map(opt => `<option value="${opt.value}">${opt.label}</option>`)
      .join('')

    return `
      <div class="form-field">
        <label>${this.label}</label>
        <select name="${this.name}" ${this.required ? 'required' : ''}>
          ${options}
        </select>
      </div>
    `
  }
}

class DateField extends FormField {
  render() {
    return `
      <div class="form-field">
        <label>${this.label}</label>
        <input type="date" name="${this.name}"
               value="${this.value}"
               ${this.required ? 'required' : ''}>
      </div>
    `
  }
}

// Factory
class FormFieldFactory {
  static createField(type, config) {
    switch (type) {
      case 'text':
        return new TextField(config)
      case 'select':
        return new SelectField(config)
      case 'date':
        return new DateField(config)
      default:
        throw new Error(`Unknown field type: ${type}`)
    }
  }
}

// Usage
const nameField = FormFieldFactory.createField('text', {
  name: 'username',
  label: 'Username',
  required: true
})

const cityField = FormFieldFactory.createField('select', {
  name: 'city',
  label: 'City',
  options: [
    { value: 'ny', label: 'New York' },
    { value: 'la', label: 'Los Angeles' }
  ]
})

console.log(nameField.render())
console.log(cityField.render())
```

## 4. Module Pattern

### When to Use
- Encapsulation and privacy
- Organizing related functionality
- Preventing global namespace pollution

### Implementation

```javascript
const ShoppingCart = (() => {
  // Private variables
  let items = []
  let total = 0

  // Private methods
  function calculateTotal() {
    total = items.reduce((sum, item) =>
      sum + (item.price * item.quantity), 0
    )
  }

  // Public API
  return {
    addItem(product, quantity) {
      const existingItem = items.find(item => item.id === product.id)

      if (existingItem) {
        existingItem.quantity += quantity
      } else {
        items.push({ ...product, quantity })
      }

      calculateTotal()
    },

    removeItem(productId) {
      items = items.filter(item => item.id !== productId)
      calculateTotal()
    },

    getItems() {
      return [...items] // Return copy
    },

    getTotal() {
      return total
    },

    clear() {
      items = []
      total = 0
    }
  }
})()

// Usage
ShoppingCart.addItem({ id: 1, name: 'Book', price: 20 }, 2)
ShoppingCart.addItem({ id: 2, name: 'Pen', price: 5 }, 5)
console.log(ShoppingCart.getTotal()) // 65
console.log(ShoppingCart.getItems())
// Cannot access private 'items' or 'calculateTotal'
```

## 5. Strategy Pattern

### When to Use
- Multiple algorithms for same task
- Switching behavior at runtime
- Avoiding complex conditionals

### Implementation

```javascript
// Payment strategies
class CreditCardPayment {
  pay(amount) {
    console.log(`Processing credit card payment: $${amount}`)
    // Credit card processing logic
    return { success: true, transactionId: 'CC_' + Date.now() }
  }
}

class PayPalPayment {
  pay(amount) {
    console.log(`Processing PayPal payment: $${amount}`)
    // PayPal API logic
    return { success: true, transactionId: 'PP_' + Date.now() }
  }
}

class CryptoPayment {
  pay(amount) {
    console.log(`Processing crypto payment: $${amount}`)
    // Cryptocurrency logic
    return { success: true, transactionId: 'BTC_' + Date.now() }
  }
}

// Context
class PaymentProcessor {
  constructor(strategy) {
    this.strategy = strategy
  }

  setStrategy(strategy) {
    this.strategy = strategy
  }

  processPayment(amount) {
    return this.strategy.pay(amount)
  }
}

// Usage
const processor = new PaymentProcessor(new CreditCardPayment())
processor.processPayment(100)

// Switch strategy
processor.setStrategy(new PayPalPayment())
processor.processPayment(50)

// Or choose based on user input
function getPaymentStrategy(method) {
  const strategies = {
    'credit-card': new CreditCardPayment(),
    'paypal': new PayPalPayment(),
    'crypto': new CryptoPayment()
  }
  return strategies[method]
}

const strategy = getPaymentStrategy('paypal')
const processor2 = new PaymentProcessor(strategy)
processor2.processPayment(75)
```

## 6. Decorator Pattern

### When to Use
- Adding functionality to objects dynamically
- Enhancing classes without modifying them
- Composing behavior

### Implementation

```javascript
// Base component
class Coffee {
  cost() {
    return 5
  }

  description() {
    return 'Coffee'
  }
}

// Decorators
class MilkDecorator {
  constructor(coffee) {
    this.coffee = coffee
  }

  cost() {
    return this.coffee.cost() + 1
  }

  description() {
    return this.coffee.description() + ', Milk'
  }
}

class SugarDecorator {
  constructor(coffee) {
    this.coffee = coffee
  }

  cost() {
    return this.coffee.cost() + 0.5
  }

  description() {
    return this.coffee.description() + ', Sugar'
  }
}

class WhipDecorator {
  constructor(coffee) {
    this.coffee = coffee
  }

  cost() {
    return this.coffee.cost() + 2
  }

  description() {
    return this.coffee.description() + ', Whipped Cream'
  }
}

// Usage
let myCoffee = new Coffee()
console.log(myCoffee.description(), '$' + myCoffee.cost())
// "Coffee $5"

myCoffee = new MilkDecorator(myCoffee)
console.log(myCoffee.description(), '$' + myCoffee.cost())
// "Coffee, Milk $6"

myCoffee = new SugarDecorator(myCoffee)
myCoffee = new WhipDecorator(myCoffee)
console.log(myCoffee.description(), '$' + myCoffee.cost())
// "Coffee, Milk, Sugar, Whipped Cream $8.5"
```

## Pattern Selection Guide

```
Need global state? → Singleton
Need event system? → Observer
Need flexible object creation? → Factory
Need to hide implementation? → Module
Need swappable algorithms? → Strategy
Need to add features dynamically? → Decorator
```

## Best Practices

### Do's ✅

1. **Understand the problem first** - Don't force patterns
2. **Use patterns to solve problems** - Not to show off
3. **Keep it simple** - Don't over-engineer
4. **Document pattern usage** - Help future developers
5. **Consider alternatives** - Sometimes simple is better

### Don'ts ❌

1. **Don't use patterns everywhere** - They add complexity
2. **Don't memorize implementations** - Understand concepts
3. **Don't mix too many patterns** - Keep it maintainable
4. **Don't ignore simpler solutions** - Patterns aren't always needed

## Practice Exercise

Choose and implement the appropriate pattern:

1. **Task**: Create a notification system that can send alerts via email, SMS, and push notifications
   - Which pattern?
   - Why?
   - Implement it

2. **Task**: Build a logger that can write to console, file, or remote server
   - Which pattern?
   - Why?
   - Implement it

## Next Steps

Learn robust error handling techniques:

👉 **[1.5 Error Handling](./05-error-handling.md)** - Implement comprehensive error handling

---

📚 **Back to**: [Lesson 7 Overview](../07-advanced-techniques.md)
