# Fitness Website (FitZone)

A Node.js + Express + MySQL fitness web app with:
- User registration and login
- Session-based authentication (cookie `sid`)
- Dashboard with profile, workout and nutrition sections
- Product showcase page
- AI assistant page using a Groq API proxy endpoint

## Tech Stack

- Backend: Node.js, Express, body-parser, bcrypt, dotenv, mysql2
- Frontend: HTML, CSS, JavaScript, Bootstrap, Font Awesome
- Database: MySQL

## Project Structure

```text
.
|- index.js                  # Express server and routes
|- db_conn.js                # MySQL connection
|- .env                      # Environment variables (GROQ_API_KEY)
|- public/
|  |- home.html
|  |- login.html
|  |- registration.html
|  |- dashboard.html
|  |- ai.html
|  |- product1.html
|  |- pagenotfound.html
|- package.json
```

## Features

- Registration with password confirmation and bcrypt hashing
- Login by email or username
- Legacy plain-text password compatibility in login logic
- Session creation with HttpOnly cookie
- Authenticated `GET /api/me` profile endpoint
- Logout route that clears session
- `POST /api/groq-chat` server proxy to Groq Chat Completions API
- Static file hosting from `public/`

## Prerequisites

- Node.js 18+ (recommended; this app uses global `fetch`)
- MySQL 8+ (or compatible)

## Setup

1. Install dependencies:

```bash
npm install
```

2. Create `.env` file in project root:

```env
GROQ_API_KEY=your_groq_api_key_here
```

3. Configure MySQL connection in `db_conn.js`:
- `host`
- `user`
- `password`
- `database`

4. Create database/table (example):

```sql
CREATE DATABASE IF NOT EXISTS fit;
USE fit;

CREATE TABLE IF NOT EXISTS user (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL,
  address TEXT,
  gender VARCHAR(20),
  hobbies TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

5. Start server:

```bash
node index.js
```

Server runs on:

```text
http://localhost:6800
```

## Main Routes

- `GET /` -> serves static entry (`public/index.html`) via Express static
- `GET /Home` -> `public/home.html`
- `GET /Login` -> `public/login.html`
- `GET /Registration` -> `public/registration.html`
- `POST /RegistrationValidation` -> register user
- `POST /LoginValidation` -> login user
- `GET /api/me` -> current logged-in user (requires session)
- `GET /Logout` -> clear session and redirect to login
- `POST /api/groq-chat` -> Groq proxy
- Fallback unmatched routes -> `public/pagenotfound.html`

## Authentication Notes

- Sessions are stored in-memory (`Map`) in `index.js`.
- If the server restarts, active sessions are lost.
- Cookie is set as: `HttpOnly; Path=/; SameSite=Lax`.

## AI Chat Integration

- Frontend (`public/ai.html`) sends requests to backend proxy at `/api/groq-chat`.
- Backend forwards payload to:
  - `https://api.groq.com/openai/v1/chat/completions`
- Keeps API key on server instead of exposing it in browser code.

## Known Limitations

- No persistent session store (Redis/DB) yet
- No CSRF protection
- No rate-limiting on auth or AI endpoints
- Mixed legacy/unused files exist in root (`script.js`, `dashboard.js`, etc.)
- `package.json` has no `scripts` section (run with `node index.js`)

## Recommended Improvements

- Add `npm` scripts (`start`, `dev`)
- Move DB credentials to environment variables
- Use `express-session` + persistent store
- Add input validation (Joi/Zod/express-validator)
- Add security middleware (`helmet`, rate limiter)
- Add logging and error middleware

## Security Note

If any real API key was committed or shared in `.env`, rotate it immediately in Groq dashboard and replace it with a new key.

## License

No license file is currently included.
