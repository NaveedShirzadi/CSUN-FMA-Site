# CSUN FMA Website

The website for the Financial Management Association (FMA) at California State University, Northridge.

**Live site:** https://csun-fma.com (also reachable at https://csun-fma.web.app)

## What the site has

Pages everyone can see:

| Page | What it is |
|---|---|
| Dashboard | Welcome section, what we offer, upcoming events, recent photos |
| About | About the chapter |
| Events | Calendar and list of meetings, with times, rooms, and guest speakers |
| Weekly Reports | Weekly market reports with a cover image and a PDF for each |
| Officers | President and Vice President, plus a group photo of the rest of the board |
| Membership | Membership options, how to pay dues, and the membership form |
| Resources | Downloadable files and links for members |
| Connect | FMA's LinkedIn and Instagram, plus LinkedIn links for every officer |

Pages that are hidden from visitors for now (editors can turn them on from the page itself): **Tutoring** and **Tech Meetings**.

Pages only editors can see: **Responses**, **Activity** (visitor counts), and **Editors** (who is allowed to edit).

## How it works

- The whole site is one file, `index.html`, with its styles and code inside it. There is no build step and no framework.
- It runs on Firebase: **Hosting** serves the files, **Firestore** stores the content, **Storage** holds uploaded pictures and files, and **Authentication** handles Google sign in.
- The Firebase scripts load from Google's CDN (version 10.12.2).
- **The content is not in this repository.** Events, officers, membership options, and the rest live in the Firebase database. Editors change them on the live site. This repository holds the code, the database rules, and the report and picture files that ship with the site.
- The Firebase settings near the top of `index.html` are meant to be public. What protects the data is the rules in `firestore.rules` and `storage.rules`, plus the editor list.

## Who can edit

Anyone can read the site. To edit, you sign in with Google, and your email must be listed in the `editors` collection. The first editor is added by hand in the Firebase console. After that, editors add and remove each other from the Editors page.

## Files in this repository

| File or folder | What it is |
|---|---|
| `index.html` | The entire website |
| `404.html` | The "page not found" page |
| `firebase.json` | Firebase settings: what to publish and where the rules files are |
| `.firebaserc` | Points the Firebase tools at the `csun-fma` project |
| `firestore.rules` | Who can read and write each part of the database |
| `storage.rules` | Who can upload to and read from file storage |
| `reports/` | Weekly report PDFs and their cover images |
| `dues/` | Screenshots used in the dues instructions |
| `SETUP.md` | The original setup notes, kept for reference. Parts are out of date, for example it calls the site file `fma-site.html` |

## Deploying changes

You need the Firebase tools installed and to be signed in (`firebase login`). Run these from the project folder:

```
firebase deploy --only hosting
```
publishes the site files (`index.html`, `reports/`, `dues/`).

```
firebase deploy --only firestore:rules,hosting
```
publishes the site and the database rules together. Do this whenever `firestore.rules` changes.

```
firebase deploy --only storage
```
publishes `storage.rules`.

After a deploy, hard refresh the page (Cmd+Shift+R on a Mac) so the browser does not show the old version.

## Saving changes to GitHub

```
git add -A
git commit -m "Describe what changed"
git push
```

GitHub only stores the code. It does not update the live site. Run a Firebase deploy for that.

## Where things are stored

**Database collections:** `events`, `officers`, `membership`, `resources`, `socials`, `offerings`, `gallery`, `weeklyReports`, `tutoringRequests`, `visits`, `editors`. `joinResponses` is left over from an old sign up form that is no longer on the site.

**Settings documents** (inside the `settings` collection): `dashboard`, `dashSections`, `about`, `footer`, `branding`, `pageTitles`, `officersPage`, `motm`, `meetingDefaults`, `duesInstructions`, `connectOfficers`, `tutoring`, `techMeetings`.

**File storage folders:** `officers`, `branding`, `gallery`, `motm`, `events`, `resources`, `reports`.

## Visitor counts and privacy

The Activity page counts visitors using the `visits` collection. Each visit stores only a date, a page name, a time, and a random ID created in the visitor's own browser. No names, emails, or IP addresses are stored. A visitor means one browser or device, counted once per day. Editors who have signed in are not counted. Only editors can read the counts.

## Custom domain

`csun-fma.com` points at Firebase Hosting through DNS records set at the domain registrar: an A record for the site and a TXT record that verifies ownership.
