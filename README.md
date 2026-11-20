# ManageAll 2.0

**A student attendance and fee management portal for colleges.**

ManageAll 2.0 gives faculty and administrators one place to record attendance, track fee collection, handle student admissions and review reports. It is a single-page web app (one `index.html`) with Firebase email verification and an admin approval workflow for faculty accounts. If Firebase isn't configured, it falls back to an offline demo mode so you can try everything locally.

![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6-F7DF1E?logo=javascript&logoColor=black)
![Tailwind CSS](https://img.shields.io/badge/Tailwind-CDN-06B6D4?logo=tailwindcss&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-Auth%20%26%20Firestore-FFCA28?logo=firebase&logoColor=black)

> **Demo data notice:** all the data in this project is **dummy data**. The students, attendance records, fee records, course mentors and activity feed are generated sample data for demonstration. They do not describe real people, and any names, emails and phone numbers are made up.

---

## Features

### Access and onboarding
- **Faculty registration with email verification**: faculty register, receive a verification link by email, then wait for an administrator to approve them. The login only unlocks after approval.
- **Admin approvals dashboard**: administrators review pending faculty and approve or reject them.
- **Public student admission form**: applicants submit an application without signing in; faculty or admins approve it from **Admission Approvals**. Approval adds the student to the roster and sets up their fee schedule for the chosen course.
- **Password reset** by email (when Firebase is connected).

### Day-to-day management
- **Dashboard**: summary cards, a 7-day attendance trend, fee collection summary and a recent activity feed.
- **Students**: searchable roster filtered by course, add/edit/delete, a student profile drawer and **CSV export**.
- **Attendance**: mark each student present, absent or late, or mark everyone at once, with a daily summary and a monthly calendar view.
- **Fees**: per-student fee records with payment history and the course fee structure.
- **Fines and penalties**: impose or remove fines from preset categories (late tuition fee, attendance shortage, library overdue, lab damage, exam late fee, disciplinary) or a custom amount.
- **Reports and analytics**: attendance report, fee collection report and a **defaulter list** (attendance below 75% or pending fees above ₹10,000), each exportable to CSV.
- **Settings**: institution name, academic year, and a reset to sample data.

### Roles
| Capability | Faculty | Administrator |
| --- | :---: | :---: |
| Record attendance, manage students, view reports | ✅ | ✅ |
| Approve student admissions | ✅ | ✅ |
| Approve or reject faculty accounts | | ✅ |
| Record or edit fee payments | | ✅ |
| Impose or remove fines | | ✅ |

### Courses and fees
Seven courses are built in, with these total fees per student:

| Course | Fee (₹) | Course | Fee (₹) |
| --- | ---: | --- | ---: |
| BCA | 45,000 | MBA | 65,000 |
| BBA | 40,000 | MCA | 55,000 |
| B.Com | 35,000 | BSc | 38,000 |
| BE | 85,000 | | |

---

## Tech stack

| Area | Technology |
| --- | --- |
| App | Single-file HTML, CSS and vanilla JavaScript (`index.html`, no build step) |
| Styling | Tailwind CSS (CDN), Google Fonts (Hanken Grotesk, Inter, JetBrains Mono), Material Symbols |
| Auth | Firebase Authentication (email and password, email verification) |
| Database | Cloud Firestore for faculty accounts and student admission applications |
| Local storage | Browser `localStorage` for students, attendance, fees and activity |
| Hosting | Firebase Hosting (configured in `firebase.json`) |

> **Where data lives:** with Firebase connected, faculty accounts (`users`) and admission applications (`student_admissions`) are stored in Firestore. Students, attendance, fee records and the activity log are kept in the browser's `localStorage`, so they stay on the device and browser where they were created.

---

## Project structure

```
ManageAll2.0/
├── index.html          # The entire app: markup, styles and logic
├── FIREBASE_SETUP.md   # Step-by-step Firebase and approval-workflow guide
├── firebase.json       # Firebase Hosting config (serves the repo root)
└── .firebaserc         # Firebase project alias
```

---

## Getting started

### Quick start (offline demo mode)

No installation is needed.

1. Clone the repository:
   ```bash
   git clone https://github.com/Saksham07518/ManageAll2.0.git
   cd ManageAll2.0
   ```
2. Open `index.html` in a browser, or serve the folder:
   ```bash
   python3 -m http.server 8000
   ```
   then visit `http://localhost:8000`.
3. A banner explains that Firebase isn't configured and the app is running in simulation mode. The portal loads with dummy students, attendance and fee data (15 sample students across five courses), and the sign-in page offers demo accounts you can fill in with one click.

In demo mode, the email verification step is simulated with a button, so you can walk through the whole register, verify, approve and log in flow without sending real email.

### Connect your own Firebase project

1. Create a Firebase project, enable **Email/Password** authentication and create a **Cloud Firestore** database.
2. Register a web app in the Firebase console and copy its config.
3. Replace the `firebaseConfig` object near the top of the main `<script>` in `index.html` with your values.
4. Add Firestore security rules appropriate for your deployment. [`FIREBASE_SETUP.md`](FIREBASE_SETUP.md) walks through every step, including rules and creating your first administrator.
5. Reload the app. The configuration banner disappears once Firebase initialises.

### Deploy to Firebase Hosting

```bash
npm install -g firebase-tools
firebase login
firebase use <your-project-id>
firebase deploy --only hosting
```

`firebase.json` serves the repository root and excludes the setup docs and config files from the deployed site.

---

## The faculty lifecycle

| Stage | What happens | Portal access |
| --- | --- | --- |
| 1. Registration | Faculty submits name, department, email and password | Blocked |
| 2. Email verification | Faculty clicks the link sent to their inbox | Blocked, awaiting admin approval |
| 3. Admin approval | An administrator approves the request under **Approvals** | Unlocked |
| 4. Login | Faculty signs in with their verified email | Full faculty dashboard |

---

## Security notes

This project is built for learning and demonstration. Before using it with real student data:

- Replace the built-in demo administrator and demo faculty logins, which are defined in the client-side code.
- Tighten the Firestore security rules so users can only read and write what their role allows.
- Move student, attendance and fee data from `localStorage` to a server-side database so it is shared, backed up and access-controlled.

---

## Author

**Saksham**: [GitHub](https://github.com/Saksham07518)
