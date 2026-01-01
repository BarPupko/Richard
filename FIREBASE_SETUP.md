# Firebase Setup Guide

This document explains how to set up Firebase Realtime Database security rules for the Richard Game project.

## Current Status

The application now works **offline-first** with localStorage fallback. Even if Firebase is not configured or has permission errors, the app will:
- ✅ Save user names locally
- ✅ Track high scores locally
- ✅ Allow users to change their name
- ✅ Maintain all data across sessions

Firebase is used for **optional cloud sync** when properly configured.

## Firebase Security Rules

To enable Firebase cloud sync and leaderboards, you need to configure the security rules in your Firebase Console.

### Step 1: Access Firebase Console

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select your project: **richardgame-67766**
3. Click on **Realtime Database** in the left sidebar
4. Click on the **Rules** tab

### Step 2: Update Security Rules

Replace the existing rules with the following:

```json
{
  "rules": {
    "users": {
      ".read": true,
      "$userId": {
        ".write": true
      }
    }
  }
}
```

**Important Changes:**
- `.read: true` at the users level allows all users to read the entire users list (required for leaderboards)
- `.write: true` for individual user nodes allows anyone to write to their own data

**Note:** These rules allow anyone to read all user data (needed for leaderboards) and write to any user node. This is appropriate for a casual game where users don't sign in. For more security, see the alternative rules below.

### Step 3: Publish Rules

Click the **Publish** button to apply the new rules.

## Alternative Security Rules (More Secure)

If you want more control, you can use these rules that only allow users to read/write their own data:

```json
{
  "rules": {
    "users": {
      "$userId": {
        ".read": true,
        ".write": "newData.child('userId').val() === $userId || !data.exists()"
      }
    }
  }
}
```

However, this requires implementing proper user ID validation in the client code.

## Testing the Setup

After updating the rules:

1. Open the browser console (F12)
2. Clear localStorage and sessionStorage:
   ```javascript
   localStorage.clear();
   sessionStorage.clear();
   ```
3. Refresh the page
4. Enter your name when prompted
5. Check the console - you should see no Firebase errors
6. Go to Firebase Console > Realtime Database > Data tab
7. You should see your user data under `users/user_[your_id]`

## How Data Storage Works

### Local Storage (Always Available)
- **localStorage.richardGameUserId**: Unique user ID
- **localStorage.richardUserProfile**: User name and high scores (permanent)

### Session Storage (Per Tab)
- **sessionStorage.richardUserData**: Shared data passed between pages

### Firebase Realtime Database (Cloud Sync + Leaderboards)
```
users/
  user_[unique_id]/
    name: "Player Name"
    createdAt: timestamp
    updatedAt: timestamp
    highScores/
      fetchGame: 150        (higher is better)
      boneCollector: 45     (lower time is better)
      lastPlayed: timestamp
```

## Leaderboard Feature

Both games now display a **Top 10 Leaderboard** when you finish:
- Shows the best 10 players from Firebase
- Highlights your position with a yellow background
- Medal icons for top 3 (🥇🥈🥉)
- **Fetch Game**: Sorted by highest score
- **Bone Collector**: Sorted by fastest time (lowest seconds)
- Falls back to "offline mode" message if Firebase is unavailable

## Troubleshooting

### Permission Denied Errors
If you see `PERMISSION_DENIED` errors:
1. The app will continue to work offline (data saved to localStorage)
2. Check Firebase security rules are set correctly
3. Verify your Firebase configuration in the HTML files

### Data Not Syncing
If data isn't syncing to Firebase:
1. Check browser console for errors
2. Verify internet connection
3. Check Firebase Console to see if data appears
4. The app will still save everything locally

### Lost Data
User data is stored in:
1. **localStorage** (survives browser restarts)
2. **Firebase** (accessible from any device if synced)

Clearing browser data will delete localStorage but Firebase data persists.

## Development vs Production

For development (current setup):
- Open read/write rules allow easy testing
- No authentication required
- Simple user experience

For production (recommended):
- Implement proper authentication (Firebase Auth)
- Restrict write access to authenticated users only
- Add data validation rules
- Implement rate limiting

## Firebase Configuration

The current Firebase config is in:
- [richard-index.html](richard-index.html) (lines 262-271)
- [bone-collector-game.html](bone-collector-game.html) (lines 229-240)
- [fetch-game.html](fetch-game.html) (lines 217-226)

All three files use the same configuration.
