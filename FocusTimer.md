# 🍅 FocusTimer

A Pomodoro-style productivity app built with **React Native + Expo**. Create tasks, run timed focus sessions, track streaks and stats — all stored locally on your device with full dark mode support.

---

## 📱 Screenshots & Video

https://github.com/user-attachments/assets/2b6e41fa-a19e-4788-9e9a-6e9484795b86

<img width="1080" height="2412" alt="WhatsApp Image 2026-06-20 at 5 56 34 PM" src="https://github.com/user-attachments/assets/d7215433-078a-41b1-af58-2e12f54a5bb0" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 34 PM (4)" src="https://github.com/user-attachments/assets/2abc6cab-30c1-446a-a071-92e0592bb36f" />
<img width="270" height="1178" alt="WhatsApp Image 2026-06-20 at 5 56 34 PM (3)" src="https://github.com/user-attachments/assets/4e9fba90-bf4a-41ef-8b48-824f13627ff1" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 34 PM (2)" src="https://github.com/user-attachments/assets/102ed09d-dde2-4b16-aaa3-e680830a6406" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 34 PM (1)" src="https://github.com/user-attachments/assets/a17e3931-0a69-4e9c-a701-d7ce053fad83" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 33 PM" src="https://github.com/user-attachments/assets/096d93fe-03ce-4d10-82ed-5bb3a35359dc" />
<img width="415" height="1280" alt="WhatsApp Image 2026-06-20 at 5 56 33 PM (5)" src="https://github.com/user-attachments/assets/de11f5be-fc4b-4562-8bfc-6c809724f057" />
<img width="1080" height="2412" alt="WhatsApp Image 2026-06-20 at 5 56 33 PM (4)" src="https://github.com/user-attachments/assets/182d9524-0a1d-4dd4-8381-11dffa08b7ca" />
<img width="409" height="1280" alt="WhatsApp Image 2026-06-20 at 5 56 33 PM (3)" src="https://github.com/user-attachments/assets/18353e5c-043e-4f3e-8e1c-7b4b48e00981" />
<img width="1080" height="2412" alt="WhatsApp Image 2026-06-20 at 5 56 33 PM (2)" src="https://github.com/user-attachments/assets/fcb88337-8f8f-4783-bdff-a24459681c14" />
<img width="270" height="1227" alt="WhatsApp Image 2026-06-20 at 5 56 33 PM (1)" src="https://github.com/user-attachments/assets/d99502d3-988f-40f0-961f-ca80e41365ba" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 32 PM" src="https://github.com/user-attachments/assets/86bafec3-a270-48ea-8273-9afab19c9baf" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 32 PM (2)" src="https://github.com/user-attachments/assets/95b2f438-ed0b-4d95-9a96-d2470b78524c" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 32 PM (1)" src="https://github.com/user-attachments/assets/78be3d1b-73fa-4e76-aade-9955cbc66d21" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 30 PM" src="https://github.com/user-attachments/assets/36c0eac1-5d0f-4a77-8e0e-a4d2dba9df11" />
<img width="716" height="1600" alt="WhatsApp Image 2026-06-20 at 5 56 30 PM (1)" src="https://github.com/user-attachments/assets/ae6d45fb-94e9-416c-8bca-c4f49411fbaa" />






> Add your screenshots here after final build

---

## ✨ Features

### 📝 Tasks
- Create, edit and delete focus tasks
- Mark tasks complete with animated checkbox
- Filter by All / Active / Done
- Session count per task
- Animated card entrance on load
- Time-based greeting (morning / afternoon / evening)

### ⏱️ Timer
- Pomodoro countdown with animated progress ring
- Duration picker — 15 / 25 / 50 min or custom (1–180 min)
- Pause, Resume and Reset controls
- Auto 5-minute break mode after each session
- Background timer — stays accurate when app is minimised
- Haptic feedback on every interaction
- Push notification when session ends (real APK build only)

### 📊 Stats
- Sessions and minutes tracked today
- Daily streak counter with motivational banner
- 7-day bar chart of focus sessions
- All-time total focus hours and sessions
- Recent session history list (last 20)
- Stats update in real time after every session

### ⚙️ Settings
- Dark mode / Light mode / System theme toggle
- Persistent theme preference saved to device
- App info and how-it-works guide

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| Framework | React Native 0.74 (Expo SDK 51 managed workflow) |
| Navigation | React Navigation v6 (Bottom Tabs + Stack) |
| Storage | @react-native-async-storage/async-storage |
| Notifications | expo-notifications |
| Haptics | expo-haptics |
| Theme System | React Context API (light / dark / system) |
| Build Tool | EAS Build (Expo Application Services) |
| Language | JavaScript ES2022 |

---

## 📁 Project Structure

```
FocusTimer/
├── App.js                          # Entry point with providers
├── app.json                        # Expo config + build settings
├── eas.json                        # EAS build profiles
├── assets/
│   ├── icon.png                    # App icon (1024×1024)
│   ├── notification-icon.png       # Notification icon (white, 96×96)
│   ├── splash-icon.png             # Splash screen
│   └── adaptive-icon.png           # Android adaptive icon
└── src/
    ├── context/
    │   └── ThemeContext.js         # Dark/light/system theme context
    │   └── TimerContext.js         # Timer context
    ├── navigation/
    │   └── RootNavigator.js        # Tab + stack navigation setup
    ├── screens/
    │   ├── TasksScreen.js          # Task list with filters + animations
    │   ├── TimerScreen.js          # Pomodoro timer with progress ring
    │   ├── StatsScreen.js          # Session stats + 7-day chart
    │   └── SettingsScreen.js       # Theme toggle + app info
    ├── components/
    │   ├── TaskCard.js             # Animated task card with edit/delete
    │   ├── AddTaskModal.js         # Bottom sheet to add tasks
    │   ├── EditTaskModal.js        # Bottom sheet to edit tasks
    │   └── EmptyState.js           # Empty list placeholder
    ├── storage/
    │   └── index.js                # All AsyncStorage CRUD helpers
    ├── services/
    │   └── notifications.js        # Push notification setup + handlers
    └── theme/
        └── index.js                # Colors, spacing, typography, shadows
```

---

## 🚀 Getting Started

### Prerequisites

- Node.js 18+
- Expo Go app on your Android device
- Both device and laptop on the same WiFi

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/FocusTimer.git

# Navigate into the project
cd FocusTimer

# Install dependencies
npm install
```

### Run in development (Expo Go)

```bash
# Start the development server
npx expo start

# Scan the QR code with Expo Go on your Android phone
# The app hot-reloads every time you save a file
```

### Run on specific platform

```bash
# Android only
npx expo start --android

# iOS only
npx expo start --ios

# Web (limited — no haptics/notifications)
npx expo start --web
```

---

## 📦 Building the APK

We use **EAS Build** (Expo Application Services) to generate real APK/IPA files. Builds run on Expo's cloud servers — no Android Studio or Xcode needed.

### First time setup

```bash
# Install EAS CLI globally
npm install -g eas-cli

# Login to your Expo account (create one free at expo.dev)
eas login

# Configure EAS for this project (run once)
eas build:configure
```

### Android builds

```bash
# Preview build — fast, for testing on real device (~68MB)
# Generates a downloadable APK link
eas build -p android --profile preview

# Production build — optimised, smaller (~20MB), for Play Store
eas build -p android --profile production

# Check build status / download link
eas build:list
```

### iOS builds

```bash
# Preview build — requires Apple Developer account ($99/year)
eas build -p ios --profile preview

# Production build — for App Store submission
eas build -p ios --profile production
```

### Install APK on Android

After the build finishes EAS gives you a download link. Either:
- Open the link on your phone browser → download → install
- Or run: `eas build:run -p android` (installs via USB)

> **Note:** When installing outside the Play Store, Android will ask to allow installs from unknown sources. Go to Settings → Security → enable it.

---

## 🔔 Notifications Setup

Notifications require a real device build (not Expo Go). They are already configured — just build the APK and they work automatically.

The notification channel is set up in `src/services/notifications.js`:
- Channel name: `Focus Timer`
- Priority: MAX
- Vibration pattern: enabled
- Sound: enabled

For the notification icon, add a white PNG at `assets/notification-icon.png` (96×96, transparent background) and reference it in `app.json`:

```json
"plugins": [
  ["expo-notifications", {
    "icon": "./assets/notification-icon.png",
    "color": "#6C63FF"
  }]
]
```

---

## 🧠 Key Technical Decisions

**Context API for theming**
A `ThemeContext` wraps the entire app providing `colors`, `isDark`, and `setTheme` to every component. Theme preference persists to AsyncStorage so it survives app restarts.

**AsyncStorage with typed wrappers**
All storage logic lives in `src/storage/index.js` with named exports (`getTasks`, `addTask`, `editTask`, `deleteTask`, `addSession`, `getStreak` etc). Screens never call AsyncStorage directly — easy to swap to MMKV later.

**Background timer via AppState**
When the app is backgrounded we record `Date.now()`. On foreground return we subtract elapsed time from the countdown — the timer stays accurate even if the phone locks for 20 minutes.

**Task edit syncs sessions**
When you rename a task, `editTask()` also updates `taskTitle` in all stored sessions for that task — so Stats always shows the current name.

**EAS Build for notifications**
`expo-notifications` requires a development build for full Android notification support. Expo Go removed push notification support in SDK 53+. We use `eas build --profile preview` to generate a real APK with proper notification channels.

## 📦 Dependencies

```json
{
  "expo": "~51.0.0",
  "react": "18.2.0",
  "react-native": "0.74.5",
  "@react-navigation/native": "^6.0.0",
  "@react-navigation/bottom-tabs": "^6.0.0",
  "@react-navigation/stack": "^6.0.0",
  "@react-native-async-storage/async-storage": "^1.0.0",
  "react-native-gesture-handler": "^2.0.0",
  "react-native-screens": "^3.0.0",
  "react-native-safe-area-context": "^4.0.0",
  "expo-haptics": "^13.0.0",
  "expo-notifications": "^0.28.0",
  "expo-device": "^6.0.0",
  "eas-cli": "latest"
}
```

---

## 👤 Author

Built by **Zain**.

- GitHub: [@mzaintariqdev](https://github.com/mzaintariqdev)
- LinkedIn: [Zain Tariq](https://www.linkedin.com/in/muhammad-zain-tariq)

---

## 📄 License

MIT — free to use and modify.
