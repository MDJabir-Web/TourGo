# TourGo - Full Stack Deployment Guide

This guide covers deploying both the **Django Backend** and **React Native Web Frontend** to GitHub with Railway/Render.

## 📋 Overview

```
TourGo Project Structure:
├── TourGo/                          # React Native Frontend (Expo)
│   ├── src/                         # App source code
│   ├── .github/workflows/           # GitHub Actions for APK builds
│   └── app.json                     # Expo configuration
│
├── backend/                         # Django Backend (CREATE THIS)
│   ├── requirements.txt
│   ├── manage.py
│   ├── Dockerfile                   # For containerization
│   ├── railway.json or render.yaml  # Deployment config
│   └── apps/                        # Django apps
│
└── .github/workflows/               # Shared CI/CD workflows
    ├── backend-deploy.yml           # Auto-deploy backend
    └── frontend-web-deploy.yml      # Deploy web version
```

## 🎯 Step 1: Set Up Backend Repository

### 1.1 Create Backend Folder Structure

You'll need a proper Django project. Here's the minimal structure:

```bash
backend/
├── requirements.txt              # Python dependencies
├── manage.py                     # Django management
├── Dockerfile                    # For Railway/Render deployment
├── .env.example                  # Environment template
├── runtime.txt                   # Python version
├── sylhet_go_api/
│   ├── settings.py              # Django settings
│   ├── urls.py                  # API routes
│   ├── wsgi.py                  # WSGI entry point
│   └── apps/
│       ├── auth/                # Authentication app
│       ├── directory/           # Accommodations & Guides
│       ├── attractions/         # Attractions app
│       └── transport/           # Transport app
└── migrations/                  # Database migrations
```

### 1.2 Django Settings for Deployment

Create `sylhet_go_api/settings.py` with CORS enabled:

```python
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = os.getenv('SECRET_KEY', 'your-secret-key-change-in-production')
DEBUG = os.getenv('DEBUG', 'False') == 'True'
ALLOWED_HOSTS = os.getenv('ALLOWED_HOSTS', 'localhost,127.0.0.1').split(',')

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'rest_framework',
    'corsheaders',
    'sylhet_go_api.apps.auth',
    'sylhet_go_api.apps.directory',
    'sylhet_go_api.apps.attractions',
    'sylhet_go_api.apps.transport',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',  # Static files
    'corsheaders.middleware.CorsMiddleware',       # CORS
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
]

# CORS Configuration
CORS_ALLOWED_ORIGINS = os.getenv('CORS_ALLOWED_ORIGINS', 'http://localhost:3000').split(',')
CORS_ALLOW_CREDENTIALS = True

# Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DATABASE_NAME', 'tourgo_db'),
        'USER': os.getenv('DATABASE_USER', 'postgres'),
        'PASSWORD': os.getenv('DATABASE_PASSWORD', ''),
        'HOST': os.getenv('DATABASE_HOST', 'localhost'),
        'PORT': os.getenv('DATABASE_PORT', '5432'),
    }
}

# Static Files
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

# REST Framework
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'
USE_I18N = True
USE_TZ = True
```

## 🚀 Step 2: Create Deployment Files

### 2.1 Dockerfile (for Backend)

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy project
COPY . .

# Collect static files
RUN python manage.py collectstatic --noinput || true

# Expose port
EXPOSE 8000

# Run gunicorn
CMD ["gunicorn", "sylhet_go_api.wsgi:application", "--bind", "0.0.0.0:8000"]
```

### 2.2 requirements.txt

```
Django==4.2.0
djangorestframework==3.14.0
django-cors-headers==4.0.0
django-filter==23.1
djangorestframework-simplejwt==5.2.2
psycopg2-binary==2.9.6
gunicorn==20.1.0
whitenoise==6.4.0
python-decouple==3.8
Pillow==9.5.0
```

### 2.3 runtime.txt

```
python-3.11.3
```

## 🔗 Step 3: Connect Frontend to Backend

### Update your TourGo app.json or environment config:

**Create `.env.local` in TourGo root:**

```
EXPO_PUBLIC_API_URL=http://localhost:8000
EXPO_PUBLIC_API_URL_PROD=https://your-backend-domain.com
```

**In your API calls:**

```typescript
// src/constants/api.ts
const API_URL = process.env.EXPO_PUBLIC_API_URL_PROD || process.env.EXPO_PUBLIC_API_URL;

export const API_ENDPOINTS = {
  AUTH_LOGIN: `${API_URL}/api/auth/login/`,
  ATTRACTIONS: `${API_URL}/api/attractions/`,
  ACCOMMODATIONS: `${API_URL}/api/accommodations/`,
  GUIDES: `${API_URL}/api/guides/`,
  TRANSPORT: `${API_URL}/api/transport/`,
};
```

## 📦 Step 4: GitHub Setup

### 4.1 Push to GitHub

```bash
# If new repo
git init
git add .
git commit -m "Initial commit: TourGo full stack"
git branch -M main
git remote add origin https://github.com/YourUsername/TourGo.git
git push -u origin main

# Add backend folder
git add backend/
git commit -m "Add Django backend"
git push
```

### 4.2 Add Secrets to GitHub

**Go to: Settings → Secrets and variables → Actions**

Add these secrets:

- `EXPO_TOKEN` - From https://expo.dev/settings/tokens
- `DATABASE_URL` - PostgreSQL connection string
- `SECRET_KEY` - Django secret key
- `RAILWAY_TOKEN` or `RENDER_TOKEN` - From Railway/Render

## 🚄 Step 5: Deploy to Railway

### 5.1 Railway Setup (Free tier available)

1. Go to https://railway.app
2. Sign in with GitHub
3. Create new project
4. Select "Deploy from GitHub repo"
5. Choose your TourGo repo

### 5.2 Railway Configuration

In Railway dashboard:

1. Add service for **Backend**:
   - Service: Select your repo
   - Root directory: `backend`
   - Build command: `pip install -r requirements.txt`
   - Start command: `gunicorn sylhet_go_api.wsgi:application`

2. Add **PostgreSQL** plugin:
   - Add service → Database → PostgreSQL
   - This auto-generates `DATABASE_URL`

3. Environment variables:
   - `DJANGO_SETTINGS_MODULE=sylhet_go_api.settings`
   - `DEBUG=False`
   - `ALLOWED_HOSTS=*.railway.app`

### 5.3 Access Your Backend

After deployment, Railway gives you a URL like:
```
https://tourgo-backend-prod.railway.app
```

## 🌐 Step 6: Deploy Frontend Web Version

### 6.1 Build Web Version

```bash
# In TourGo folder
npm run web
# This creates a web build
```

### 6.2 Deploy to Vercel (Free & Easy)

1. Go to https://vercel.com
2. Sign in with GitHub
3. Import your TourGo repo
4. Framework: Expo
5. Deploy

**Add Environment Variables in Vercel:**
```
EXPO_PUBLIC_API_URL_PROD=https://your-railway-backend.railway.app
```

OR Deploy to **Netlify** the same way.

## 📱 Step 7: Verify Integration

Test that frontend connects to backend:

```bash
# Test backend API
curl https://your-railway-backend.railway.app/api/attractions/

# Test frontend
# Visit: https://your-vercel-app.vercel.app
```

## 🔄 GitHub Actions Workflows

Create these workflows:

### `.github/workflows/backend-test.yml`

```yaml
name: Backend Tests

on:
  push:
    branches: [main, develop]
    paths:
      - 'backend/**'
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: |
          cd backend
          pip install -r requirements.txt
      
      - name: Run migrations
        run: cd backend && python manage.py migrate
      
      - name: Run tests
        run: cd backend && python manage.py test
```

### `.github/workflows/frontend-web-test.yml`

```yaml
name: Frontend Web Build

on:
  push:
    branches: [main, develop]
    paths:
      - 'src/**'
      - 'app.json'
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm install
      
      - name: Build web
        run: npm run web
```

## ✅ Deployment Checklist

- [ ] Backend code in `backend/` folder
- [ ] `requirements.txt` created with dependencies
- [ ] `Dockerfile` and `railway.json` added
- [ ] GitHub secrets configured (EXPO_TOKEN, DATABASE_URL, etc.)
- [ ] Railway project created and connected
- [ ] PostgreSQL database provisioned
- [ ] Backend deployed and accessible
- [ ] Frontend `.env` configured with backend URL
- [ ] Vercel/Netlify connected for web deployment
- [ ] GitHub Actions workflows created
- [ ] CORS configured in Django settings
- [ ] API endpoints tested

## 🔗 Useful Links

- **Railway**: https://railway.app/docs
- **Render**: https://render.com/docs
- **Vercel**: https://vercel.com/docs
- **Netlify**: https://docs.netlify.com
- **Django Deployment**: https://docs.djangoproject.com/en/stable/howto/deployment/

## 📞 Support

Common issues:
- **CORS errors**: Update `CORS_ALLOWED_ORIGINS` in Django settings
- **Database connection fails**: Check `DATABASE_URL` format
- **Frontend can't reach backend**: Verify backend URL in `.env` files
- **Static files not loading**: Run `collectstatic` before deployment
