# Enable BDR Scoring Portal

A full-stack web application for BDR managers to score, track, and analyse rep performance.

## Tech Stack

- **Frontend**: React + Vite (deployed to Railway or Vercel)
- **Backend**: Node.js + Express (deployed to Railway)
- **Database**: PostgreSQL (Railway managed)
- **Auth**: JWT

---

## Local Development

### Prerequisites
- Node.js 18+
- PostgreSQL (or use Railway for the DB)

### 1. Clone & install

```bash
git clone https://github.com/YOUR_ORG/bdr-portal.git
cd bdr-portal

# Backend
cd backend
npm install
cp .env.example .env
# Fill in your .env values

# Frontend
cd ../frontend
npm install
cp .env.example .env
# Set VITE_API_URL=http://localhost:3001
```

### 2. Set up the database

```bash
cd backend
npm run migrate
```

### 3. Create your first manager account

```bash
# Using curl
curl -X POST http://localhost:3001/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Your Name","email":"you@enable.com","password":"yourpassword"}'
```

### 4. Run locally

```bash
# Terminal 1 — backend
cd backend && npm run dev

# Terminal 2 — frontend
cd frontend && npm run dev
```

---

## Deploy to Railway

### Step 1: Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_ORG/bdr-portal.git
git push -u origin main
```

### Step 2: Create Railway project

1. Go to [railway.app](https://railway.app) → **New Project**
2. Select **Deploy from GitHub repo**
3. Choose your `bdr-portal` repo

### Step 3: Add PostgreSQL

1. In Railway project → **+ New** → **Database** → **PostgreSQL**
2. Railway automatically provides `DATABASE_URL` to services in the same project

### Step 4: Deploy the Backend service

1. Railway auto-detects the root — you need to point it at `/backend`
2. In service settings → **Root Directory** → set to `backend`
3. Add environment variables:
   ```
   JWT_SECRET=your-long-random-secret-here
   FRONTEND_URL=https://your-frontend.railway.app
   NODE_ENV=production
   ```
4. Railway will auto-detect `npm start` from `package.json`

### Step 5: Run migrations on Railway

In the backend service → **Shell** tab:
```bash
npm run migrate
```

Then create your first manager:
```bash
node -e "
const bcrypt = require('bcryptjs');
const { pool } = require('./db');
async function seed() {
  const hash = await bcrypt.hash('YourPassword123', 10);
  await pool.query(
    'INSERT INTO managers (name, email, password_hash, avatar) VALUES (\$1,\$2,\$3,\$4)',
    ['Admin User', 'admin@enable.com', hash, 'AU']
  );
  console.log('Manager created!');
  process.exit();
}
seed();
"
```

### Step 6: Deploy the Frontend service

1. In Railway project → **+ New** → **GitHub Repo** (same repo, second service)
2. Set **Root Directory** to `frontend`
3. Add environment variable:
   ```
   VITE_API_URL=https://your-backend.railway.app
   ```
4. Build command: `npm run build`
5. Start command: `npx serve dist -p $PORT`

Or deploy frontend to **Vercel** instead (free tier):
```bash
cd frontend
npx vercel --prod
# Set VITE_API_URL in Vercel dashboard
```

---

## Environment Variables Reference

### Backend
| Variable | Description |
|----------|-------------|
| `DATABASE_URL` | PostgreSQL connection string (auto-set by Railway) |
| `JWT_SECRET` | Long random string for signing JWTs |
| `FRONTEND_URL` | URL of frontend (for CORS) |
| `NODE_ENV` | `production` or `development` |
| `PORT` | Auto-set by Railway |

### Frontend
| Variable | Description |
|----------|-------------|
| `VITE_API_URL` | URL of the backend API |

---

## Scoring Categories

| Category | Skills |
|----------|--------|
| Prospecting Quality | Account Research, ICP Alignment, Multi-threading |
| Messaging & Communication | Objection Handling, Personalisation |
| Discovery | Identify L2 Pain, CoM Usage & Competency, Product Knowledge |
| Tools Mastery | SFDC Hygiene, Outreach Competency, Zoominfo Competency, Salesnav Competency |
| Professionalism & Coachability | Receptive to Feedback, Implements Changes Quickly, Positive Mindset, Time Management |

All skills scored 1–5. Fiscal year starts February.
