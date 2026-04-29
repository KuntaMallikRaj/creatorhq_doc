<div align="center">
# CreatorHQ
 
**The all-in-one creator business platform for Instagram creators.**
 
Track brand deals, sync Instagram analytics, build media kits, and price your work — all in one place.
 
[![Live Demo](https://img.shields.io/badge/Live-createhq.netlify.app-1F3A5F?style=for-the-badge)](https://createhq.netlify.app)
[![Status](https://img.shields.io/badge/status-active-success?style=for-the-badge)]()
[![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)]()
 
[Live App](https://createhq.netlify.app) • [Architecture Doc](./docs/architecture.md) • [Report Bug](https://github.com/KuntaMallikRaj/creatorhq/issues)
 
</div>
---
 
## 📸 Overview
 
CreatorHQ is a **multi-tenant SaaS platform** built for Instagram creators to consolidate their entire creator business — content analytics, brand-deal pipelines, rate cards, media kits, and benchmarks — into a single workspace.
 
Built with a hybrid architecture: a Next.js frontend that talks directly to Supabase for CRUD, and a FastAPI backend that handles Instagram OAuth, computed analytics, and admin operations.
 
---
 
## 🏗️ Architecture
 
![CreatorHQ Architecture](./docs/architecture-diagram.png)
 
### Three-tier design
 
```
┌──────────────────────────────────────────────────────────┐
│  Presentation       Next.js 14 + React + Tailwind        │
│                     Hosted on Netlify (CDN + Edge)       │
├──────────────────────────────────────────────────────────┤
│  Orchestration      FastAPI + Python 3.11                │
│                     Hosted on Render (Docker)            │
├──────────────────────────────────────────────────────────┤
│  Persistence        Supabase (Postgres + Auth + RLS)     │
└──────────────────────────────────────────────────────────┘
            │              │              │
            ▼              ▼              ▼
       Instagram        SendGrid        Stripe
       Graph API        (Email)         (Planned)
```
 
### Two request paths
 
- **Direct CRUD** — Browser → Supabase JS SDK → Postgres (RLS-enforced). Used for deals, media kits, settings.
- **Backend-mediated** — Browser → FastAPI → external API / compute → Supabase. Used for Instagram sync, analytics, admin emails.
📖 **Full design doc:** [docs/architecture.md](./docs/architecture.md)
 
---
 
## ✨ Features
 
| Feature | Description |
|---------|-------------|
| 📊 **Content Analytics** | Sync Instagram posts, calculate real engagement rate, get performance insights |
| 💼 **Brand Deal Tracker** | Six-state deal pipeline from pitch → negotiating → confirmed → in-progress → paid |
| 💰 **Rate Card** | Auto-suggested deal rates based on followers and engagement |
| 🎨 **Media Kit Generator** | Build and share a public media kit via stable slug-based URL |
| 📈 **Benchmarks** | Compare your performance against niche-level averages |
| 🔗 **Instagram Connect** | OAuth-based Instagram Business Login integration |
| 🛠️ **Admin Panel** | Manage tester requests and dispatch activation emails |
 
---
 
## 🧱 Tech Stack
 
**Frontend**
- Next.js 14.2 (App Router, SSR, Edge Middleware)
- TypeScript 5.x
- TailwindCSS 3.x
- Supabase JS SDK 2.x
**Backend**
- FastAPI (Python 3.11)
- Pydantic 2.x for request/response validation
- httpx for async Instagram API calls
- Docker for deployment
**Database & Auth**
- Supabase (PostgreSQL 15)
- Supabase Auth (JWT, OTP, password reset)
- Row Level Security on every table
**Infra & Services**
- Netlify (frontend hosting + CDN)
- Render (backend Docker hosting)
- Meta Graph API (Instagram Business Login)
- SendGrid (transactional email)
- Stripe (subscription billing — planned)
---
 
## 📂 Project Structure
 
```
creatorhq/
├── creatorhq-frontend/          # Next.js frontend
│   ├── app/
│   │   ├── (public)/            # Landing, tools, legal pages
│   │   ├── (auth)/              # Login, signup, password reset
│   │   ├── (dashboard)/         # Protected app pages
│   │   └── api/                 # Next.js API route proxies
│   ├── components/              # Reusable UI components
│   ├── lib/supabase/            # Supabase client setup
│   ├── middleware.ts            # Edge auth middleware
│   └── netlify.toml             # Netlify build config
│
├── creatorhq-backend/           # FastAPI backend
│   ├── app/
│   │   ├── routers/             # API route handlers
│   │   ├── services/            # Business logic
│   │   ├── schemas/             # Pydantic models
│   │   ├── utils/               # Auth helpers, validators
│   │   ├── config.py            # Environment config
│   │   ├── database.py          # Supabase client
│   │   └── main.py              # FastAPI app
│   └── Dockerfile
│
├── database/
│   └── schema.sql               # Complete DB schema
│
└── docs/
    ├── architecture.md          # Full design + architecture doc
    └── architecture-diagram.png
```
 
---
 
## 🚀 Getting Started
 
### Prerequisites
- Node.js 18+
- Python 3.11+
- A Supabase project ([create one free](https://supabase.com))
- A Meta Developer app with Instagram Business Login enabled
### 1. Clone the repo
 
```bash
git clone https://github.com/KuntaMallikRaj/creatorhq.git
cd creatorhq
```
 
### 2. Database setup
 
1. Create a Supabase project at [supabase.com](https://supabase.com)
2. Go to **SQL Editor** → paste the contents of `database/schema.sql` → run
3. Copy your project URL, anon key, service key, and JWT secret
### 3. Frontend setup
 
```bash
cd creatorhq-frontend
npm install
cp .env.example .env.local
# Fill in values from Supabase + Meta + your backend URL
npm run dev
# → http://localhost:3000
```
 
### 4. Backend setup
 
```bash
cd creatorhq-backend
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
# Fill in values for Supabase, Instagram, SendGrid
uvicorn app.main:app --reload
# → http://localhost:8000
# → Swagger UI at http://localhost:8000/docs
```
 
---
 
## 🔐 Environment Variables
 
### Frontend (`.env.local`)
```env
NEXT_PUBLIC_SUPABASE_URL=https://<project>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=...
NEXT_PUBLIC_BACKEND_URL=https://creatorhq.onrender.com
NEXT_PUBLIC_IG_APP_ID=...
NEXT_PUBLIC_REDIRECT_URI=https://createhq.netlify.app/api/auth/callback
```
 
### Backend (`.env`)
```env
SUPABASE_URL=https://<project>.supabase.co
SUPABASE_SERVICE_KEY=...
JWT_SECRET=...
IG_APP_ID=...
IG_APP_SECRET=...
REDIRECT_URI=https://createhq.netlify.app/api/instagram/callback
IG_WEBHOOK_VERIFY_TOKEN=...
SENDGRID_API_KEY=...
ALLOWED_ORIGINS=["https://createhq.netlify.app"]
```
 
---
 
## 🛡️ Security
 
CreatorHQ uses **defense in depth** with four independent authorization layers:
 
1. **Edge middleware** — Next.js middleware checks the Supabase session cookie before any protected page renders.
2. **Row Level Security** — Every table has policies keyed on `auth.uid()`. Wrong user → zero rows, no matter the query.
3. **JWT verification** — FastAPI decodes the bearer token on every request using the shared `JWT_SECRET`.
4. **Role check** — Admin endpoints compare the token email against the admin list before responding.
The Supabase service key never leaves the backend. The browser only ever sees the anon key, which is RLS-gated.
 
---
 
## 📡 API Reference
 
Base URL: `https://creatorhq.onrender.com`
 
| Domain | Endpoints |
|--------|-----------|
| `/instagram` | OAuth callback, webhook, post sync |
| `/analytics` | Engagement rate, content insights, suggested rates |
| `/deals` | Brand deal CRUD (free tier: 3 max) |
| `/media-kit` | Media kit CRUD + PDF export |
| `/benchmarks` | Niche benchmark lookups |
| `/waitlist` | Tester requests + admin invite dispatch |
| `/auth` | Current user info |
| `/health` | Liveness probe |
 
Full endpoint catalog in [docs/architecture.md](./docs/architecture.md#6-api-design).
 
Interactive Swagger docs available at `https://creatorhq.onrender.com/docs`.
 
---
 
## 🗺️ Roadmap
 
### Near-term
- [ ] Stripe subscription billing ($5/mo Premium tier)
- [ ] Meta App Review submission (unlocks public sign-up)
- [ ] Background job queue for Instagram sync
### Mid-term
- [ ] TikTok and YouTube Shorts integration
- [ ] Email outreach module — pitch brands directly from the deal pipeline
- [ ] Agency-tier accounts with multiple managed creators
### Long-term
- [ ] AI-driven content recommendations
- [ ] Brand discovery marketplace
- [ ] Native iOS app
---
 
## 🤝 Contributing
 
Contributions are welcome. For major changes, please open an issue first to discuss what you'd like to change.
 
1. Fork the project
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
---
 
## 📄 License
 
Distributed under the MIT License. See `LICENSE` for more information.
 
---
 
## 👤 Author
 
**Kunta Mallik Raj**
 
- GitHub: [@KuntaMallikRaj](https://github.com/KuntaMallikRaj)
- Project Link: [github.com/KuntaMallikRaj/creatorhq](https://github.com/KuntaMallikRaj/creatorhq)
- Live Demo: [createhq.netlify.app](https://createhq.netlify.app)
---
 
<div align="center">
If CreatorHQ helped you, give it a ⭐ — it really helps.
 
</div>
 
