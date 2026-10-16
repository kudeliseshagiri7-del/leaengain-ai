# LearnGain AI — Supabase + Ollama setup

## 1. Local Supabase credentials

Open `.env` in the project root and fill these values:

```env
VITE_SUPABASE_URL=https://YOUR_PROJECT_ID.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_PUBLISHABLE_OR_ANON_KEY
```

These are client-side Supabase settings. Never put a Supabase service-role key or Ollama API key in this file. Restart Vite after saving.

## 2. Create database tables

If the LearnGain tables are not already in your Supabase database, open Supabase Dashboard → SQL Editor and run the complete SQL from `supabase/migrations/20261011015513_create_learngain_schema.sql`. The migration uses owner-scoped Row Level Security policies.

## 3. Store the Ollama key securely

Supabase Dashboard → Edge Functions → Secrets → Add secret:

- Name: `OLLAMA_API_KEY`
- Value: your Ollama Cloud API key

Optional secret: `OLLAMA_MODEL` = `gpt-oss:120b-cloud`. The function defaults to this model if the optional secret is absent.

## 4. Deploy the Edge Function

The existing Supabase endpoint shown in the project setup is `/functions/v1/hyper-processor`, so the local function folder and frontend URL are aligned to the `hyper-processor` slug. Deploy the code in `supabase/functions/hyper-processor/index.ts` to that existing function. Keep JWT verification enabled.

If deploying via CLI, from the project root run:

```powershell
npx supabase login
npx supabase link --project-ref YOUR_PROJECT_REF
npx supabase functions deploy hyper-processor
```

Use the project reference shown in your Supabase Dashboard URL/settings. If you already edit the function in the Dashboard editor, paste the contents of `supabase/functions/hyper-processor/index.ts` there and deploy the update instead.

## 5. Run and use

```powershell
npm install
npm run dev
```

Sign in to the site and open AI Tutor. Text questions are supported; use the microphone button for voice input. AI responses are shown in chat, stored in `chat_history`, and read aloud by browser speech synthesis. Use the Listen button to replay an answer. Microphone input requires browser permission and works best in current Chrome or Edge.

## What is stored

Supabase Auth stores user accounts; the migration creates profiles, notes, bookmarks, quiz results, study plans, chat history, and achievements with row-level security. Course/video catalog content remains in the existing source files because it is app content, not user-specific records.
