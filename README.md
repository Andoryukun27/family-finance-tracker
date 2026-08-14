# Family Ledger

A lightweight personal finance tracker, self-hosted as a single HTML file. No backend server, no build step — open it in any browser, on any device.

**Live app:** https://finance-tracker-2k26.netlify.app/

## Features

- **Dashboard** — passbook-style summary of monthly income, expenses, net, and savings rate
- **Categories** — customizable groups: Income, Bills, Subscriptions, Expenses, Savings & Investments, Debt
- **Transaction Log** — add, view, and delete transactions (with confirmation dialogs)
- **Monthly Budget Overview** — spend vs. income breakdown by month
- **Savings Tracker** — goal cards with progress bars
- **Debt Tracker** — balances and percent paid off
- **No-Spend Day Tracker** — calendar for marking no-spend days
- **Financial Health** — a simple score based on savings rate and debt-to-income ratio
- **Cross-device sync** — optional, via a free Firebase Firestore project

## Getting Started

1. Open `finance-tracker.html` directly in a browser, or visit the live link above.
2. Data saves automatically to that browser (`localStorage`) — no account needed for single-device use.

## Enabling Sync Across Devices

1. Create a free project at [console.firebase.google.com](https://console.firebase.google.com).
2. Enable **Firestore Database** in test mode.
3. Register a Web App in Project Settings to get an **API Key** and **Project ID**.
4. In the app, go to **Sync Devices**, enter those two values, and either:
   - leave **Sync Code** blank to generate a new one (first device), or
   - enter an existing sync code (additional devices).
5. Repeat step 4 with the same values on every device you want linked.

⚠️ Anyone with your API Key + Project ID + Sync Code can read/write this data. Treat that combination like a shared password.

## Deployment

This is a static file — any static host works:

- **Netlify Drop** — drag and drop `finance-tracker.html` at [app.netlify.com/drop](https://app.netlify.com/drop)
- **GitHub Pages** — enable in repo Settings → Pages (branch: `main`, folder: `/root`)

## Tech

Single-file HTML/CSS/JS, no build tools or dependencies beyond the Firebase SDK (loaded via CDN, only used if sync is configured).

Moving Off Test-Mode Firestore Rules

Test mode rules expire after 30 days. To keep the app working indefinitely without breaking sync (the app has no login, so full "production mode" default rules will deny everything), replace your Firestore rules with:

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /ledger-sync/{syncCode} {
      allow read, write: if true;
    }
  }
}

Go to Firebase Console → Firestore Database → Rules tab → paste this in → Publish.

⚠️ This removes the expiration date but does not add real security — it's still open to anyone with your API Key, Project ID, and Sync Code, same as test mode. True security would require adding Firebase Authentication and rules based on request.auth.

## Notes

- Firestore's free tier comfortably supports years of typical personal use before any storage limits come into play.
- Firestore "test mode" security rules expire after 30 days and need to be renewed or changed to keep the database writable.
