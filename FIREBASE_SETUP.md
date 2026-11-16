# 🔥 Firebase Setup & Faculty Approval Workflow Guide

This document explains how to connect **ManageAll 2.0** to Firebase so that:
1. **Faculty registers** with their official email & password.
2. **A verification email** is automatically sent to their Gmail.
3. **Faculty clicks the link** in Gmail to verify their email.
4. **The request is submitted to the Administrator's queue**.
5. **Admin reviews & approves** the faculty from the *Approvals* dashboard.
6. **Only after approval**, the login window unlocks for the faculty.

---

## 📋 Step 1: Create a Firebase Project

1. Go to the [Firebase Console](https://console.firebase.google.com/).
2. Click **Add project** (or **Create a project**).
3. Name your project (e.g., `ManageAll-Portal`).
4. (Optional) Disable Google Analytics or keep it enabled, then click **Create Project**.

---

## 🔐 Step 2: Enable Email/Password Authentication

1. In the Firebase console left sidebar, navigate to **Build** &rarr; **Authentication**.
2. Click **Get Started**.
3. Under the **Sign-in method** tab:
   - Click on **Email/Password**.
   - Toggle **Enable** to ON.
   - (Leave *Email link (passwordless)* OFF).
   - Click **Save**.
4. (Optional) Under the **Templates** tab, you can customize the **Email address verification** message that faculty will receive in Gmail.

---

## 🗄️ Step 3: Create Cloud Firestore Database

1. In the left sidebar, navigate to **Build** &rarr; **Firestore Database**.
2. Click **Create database**.
3. Choose a location closest to you (e.g., `asia-south1` for India or `us-central1`).
4. Select **Start in production mode** (or test mode) and click **Create**.

### Add Firestore Security Rules:
Go to the **Rules** tab in Firestore and replace with the following rules, then click **Publish**:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // User / Faculty profiles
    match /users/{userId} {
      // Any authenticated user can read profiles (needed for verification checks)
      allow read: if request.auth != null;
      
      // User can create their own document during registration
      allow create: if request.auth != null && request.auth.uid == userId;
      
      // Admin can update any user; users can only update their own verified status
      allow update: if request.auth != null && (
        request.auth.uid == userId ||
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin' ||
        request.auth.token.email == 'admin@manageall.edu'
      );
      
      // Only admins can delete user records
      allow delete: if request.auth != null && (
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin' ||
        request.auth.token.email == 'admin@manageall.edu'
      );
    }
    
    // Default rule for attendance, fees, and students
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

## 💻 Step 4: Register Web App & Get Configuration

1. In Firebase Console, click the ⚙️ **Gear icon** next to *Project Overview* &rarr; **Project settings**.
2. Scroll down to the **Your apps** card and click the **Web icon** (`</>`).
3. App nickname: `ManageAll Web`.
4. Click **Register app** (no need to check Firebase Hosting).
5. Firebase will present your configuration object:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyDEV-UB7feRgnBNm8lzoB4MrYLlyL59k1s",
  authDomain: "manageall-9750a.firebaseapp.com",
  projectId: "manageall-9750a",
  storageBucket: "manageall-9750a.firebasestorage.app",
  messagingSenderId: "541461529175",
  appId: "1:541461529175:web:87f131481f6e2ff17a1912"
};
```

---

## ✏️ Step 5: Config Connected to `index.html` ✅

Your Firebase project config is now **active** in `index.html`:

```javascript
// ============================================================
//  FIREBASE CONFIGURATION (Connected to manageall-9750a)
// ============================================================
const firebaseConfig = {
  apiKey: "AIzaSyDEV-UB7feRgnBNm8lzoB4MrYLlyL59k1s",
  authDomain: "manageall-9750a.firebaseapp.com",
  projectId: "manageall-9750a",
  storageBucket: "manageall-9750a.firebasestorage.app",
  messagingSenderId: "541461529175",
  appId: "1:541461529175:web:87f131481f6e2ff17a1912"
};
```

---

## 👑 Step 6: Create the First Administrator Account

There are two easy ways:

### Option A: Use the Built-in Master Admin
The portal includes a pre-configured master admin:
- **Email:** `admin@manageall.edu`
- **Password:** `admin123`

You can log in directly with these credentials at any time to access the **Approvals** dashboard and approve faculty members.

### Option B: Create a Custom Admin in Firebase
1. Register a new account via the **Faculty Registration** tab with your email (e.g. `principal@apex.edu`).
2. Go to the **Firebase Console** &rarr; **Firestore Database** &rarr; `users` collection.
3. Click on the document for your user.
4. Change:
   - `role`: from `"faculty"` to `"admin"`
   - `status`: from `"pending_verification"` to `"approved"`
   - `emailVerified`: `true`
5. Click **Save**. You are now a full system administrator!

---

## 🔄 The Complete Faculty Lifecycle

| Stage | Action | System Status | Portal Access |
|---|---|---|---|
| **1. Registration** | Faculty enters Name, Dept, Email, Password | `pending_verification` | 🔒 Blocked |
| **2. Gmail Verification** | Faculty opens Gmail and clicks verification link | `email_verified` / `pending_approval` | 🔒 Blocked (Notice: awaiting admin approval) |
| **3. Admin Approval** | Admin logs in, opens **Approvals**, clicks **Approve** | `approved` | 🔓 **Unlocked** |
| **4. Login** | Faculty enters verified email and password | `approved` | ✅ Full Faculty Dashboard |

---

## 🧪 Testing in Offline Demo Mode

If you haven't set up Firebase yet:
1. Open `index.html` in your browser.
2. A yellow banner notifies you that fallback simulation mode is active.
3. Switch to **Faculty Registration** and register a test faculty (e.g., `dr.sharma@apex.edu`).
4. Try to sign in — the system will prompt you that Gmail verification is required, with a simulation button.
5. Click **Simulate Gmail Verification Link**.
6. Try to sign in again — the system will inform you that the account is awaiting Admin approval.
7. Click **Fill** on the demo admin box (`admin@manageall.edu` / `admin123`) and log in.
8. Navigate to the **Approvals** tab in the sidebar.
9. Click **Approve** next to `dr.sharma@apex.edu`.
10. Log out, then log in as `dr.sharma@apex.edu` — access granted!
