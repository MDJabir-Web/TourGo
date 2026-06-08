# TourGo

Your travel companion for exploring Sylhet. Discover attractions, find accommodations, connect with local guides, and navigate transport options—all in one app.

---

## What's Inside

- 📍 **Attractions** - Explore tourist spots and local hidden gems
- 🏨 **Accommodations** - Book hotels, homestays, and lodges
- 👨‍🏫 **Local Guides** - Connect with verified tour guides
- 🚗 **Transport** - Find rides and transportation options
- 📱 **Works Everywhere** - Web, iOS, and Android

---

## Getting Started

### Prerequisites
- Node.js 18+
- npm or yarn
- Git

### Installation

```bash
# Clone the repo
git clone https://github.com/MDJabir-Web/TourGo.git
cd TourGo

# Install dependencies
npm install

# Start development
npm run start
```

### Build & Deploy

**Web Version:**
```bash
npm run web
```

**Android APK:**
```bash
npm run build:apk-production
```

**iOS (macOS only):**
```bash
npm run ios
```

---

## Tech Stack

- **Frontend**: React Native (Expo)
- **Backend**: Django REST API
- **Database**: PostgreSQL
- **Deployment**: Railway (Backend), Vercel (Web)
- **Mobile Builds**: EAS Build

---

## Project Structure

```
TourGo/
├── src/                   # Frontend source code
│   ├── app/              # App screens
│   ├── components/       # Reusable components
│   ├── constants/        # Configuration
│   └── hooks/            # Custom React hooks
├── backend/              # Django REST API
│   ├── sylhet_go_api/   # Django project
│   ├── requirements.txt  # Python dependencies
│   └── Dockerfile        # Container config
├── .github/workflows/    # GitHub Actions CI/CD
└── app.json             # Expo configuration
```

---

## Development

### Running Locally

**Terminal 1 - Backend:**
```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
python manage.py runserver
```

**Terminal 2 - Frontend (Web):**
```bash
npm run web
```

**Terminal 3 - Frontend (Mobile):**
```bash
npm run android
```

---

## Deployment

### Backend (Railway)
1. Push code to GitHub
2. Connect repo to Railway
3. Set environment variables
4. Auto-deploys on push

### Frontend (Vercel)
1. Import repo to Vercel
2. Add environment variables
3. Auto-deploys on push

### Mobile APK
- Builds automatically with GitHub Actions
- Download from Actions tab or GitHub Releases

---

## API Endpoints

```
POST   /api/auth/login/
POST   /api/auth/register/
GET    /api/attractions/
GET    /api/accommodations/
GET    /api/guides/
GET    /api/transport/
```

---

## Contributing

1. Fork the repo
2. Create feature branch: `git checkout -b feature/your-feature`
3. Commit changes: `git commit -m "Add feature"`
4. Push: `git push origin feature/your-feature`
5. Open Pull Request

---

## Environment Variables

Copy `.env.example` and update values:

```
EXPO_PUBLIC_API_URL_PROD=https://your-backend.railway.app
EXPO_PUBLIC_API_URL=http://localhost:8000
DEBUG=True/False
SECRET_KEY=your-secret-key
DATABASE_URL=postgresql://...
```

---

## Build Commands

```bash
npm run start              # Start development server
npm run web                # Build web version
npm run android            # Build Android dev version
npm run build:apk-preview  # Build preview APK
npm run build:apk-production  # Build production APK
npm run lint               # Run linter
```

---

## Troubleshooting

**APK showing "Expo Go"?**
- Use `npm run build:apk-production` (not `npm run android`)

**CORS errors?**
- Update `CORS_ALLOWED_ORIGINS` in Django settings

**Frontend can't reach backend?**
- Check API URL in environment variables
- Verify backend is deployed and running

**Dependencies issues?**
```bash
rm -rf node_modules
npm install
```

---

## Support

- 📖 Check documentation in repo
- 🐛 Report issues on GitHub
- 💬 Discussions available in repo

---

## License

MIT License - See LICENSE file for details

---

## Roadmap

- [ ] Payment integration (bKash, Nagad)
- [ ] Real-time chat with guides
- [ ] Offline maps
- [ ] Reviews and ratings system
- [ ] Booking history
- [ ] Multi-language support

---

**Built with ❤️ for travelers exploring Sylhet**

## Join the community

Join our community of developers creating universal apps.

- [Expo on GitHub](https://github.com/expo/expo): View our open source platform and contribute.
- [Discord community](https://chat.expo.dev): Chat with Expo users and ask questions.
