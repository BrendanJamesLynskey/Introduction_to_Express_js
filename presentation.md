# Introduction to Express.js — Presentation Notes

---

## Slide 01 — Title

**Introduction to Express.js**

Fast, Unopinionated, Minimalist Web Framework for Node.js

19 slides · routing, middleware, error handling, security, production · 2026

---

## Slide 02 — Agenda

### Foundations
- What Is Express.js?
- Hello World & App Structure
- Routing — Methods, Paths, Parameters
- Route Organisation
- Request & Response Objects

### Middleware & Built-Ins
- Middleware Fundamentals
- Built-in Middleware
- Third-Party Middleware
- Error Handling

### Integration & Configuration
- Template Engine Integration
- Static File Serving
- Environment & Configuration
- Security Best Practices

### Production
- Project Structure & Patterns
- Express 5 — What's New
- Complete Working Example
- Summary & Next Steps

---

## Slide 03 — What Is Express.js?

### History & Philosophy

Express was created by **TJ Holowaychuk** in 2010, inspired by Ruby's **Sinatra**. It is the de facto standard web framework for Node.js — minimalist by design, providing a thin layer of features without obscuring Node's own APIs.

### Key Characteristics

- **Minimalist core** — routing, middleware, and HTTP utilities only
- **Unopinionated** — no prescribed project structure or ORM
- **Middleware-driven** — everything is a function in the request pipeline
- **~35M weekly npm downloads** — most popular Node.js framework
- **Massive ecosystem** — thousands of middleware packages available

### Install & Quick Start

```bash
mkdir my-app && cd my-app
npm init -y
npm install express
```

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello Express!');
});

app.listen(3000, () => {
  console.log('Server running on :3000');
});
```

### Built On Node's http Module

Express wraps Node's native `http.createServer()`, adding routing, middleware chaining, and a cleaner API — but you can still access the raw `req` and `res` objects underneath.

---

## Slide 04 — Hello World & App Structure

### Creating the App

```javascript
const express = require('express');
const app = express();

// app is both a function and an object
// — it holds settings, routes, and acts as the request handler
```

### Defining Routes

```javascript
app.get('/', (req, res) => {
  res.send('Homepage');
});

app.get('/about', (req, res) => {
  res.send('About page');
});

app.post('/contact', (req, res) => {
  res.json({ received: true });
});
```

### Starting the Server

```javascript
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});

// Returns an http.Server instance — you can use it for WebSockets, etc.
```

### Request Lifecycle

Incoming Request → Middleware Stack → Route Handler → Response Sent

### app.set() & app.get()

```javascript
app.set('view engine', 'ejs');
app.set('port', 3000);

app.get('view engine'); // 'ejs'
app.get('env');         // 'development'

// Note: app.get(path, handler) is also the route method
// — Express overloads based on argument count
```

---

## Slide 05 — Routing — Methods, Paths, Parameters

### HTTP Methods

```javascript
app.get('/users',    handler); // Read
app.post('/users',   handler); // Create
app.put('/users/1',  handler); // Replace
app.patch('/users/1',handler); // Update
app.delete('/users/1',handler);// Delete

app.all('/secret', requireAuth, handler); // All methods
```

### Route Parameters

```javascript
app.get('/users/:id', (req, res) => {
  console.log(req.params.id); // '42'
  res.send(`User ${req.params.id}`);
});

// Multiple params
app.get('/users/:userId/posts/:postId', (req, res) => {
  // req.params = { userId, postId }
});
```

### Query Strings

```javascript
// GET /search?q=express&page=2
app.get('/search', (req, res) => {
  req.query.q;    // 'express'
  req.query.page; // '2' (always string)
});
```

### Pattern Matching

```javascript
app.get(/.*fly$/, handler); // regex routes
app.get('/users/:id?', handler); // optional param

app.route('/books')
  .get((req, res) => { /* list */ })
  .post((req, res) => { /* create */ });
```

### Multiple Handlers

```javascript
app.get('/dashboard',
  authenticate,
  authorise('admin'),
  (req, res) => {
    res.render('dashboard');
  }
);
```

---

## Slide 06 — Route Organisation

### express.Router()

```javascript
// routes/users.js
const router = require('express').Router();

router.get('/', (req, res) => {
  res.json(users);
});

router.get('/:id', (req, res) => {
  res.json(users[req.params.id]);
});

router.post('/', (req, res) => {
  // create user
});

module.exports = router;
```

### Mounting Routers

```javascript
// app.js
const usersRouter = require('./routes/users');
const postsRouter = require('./routes/posts');

app.use('/api/users', usersRouter);
app.use('/api/posts', postsRouter);

// GET /api/users     → router.get('/')
// GET /api/users/42  → router.get('/:id')
```

### Router-Level Middleware

```javascript
const adminRouter = express.Router();

adminRouter.use(requireAuth);
adminRouter.use(requireAdmin);

adminRouter.get('/dashboard', handler);
adminRouter.get('/settings', handler);

app.use('/admin', adminRouter);
```

### Modular Route Files

```
routes/
├── index.js      ← loads all routers
├── auth.js       ← /auth/*
├── users.js      ← /api/users/*
├── posts.js      ← /api/posts/*
└── admin.js      ← /admin/*
```

### router.param()

```javascript
router.param('id', async (req, res, next, id) => {
  req.user = await User.findById(id);
  if (!req.user) return res.status(404).json({ error: 'Not found' });
  next();
});
```

---

## Slide 07 — Request & Response Objects

### req — Key Properties

| Property | Description |
|----------|-------------|
| `req.params` | Route parameters (`:id`) |
| `req.query` | Query string (`?q=x`) |
| `req.body` | Parsed body (needs middleware) |
| `req.headers` | Request headers object |
| `req.method` | GET, POST, PUT, etc. |
| `req.path` | URL path without query |
| `req.cookies` | Cookies (needs cookie-parser) |
| `req.ip` | Client IP address |

### req.body Example

```javascript
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.post('/api/users', (req, res) => {
  console.log(req.body.name);
  console.log(req.body.email);
});
```

### res — Key Methods

| Method | Purpose |
|--------|---------|
| `res.send()` | Send string/Buffer/object |
| `res.json()` | Send JSON response |
| `res.render()` | Render a view template |
| `res.redirect()` | Redirect to another URL |
| `res.status()` | Set HTTP status code |
| `res.sendFile()` | Send a file as response |
| `res.set()` | Set response headers |
| `res.cookie()` | Set a cookie |

### Chaining & Common Patterns

```javascript
res.status(201).json({ id: newUser.id });
res.status(404).render('404', { title: 'Not Found' });
res.redirect(301, '/new-url');
res.set('X-Request-Id', uuid()).json(data);
```

---

## Slide 08 — Middleware Fundamentals

### The Middleware Signature

```javascript
const logger = (req, res, next) => {
  console.log(`${req.method} ${req.path}`);
  next(); // pass to next middleware
};

app.use(logger);
```

If you don't call `next()`, the request **hangs** — no response is ever sent.

### Order Matters

```javascript
app.use(express.json());        // 1st
app.use(cors());                // 2nd
app.use(helmet());              // 3rd
app.use('/api', authMiddleware);// 4th
app.get('/api/data', handler);  // 5th
app.use(errorHandler);          // 6th — LAST
```

### Types of Middleware

| Type | Scope |
|------|-------|
| Application-level | `app.use(fn)` — runs on every request |
| Router-level | `router.use(fn)` — scoped to router |
| Route-level | Inline: `app.get(path, fn, handler)` |
| Error-handling | `(err, req, res, next)` — 4 args |
| Built-in | `express.json()`, `express.static()` |
| Third-party | `cors()`, `helmet()`, `morgan()` |

### Middleware Pipeline

`json()` → `cors()` → `auth()` → `route handler`

Each middleware can modify `req`/`res`, end the cycle, or call `next()` to continue.

---

## Slide 09 — Built-in Middleware

### express.json()

```javascript
app.use(express.json());

app.use(express.json({
  limit: '10kb',
  strict: true,
  type: 'application/json'
}));
```

### express.urlencoded()

```javascript
app.use(express.urlencoded({
  extended: true
}));
```

### express.static()

```javascript
const path = require('path');

app.use(express.static(path.join(__dirname, 'public')));

// With virtual path prefix
app.use('/assets', express.static(path.join(__dirname, 'public')));

// Options
app.use(express.static('public', {
  maxAge: '1d',
  index: 'index.html',
  dotfiles: 'ignore'
}));
```

### express.raw() & express.text()

```javascript
app.use('/webhook', express.raw({ type: 'application/octet-stream' }));
app.use('/logs', express.text({ type: 'text/plain' }));
```

Less common but useful for webhooks (Stripe, GitHub) and log ingestion endpoints.

---

## Slide 10 — Third-Party Middleware

### Essential Packages

| Package | Purpose |
|---------|---------|
| `cors` | Cross-Origin Resource Sharing headers |
| `helmet` | Security headers (15+ protections) |
| `morgan` | HTTP request logging |
| `compression` | Gzip/Brotli response compression |
| `express-rate-limit` | Rate limiting per IP/key |
| `cookie-parser` | Parse cookies into req.cookies |
| `express-session` | Server-side session management |
| `multer` | Multipart/file upload handling |

### Setup Example

```javascript
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');
const compression = require('compression');
const rateLimit = require('express-rate-limit');

app.use(helmet());
app.use(cors({ origin: 'https://mysite.com' }));
app.use(compression());
app.use(morgan('combined'));

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});
app.use('/api', limiter);
```

**Tip:** Security first (helmet, cors), then parsing (json, urlencoded), then logging (morgan), then routes, then error handlers last.

---

## Slide 11 — Error Handling

### Error Middleware Signature

```javascript
// MUST have exactly 4 parameters
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({
    error: err.message
  });
});

// Trigger with next(err)
app.get('/fail', (req, res, next) => {
  const err = new Error('Something broke');
  err.status = 500;
  next(err);
});
```

### Custom Error Classes

```javascript
class AppError extends Error {
  constructor(message, status = 500) {
    super(message);
    this.status = status;
    this.isOperational = true;
  }
}

class NotFoundError extends AppError {
  constructor(resource = 'Resource') {
    super(`${resource} not found`, 404);
  }
}
```

### Async Error Catching

```javascript
// Express 4: wrap async handlers
const asyncHandler = (fn) =>
  (req, res, next) =>
    Promise.resolve(fn(req, res, next)).catch(next);

app.get('/users', asyncHandler(async (req, res) => {
  const users = await User.find();
  res.json(users);
}));

// Express 5: automatic — no wrapper needed!
```

### 404 Handler

```javascript
app.use((req, res, next) => {
  res.status(404).json({
    error: `Cannot ${req.method} ${req.path}`
  });
});
```

**Tip:** Use operational errors (bad input, not found) for client-facing messages and programmer errors (bugs) for logging + generic 500. Never leak stack traces in production.

---

## Slide 12 — Template Engine Integration

### Setting Up a View Engine

```javascript
const path = require('path');

app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

res.render('home'); // views/home.ejs
```

### res.render() in Action

```javascript
app.get('/', (req, res) => {
  res.render('home', {
    title: 'Welcome',
    user: req.user,
    items: [1, 2, 3]
  });
});
```

### app.locals & res.locals

```javascript
// Global — available in every render
app.locals.siteName = 'My App';

// Per-request — set in middleware
app.use((req, res, next) => {
  res.locals.currentUser = req.user;
  next();
});
```

### Popular Engines

| Engine | Syntax | Install |
|--------|--------|---------|
| EJS | `<%= %>` JS in HTML | `npm i ejs` |
| Pug | Indentation-based | `npm i pug` |
| Handlebars | `{{ }}` Mustache | `npm i hbs` |
| Nunjucks | `{% %}` Jinja2 | `npm i nunjucks` |

### Using Multiple Engines

```javascript
const ejs = require('ejs');
const pug = require('pug');

app.engine('ejs', ejs.renderFile);
app.engine('pug', pug.renderFile);

res.render('email.ejs', data);
res.render('page.pug', data);
```

---

## Slide 13 — Static File Serving

### express.static() Basics

```javascript
const path = require('path');

app.use(express.static(path.join(__dirname, 'public')));

// public/css/style.css  → /css/style.css
// public/js/app.js      → /js/app.js
// public/index.html      → / (auto-served)
```

### Virtual Path Prefix

```javascript
app.use('/static', express.static(path.join(__dirname, 'public')));
// public/css/style.css → /static/css/style.css

// Multiple static directories
app.use(express.static('public'));
app.use(express.static('uploads'));
// Express searches in order declared
```

### Caching & Performance Headers

```javascript
app.use(express.static('public', {
  maxAge: '30d',
  etag: true,
  lastModified: true,
  immutable: true,
  index: false,
  dotfiles: 'ignore',
  redirect: false,
  fallthrough: true
}));
```

### Production Tips

- Use a **reverse proxy** (Nginx, Cloudflare) for static files in production
- Set long `maxAge` for versioned/hashed assets
- Use `compression` middleware for dynamic responses
- Serve static before session/auth middleware to avoid unnecessary processing
- Consider a CDN for global distribution

---

## Slide 14 — Environment & Configuration

### NODE_ENV

```javascript
// Express checks NODE_ENV internally
// 'production' enables:
//   - view caching
//   - less verbose errors
//   - better performance

if (app.get('env') === 'production') {
  app.set('trust proxy', 1);
  app.use(helmet());
}
```

### dotenv for Config

```javascript
require('dotenv').config();

// .env file
// PORT=3000
// DB_URL=mongodb://localhost/mydb
// SESSION_SECRET=keyboard_cat

const PORT = process.env.PORT || 3000;
// NEVER commit .env to git!
```

### app.set() / app.get()

| Setting | Purpose |
|---------|---------|
| `env` | Environment mode |
| `view engine` | Template engine |
| `views` | Views directory |
| `view cache` | Cache compiled templates |
| `trust proxy` | Trust X-Forwarded-* headers |
| `json spaces` | Prettify JSON output |
| `etag` | ETag response header |
| `case sensitive routing` | /Foo ≠ /foo |

### trust proxy

```javascript
app.set('trust proxy', 1);

// Enables correct:
// - req.ip (client IP, not proxy IP)
// - req.protocol ('https' not 'http')
// - req.hostname
// - secure cookies over HTTPS
```

**Important:** Only enable when behind a trusted proxy. Otherwise clients can spoof headers.

---

## Slide 15 — Security Best Practices

### Helmet — Security Headers

```javascript
const helmet = require('helmet');
app.use(helmet());

// Sets 15+ HTTP headers:
// - Content-Security-Policy
// - X-Content-Type-Options: nosniff
// - X-Frame-Options: DENY
// - Strict-Transport-Security

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "cdn.example.com"]
    }
  }
}));
```

### CORS Configuration

```javascript
const cors = require('cors');

app.use(cors({
  origin: 'https://myapp.com',
  methods: ['GET', 'POST'],
  credentials: true
}));
```

### Rate Limiting

```javascript
const rateLimit = require('express-rate-limit');

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: { error: 'Too many requests' },
  standardHeaders: true,
  legacyHeaders: false
});

app.use('/api/', apiLimiter);
```

### Input Validation & CSRF

```javascript
const { body, validationResult } = require('express-validator');

app.post('/users',
  body('email').isEmail().normalizeEmail(),
  body('name').trim().notEmpty().escape(),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty())
      return res.status(400).json({ errors: errors.array() });
  }
);
```

Always **validate and sanitise** input. Never trust `req.body`, `req.params`, or `req.query` directly.

---

## Slide 16 — Project Structure & Patterns

### MVC Directory Layout

```
my-express-app/
├── app.js              ← Express setup
├── server.js           ← listen() entry
├── package.json
├── .env
├── config/
│   ├── db.js
│   └── index.js
├── routes/
│   ├── index.js
│   ├── auth.js
│   ├── users.js
│   └── posts.js
├── controllers/
│   ├── authController.js
│   ├── userController.js
│   └── postController.js
├── services/
│   ├── userService.js
│   └── emailService.js
├── models/
│   ├── User.js
│   └── Post.js
├── middleware/
│   ├── auth.js
│   ├── validate.js
│   └── errorHandler.js
├── views/
│   ├── layouts/
│   ├── partials/
│   └── pages/
├── public/
│   ├── css/
│   ├── js/
│   └── images/
└── tests/
    ├── routes/
    └── services/
```

### Separation of Concerns

- **app.js** — Express config, middleware, mount routes
- **server.js** — `app.listen()` only (testable app)
- **routes/** — URL mapping, input validation
- **controllers/** — handle req/res, call services
- **services/** — business logic, DB queries
- **models/** — data schemas (Mongoose, Sequelize)
- **middleware/** — reusable req/res transformations

### Route → Controller → Service

```javascript
// routes/users.js
router.get('/:id', userController.getById);

// controllers/userController.js
exports.getById = async (req, res, next) => {
  try {
    const user = await userService.findById(req.params.id);
    res.json(user);
  } catch (err) { next(err); }
};

// services/userService.js
exports.findById = (id) => User.findById(id).lean();
```

**Tip:** Export `app` from `app.js` and import it in `server.js` and in tests. This lets you run `supertest(app)` without starting a real server.

---

## Slide 17 — Express 5 — What's New

### Automatic Promise Rejection Handling

```javascript
// Express 4 — must wrap or use library
app.get('/data', async (req, res, next) => {
  try {
    const data = await fetchData();
    res.json(data);
  } catch (err) {
    next(err);
  }
});

// Express 5 — just throw!
app.get('/data', async (req, res) => {
  const data = await fetchData();
  res.json(data);
  // Rejected promise → error middleware automatically
});
```

### Stricter Path Matching

```javascript
// Express 4: loose, regex-like
app.get('/ab?cd', handler); // matches /acd, /abcd

// Express 5: uses path-to-regexp v8
app.get('/users{/:id}', handler); // matches /users and /users/42
app.get('/files/*path', handler); // req.params.path = rest of URL
```

### Removed / Changed APIs

| Express 4 | Express 5 |
|-----------|-----------|
| `app.del()` | Removed — use `app.delete()` |
| `req.host` | Removed — use `req.hostname` |
| `req.param()` | Removed — use `req.params` / `req.query` |
| `res.json(status, obj)` | Removed — chain `.status().json()` |
| `res.send(status)` | Removed — use `res.sendStatus()` |

### Other Improvements

- **req.query** now returns a getter (re-parsed on each access)
- **Brotli** encoding support for `res.sendFile()`
- **res.render()** returns a Promise when no callback given
- **Node 18+** required as minimum version

Express 5 reached stable release in 2024 after years in alpha/beta. Migration is straightforward for most apps — the biggest change is path matching syntax.

---

## Slide 18 — Complete Working Example

### app.js — REST API + Views

```javascript
const express = require('express');
const path = require('path');
const helmet = require('helmet');
const morgan = require('morgan');
const app = express();

app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

app.use(helmet());
app.use(morgan('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));

app.locals.siteName = 'TaskBoard';
app.locals.year = new Date().getFullYear();

let tasks = [
  { id: 1, title: 'Learn Express', done: false },
  { id: 2, title: 'Build an API', done: true },
];
let nextId = 3;

app.get('/', (req, res) => {
  res.render('home', { title: 'Tasks', tasks });
});

app.get('/api/tasks', (req, res) => {
  res.json(tasks);
});

app.post('/api/tasks', (req, res) => {
  const { title } = req.body;
  if (!title) return res.status(400).json({ error: 'Title required' });
  const task = { id: nextId++, title, done: false };
  tasks.push(task);
  res.status(201).json(task);
});

app.patch('/api/tasks/:id', (req, res) => {
  const task = tasks.find(t => t.id === +req.params.id);
  if (!task) return res.status(404).json({ error: 'Not found' });
  Object.assign(task, req.body);
  res.json(task);
});

app.delete('/api/tasks/:id', (req, res) => {
  tasks = tasks.filter(t => t.id !== +req.params.id);
  res.sendStatus(204);
});
```

### Error Handling & Start

```javascript
app.use((req, res) => {
  res.status(404).render('404', { title: 'Not Found' });
});

app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Internal Server Error' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`TaskBoard running on :${PORT}`);
});
```

### views/home.ejs

```html
<h1>Task List</h1>
<ul>
  <% tasks.forEach(t => { %>
    <li class="<%= t.done ? 'done' : '' %>">
      <%= t.title %>
    </li>
  <% }) %>
</ul>
<form method="POST" action="/api/tasks">
  <input name="title" placeholder="New task">
  <button type="submit">Add</button>
</form>
```

### Test with cURL

```bash
# List tasks
curl localhost:3000/api/tasks

# Create task
curl -X POST localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Deploy app"}'

# Toggle done
curl -X PATCH localhost:3000/api/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"done":true}'

# Delete task
curl -X DELETE localhost:3000/api/tasks/2
```

---

## Slide 19 — Summary & Next Steps

### Core Takeaways

- Express = minimal, middleware-driven web framework for Node.js
- Routing maps HTTP methods + paths to handler functions
- Middleware is the core pattern — everything flows through the pipeline
- Built-in parsers for JSON, URL-encoded, and static files
- Error handling uses the 4-argument `(err, req, res, next)` signature

### Best Practices

- Use `express.Router()` for modular route files
- Apply security middleware (helmet, cors, rate-limit)
- Always validate and sanitise user input
- Separate app.js from server.js for testability
- Use environment variables for config (`dotenv`)
- Handle errors centrally with custom error classes

### Next Steps

- Build a full CRUD REST API with Express
- Add a database (MongoDB + Mongoose or PostgreSQL + Sequelize)
- Implement authentication (Passport.js, JWT)
- Add WebSocket support (Socket.IO)
- Deploy to production (PM2, Docker, Railway)
- Explore Express 5 path matching and async handling

### Essential Resources

- **Official Docs:** expressjs.com
- **GitHub:** github.com/expressjs/express
- **npm:** npmjs.com/package/express
- **MDN Express Tutorial:** developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs

### Key Packages

| Package | Purpose |
|---------|---------|
| `express` | Web framework |
| `helmet` | Security headers |
| `cors` | Cross-origin resource sharing |
| `morgan` | HTTP request logging |
| `express-validator` | Input validation |
