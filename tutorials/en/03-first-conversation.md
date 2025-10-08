# Lesson 3: First Conversation - Create Your First Project

> 🎯 **Learning Objectives**: Learn how to communicate effectively with Claude Code and complete your first complete project

## Pre-lesson Preparation

- ✅ Have completed Lesson 2's installation and configuration
- ✅ Can successfully start Claude Code
- ✅ Create a new project folder for practice

## 1. Secrets of Effective Communication

### How to Ask Questions Most Effectively?

**❌ Poor Questioning**:
```
Help me write code
```

**✅ Good Questioning**:
```
Help me create a personal homepage that includes: title, self-introduction, contact information, using a blue theme
```

### Golden Rules for Questioning

**1. Be Specific and Clear**
- ❌ "Make a website"
- ✅ "Make a website to showcase my photography work, with image display and work descriptions"

**2. Explain Your Needs**
- ❌ "This doesn't work"
- ✅ "Can you make the font bigger? I think it's too small right now"

**3. Step by Step**
- ❌ Give 10 requirements at once
- ✅ Complete basic functionality first, then optimize gradually

**4. Provide Examples**
```
Help me make a login page that's simple like the Apple website
```

## 2. Hands-on Project: Personal Business Card Webpage

### Project Goals

Create a digital business card webpage that includes:
- Name and title
- Personal introduction
- Contact information (email, phone)
- Social media links
- Simple and beautiful design

### Step 1: Create Project Directory

```bash
# Create project folder
mkdir my-card
cd my-card

# Start Claude Code
claude
```

### Step 2: Initial Conversation

```
You: Hello! I want to create a personal digital business card webpage.

Claude: Hello! I'd be happy to help you create a personal digital business card webpage. Please tell me:
1. What is your name and title?
2. What information would you like to include?
3. What style do you prefer (modern/classic/minimalist, etc.)?

You: My name is Zhang San, I'm a designer. Please use a simple and modern style,
including my email zhangsan@email.com and WeChat QR code.

Claude: Okay, I'll create a personal business card webpage for you now...
```

### Step 3: Refine Details

After seeing Claude's initial version, you can continue optimizing:

```
You: Can you change the background to a gradient? From blue to purple

Claude: Okay, I'll modify the CSS to add a gradient background...

You: Can you make the name bigger and more prominent?

Claude: I've adjusted the title's font size and weight...

You: Great! Can you add a space for an avatar? The round kind

Claude: I've added a circular avatar container, you can replace it with your own photo...
```

## 3. Understanding Claude Code's Workflow

### What Does Claude Code Do?

When you say "create a personal business card webpage", Claude Code will:

**1. Understand Requirements**
- Analyze what you want
- Determine which files are needed

**2. Create Files**
```
📁 Creating index.html - webpage structure
📁 Creating style.css - style design
📁 Creating script.js - interactive functionality (if needed)
```

**3. Write Code**
- Automatically write HTML structure
- Design CSS styles
- Add necessary functionality

**4. Tell You the Results**
```
✅ Created index.html
✅ Created style.css
💡 You can open index.html in your browser to view the result
```

### View Created Files

**Method 1: Use Code Editor**
```bash
# If you have VS Code installed
code .
```

**Method 2: Preview in Browser**
- Find the project folder
- Double-click `index.html`
- Browser will open automatically

**Method 3: View Code Content**
```bash
# Mac/Linux
cat index.html

# Windows
type index.html
```

## 4. Common Interaction Scenarios

### Scenario 1: Modify Content

```
You: Change "Designer" to "UI/UX Designer"

Claude: Title has been updated...
```

### Scenario 2: Adjust Styles

```
You: The background is too bright, can you make it darker?

Claude: Background brightness has been reduced...
```

### Scenario 3: Add Functionality

```
You: When clicking the email, can it directly open the email client?

Claude: I've added a mailto link...
```

### Scenario 4: Explain Code

```
You: What does this CSS code do?

Claude: This code creates a flex layout container,
centers the content on the page, and sets padding and margins...
```

### Scenario 5: Fix Issues

```
You: Why isn't my avatar showing up?

Claude: Let me check... It's because the image path is incorrect,
you should put the image in the project folder and make sure the filename matches...
```

## 5. Complete Conversation Example

### Creating a Project from Scratch

```
🟢 You: Help me create a personal portfolio webpage

🤖 Claude: Sure! To better meet your needs, please tell me:
1. What field are you a creator in? (design/photography/development, etc.)
2. About how many works do you want to showcase?
3. What information should be displayed for each work?

🟢 You: I'm a photographer, want to showcase 6 photos,
each photo should have a title, shooting location, and brief description

🤖 Claude: Got it! I'll create a photography portfolio webpage for you.
[Starting to create files...]
✅ Created index.html
✅ Created style.css
✅ Created images folder
💡 Please put your photos in the images folder

🟢 You: Can you arrange the photos in two columns?

🤖 Claude: I've adjusted it to a two-column grid layout...

🟢 You: Can there be a zoom effect on mouse hover?

🤖 Claude: I've added a hover zoom animation effect...

🟢 You: Perfect! Help me generate a README file explaining how to use it

🤖 Claude: I've created the README.md file with usage instructions...
```

## 6. File Operation Tips

### Let Claude Code Read Files

```
You: Look at the content of index.html

Claude: [Shows file content]
This file contains...
```

### Let Claude Code Modify Specific Parts

```
You: Change the color on line 20 from red to blue

Claude: I've changed line 20's color: red to color: blue
```

### Create New Files

```
You: Create an about.html file to introduce my background

Claude: Okay, I'll create about.html...
```

### Delete Files

```
You: Delete test.js, I don't need it anymore

Claude: Deleted test.js
```

## 7. Advanced Tips

### Use Code Blocks to Provide Examples

````
You: Help me create a function like this:
```javascript
function greet(name) {
    return "Hello, " + name;
}
```
But using arrow function syntax

Claude: Okay, I'll convert it to an arrow function...
````

### Batch Modifications

```
You: Change all fonts from Arial to 'Helvetica Neue'

Claude: I'll search all CSS files and update the font settings...
```

### Add Comments

```
You: Add comments to each function explaining what they do

Claude: I'll add detailed comments to the code...
```

## 8. Practical Conversation Templates

### Create Project
```
Help me create a [project type] that includes [feature 1], [feature 2], [feature 3],
implemented using [tech stack], with a [design style] style
```

### Modify Styles
```
Change [element]'s [property] from [old value] to [new value]
```

### Add Functionality
```
Add a feature: when the user [action], display [effect]
```

### Debug Issues
```
[Some feature] isn't working, the error message is [error content], can you help me look at it?
```

### Optimize Code
```
Can you optimize this code? I think [specific problem]
```

## 9. Common Errors and Solutions

### Error 1: Unclear Description

**Problem**:
```
You: Make a good-looking website
Claude: Need more information...
```

**Solution**:
```
You: Make a coffee shop website, homepage should have shop photos, menu preview, and business hours
Claude: Okay, I'll start creating...
```

### Error 2: Unrealistic Expectations

**Problem**:
```
You: Make a complete e-commerce website like Taobao
```

**Solution**:
```
You: Make a simple product display page with 3-5 products, each showing image, name, and price
```

### Error 3: Not Verifying Results

**Problem**:
- Ask Claude to modify code
- Don't check the result
- Continue with new requests
- Result gets more and more messy

**Solution**:
```
1. Make a modification request
2. Wait for Claude to complete
3. Refresh browser to view result
4. Confirm before making next request
```

## 10. Project Completion Checklist

After completing a project, check:

- [ ] All files have been created
- [ ] Can open normally in browser
- [ ] All features work properly
- [ ] Styles meet expectations
- [ ] No error messages
- [ ] Code has appropriate comments

## 11. Save and Share

### Save Project

All your files are in the project folder:
```
my-card/
├── index.html
├── style.css
├── script.js
└── images/
```

### Backup Project

```bash
# Compress entire folder
# Mac/Linux
zip -r my-card.zip my-card/

# Windows (PowerShell)
Compress-Archive -Path my-card -DestinationPath my-card.zip
```

### Publish Online

You can use free services:
- **GitHub Pages** - Suitable for static websites
- **Netlify** - Drag and drop to deploy
- **Vercel** - Supports more frameworks

(Specific deployment methods will be covered in later lessons)

## 12. Practice Tasks

### Task 1: To-Do List (Simple)

Create a to-do list webpage:
- Input box to add tasks
- Display task list
- Click to delete tasks

### Task 2: Personal Blog Page (Medium)

Create a blog article page:
- Article title and date
- Article content (use placeholder text)
- Comment section
- Return to homepage button

### Task 3: Product Display Page (Challenging)

Create a product display page:
- 4-6 product cards
- Each product has image, name, price, description
- Hover effects
- Responsive design (works on mobile and desktop)

## 13. Next Steps

🎉 Congratulations on completing your first complete project!

Now you have mastered:
- ✅ How to communicate effectively with Claude Code
- ✅ How to describe requirements
- ✅ How to gradually refine a project
- ✅ Basic file operations

**Ready to learn more skills?**

👉 Next Lesson: [Basic Commands and Operations](./04-basic-commands.md)

There, we will learn:
1. Claude Code's built-in commands
2. Shortcut operation tips
3. Project management methods
4. Efficient workflows

---

💡 **Tips**:
- Practice more, try more
- Don't be afraid of making mistakes, you can always ask Claude to undo changes
- Save projects you think are good for future reference
- When talking with Claude, treat it like a patient teacher
