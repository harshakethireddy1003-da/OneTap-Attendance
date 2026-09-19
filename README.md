# OneTap Attendance

An Android app for fast, tamper-resistant classroom attendance. Teachers start a timed session and share a 6-character code; students mark themselves present from their phones. Location checks and an offline-mode rule make it harder to mark attendance for someone who isn't in the room.

**Version:** 1.4 · **Package:** `com.onetap.app` · **Min SDK:** 26 (Android 8.0) · **Target SDK:** 34

## Features

### Roles
- **Student** – join a session with its code, mark attendance, view attendance by subject.
- **Teacher** – create timed sessions, watch active sessions, review session history and per-student attendance, export reports.
- **Admin** – approve or reject teacher sign-ups, view approved teachers and all students.

Teachers register as `pending` and can only use the app once an admin marks them `approved`.

### Attendance rules
- **Session codes** – each session gets a 6-character code and an end time based on the duration the teacher enters (in minutes).
- **Location boundary** – the classroom is defined as a rectangular boundary around a center point. If a student marks attendance from outside it, the record is set to absent with the reason "Outside classroom boundary".
- **Offline marking** – students can mark attendance without a connection. Records are stored locally (Room) and uploaded later by the sync manager.
- **Anti-proxy check** – if a device comes back online *before* the session ends, the offline record is treated as absent. The app also records whether airplane mode, Wi-Fi, or mobile data was off.
- **De-duplication** – sync skips records for a student/session pair that already exists in Firebase.

### Reports and updates
- **Excel export** – subject-level attendance reports (`.xlsx`) via Apache POI, saved to Downloads.
- **Google Drive upload** – exported reports can be uploaded to Drive.
- **In-app updates** – checks Firebase Remote Config for a newer version and downloads/installs the APK.

## Tech stack

| Area | Technology |
|---|---|
| Language | Java 11 |
| Build | Gradle (Kotlin DSL), Android Gradle Plugin 8.5.2 |
| Backend | Firebase Auth, Realtime Database, Firestore, Remote Config |
| Local storage | Room 2.6.1 |
| UI | Material Components, RecyclerView, ViewPager2, CardView, Lottie, Glide, CircleImageView |
| Reports | Apache POI 5.2.5 (XLSX) |
| Google APIs | Play Services Auth, Google Drive API v3 |
| Testing | JUnit 4, Mockito, Espresso, Room testing |

## Project structure

```
app/src/main/java/com/onetap/app/
├── activities/   Screens: login, signup, dashboards, sessions, attendance
├── adapters/     RecyclerView adapters
├── database/     Room database and DAO for offline attendance
├── firebase/     Auth, session, attendance and admin managers
├── models/       User, Teacher, Student, Session, Attendance, OfflineAttendance
├── sync/         SyncManager: uploads offline records
├── utils/        Constants, location math, Excel export, Drive upload, validation
└── UpdateManager.java
```

## Getting started

### Prerequisites
- Android Studio (recent stable release) with JDK 11 or newer
- An Android device or emulator running Android 8.0+
- A Firebase project

### Firebase setup
1. Create a Firebase project and add an Android app with package name `com.onetap.app`.
2. Download `google-services.json` and place it in `app/`.
3. Enable **Authentication** (Email/Password), **Realtime Database**, and **Firestore**.
4. Set security rules for your databases so users can only read and write what they should.
5. In **Remote Config**, add two parameters to use the in-app updater:
   - `latest_version_code` (number) – the newest `versionCode` you have published
   - `update_url` (string) – direct download URL of the APK

### Google Drive upload (optional)
Drive upload needs an OAuth client configured in Google Cloud for the app's package name and signing key, with the Drive API enabled.

### Build and run
```bash
git clone https://github.com/harshakethireddy1003-da/OneTap-Attendance.git
cd OneTap-Attendance
./gradlew assembleDebug
```
Or open the project in Android Studio and press **Run**.

### Tests
```bash
./gradlew test                    # unit tests
./gradlew connectedAndroidTest    # instrumented tests (device/emulator required)
```

## Permissions

| Permission | Why |
|---|---|
| `INTERNET`, `ACCESS_NETWORK_STATE`, `ACCESS_WIFI_STATE` | Firebase sync and connectivity checks |
| `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` | Classroom boundary check |
| `POST_NOTIFICATIONS` | Notifications on Android 13+ |
| `REQUEST_INSTALL_PACKAGES` | In-app APK updates |
| `READ/WRITE_EXTERNAL_STORAGE` (API 28 and below) | Saving Excel reports |
