# FMA site — connecting Firebase & publishing

The site (`fma-site.html`) works right now with no setup: it runs in
**local preview mode** — fully functional, but data only lives in your
own browser and resets on refresh. Below is how to make it real, public,
and editable by multiple people without a public signup flow.

## How access works

- The public can read everything — events, officers, membership info —
  with no account of any kind.
- Editing (adding/removing events or officers) requires signing in with
  Google, **and** that Google account's email being on an "editors"
  allowlist in the database.
- There's no signup form anywhere on the site. The only way to become an
  editor is for an existing editor to add your email from the **Editors**
  tab (which only appears once you're signed in as one).
- After the very first editor is set up (a one-time manual step below),
  you never have to be involved in adding future editors — any current
  editor can add or remove others themselves.

## 1. Create the Firebase project (skip if reusing an existing one)

1. Go to https://console.firebase.google.com → **Add project**.
2. Click the **</>** (web) icon to register a web app.
3. Copy the `firebaseConfig` object it gives you.

## 2. Paste your config into the site

In `fma-site.html`, near the top of the `<script>`, replace the
placeholder values in `firebaseConfig` with your real ones. The site
detects this automatically and switches from local preview mode to live
Firestore + Google Sign-In. This config is meant to be public — it's not
a secret; the security rules below are what actually protect the data.

## 3. Turn on Firestore and Google Sign-In

In the Firebase console:

- **Build → Firestore Database → Create database** (start in production
  mode — the rules below lock it down properly).
- **Build → Authentication → Get started → Sign-in method → Google →
  Enable.** You don't need Email/Password for this setup.

## 4. Set the security rules

In Firestore → **Rules**, paste the contents of `firestore.rules`
(included alongside this file) and publish. It does three things:
anyone can read events/officers, only someone on the `editors`
allowlist can write them, and only an existing editor can modify that
allowlist.

## 5. Create the first editor (one-time, by hand)

Because the rules require an existing editor to add a new one, the very
first one has to be created directly in the console:

1. Firestore Database → **Start collection** → collection ID `editors`.
2. Document ID: your own email, all lowercase (e.g. `you@csun.edu`) —
   this has to match exactly what Google gives back on sign-in.
3. Add a field, e.g. `addedBy: "manual setup"`. The value doesn't matter
   much — the document existing is what grants access.

From here on, sign in on the site with that Google account, open the
**Editors** tab, and add anyone else the same way — no more console
work needed for future officers.

## 6. Publish it

Same flow as the Entrepreneurs Club portal:

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# point the public directory at the folder containing fma-site.html
# (rename it to index.html, or set that as your entry file)
firebase deploy
```

That gives you a live `your-project.web.app` URL.

I can't run these deploy commands myself — I don't have network access
to Firebase's hosting endpoints from here — but everything above is
ready to go as soon as you run them.
