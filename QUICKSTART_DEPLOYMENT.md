# 🚀 TourGo Full Stack Deployment - Quick Start

Deploy both **Django Backend** and **React Native Web Frontend** on GitHub with automated CI/CD.

## 📊 Architecture Overview

```
GitHub Repository (TourGo)
├── Frontend (React Native/Expo)
│   └── Deployed to: Vercel or Netlify (Web)
│   └── Build: APK via EAS Build (Mobile)
│
├── Backend (Django REST API)
│   └── Deployed to: Railway or Render
│   └── Database: PostgreSQL
│
└── CI/CD Workflows
    └── Auto-deploy on push to main/develop
```

---

## 🎯 5-Minute Setup

### Step 1: Create GitHub Repository

```bash
cd TourGo
git init
git add .
git commit -m "Initial commit: TourGo full stack"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/TourGo.git
git push -u origin main
```

---

## 🔧 Backend Deployment (Railway)

### Step 1: Railway Account Setup

1. Go to **https://railway.app**
2. Click "Start Project"
3. Select "Deploy from GitHub repo"
4. Choose **TourGo** repository
5. Click "Deploy Now"

### Step 2: Configure Railway Project

1. In Railway dashboard, click "Configure"
2. Add **ROOT_DIRECTORY**: `backend`
3. Add these **Environment Variables**:

```
DEBUG=False
ALLOWED_HOSTS=*.railway.app,localhost
SECRET_KEY=generate-a-random-secret-key-here
DJANGO_SETTINGS_MODULE=sylhet_go_api.settings
```

### Step 3: Add PostgreSQL Database

1. In your Railway project, click **"Add Service"**
2. Select **PostgreSQL**
3. Railway auto-generates `DATABASE_URL`
4. Database variables set automatically ✅

### Step 4: Run Django Migrations

```bash
# In your local terminal
cd backend

# Create .env file (copy from .env.example)
cp .env.example .env

# Update with Railway database credentials
# Get DATABASE_URL from Railway dashboard
```

After deployment, Railway shows your backend URL:
```
https://tourgo-prod.railway.app
```

---

## 🌐 Frontend Web Deployment (Vercel)

### Step 1: Vercel Account Setup

1. Go to **https://vercel.com**
2. Click "Add New..." → "Project"
3. Import **TourGo** GitHub repo
4. Select framework: **Expo**
5. Click "Deploy"

### Step 2: Add Environment Variables in Vercel

1. Go to your Vercel project → **Settings**
2. Click **Environment Variables**
3. Add:

```
EXPO_PUBLIC_API_URL_PROD=https://tourgo-prod.railway.app
EXPO_PUBLIC_API_URL=http://localhost:8000
```

4. Redeploy to apply variables

### Step 3: Test Web Version

After deployment:
```
Frontend: https://tourgo.vercel.app
API: https://tourgo-prod.railway.app/api/
```

---

## 📱 GitHub Secrets Setup

Add these to GitHub for CI/CD automation:

**Go to: Settings → Secrets and variables → Actions**

### Required Secrets:

```
EXPO_TOKEN              # From https://expo.dev/settings/tokens
RAILWAY_TOKEN           # From Railway dashboard → Account
VERCEL_TOKEN            # From Vercel → Settings → Tokens
API_URL                 # https://your-railway-backend.railway.app
```

---

## 🔄 GitHub Actions Workflows

Your workflows are ready in `.github/workflows/`:

### ✅ Automated Workflows

1. **build-apk.yml** - Build APK on push (Expo)
2. **backend-deploy.yml** - Deploy backend on changes
3. **frontend-web-deploy.yml** - Deploy web version on changes

Workflows trigger automatically when you:
- Push to `main` or `develop`
- Make pull requests
- Manually trigger via "Run workflow"

---

## 🧪 Verify Integration

### Test Backend API

```bash
# Get attractions
curl https://tourgo-prod.railway.app/api/attractions/

# Get accommodations
curl https://tourgo-prod.railway.app/api/accommodations/
```

### Test Frontend

Visit your Vercel URL in browser:
```
https://tourgo.vercel.app
```

---

## 🔗 Update Frontend to Use Backend

In `src/constants/api.ts` (or wherever your API config is):

```typescript
const API_BASE_URL = process.env.EXPO_PUBLIC_API_URL_PROD 
  || process.env.EXPO_PUBLIC_API_URL 
  || 'http://localhost:8000';

export const API_ENDPOINTS = {
  AUTH: {
    LOGIN: `${API_BASE_URL}/api/auth/login/`,
    REGISTER: `${API_BASE_URL}/api/auth/register/`,
  },
  ATTRACTIONS: `${API_BASE_URL}/api/attractions/`,
  ACCOMMODATIONS: `${API_BASE_URL}/api/accommodations/`,
  GUIDES: `${API_BASE_URL}/api/guides/`,
  TRANSPORT: `${API_BASE_URL}/api/transport/`,
};
```

---

## 📦 Local Testing

### Test Backend Locally

```bash
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set up .env
cp .env.example .env

# Create database
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Run server
python manage.py runserver

# Admin: http://localhost:8000/admin/
# API: http://localhost:8000/api/
```

### Test Frontend Locally

```bash
cd TourGo

# Web version
npm run web

# Android development
npm run android

# iOS development (macOS only)
npm run ios
```

---

## 🚨 Troubleshooting

### Backend won't deploy
```
❌ Error: Dockerfile not found
✅ Solution: Check backend/Dockerfile exists

❌ Error: PORT not specified
✅ Solution: Railway detects port 8000 automatically

❌ Database connection failed
✅ Solution: Verify DATABASE_URL in environment variables
```

### Frontend can't reach backend
```
❌ CORS Error in browser
✅ Add to Django settings:
   CORS_ALLOWED_ORIGINS = ['https://your-vercel-url.com']

❌ API_URL undefined
✅ Add environment variable in Vercel dashboard
```

### APK still showing "Expo Go"
```
✅ Use: npm run build:apk-production (not npm run android)
✅ Download from EAS Build dashboard
```

---

## 📱 Full Development Flow

```bash
# 1. Create feature branch
git checkout -b feature/new-attraction

# 2. Make changes
# ... edit src/ files or backend/ files ...

# 3. Test locally
npm run web          # Test frontend
python manage.py runserver  # Test backend

# 4. Commit and push
git add .
git commit -m "feat: add new attractions"
git push origin feature/new-attraction

# 5. Create Pull Request on GitHub
# ✅ GitHub Actions runs tests automatically
# ✅ Once merged to main, auto-deploys to Railway + Vercel

# 6. Verify deployment
# Frontend: https://tourgo.vercel.app
# Backend: https://tourgo-prod.railway.app
```

---

## 🎯 What's Deployed Where

| Component | Deploy To | URL |
|-----------|-----------|-----|
| **Web Frontend** | Vercel | tourgo.vercel.app |
| **Mobile APK** | GitHub Releases | releases/download |
| **REST API** | Railway | tourgo-prod.railway.app |
| **Admin Panel** | Railway | tourgo-prod.railway.app/admin |
| **Database** | Railway PostgreSQL | Auto-managed |

---

## 💾 Useful Commands

```bash
# Build APK for testing
npm run build:apk-preview

# Build production APK
npm run build:apk-production

# Deploy backend only
git add backend/
git commit -m "Update backend"
git push  # Railway auto-deploys

# Deploy frontend only
git add src/
git commit -m "Update frontend"
git push  # Vercel auto-deploys
```

---

## 📚 Documentation Links

- [Railway Docs](https://railway.app/docs)
- [Vercel Docs](https://vercel.com/docs)
- [Django Deployment](https://docs.djangoproject.com/stable/howto/deployment/)
- [Expo Web Docs](https://docs.expo.dev/develop/user-interface/routing/)
- [GitHub Actions](https://docs.github.com/actions)

---

## ✨ You're Ready!

Your TourGo app is now:
- ✅ Built automatically with GitHub Actions
- ✅ Deployed to web (Vercel) and mobile (EAS)
- ✅ Backend API running on Railway
- ✅ Database managed by PostgreSQL
- ✅ CI/CD pipeline fully automated

**Next Steps:**
1. Configure your Django apps in `backend/`
2. Connect your frontend components to API endpoints
3. Add your custom business logic
4. Deploy with confidence!

Questions? Check the full guide: [FULLSTACK_DEPLOYMENT.md](./FULLSTACK_DEPLOYMENT.md)
