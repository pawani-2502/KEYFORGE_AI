# KeyForge AI

> **Forge Your Speed. Master Your Craft.**

A full-stack typing practice platform for developers, students, and professionals. Train across coding languages, English, Hindi, and specialized formats while tracking WPM, accuracy, and long-term performance trends powered by analytics and linear regression forecasts.

**Developed by [Pawani Sharma](https://github.com/pawani-2502)**

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Solution](#solution)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [User Flow](#user-flow)
- [Data Flow](#data-flow)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Database Schema](#database-schema)
- [API Reference](#api-reference)
- [Typing Modes](#typing-modes)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Available Scripts](#available-scripts)
- [Production Build](#production-build)
- [Screenshots](#screenshots)
- [Future Roadmap](#future-roadmap)
- [Contributing](#contributing)
- [License](#license)
- [Author](#author)

---

## Problem Statement

Typing speed and accuracy are critical skills for developers, competitive exam aspirants, and office professionals — yet most practice tools fall short:

- Generic typing tutors ignore **code-specific challenges** (brackets, indentation, syntax)
- No unified platform covers **English, Hindi, and Hinglish** in one place
- Learners lack **data-driven feedback** on whether they are actually improving
- Blind typing and placement-prep modes are rarely available together
- Progress is lost across sessions without accounts and persistent history
- Leaderboards and analytics are either missing or too basic to motivate daily practice

**KeyForge AI** solves this by combining a developer-grade typing engine, multi-language support, AI-powered coaching, and predictive analytics in one polished platform.

---

## Solution

KeyForge AI is a **Performance Typing Platform** that:

1. Lets users pick a mode (Coding, English, Hindi, Blind, Placement, etc.) and start typing instantly
2. Tracks **live WPM, accuracy, errors, and elapsed time** during every test
3. Saves completed tests to PostgreSQL and builds a personal performance history
4. Surfaces **analytics dashboards** with trend charts and mode-wise breakdowns
5. Uses **linear regression** to forecast future WPM and accuracy
6. Ranks users on **daily, weekly, monthly, and all-time leaderboards**
7. Optionally generates dynamic practice content and coaching feedback via **Google Gemini AI**

---

## Key Features

| Feature | Description |
|---------|-------------|
| **8+ Typing Modes** | Coding, English, Hindi, Hinglish, Blind, Placement, Resume, and Government exam formats |
| **IDE-Style Code Editor** | VS Code–inspired dark theme with syntax-aware whitespace and bracket handling |
| **Live Stats** | Real-time WPM, accuracy, error count, and timer during each test |
| **Analytics Dashboard** | Trend charts, mode breakdowns, best/average WPM, and accuracy over time |
| **Performance Predictions** | Linear-regression forecasts for WPM, accuracy, and target milestone dates |
| **Leaderboards** | Daily, weekly, monthly, and all-time rankings by mode and language |
| **User Accounts** | JWT authentication with bcrypt password hashing and persistent profiles |
| **AI Content Generation** | Dynamic practice text via Google Gemini (optional) |
| **AI Coaching Feedback** | Personalized tips after each completed test |
| **Content Library** | Curated seed content across modes, languages, and difficulty levels |
| **Hindi Guide** | Interactive Devanagari keyboard reference for Hindi typing learners |
| **Guest Mode** | Practice without an account; link results to a username |
| **Responsive UI** | Mobile-first dark theme built with Tailwind CSS, shadcn/ui, and Framer Motion |

---

## System Architecture

```mermaid
graph TB
    subgraph Client["Frontend (React + Vite)"]
        A[Home<br/>Mode Selector]
        B[Type Test Page<br/>Live WPM Engine]
        C[Analytics Dashboard]
        D[Leaderboard]
        E[Profile & Auth]
        F[Hindi Guide]
    end

    subgraph Server["Backend (Express + TypeScript)"]
        G[API Router<br/>/api/*]
        H[Auth Routes<br/>JWT + bcrypt]
        I[Typing Tests Routes]
        J[Stats & Predictions]
        K[Leaderboard Routes]
        L[AI Routes<br/>Gemini API]
        M[Zod Validation]
    end

    subgraph Data["Database (PostgreSQL)"]
        N[(users)]
        O[(typing_tests)]
        P[(typing_content)]
    end

    subgraph External["External Services"]
        Q[Google Gemini API<br/>Content & Feedback]
    end

    A --> B
    B --> C
    B --> D
    E --> B
    F --> B

    B -->|POST /api/typing-tests| G
    C -->|GET /api/stats/*| G
    C -->|GET /api/predictions/*| G
    D -->|GET /api/leaderboard| G
    E -->|POST /api/auth/*| G
    B -->|POST /api/ai/*| G

    G --> H
    G --> I
    G --> J
    G --> K
    G --> L
    G --> M

    H --> N
    I --> O
    I --> P
    J --> O
    K --> O
    L --> Q
```

---

## User Flow

```mermaid
flowchart TD
    Start([User visits KeyForge AI]) --> Home[Home Page]
    Home --> Choice{User Action}

    Choice -->|Browse modes| Mode[Select Typing Mode]
    Choice -->|View rankings| LB[Leaderboard Page]
    Choice -->|View stats| Analytics[Analytics Dashboard]
    Choice -->|Sign in| Auth[Login / Register]

    Auth --> Profile[Profile Page]
    Profile --> Mode

    Mode --> Config[Choose Language & Difficulty]
    Config --> Test[Start Typing Test]
    Test --> Live[Live WPM / Accuracy Tracking]
    Live --> Done{Test Complete?}

    Done -->|Yes| Results[Show Results + AI Feedback]
    Results --> Save[Save to Database]
    Save --> Next{What's Next?}

    Next -->|Retry| Test
    Next -->|Analytics| Analytics
    Next -->|Leaderboard| LB
    Next -->|Home| Home

    LB --> Filter[Filter by Period & Category]
    Filter --> Rankings[View Top Typists]

    Analytics --> Trends[WPM & Accuracy Trends]
    Trends --> Predict[Regression Forecasts]
```

---

## Data Flow

```mermaid
sequenceDiagram
    participant U as User Browser
    participant F as React Frontend
    participant B as Express API
    participant D as PostgreSQL
    participant G as Gemini AI

    U->>F: Complete typing test
    F->>B: POST /api/typing-tests {wpm, accuracy, mode, ...}
    B->>D: INSERT INTO typing_tests
    D-->>B: Saved record
    B-->>F: 201 Created

    F->>B: POST /api/ai/generate-feedback {wpm, accuracy, errors}
    B->>G: Generate coaching tips
    G-->>B: AI feedback text
    B-->>F: Personalized feedback
    F-->>U: Display results + tips

    U->>F: Open Analytics page
    F->>B: GET /api/stats/trends?username=...
    B->>D: SELECT FROM typing_tests ORDER BY created_at
    D-->>B: Test history rows
    B-->>F: Trend data points

    F->>B: GET /api/predictions/wpm?username=...
    B->>B: Linear regression on WPM history
    B-->>F: Forecast {predictedWpm, trend, confidence}
    F-->>U: Render prediction chart
```

---

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Frontend** | React 19, TypeScript, Vite, Tailwind CSS, shadcn/ui, TanStack Query, Wouter, Framer Motion, Recharts |
| **Backend** | Node.js, Express 5, TypeScript, Zod validation, Pino logging |
| **Database** | PostgreSQL, Drizzle ORM, drizzle-kit |
| **Authentication** | JWT (jsonwebtoken), bcryptjs |
| **AI** | Google Gemini API (`@google/genai`) — optional |
| **Analytics** | Custom linear regression service for WPM/accuracy forecasting |
| **Tooling** | npm workspaces, esbuild, tsx, TypeScript |

---

## Project Structure

```
KEYFORGE_AI/
├── frontend/                    # React + TypeScript SPA
│   ├── src/
│   │   ├── pages/               # home, type, analytics, leaderboard, profile, about
│   │   ├── components/          # UI primitives, layout, UsernameDialog
│   │   ├── hooks/               # useAuth, useToast, useUsername
│   │   └── lib/                 # API client, custom-fetch, utilities
│   ├── public/                  # Static assets
│   └── vite.config.ts           # Dev server + API proxy
│
├── backend/                     # Express REST API
│   ├── src/
│   │   ├── routes/              # auth, typing-tests, stats, leaderboard, ai, predictions
│   │   ├── db/schema/           # Drizzle table definitions
│   │   ├── services/            # linearRegression prediction engine
│   │   ├── middlewares/         # JWT auth middleware
│   │   └── validation/          # Zod schemas for API types
│   ├── scripts/                 # Content library seed script
│   └── drizzle.config.ts        # Database migration config
│
├── package.json                 # npm workspaces root
└── README.md                    # You are here
```

---

## Database Schema

```mermaid
erDiagram
    USERS {
        serial id PK
        text username UK
        text email UK
        text password_hash
        text role
        timestamp created_at
    }

    TYPING_TESTS {
        serial id PK
        text username
        text mode
        text language
        real wpm
        real accuracy
        integer errors
        integer duration
        integer lines_count
        text code_theme
        timestamp created_at
    }

    TYPING_CONTENT {
        serial id PK
        text mode
        text language
        text difficulty
        text title
        text content
        timestamp created_at
    }

    USERS ||--o{ TYPING_TESTS : "username (logical link)"
    TYPING_CONTENT ||--o{ TYPING_TESTS : "provides practice text by mode"
```

---

## API Reference

### Authentication

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/api/auth/register` | — | Create a new account |
| `POST` | `/api/auth/login` | — | Sign in and receive JWT token |

### Typing Tests

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/typing-tests` | Optional | List test history (filter by username) |
| `POST` | `/api/typing-tests` | Optional | Save a completed test result |

### Statistics

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/stats/summary` | — | Best/average WPM, accuracy, mode breakdown |
| `GET` | `/api/stats/trends` | — | WPM and accuracy trend data points |

### Predictions

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/predictions/wpm` | Optional | Linear-regression WPM forecast |
| `GET` | `/api/predictions/accuracy` | Optional | Accuracy trend prediction |
| `GET` | `/api/predictions/target-date` | Optional | Estimated date to reach target WPM |
| `GET` | `/api/predictions/by-language` | Optional | Per-language performance predictions |

### Leaderboard

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/leaderboard` | — | Rankings by period (`daily`, `weekly`, `monthly`, `all_time`) and category |

### AI (requires `GEMINI_API_KEY`)

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/api/ai/generate-content` | — | Generate practice text for a mode/language |
| `POST` | `/api/ai/generate-feedback` | — | Get personalized coaching after a test |

### Health

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/health` | — | Server and database health check |

### Example Requests

```bash
# Register a new account
curl -X POST http://localhost:3001/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"typist01","email":"typist@example.com","password":"securepass"}'

# Save a completed test
curl -X POST http://localhost:3001/api/typing-tests \
  -H "Content-Type: application/json" \
  -d '{"username":"typist01","mode":"coding","language":"javascript","wpm":72.5,"accuracy":96.2,"errors":3,"duration":60}'

# Get weekly leaderboard
curl "http://localhost:3001/api/leaderboard?period=weekly&limit=10"

# Get WPM prediction
curl "http://localhost:3001/api/predictions/wpm?username=typist01"
```

---

## Typing Modes

| Mode | Route | Description |
|------|-------|-------------|
| **Coding** | `/type/coding` | 15+ programming languages with IDE-style editor |
| **English** | `/type/english` | Classic prose, vocabulary, and general text |
| **Hindi** | `/type/hindi` | Devanagari script typing practice |
| **Hinglish** | `/type/hinglish` | Mixed Hindi-English challenges |
| **Blind** | `/type/blind` | Memorize text, then type from memory |
| **Placement** | `/type/placement` | Interview-style coding questions |
| **Resume** | `/type/resume` | Professional and business terminology |
| **Government** | `/type/government` | Official document and exam formats |

---

## Getting Started

### Prerequisites

- **Node.js** 20 or higher
- **PostgreSQL** 14+ (local install or hosted — e.g. Neon, Supabase, Railway)
- **npm** 9+ (comes with Node.js)
- **Google Gemini API key** (optional — enables AI content and feedback)

### Installation

**1. Clone the repository**

```bash
git clone https://github.com/pawani-2502/KEYFORGE_AI.git
cd KEYFORGE_AI
```

**2. Install dependencies**

```bash
npm install
```

**3. Configure environment variables**

Create `backend/.env`:

```env
PORT=3001
DATABASE_URL=postgresql://user:password@localhost:5432/keyforge
JWT_SECRET=your-super-secret-jwt-key
GEMINI_API_KEY=your-gemini-api-key
```

**4. Set up the database**

```bash
npm run db:push -w backend
```

**5. (Optional) Seed the content library**

```bash
npm run seed -w backend
```

**6. Start development servers**

```bash
npm run dev
```

| Service | URL |
|---------|-----|
| Frontend | http://localhost:5173 |
| Backend API | http://localhost:3001 |
| Health Check | http://localhost:3001/api/health |

---

## Environment Variables

| Variable | Location | Required | Description |
|----------|----------|----------|-------------|
| `PORT` | `backend/.env` | No | Express server port (default: `3001`) |
| `DATABASE_URL` | `backend/.env` | **Yes** | PostgreSQL connection string |
| `JWT_SECRET` | `backend/.env` | **Yes** | Secret key for signing JWT tokens |
| `GEMINI_API_KEY` | `backend/.env` | No | Google Gemini API key for AI features |

---

## Available Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start frontend and backend concurrently |
| `npm run build` | Build frontend and backend for production |
| `npm run typecheck` | Run TypeScript checks across workspace |
| `npm run lint` | Lint TypeScript across workspace |
| `npm run db:push -w backend` | Push Drizzle schema to PostgreSQL |
| `npm run seed -w backend` | Seed typing content library |
| `npm run start -w backend` | Start production backend server |

---

## Production Build

```bash
# Build both packages
npm run build

# Start the backend
npm run start -w backend
```

Serve `frontend/dist/` with any static file host (Nginx, Vercel, Netlify, etc.) and point API requests to your deployed backend.

---

## Screenshots

| Home — Mode Selection | Typing Test | Live Stats |
|:---:|:---:|:---:|
| _Pick from 8+ typing modes_ | _IDE-style code editor_ | _Real-time WPM & accuracy_ |

| Analytics Dashboard | Leaderboard | AI Feedback |
|:---:|:---:|:---:|
| _Trend charts & predictions_ | _Daily/weekly/monthly ranks_ | _Personalized coaching tips_ |

---

## Future Roadmap

- [ ] Multiplayer real-time typing races
- [ ] Custom text upload for practice
- [ ] Typing certificates and shareable score cards
- [ ] Mobile app (React Native)
- [ ] OAuth login (Google, GitHub)
- [ ] Advanced streak tracking and daily goals
- [ ] Community-created content packs
- [ ] Voice-guided Hindi typing lessons

---

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

Please ensure your code passes `npm run typecheck` before submitting.

---

## License

Private project — all rights reserved.

---

## Author

**Pawani Sharma**

Full-Stack Developer passionate about performance-focused web applications and developer tooling.

- GitHub: [@pawani-2502](https://github.com/pawani-2502)

---

<p align="center">
  <strong>KeyForge AI</strong> — Forge your speed. Master your craft.
  <br />
  Built with ❤️ by <a href="https://github.com/pawani-2502">Pawani Sharma</a>
</p>
