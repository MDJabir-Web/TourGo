# ✅ Deployment Checklist

Complete this checklist to deploy TourGo frontend + backend on GitHub with Railway & Vercel.

---

## 📋 Phase 1: Preparation

- [ ] **GitHub Account** - Have GitHub account ready
- [ ] **Railway Account** - Sign up at https://railway.app
- [ ] **Vercel Account** - Sign up at https://vercel.app
- [ ] **Expo Account** - Have https://expo.dev account ready
- [ ] **Repository Ready** - Push TourGo to GitHub

---

## 🔐 Phase 2: GitHub Setup

### Create Repository

- [ ] Go to https://github.com/new
- [ ] Repository name: `TourGo`
- [ ] Description: "Travel guide app for Sylhet"
- [ ] Create repository
- [ ] Push your code:
  ```bash
  git remote add origin https://github.com/YOUR-USERNAME/TourGo.git
  git branch -M main
  git push -u origin main
  ```

### Add GitHub Secrets

**Settings → Secrets and variables → Actions**

- [ ] Add `EXPO_TOKEN` - Get from https://expo.dev/settings/tokens
- [ ] Add `RAILWAY_TOKEN` - Get from Railway dashboard → Account
- [ ] Add `VERCEL_TOKEN` - Get from Vercel → Settings → Tokens
- [ ] Add `API_URL` - Will update after Railway deployment
- [ ] Add `VERCEL_ORG_ID` - From Vercel project settings
- [ ] Add `VERCEL_PROJECT_ID` - From Vercel project settings

---

## 🚄 Phase 3: Backend (Django) on Railway

### Create Railway Project

- [ ] Go to https://railway.app
- [ ] Click "Start Project" → "Deploy from GitHub repo"
- [ ] Select your **TourGo** repo
- [ ] Configure build:
  - [ ] Root directory: `backend`
  - [ ] Build command: `pip install -r requirements.txt`
  - [ ] Start command: `gunicorn sylhet_go_api.wsgi:application --bind 0.0.0.0:8000`

### Add PostgreSQL Database

- [ ] In Railway, click "Add Service"
- [ ] Select "PostgreSQL"
- [ ] Confirm it's added to your project
- [ ] Note the `DATABASE_URL` (auto-generated)

### Configure Environment Variables

In Railway → Variables:

- [ ] `DEBUG` = `False`
- [ ] `SECRET_KEY` = Generate random string
- [ ] `ALLOWED_HOSTS` = `*.railway.app,localhost`
- [ ] `CORS_ALLOWED_ORIGINS` = Will update after Vercel setup
- [ ] `DJANGO_SETTINGS_MODULE` = `sylhet_go_api.settings`

### Test Backend

- [ ] Wait for deployment (~3-5 min)
- [ ] Copy backend URL from Railway
- [ ] Test: `https://your-railway-url.railway.app/api/`
- [ ] You should see API endpoints (404 or list is fine)

---

## 🌐 Phase 4: Frontend (Web) on Vercel

### Create Vercel Project

- [ ] Go to https://vercel.com
- [ ] Click "Add New" → "Project"
- [ ] Select "Import Git Repository"
- [ ] Choose your **TourGo** repo
- [ ] Framework preset: **Expo** (or Next.js)
- [ ] Click "Deploy"

### Add Environment Variables

In Vercel → Settings → Environment Variables:

- [ ] `EXPO_PUBLIC_API_URL_PROD` = Your Railway backend URL
- [ ] `EXPO_PUBLIC_API_URL` = `http://localhost:8000`

- [ ] **Redeploy** to apply new variables

### Test Frontend

- [ ] Wait for Vercel build (~5-10 min)
- [ ] Copy Vercel URL: `https://tourgo.vercel.app`
- [ ] Visit it in browser
- [ ] You should see your TourGo app

---

## 🔄 Phase 5: Connect Frontend to Backend

### Update CORS

In `backend/sylhet_go_api/settings.py`:

- [ ] Update `CORS_ALLOWED_ORIGINS` to include Vercel URL
  ```python
  CORS_ALLOWED_ORIGINS = [
      'https://tourgo.vercel.app',
      'http://localhost:3000',
  ]
  ```

### Update Frontend API Config

In `src/constants/api.ts` (or your API file):

- [ ] Ensure it uses `EXPO_PUBLIC_API_URL_PROD` environment variable
- [ ] Example:
  ```typescript
  const API_URL = process.env.EXPO_PUBLIC_API_URL_PROD || 'http://localhost:8000';
  ```

### Test Integration

- [ ] Make an API call from frontend to backend
- [ ] Check browser console for CORS errors
- [ ] If CORS error: update Django `CORS_ALLOWED_ORIGINS`

---

## 📱 Phase 6: Mobile APK (EAS Build)

### Build APK

- [ ] Run locally:
  ```bash
  npm run build:apk-production
  ```

### Or Auto-Build

- [ ] GitHub Actions workflows run automatically
- [ ] Go to repo → Actions → Build APK
- [ ] Download artifact from completed workflow

---

## 🧪 Phase 7: Testing

### Backend Testing

- [ ] API health check: `https://your-railway-url/health/` (should return 200)
- [ ] List attractions: `https://your-railway-url/api/attractions/`
- [ ] Admin panel: `https://your-railway-url/admin/`

### Frontend Testing

- [ ] Visit web version in browser
- [ ] Check mobile version works on Android
- [ ] Test API calls (check network tab in DevTools)

### Integration Testing

- [ ] [ ] Frontend successfully retrieves data from backend
- [ ] [ ] No CORS errors in browser console
- [ ] [ ] Images load correctly
- [ ] [ ] Forms submit data to backend

---

## 🚀 Phase 8: Automation & CI/CD

### Verify GitHub Actions

- [ ] Go to repo → Actions
- [ ] Check workflows:
  - [ ] `build-apk.yml` - Builds Android APK
  - [ ] `backend-deploy.yml` - Deploys backend to Railway
  - [ ] `frontend-web-deploy.yml` - Deploys frontend to Vercel

### Test Automation

- [ ] Make a small change to frontend code
- [ ] Commit and push to `main`
- [ ] Watch Actions tab → verify build runs
- [ ] Wait for Vercel deployment notification
- [ ] Verify changes live on https://tourgo.vercel.app

### Test Backend Automation

- [ ] Make a change in `backend/` folder
- [ ] Commit and push to `main`
- [ ] Watch Railway auto-deploy
- [ ] Verify API still works

---

## 📊 Phase 9: Production Checklist

- [ ] Backend database has proper backups (Railway handles this)
- [ ] Environment variables are secure (no secrets in code)
- [ ] CORS is configured correctly
- [ ] API endpoints are protected/authenticated where needed
- [ ] Frontend has proper error handling
- [ ] Logging is set up
- [ ] SSL/HTTPS is enabled (automatic with Railway & Vercel)

---

## 📱 Phase 10: Distribution

### Share Your App

- [ ] Web version: https://tourgo.vercel.app
- [ ] Android APK: Download from GitHub Releases
- [ ] iOS: Build with EAS (requires paid plan) or TestFlight

### Create GitHub Releases

```bash
# Tag your version
git tag v1.0.0
git push origin v1.0.0

# GitHub automatically creates release with APK
```

---

## ✨ You're Done!

Your TourGo app is now:

✅ Deployed to production  
✅ Automated with GitHub Actions  
✅ Scaled across web and mobile  
✅ Database-backed with PostgreSQL  
✅ API secured with CORS  
✅ Ready for users!

---

## 🔗 Quick Links

| Service | Dashboard | Docs |
|---------|-----------|------|
| GitHub | https://github.com/settings/repositories | [GitHub Docs](https://docs.github.com) |
| Railway | https://railway.app | [Railway Docs](https://railway.app/docs) |
| Vercel | https://vercel.com/dashboard | [Vercel Docs](https://vercel.com/docs) |
| Expo | https://expo.dev | [Expo Docs](https://docs.expo.dev) |

---

## 🆘 Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| CORS Error | Update `CORS_ALLOWED_ORIGINS` in Django settings |
| Database connection failed | Check `DATABASE_URL` in Railway variables |
| Frontend showing "Expo Go" | Use `npm run build:apk-production` not `npm run android` |
| Vercel build fails | Check Node.js version is 18+ in Vercel settings |
| Railway deployment hangs | Check logs in Railway dashboard |

---

## 📞 Support

Stuck? Check:
1. [FULLSTACK_DEPLOYMENT.md](./FULLSTACK_DEPLOYMENT.md) - Detailed guide
2. [QUICKSTART_DEPLOYMENT.md](./QUICKSTART_DEPLOYMENT.md) - Quick reference
3. [build-output/BUILD_GUIDE.md](./build-output/BUILD_GUIDE.md) - APK building
4. Official docs links above

---

**Last Updated:** 2026-06-08  
**Version:** 1.0
