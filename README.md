# Doctor WOYZ - GitHub Pages + Firestore

This edition uses:

- GitHub Pages for the website
- Firebase Anonymous Authentication for device identity
- Firebase Email/Password Authentication for the administrator
- Cloud Firestore for centralized device approval
- A browser-local Gemini API key entered by each user

## Upload These Files To GitHub

Upload these files to the repository root:

```text
index.html
main.html
admin.html
.nojekyll
README.md
firestore.rules
manifest.webmanifest
sw.js
offline.html
icon-192.png
icon-512.png
icon-maskable-512.png
```

`firestore.rules` is not used directly by GitHub Pages. Copy its contents into
the Firebase Console as explained below.

## Firebase Setup

### 1. Create Firestore

1. Open Firebase Console and select `Doctor WOYZ`.
2. Open **Build > Firestore Database**.
3. Select **Create database**.
4. Choose **Standard edition / Native mode**.
5. Select **Start in production mode**.
6. Select a nearby database location.
7. Select **Enable**.

Do not manually create the `devices` collection. It is created when the first
browser requests approval.

### 2. Enable Authentication

1. Open **Build > Authentication**.
2. Select **Get started**.
3. Open **Sign-in method**.
4. Enable **Anonymous**.
5. Enable **Email/Password**.

### 3. Create The Administrator

1. Open **Authentication > Users**.
2. Select **Add user**.
3. Use this exact email:

```text
gigy@woyz.in
```

4. Choose a strong password.

The password is not stored in these GitHub files.

### 4. Publish Firestore Rules

1. Open **Firestore Database > Rules**.
2. Replace the existing rules with the contents of `firestore.rules`.
3. Select **Publish**.

These rules allow anonymous users to create and read only their own device
request. Only `gigy@woyz.in` can list and modify all devices.

### 5. Add The GitHub Domain

After GitHub Pages provides the website URL:

1. Open **Authentication > Settings**.
2. Open **Authorized domains**.
3. Select **Add domain**.
4. Add the domain portion of the GitHub Pages address, for example:

```text
USERNAME.github.io
```

Do not include `https://`, a slash, or the repository name.

## Enable GitHub Pages

1. Open the GitHub repository.
2. Open **Settings > Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**.
4. Select `main` and `/ (root)`.
5. Select **Save**.

The main site will be:

```text
https://USERNAME.github.io/REPOSITORY/
```

The admin page will be:

```text
https://USERNAME.github.io/REPOSITORY/admin.html
```

## PWA And TWA Setup

The repository includes the Web App Manifest, service worker, offline fallback,
and install icons required by PWABuilder.

After uploading the latest files:

1. Wait for GitHub Pages to finish publishing.
2. Open `https://drgigy.github.io/doctorwoyz/manifest.webmanifest` and confirm
   that JSON appears.
3. Open `https://drgigy.github.io/doctorwoyz/sw.js` and confirm that the service
   worker file appears.
4. Return to PWABuilder and analyze:

```text
https://drgigy.github.io/doctorwoyz/
```

5. Refresh the PWABuilder report.
6. Package the Android app after the manifest and service-worker checks pass.

For a fullscreen Trusted Web Activity, PWABuilder will later provide an Android
application package name and SHA-256 signing fingerprint. Use those values to
create `.well-known/assetlinks.json` in the website repository. The exact file
cannot be completed before the Android package and signing key exist.

## Approval Workflow

1. A user opens the main website.
2. The browser signs in anonymously to Firebase.
3. The user enters the doctor's name and device name.
4. A Firestore document is created with status `pending`.
5. The administrator opens `admin.html`.
6. Sign in as `gigy@woyz.in`.
7. Select **Approve** beside the device.
8. The user's page unlocks automatically.
9. The user opens Settings and saves their Gemini API key in that browser.

The administrator can later select **Block**. The user will be blocked the next
time the page receives the updated Firestore status.

## Important Security Notes

- Firebase's web `apiKey` in these HTML files identifies the Firebase project;
  it is not the Gemini API key and is expected to be present in browser code.
- Firestore security depends on the published security rules.
- Each user's Gemini API key remains in that browser's local storage and can be
  retrieved by someone with access to that browser profile.
- The Gemini key is sent directly from the browser to Gemini.
- This architecture is suitable for closed testing, not production use with
  identifiable patient information.
