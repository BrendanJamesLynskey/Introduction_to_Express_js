# ◇ Introduction to Express.js

An interactive Reveal.js presentation covering Express.js — from routing and middleware through to error handling, security, project structure, and production deployment.

## ▶ [Open the Presentation](https://brendanjameslynskey.github.io/Introduction_to_Express_js/)

## 📄 [Markdown Version](https://github.com/BrendanJamesLynskey/Introduction_to_Express_js/blob/main/presentation.md)

---

## Contents

| # | Topic | Description |
|---|-------|-------------|
| 01 | Title | Introduction to Express.js — Node.js Web Framework |
| 02 | Agenda | Overview of all topics covered |
| 03 | What Is Express.js? | History, minimalist philosophy, npm stats, install & quick start |
| 04 | Hello World & App Structure | App creation, listen, basic routing, request lifecycle |
| 05 | Routing — Methods, Paths, Parameters | GET/POST/PUT/DELETE, route params, query strings, patterns |
| 06 | Route Organisation | express.Router, mounting, modular route files, router.param |
| 07 | Request & Response Objects | req.params, req.query, req.body, res.json, res.render, res.redirect |
| 08 | Middleware Fundamentals | app.use, next(), order matters, application vs router vs error |
| 09 | Built-in Middleware | express.json, express.urlencoded, express.static, express.raw |
| 10 | Third-Party Middleware | cors, helmet, morgan, compression, rate-limit, multer |
| 11 | Error Handling | Error middleware signature, async error catching, custom error classes |
| 12 | Template Engine Integration | app.set view engine, res.render, app.locals, multiple engines |
| 13 | Static File Serving | express.static, virtual path prefix, caching headers, production tips |
| 14 | Environment & Configuration | NODE_ENV, dotenv, app.get/set, trust proxy |
| 15 | Security Best Practices | helmet, CORS, rate limiting, input validation, CSRF |
| 16 | Project Structure & Patterns | MVC layout, service layer, route → controller → service |
| 17 | Express 5 — What's New | Path matching, rejected promises, removed APIs |
| 18 | Complete Working Example | Full REST API + views TaskBoard app |
| 19 | Summary & Next Steps | Core takeaways, best practices, resources, key packages |

---

## Slide Controls

| Action | Key |
|--------|-----|
| Next / Previous | `→` `←` or swipe |
| Overview | `Esc` |
| Fullscreen | `F` |
| Export to PDF | Append `?print-pdf` to URL, then print |

## Technology

[Reveal.js 4.6](https://revealjs.com) · [highlight.js](https://highlightjs.org) · Playfair Display + DM Sans + JetBrains Mono

Single self-contained `index.html` — no build step, no npm, no dependencies to install.

## References

- [Express.js Official Site](https://expressjs.com) — documentation and API reference
- [Express.js GitHub Repository](https://github.com/expressjs/express) — source code and issues
- [MDN Express/Node Tutorial](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs) — comprehensive learning guide
- [helmet](https://helmetjs.github.io/) — security headers middleware
- [cors](https://github.com/expressjs/cors) — Cross-Origin Resource Sharing middleware
- [express-validator](https://express-validator.github.io/) — input validation middleware
- [Express 5 Migration Guide](https://expressjs.com/en/guide/migrating-5.html) — upgrading from Express 4

## License

Educational use. Code examples provided as-is.
