# LearnGain AI

A complete advanced personalized learning platform with an AI tutor, interactive quizzes, structured courses, educational videos, notes, bookmarks, study planner, analytics, achievements, and settings.

Built with **React + Vite + TypeScript**, **Supabase** (auth, database, edge functions), and **Tailwind CSS** with a premium glassmorphism interface.

---

## Features

| Feature | Description |
|---|---|
| **Landing Page** | Beautiful glassmorphism hero with animated gradients, feature showcase, and call-to-action |
| **Authentication** | Email/password sign-up and sign-in with automatic profile creation |
| **Dashboard** | Personalized overview with stats, quick actions, recent activity, and study plan summary |
| **AI Tutor** | Real AI-powered chat tutor with server-side API key, plus honest offline fallback |
| **Educational Videos** | 12 verified videos from Khan Academy, CrashCourse, and 3Blue1Brown with embedded playback |
| **Quizzes** | 8 multi-category quizzes with 50+ questions, instant feedback, and detailed explanations |
| **Courses** | 6 structured courses with 24 lessons across math, biology, physics, history, CS, and English |
| **Notes** | Create, edit, pin, tag, search, and delete notes by category |
| **Bookmarks** | Save and organize external learning resources |
| **Study Planner** | Plan study sessions with dates, times, priorities, and completion tracking |
| **Analytics** | Performance charts, category breakdowns, quiz history, and progress tracking |
| **Achievements** | 12 unlockable badges that reward learning milestones |
| **Settings** | Manage profile, view security info, and sign out |

---

## Tech Stack

- **Frontend**: React 18, Vite 5, TypeScript 5, Tailwind CSS 3
- **Backend**: Supabase (PostgreSQL, Auth, Edge Functions)
- **AI**: Server-side AI proxy via Supabase Edge Function (OpenAI / Anthropic / OpenAI-compatible)
- **Icons**: lucide-react
- **Routing**: react-router-dom

---

## Project Structure

```
src/
├── components/          # Shared UI components
│   ├── AppLayout.tsx    # Sidebar navigation + main content wrapper
│   ├── GlassCard.tsx    # Reusable glassmorphism card
│   ├── PageHeader.tsx   # Page title + subtitle + action
│   ├── ProtectedRoute.tsx  # Auth guard
│   └── Toast.tsx        # Toast notifications
├── contexts/
│   └── AuthContext.tsx  # Auth state, sign in/up/out, profile management
├── data/
│   ├── achievements.ts  # 12 achievement definitions
│   ├── courses.ts       # 6 courses with 24 lessons
│   ├── quizzes.ts       # 8 quizzes with 50+ questions
│   └── videos.ts        # 12 verified educational videos
├── hooks/
│   └── useData.ts       # Data hooks for notes, bookmarks, quiz results, etc.
├── lib/
│   └── supabase.ts      # Supabase client singleton
├── pages/
│   ├── LandingPage.tsx
│   ├── AuthPage.tsx
│   ├── Dashboard.tsx
│   ├── AITutor.tsx
│   ├── Videos.tsx
│   ├── Quizzes.tsx
│   ├── Courses.tsx
│   ├── Notes.tsx
│   ├── Bookmarks.tsx
│   ├── StudyPlanner.tsx
│   ├── Analytics.tsx
│   ├── Achievements.tsx
│   └── Settings.tsx
├── types/
│   └── index.ts         # TypeScript interfaces
├── App.tsx              # Router + route definitions
├── main.tsx             # React entry point
└── index.css            # Global styles + glassmorphism utilities

supabase/
├── config.toml          # Edge function configuration
└── functions/
    └── hyper-processor/
        └── index.ts     # AI tutor edge function (server-side)
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm or yarn

### Installation

```bash
# Clone the repository
git clone <repo-url>
cd learngain-ai

# Install dependencies
npm install

# Copy environment variables (Windows PowerShell)
Copy-Item .env.example .env
# Edit .env with your Supabase project URL and publishable/anon key
```

### Supabase + Ollama setup

1. In `.env`, set `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` to your own Supabase project URL and publishable/anon key. Restart Vite after changing `.env`.
2. In Supabase SQL Editor, run `supabase/migrations/20261011015513_create_learngain_schema.sql` once if the LearnGain tables and policies have not already been created. This migration sets up profiles, notes, bookmarks, quiz results, study plans, chat history, achievements, RLS policies, and automatic profile creation.
3. In Supabase Dashboard → Edge Functions → Secrets, add `OLLAMA_API_KEY` with your Ollama Cloud API key. Optionally add `OLLAMA_MODEL`; the default is `gpt-oss:120b-cloud`. Never put this key in `.env` or client code.
4. Deploy the code in `supabase/functions/hyper-processor/index.ts` to the existing function slug `hyper-processor`. Keep JWT verification enabled; the function validates the signed-in user's access token.
5. Run `npm run dev`, sign in, and open AI Tutor. Microphone input uses browser speech recognition; answers are displayed as text and read aloud using browser speech synthesis. Browser support and microphone permission are required.

### Running the Development Server

```bash
npm run dev
```

The app will be available at `http://localhost:5173`.

### Building for Production

```bash
npm run build
npm run preview
```

---

## Environment Variables

### Client-side (in `.env`)

| Variable | Description |
|---|---|
| `VITE_SUPABASE_URL` | Your Supabase project URL |
| `VITE_SUPABASE_ANON_KEY` | Your Supabase anonymous key |

### Server-side (Supabase Edge Function Secrets)

These are set as Supabase Edge Function secrets, not in client code. Students never need their own API key.

| Variable | Description | Required |
|---|---|---|
| `OLLAMA_API_KEY` | Ollama Cloud API key, set only as a Supabase Edge Function secret | Recommended for real AI |
| `OLLAMA_MODEL` | Ollama Cloud model (default `gpt-oss:120b-cloud`) | No |
| `AI_PROVIDER` | `"openai"`, `"openai-compatible"`, or `"anthropic"` (legacy providers) | No (offline fallback) |
| `AI_API_KEY` | Legacy AI provider API key | No (offline fallback) |
| `AI_MODEL` | Model name (e.g. `gpt-4o-mini`, `claude-3-5-sonnet-20241022`) | No |
| `AI_BASE_URL` | Custom endpoint for OpenAI-compatible providers | No |

**When AI is not configured**, the AI tutor runs in offline mode — it provides honest, helpful study guidance without fabricating AI responses. A banner notifies the user that offline mode is active.

---

## AI Configuration (Administrator Guide)

For Ollama Cloud, add `OLLAMA_API_KEY` under Supabase Dashboard → Edge Functions → Secrets. Optionally add `OLLAMA_MODEL`; the default is `gpt-oss:120b-cloud`. The deployed function slug for this supplied project is `hyper-processor`, matching the existing Supabase endpoint.

The AI tutor is designed so that **students never need to provide an API key**. An administrator configures the AI service once, and all students benefit.

### Option 1: OpenAI

Set these as Supabase Edge Function secrets:
- `AI_PROVIDER` = `openai`
- `AI_API_KEY` = your OpenAI API key
- `AI_MODEL` = `gpt-4o-mini` (or any supported model)

### Option 2: Anthropic Claude

- `AI_PROVIDER` = `anthropic`
- `AI_API_KEY` = your Anthropic API key
- `AI_MODEL` = `claude-3-5-sonnet-20241022`

### Option 3: OpenAI-Compatible (Groq, Together, etc.)

- `AI_PROVIDER` = `openai-compatible`
- `AI_API_KEY` = your provider API key
- `AI_MODEL` = provider-specific model name
- `AI_BASE_URL` = provider's API base URL

### Option 4: No AI (Offline Mode)

Simply don't set any AI variables. The tutor will use a built-in offline fallback that provides study tips and general guidance. This is clearly communicated to the user — no fabricated AI responses.

---

## Database & Security

The platform uses Supabase (PostgreSQL) with Row Level Security (RLS) on every table. Each user can only access their own data — policies enforce ownership checks using `auth.uid()`.

### Tables

| Table | Purpose |
|---|---|
| `profiles` | User display name, avatar, bio (auto-created on signup) |
| `notes` | Study notes with title, content, category, tags, pin |
| `bookmarks` | Saved learning resources |
| `quiz_results` | Quiz attempt history with scores and answers |
| `study_plans` | Study tasks with dates, times, priorities |
| `chat_history` | AI tutor conversation log |
| `user_achievements` | Unlocked achievement badges |

### Security Model

- **RLS enabled** on all tables
- **4 policies per table** (SELECT, INSERT, UPDATE, DELETE) — no `FOR ALL` shortcuts
- **Owner-scoped**: every query checks `auth.uid() = user_id`
- **Server-side AI**: API keys live in edge function secrets, never exposed to the client
- **Automatic profile creation**: a database trigger creates a profile row when a user signs up

---

## Educational Videos

All video IDs are verified from official, verified YouTube channels:

- **Khan Academy** — math fundamentals, scientific notation, calculus
- **CrashCourse** — biology, world history, history of science
- **3Blue1Brown** — calculus essence, linear algebra, physics
- **MIT OpenCourseWare** — multivariable calculus

Videos are embedded using the privacy-enhanced `youtube-nocookie.com` domain.

---

## Deployment

### Deploy to Bolt.new

1. Push your code to a Git repository
2. Import the repository into [Bolt.new](https://bolt.new)
3. The Supabase project is automatically provisioned
4. Set AI secrets via the Supabase dashboard if desired

### Deploy Elsewhere

1. Build the project: `npm run build`
2. Deploy the `dist/` folder to any static hosting provider (Vercel, Netlify, Cloudflare Pages)
3. Ensure the Supabase environment variables are set
4. Deploy the edge function via Supabase MCP tools

---

## License

This project is built for educational purposes. Use it freely for learning and teaching.
