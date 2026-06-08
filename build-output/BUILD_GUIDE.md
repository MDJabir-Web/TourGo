# TourGo APK Build Guide

## Local Build (on your machine)

### Quick Start - Standalone APK Build
```bash
# Build standalone APK (this creates a real app, not Expo Go)
eas build --platform android --profile production

# Or for faster preview build
eas build --platform android --profile preview
```

### Requirements
- Node.js 18+
- Expo CLI: `npm install -g eas-cli`
- Expo account: https://expo.dev/signup

### Build Steps

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Login to Expo:**
   ```bash
   eas login
   ```

3. **Build APK:**
   ```bash
   # Production build (for Play Store)
   eas build --platform android --profile production
   
   # Preview build (for testing)
   eas build --platform android --profile preview
   ```

4. **Download APK:**
   - Go to https://expo.dev/projects
   - Find your project "TourGo"
   - Click on the build and download the APK

## GitHub Actions - Automatic Builds

### Setup

1. **Add Expo Token to GitHub Secrets:**
   - Go to Settings → Secrets and variables → Actions
   - Click "New repository secret"
   - Name: `EXPO_TOKEN`
   - Value: Get from https://expo.dev/settings/tokens
   - Click "Add secret"

2. **That's it!** The workflow will now:
   - Build on every push to `main` or `develop`
   - Build on pull requests
   - Allow manual triggers via "Run workflow"

### Access Built APKs

**Option 1: From GitHub Actions**
- Go to your repo → Actions → Build APK
- Click the latest workflow run
- Download "tourgo-apk" artifact

**Option 2: Release Downloads**
- Tag your commit: `git tag v1.0.0 && git push origin v1.0.0`
- GitHub automatically creates a Release with the APK

## Folder Structure

```
TourGo/
├── .github/
│   └── workflows/
│       └── build-apk.yml          # GitHub Actions workflow
├── build-output/                   # Local build downloads
├── eas.json                        # EAS build configuration
├── app.json                        # Expo app configuration
└── ...
```

## Troubleshooting

### "Package appears to be invalid"
- Icons must be PNG format
- Minimum 192x192 pixels recommended
- Check app.json icon paths

### "App not installed"
- Ensure Android version support (minSdkVersion 21+)
- Try preview build first: `eas build --platform android --profile preview`

### Expo token not working
- Check token is active: https://expo.dev/settings/tokens
- Regenerate if expired
- Verify in GitHub Secrets (Settings → Secrets)

## Build Output

APKs are automatically stored in `build-output/` folder locally and available as GitHub artifacts.

### Install APK on Device
```bash
adb install build-output/tourgo.apk
```

Or download from GitHub Releases/Artifacts and install manually.
