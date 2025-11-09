# Aarogya-Setu

A lightweight healthcare web prototype that lets patients sign up, view a dashboard, see their doctor’s profile, and upload basic medical records. Built with HTML, CSS, and a sprinkle of JS. You can keep it static or connect a simple backend like Firebase or Supabase.

> ⚠️ Name note: “Aarogya Setu” is the name of an official Indian government app. If this is for learning or a hackathon, that’s fine. For public release, use a distinct project name.

---

## Features

- Patient sign up and login
- Doctor sign up and profile page
- Patient dashboard with quick links
- Upload and view medical documents
- Simple responsive UI ready for extension
- Optional backend integration for auth, database, and file storage

---

## Tech Stack

- Frontend: HTML, CSS, JavaScript
- Optional backend:
  - **Option A:** Firebase Authentication, Firestore, Storage
  - **Option B:** Supabase Auth, Database, Storage

---

## Project Structure

/
├─ index.html # Landing or login
├─ patient-signup.html
├─ doctor-signup.html
├─ dashboard.html
├─ doctor-profile.html # Doctor details visible to patient
├─ upload-records.html
├─ assets/
│ ├─ css/ # Stylesheets
│ ├─ img/ # Images, logos
│ └─ js/ # Client-side JS
├─ README.md
└─ LICENSE

Adjust the filenames if yours differ.

---

## Getting Started

### 1) Run locally

You can open `index.html` directly in a browser. For nicer routing and CORS-free file access, use a tiny server:

- **VS Code Live Server** extension, or
- Python: `python -m http.server 5500` then open `http://localhost:5500`

### 2) Configure environment

If you plan to connect a backend, create a config file:

/assets/js/config.example.js # copy to config.js and fill values

Do not commit real keys. Add this to `.gitignore`:

---

## Backend Integration

You can keep it static for a demo. If you want real auth and data, pick one:

### Option A: Firebase

1. Create a Firebase project at console.firebase.google.com  
2. Add a Web app and copy the SDK config
3. Enable:
   - Authentication → Sign-in method → Email/Password
   - Firestore Database
   - Storage
4. Create `assets/js/config.js`:

```js
// assets/js/config.js
// Never commit secrets in production
export const firebaseConfig = {
  apiKey: "YOUR_KEY",
  authDomain: "YOUR_DOMAIN.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_BUCKET.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
<!-- Firebase SDKs -->
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-firestore-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-storage-compat.js"></script>

<script type="module">
  import { firebaseConfig } from "./assets/js/config.js";
  firebase.initializeApp(firebaseConfig);

  const auth = firebase.auth();
  const db = firebase.firestore();
  const storage = firebase.storage();

  // Example: form signup
  async function signup(email, password) {
    const { user } = await auth.createUserWithEmailAndPassword(email, password);
    await db.collection("patients").doc(user.uid).set({ email, createdAt: Date.now() });
  }

  // Example: file upload
  async function uploadRecord(file, userId) {
    const ref = storage.ref().child(`records/${userId}/${file.name}`);
    await ref.put(file);
    const url = await ref.getDownloadURL();
    await db.collection("records").add({ userId, url, createdAt: Date.now() });
  }
</script>
<!-- Firebase SDKs -->
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-firestore-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/11.0.1/firebase-storage-compat.js"></script>

<script type="module">
  import { firebaseConfig } from "./assets/js/config.js";
  firebase.initializeApp(firebaseConfig);

  const auth = firebase.auth();
  const db = firebase.firestore();
  const storage = firebase.storage();

  // Example: form signup
  async function signup(email, password) {
    const { user } = await auth.createUserWithEmailAndPassword(email, password);
    await db.collection("patients").doc(user.uid).set({ email, createdAt: Date.now() });
  }

  // Example: file upload
  async function uploadRecord(file, userId) {
    const ref = storage.ref().child(`records/${userId}/${file.name}`);
    await ref.put(file);
    const url = await ref.getDownloadURL();
    await db.collection("records").add({ userId, url, createdAt: Date.now() });
  }
</script>
Option B: Supabase

Create a project at supabase.com

Get the Project URL and anon key

Create assets/js/config.js:
export const supabaseConfig = {
  url: "https://YOUR_PROJECT.supabase.co",
  anonKey: "YOUR_ANON_KEY"
};
Include Supabase and init:
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script type="module">
  import { supabaseConfig } from "./assets/js/config.js";
  const supabase = window.supabase.createClient(supabaseConfig.url, supabaseConfig.anonKey);

  // Example: email signup
  async function signup(email, password) {
    const { data, error } = await supabase.auth.signUp({ email, password });
    if (error) throw error;
  }

  // Example: insert a record
  async function saveProfile(userId, profile) {
    const { error } = await supabase.from("patients").upsert({ id: userId, ...profile });
    if (error) throw error;
  }
</script>
Create tables: patients(id pk), records(id uuid pk, user_id, url, created_at) and set Storage policies so users can only access their own files.
Page Wiring

patient-signup.html and doctor-signup.html

Hook the form submit to your signup() function

After signup, redirect to dashboard.html

dashboard.html

On load, check auth.currentUser (Firebase) or supabase.auth.getUser()

Show user info, recent records, link to upload-records.html and doctor-profile.html

doctor-profile.html

Render doctor data from Firestore/Supabase or static JSON for demo

Fields: name, specialization, experience, clinic address, availability, consultation modes, contact

upload-records.html

File input → upload to Storage → save record URL in DB

List uploaded records

Security and Privacy

Never commit real API keys or service account credentials

Use per-user security rules

Firebase: Firestore and Storage rules that scope by request.auth.uid

Supabase: Row Level Security with policies

Do not store sensitive PHI without consent or encryption

This is a prototype, not a medical device

Deployment

GitHub Pages: Build is static. Push to main, enable Pages in repo settings

Vercel / Netlify: Import repo and deploy as static site

Scripts

If you add a simple toolchain, you can keep these in package.json:
{
  "name": "aarogya-setu",
  "private": true,
  "scripts": {
    "start": "http-server -p 5500 -c-1 .",
    "format": "prettier -w ."
  },
  "devDependencies": {
    "http-server": "^14.1.1",
    "prettier": "^3.3.3"
  }
}
npm install
npm run start
