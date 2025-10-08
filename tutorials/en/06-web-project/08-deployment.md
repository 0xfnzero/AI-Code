# 1.8 Deployment

> Deploy your application to production and make it accessible to users

## Learning Objectives

By the end of this module, you will:
- Prepare your application for production
- Deploy to various hosting platforms
- Configure environment variables
- Set up monitoring and logging
- Implement CI/CD pipelines

## Overview

In this final module, we'll deploy our Todo App Pro to production, making it accessible to users worldwide. We'll cover multiple deployment options and best practices.

## 1. Production Preparation

### Pre-Deployment Checklist

**Your Conversation with Claude**:
```
You: What do I need to check before deploying?

Claude:
Here's a comprehensive pre-deployment checklist:
```

**Pre-Deployment Checklist**:
```markdown
## Security
- [ ] Remove all console.log statements
- [ ] Change all default secrets and keys
- [ ] Enable HTTPS/SSL
- [ ] Sanitize user inputs
- [ ] Implement rate limiting
- [ ] Add CSRF protection
- [ ] Set secure HTTP headers

## Performance
- [ ] Minify CSS and JavaScript
- [ ] Compress images
- [ ] Enable gzip compression
- [ ] Set up caching headers
- [ ] Optimize database queries
- [ ] Add CDN for static assets

## Code Quality
- [ ] Run linter (ESLint)
- [ ] Fix all warnings
- [ ] Run tests
- [ ] Check for memory leaks
- [ ] Remove unused dependencies

## Configuration
- [ ] Set up environment variables
- [ ] Configure production database
- [ ] Set up error logging
- [ ] Configure CORS properly
- [ ] Set up backup system

## Documentation
- [ ] Update README
- [ ] Document API endpoints
- [ ] Add deployment instructions
- [ ] Document environment variables
```

### Creating Production Build

**Your Conversation with Claude**:
```
You: Prepare production build scripts

Claude:
I'll create build scripts for production:
```

**package.json** (add scripts):
```json
{
  "scripts": {
    "start": "node server/index.js",
    "dev": "nodemon server/index.js",
    "build": "npm run build:css && npm run build:js",
    "build:css": "cleancss -o client/css/bundle.min.css client/css/*.css",
    "build:js": "terser client/js/*.js -o client/js/bundle.min.js",
    "test": "jest",
    "lint": "eslint server/ client/js/",
    "production": "NODE_ENV=production node server/index.js"
  }
}
```

### Environment Configuration

**Your Conversation with Claude**:
```
You: Set up environment configuration for production

Claude:
I'll create proper environment configuration:
```

**.env.production**:
```
# Production Environment Variables
NODE_ENV=production

# Server
PORT=3000
HOST=0.0.0.0

# Database
DB_PATH=/var/data/tasks.db

# Security
JWT_SECRET=your-super-secret-production-key-change-this
JWT_EXPIRE=7d

# CORS
CLIENT_URL=https://your-app.com

# Logging
LOG_LEVEL=info
LOG_FILE=/var/log/todoapp.log

# Rate Limiting
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX=100
```

### Security Enhancements

**server/middleware/security.js**:
```javascript
const rateLimit = require('express-rate-limit');
const helmet = require('helmet');

/**
 * Security middleware
 */
function setupSecurity(app) {
    // Helmet - Set security headers
    app.use(helmet({
        contentSecurityPolicy: {
            directives: {
                defaultSrc: ["'self'"],
                styleSrc: ["'self'", "'unsafe-inline'"],
                scriptSrc: ["'self'"],
                imgSrc: ["'self'", "data:", "https:"],
            },
        },
        hsts: {
            maxAge: 31536000,
            includeSubDomains: true,
            preload: true
        }
    }));

    // Rate limiting
    const limiter = rateLimit({
        windowMs: parseInt(process.env.RATE_LIMIT_WINDOW || 15) * 60 * 1000,
        max: parseInt(process.env.RATE_LIMIT_MAX || 100),
        message: 'Too many requests from this IP, please try again later.'
    });

    app.use('/api/', limiter);

    // Stricter rate limit for auth endpoints
    const authLimiter = rateLimit({
        windowMs: 15 * 60 * 1000,
        max: 5,
        message: 'Too many login attempts, please try again later.'
    });

    app.use('/api/auth/login', authLimiter);
    app.use('/api/auth/register', authLimiter);
}

module.exports = { setupSecurity };
```

## 2. Deployment Options

### Option 1: Heroku Deployment

**Your Conversation with Claude**:
```
You: Deploy to Heroku

Claude:
I'll guide you through Heroku deployment:
```

**Step 1: Create Procfile**:
```
web: node server/index.js
```

**Step 2: Heroku Configuration**:
```bash
# Install Heroku CLI
# https://devcenter.heroku.com/articles/heroku-cli

# Login to Heroku
heroku login

# Create app
heroku create todoapp-pro

# Set environment variables
heroku config:set NODE_ENV=production
heroku config:set JWT_SECRET=your-secret-key

# Deploy
git push heroku main

# Open app
heroku open

# View logs
heroku logs --tail
```

**package.json** (add engines):
```json
{
  "engines": {
    "node": "18.x",
    "npm": "9.x"
  }
}
```

### Option 2: Railway Deployment

**Your Conversation with Claude**:
```
You: Deploy to Railway

Claude:
Railway offers simple deployment with automatic HTTPS:
```

**railway.json**:
```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "startCommand": "npm start",
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 10
  }
}
```

**Deployment Steps**:
```bash
# Install Railway CLI
npm install -g @railway/cli

# Login
railway login

# Initialize project
railway init

# Link to project
railway link

# Deploy
railway up

# Set environment variables
railway variables set JWT_SECRET=your-secret-key
railway variables set NODE_ENV=production

# Open app
railway open
```

### Option 3: DigitalOcean App Platform

**Your Conversation with Claude**:
```
You: Deploy to DigitalOcean

Claude:
DigitalOcean App Platform provides a PaaS solution:
```

**.do/app.yaml**:
```yaml
name: todoapp-pro
region: nyc
services:
  - name: api
    github:
      repo: yourusername/todoapp-pro
      branch: main
    build_command: npm install
    run_command: npm start
    environment_slug: node-js
    instance_count: 1
    instance_size_slug: basic-xxs
    envs:
      - key: NODE_ENV
        value: production
      - key: JWT_SECRET
        value: ${JWT_SECRET}
        type: SECRET
    health_check:
      http_path: /api/health
    routes:
      - path: /
```

### Option 4: VPS (Ubuntu Server)

**Your Conversation with Claude**:
```
You: Deploy to a VPS server

Claude:
I'll guide you through VPS deployment with PM2:
```

**Deployment Script** (deploy.sh):
```bash
#!/bin/bash

echo "🚀 Deploying Todo App Pro..."

# Update system
sudo apt update
sudo apt upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Install PM2
sudo npm install -g pm2

# Clone repository
cd /var/www
git clone https://github.com/yourusername/todoapp-pro.git
cd todoapp-pro

# Install dependencies
npm install --production

# Create .env file
cat > .env << EOF
NODE_ENV=production
PORT=3000
JWT_SECRET=your-secret-key
EOF

# Start with PM2
pm2 start server/index.js --name todoapp-pro

# Setup PM2 startup
pm2 startup
pm2 save

# Install and configure Nginx
sudo apt install -y nginx

# Create Nginx configuration
sudo cat > /etc/nginx/sites-available/todoapp << EOF
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade \$http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host \$host;
        proxy_cache_bypass \$http_upgrade;
    }
}
EOF

# Enable site
sudo ln -s /etc/nginx/sites-available/todoapp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx

# Install SSL with Let's Encrypt
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com

echo "✅ Deployment complete!"
echo "🌐 Visit: https://your-domain.com"
```

**PM2 Ecosystem File** (ecosystem.config.js):
```javascript
module.exports = {
  apps: [{
    name: 'todoapp-pro',
    script: './server/index.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_date_format: 'YYYY-MM-DD HH:mm:ss Z',
    merge_logs: true,
    autorestart: true,
    max_memory_restart: '1G',
    watch: false
  }]
};
```

## 3. Monitoring and Logging

### Setting Up Logging

**Your Conversation with Claude**:
```
You: Set up proper logging for production

Claude:
I'll create a comprehensive logging system:
```

**server/utils/logger.js**:
```javascript
const winston = require('winston');

const logger = winston.createLogger({
    level: process.env.LOG_LEVEL || 'info',
    format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.errors({ stack: true }),
        winston.format.json()
    ),
    defaultMeta: { service: 'todoapp-pro' },
    transports: [
        // Write all logs to console
        new winston.transports.Console({
            format: winston.format.combine(
                winston.format.colorize(),
                winston.format.simple()
            )
        })
    ]
});

// Add file transport in production
if (process.env.NODE_ENV === 'production') {
    logger.add(new winston.transports.File({
        filename: 'logs/error.log',
        level: 'error'
    }));

    logger.add(new winston.transports.File({
        filename: 'logs/combined.log'
    }));
}

module.exports = logger;
```

### Request Logging Middleware

**server/middleware/logging.js**:
```javascript
const logger = require('../utils/logger');

function requestLogger(req, res, next) {
    const start = Date.now();

    res.on('finish', () => {
        const duration = Date.now() - start;

        logger.info({
            method: req.method,
            path: req.path,
            status: res.statusCode,
            duration: `${duration}ms`,
            ip: req.ip,
            userAgent: req.get('user-agent')
        });
    });

    next();
}

module.exports = { requestLogger };
```

### Error Tracking

**server/middleware/errorHandler.js**:
```javascript
const logger = require('../utils/logger');

function errorHandler(err, req, res, next) {
    // Log error
    logger.error({
        message: err.message,
        stack: err.stack,
        path: req.path,
        method: req.method,
        ip: req.ip
    });

    // Don't leak error details in production
    const isDevelopment = process.env.NODE_ENV === 'development';

    res.status(err.status || 500).json({
        error: {
            message: isDevelopment ? err.message : 'Internal server error',
            status: err.status || 500,
            ...(isDevelopment && { stack: err.stack })
        }
    });
}

module.exports = { errorHandler };
```

## 4. Database Backup

### Automated Backup Script

**Your Conversation with Claude**:
```
You: Create database backup system

Claude:
I'll create an automated backup system:
```

**scripts/backup.js**:
```javascript
const fs = require('fs');
const path = require('path');
const { exec } = require('child_process');

const BACKUP_DIR = process.env.BACKUP_DIR || './backups';
const DB_PATH = process.env.DB_PATH || './database/tasks.db';

/**
 * Create database backup
 */
function createBackup() {
    const timestamp = new Date().toISOString().replace(/:/g, '-');
    const backupFile = path.join(BACKUP_DIR, `backup-${timestamp}.db`);

    // Create backup directory if it doesn't exist
    if (!fs.existsSync(BACKUP_DIR)) {
        fs.mkdirSync(BACKUP_DIR, { recursive: true });
    }

    // Copy database file
    fs.copyFileSync(DB_PATH, backupFile);

    console.log(`✅ Backup created: ${backupFile}`);

    // Delete old backups (keep last 7 days)
    cleanOldBackups();
}

/**
 * Clean old backups
 */
function cleanOldBackups() {
    const files = fs.readdirSync(BACKUP_DIR);
    const sevenDaysAgo = Date.now() - (7 * 24 * 60 * 60 * 1000);

    files.forEach(file => {
        const filePath = path.join(BACKUP_DIR, file);
        const stats = fs.statSync(filePath);

        if (stats.mtimeMs < sevenDaysAgo) {
            fs.unlinkSync(filePath);
            console.log(`🗑️  Deleted old backup: ${file}`);
        }
    });
}

// Run backup
createBackup();
```

**Add to package.json**:
```json
{
  "scripts": {
    "backup": "node scripts/backup.js"
  }
}
```

**Cron job for automatic backups**:
```bash
# Edit crontab
crontab -e

# Add daily backup at 2 AM
0 2 * * * cd /var/www/todoapp-pro && npm run backup
```

## 5. CI/CD Pipeline

### GitHub Actions Workflow

**Your Conversation with Claude**:
```
You: Set up CI/CD with GitHub Actions

Claude:
I'll create a complete CI/CD pipeline:
```

**.github/workflows/deploy.yml**:
```yaml
name: Deploy to Production

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
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

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - uses: actions/checkout@v3

      - name: Deploy to Heroku
        uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: "todoapp-pro"
          heroku_email: ${{secrets.HEROKU_EMAIL}}

      - name: Run database migrations
        run: |
          heroku run npm run migrate --app todoapp-pro

      - name: Health check
        run: |
          sleep 10
          curl https://todoapp-pro.herokuapp.com/api/health
```

## 6. Monitoring Dashboard

### Setting Up Monitoring

**Your Conversation with Claude**:
```
You: Add application monitoring

Claude:
I'll integrate monitoring tools:
```

**Install monitoring tools**:
```bash
npm install @sentry/node express-status-monitor
```

**server/monitoring.js**:
```javascript
const Sentry = require('@sentry/node');
const statusMonitor = require('express-status-monitor');

function setupMonitoring(app) {
    // Sentry for error tracking
    if (process.env.SENTRY_DSN) {
        Sentry.init({
            dsn: process.env.SENTRY_DSN,
            environment: process.env.NODE_ENV,
            tracesSampleRate: 1.0,
        });

        app.use(Sentry.Handlers.requestHandler());
        app.use(Sentry.Handlers.tracingHandler());
    }

    // Status monitor
    app.use(statusMonitor({
        title: 'Todo App Pro Status',
        path: '/status',
        healthChecks: [{
            protocol: 'http',
            host: 'localhost',
            port: process.env.PORT || 3000,
            path: '/api/health'
        }]
    }));

    // Sentry error handler (must be last)
    if (process.env.SENTRY_DSN) {
        app.use(Sentry.Handlers.errorHandler());
    }
}

module.exports = { setupMonitoring };
```

## 7. Post-Deployment

### Verification Steps

**Your Conversation with Claude**:
```
You: What should I check after deployment?

Claude:
Here's a post-deployment checklist:
```

**Post-Deployment Checklist**:
```markdown
## Functionality
- [ ] Test user registration
- [ ] Test user login
- [ ] Test task creation
- [ ] Test task editing
- [ ] Test task deletion
- [ ] Test all filters and sorting
- [ ] Test on mobile devices

## Performance
- [ ] Check page load times
- [ ] Test API response times
- [ ] Verify database performance
- [ ] Check memory usage
- [ ] Monitor CPU usage

## Security
- [ ] Verify HTTPS is working
- [ ] Test authentication flow
- [ ] Check security headers
- [ ] Test rate limiting
- [ ] Verify CORS configuration

## Monitoring
- [ ] Check logs are being written
- [ ] Verify error tracking works
- [ ] Set up uptime monitoring
- [ ] Configure alerts
- [ ] Test backup system

## Documentation
- [ ] Update production URL in docs
- [ ] Document deployment process
- [ ] Create runbook for common issues
- [ ] Set up status page
```

### Setting Up Uptime Monitoring

Use services like:
- **UptimeRobot** (free): https://uptimerobot.com
- **Pingdom**: https://pingdom.com
- **StatusCake**: https://statuscake.com

## Key Takeaways

1. **Preparation** - Thoroughly prepare your app for production
2. **Security** - Implement security best practices
3. **Monitoring** - Set up comprehensive logging and monitoring
4. **Backups** - Automate database backups
5. **CI/CD** - Automate testing and deployment

## Practice Exercise

**Task**: Complete deployment
1. Choose a hosting platform
2. Deploy your application
3. Set up monitoring
4. Configure automated backups
5. Share your live app!

## Congratulations!

You've successfully built and deployed a complete web application! Your Todo App Pro is now live and accessible to users worldwide.

## What's Next?

Continue learning:
- 👉 **[Lesson 7: Advanced Techniques](../07-advanced-techniques.md)** - Master advanced development techniques
- 👉 **[Lesson 9: Version Control](../09-version-control.md)** - Learn advanced Git workflows
- 👉 **[Lesson 10: Team Collaboration](../10-team-collaboration.md)** - Work effectively in teams

## Resources

- [Heroku Documentation](https://devcenter.heroku.com/)
- [Railway Docs](https://docs.railway.app/)
- [DigitalOcean Tutorials](https://www.digitalocean.com/community/tutorials)
- [PM2 Documentation](https://pm2.keymetrics.io/docs/)
- [Let's Encrypt](https://letsencrypt.org/)

---

📚 **Back to**: [Lesson 6 Overview](../06-web-project.md)

**Congratulations on completing Lesson 6!** 🎉
