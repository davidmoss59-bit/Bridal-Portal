# Personata Command Center - Firebase Deployment

## Setup Instructions

### 1. Create Firebase Project
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Create a project" 
3. Name it (e.g., "personata-command-center")
4. Enable Google Analytics (optional)

### 2. Enable Authentication
1. In Firebase Console, go to **Authentication** > **Sign-in method**
2. Enable **Email/Password**
3. Enable **Google** (enter your support email)

### 3. Create Firestore Database
1. Go to **Firestore Database**
2. Click "Create database"
3. Start in **production mode**
4. Choose your region (e.g., us-east1)

### 4. Get Firebase Config
1. Go to **Project Settings** (gear icon)
2. Scroll down to "Your apps"
3. Click the web icon `</>`
4. Register your app (e.g., "personata-web")
5. Copy the `firebaseConfig` object

### 5. Update the Code
1. Open `public/index.html`
2. Find the `firebaseConfig` object (around line 470)
3. Replace with your config:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

### 6. Configure Admin Users
1. In `public/index.html`, find `ADMIN_EMAILS` array (around line 685)
2. Add your admin email addresses:
```javascript
const ADMIN_EMAILS = [
    'david@personata.com',
    'admin@personata.com',
    'your-email@example.com'
];
```

3. In `firestore.rules`, update the admin emails to match:
```javascript
function isAdmin() {
  return request.auth != null && 
         request.auth.token.email in ['david@personata.com', 'admin@personata.com'];
}
```

### 7. Update .firebaserc
Replace `YOUR_PROJECT_ID` with your actual Firebase project ID.

### 8. Deploy via GitHub

#### Option A: GitHub Actions (Recommended)
1. Push this folder to a GitHub repository
2. In Firebase Console, go to **Hosting**
3. Click "Get started" then "Connect to GitHub"
4. Select your repository
5. Firebase will auto-deploy on every push

#### Option B: Manual Deploy
1. Install Firebase CLI: `npm install -g firebase-tools`
2. Login: `firebase login`
3. Deploy: `firebase deploy`

## File Structure
```
personata-firebase/
├── public/
│   └── index.html      # Main application
├── firebase.json       # Firebase hosting config
├── firestore.rules     # Database security rules
├── firestore.indexes.json
├── .firebaserc         # Project configuration
└── README.md           # This file
```

## Features
- **Firebase Authentication**: Email/password + Google Sign-In
- **Firestore Database**: Stores user profiles, vendors, posts, and social accounts
- **Real-time sync**: Data persists across devices
- **Secure**: Users can only access their own data
- **Vendor Contact Tracking**: Mark vendors as contacted with date/time stamp
- **Admin Dashboard**: View all users, vendors, and activity stats

## Admin Features
Admin users (configured in ADMIN_EMAILS) see an extra "🔐 Admin" tab with:
- Total users count
- Total vendors found across all users
- Total vendors contacted
- Total posts saved
- User table showing each user's activity and stats

## Data Structure (Firestore)
```
users/{userId}
├── name: string
├── email: string
├── territory: {zip, state, stateName, radius, city}
├── posts: array
├── vendors: array
│   └── each vendor has: name, address, phone, website, type, contacted, contactedAt
├── socialAccounts: {facebook, instagram, tiktok, x}
├── createdAt: timestamp
└── updatedAt: timestamp
```

## Vendor Contact Tracking
Each vendor card has a checkbox to mark as "Contacted":
- ✅ Green border when contacted
- 📅 Shows date contacted
- Data saved automatically to Firestore
- Admins can see contact stats for all users
