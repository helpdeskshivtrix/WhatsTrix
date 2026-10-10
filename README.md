# WhatsTrix 2.0

Private, end-to-end encrypted rooms for chat, voice/video calls, screen sharing, voice notes and files.
Serverless: it runs as a static site, talks peer to peer, and keeps everything in a local cache on your device.
Same app, three ways to use it: **website / installable PWA**, **Android APK**, **Windows EXE**.

Look: the locked ShivTrix style (OLED black, royal blue, cyan, gold), with the PdfTrix start-up: orbiting ring, live progress bar, credit line.

## Files

| Path | Purpose |
| --- | --- |
| `index.html` | The whole app (UI, encryption, networking, QR libraries built in) |
| `manifest.json`, `sw.js` | Install as an app, offline cache |
| `logo.png`, `icon-*.png`, `icon.svg`, `apple-touch-icon.png` | Logo and app icons |
| `desktop/main.js`, `desktop/package.json` | Windows app shell (Electron) |
| `tools/android_icons.py` | Puts the logo into the Android build |
| `.github/workflows/build.yml` | Builds the APK and EXE on GitHub |

## 1. Put the website online (GitHub web only)

1. Create a **public** repository, e.g. `WhatsTrix`.
2. **Add file → Upload files**: drag in everything from this folder (keep the folders `desktop`, `tools`, `.github`), then **Commit changes**.
   If the hidden `.github` folder is skipped by your browser: **Add file → Create new file**, type `.github/workflows/build.yml` as the name (the slashes create the folders) and paste the file contents.
3. **Settings → Pages → Deploy from a branch → main / (root) → Save**.
4. About a minute later it is live at `https://YOURNAME.github.io/WhatsTrix/`.

After an update, bump `VERSION` in `sw.js` (`whatstrix-v3`, …) so phones drop the old cache. Everyone in a room must reload once: this version (protocol 4) does not talk to the first versions.

## 2. Build the Android APK and Windows EXE (GitHub web only)

1. Repository → **Actions** tab → **Build WhatsTrix apps** → **Run workflow**. (First time, click "I understand my workflows" if GitHub asks.)
2. Wait about 8-12 minutes. Three jobs run: Android, Windows, Release.
3. Download from the repository's **Releases** page (right side of the main page):
   - `WhatsTrix-android.apk`: on the phone allow "install unknown apps" for your browser, open the file.
   - `WhatsTrix-Setup-2.0.0.exe`: normal installer. `WhatsTrix-Portable-2.0.0.exe`: no install, just run.
4. The invite links inside the apps automatically point to your `github.io` address, so QR codes and links also work for people who only have a browser.

Notes
- The APK is signed with a temporary debug key, which is fine for sideloading. Each new build has a different key, so Android will ask you to uninstall the old one first (your chats on that phone are lost unless you tap **Export** under the bell first).
- Windows shows "Windows protected your PC" for unsigned apps: **More info → Run anyway**.
- The Windows app allows one window at a time, because your identity can only be online once.

## Access code

On start-up the app asks for a 4-digit code: **the current time as HHMM on your own device clock, 24 hours** (for example `1250` at 12:50). One minute either side is accepted, and typing `12:50` works too. It stays unlocked until the tab closes; 5 wrong tries pause input for 15 seconds.
Want one fixed code instead? In `index.html` change `const ACCESS_FIXED='';` to `const ACCESS_FIXED='1250';`.
It is a front-door check in client-side code, a deterrent and not real protection.

## What it does

- **Chat**: 12-digit number per person (derived from your encryption keys), groups up to 99, text, photos, videos, files up to 64 MB, voice notes, emoji, typing and online status.
- **New in 2.0**: reply with quote, emoji reactions, edit and delete-for-everyone, search inside a chat, chat search with All / Direct / Groups / Unread tabs, pin chats, **Saved messages** (notes to yourself, never leaves the device), tappable links, image viewer, day separators, chat export, screen sharing in calls (Windows and desktop browsers), Android back-button support.
- **Calls**: voice or video, up to 6 people, switch voice/video live, mute, share screen, xylophone ringtone.
- **Privacy**: P-256 keys made in your browser, AES-GCM end to end, every connection proves key ownership, messages bound to the session (no replay). Details: your number is about 40 bits, so for sensitive contacts share the QR or link in person (they carry a 100-bit fingerprint that is checked).

## Limits

- Serverless means **both people must be online**. Nothing is stored for offline members, and there are no push notifications when the app is fully closed (Android can also pause a backgrounded app).
- Text history is saved locally. Photos, videos and files are not kept after a reload.
- Signaling uses the free public PeerJS server and a free public TURN relay. If connections are unreliable, enter your own servers under the status line → Connection details.
- Edits, deletes and reactions only reach members who are online at that moment.
- Screen sharing is not available in the Android app (Android WebView does not offer it).
- Clearing site data / uninstalling deletes your identity. **Reset identity** is in Connection details.
