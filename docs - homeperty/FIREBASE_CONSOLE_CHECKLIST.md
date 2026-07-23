# Firebase console checklist — Gabriel's part

Everything the **Firebase/Play console side** needs so Google sign-in and
(later) cloud sync work. The app code is done and waiting; sign-in will
fail at runtime until steps 1–3 below are complete. Companion doc:
`FIREBASE_SETUP.md` (architecture + app-side reference).

Project: **homeperty-1659d** · App: **com.kingnerd.homeperty**

---

## 1. Enable the Google sign-in provider  ⬅ required

1. Open <https://console.firebase.google.com/project/homeperty-1659d/authentication/providers>
2. If Authentication shows a **Get started** button, click it first.
3. Under **Sign-in method → Additional providers**, click **Google** →
   toggle **Enable**.
4. Set the **public-facing project name** (HomePerty) and pick a
   **support email** (kingnerdishere@gmail.com) when prompted → **Save**.

Enabling this is what creates the OAuth clients that sign-in needs — it is
why your current `google-services.json` has an empty `"oauth_client": []`.

## 2. Register the app's SHA fingerprints  ⬅ required

Google sign-in on Android only works for APKs whose signing certificate is
registered. You need **all three** (each covers a different install path):

1. Open <https://console.firebase.google.com/project/homeperty-1659d/settings/general>
2. Scroll to **Your apps → HomePerty (Android)** → **Add fingerprint**.
3. Add these one at a time (SHA-1s are required; adding the SHA-256s too
   is recommended and harmless):

   **Debug key** (covers `flutter run` / debug builds on your PC):
   ```
   SHA-1:   86:8D:9B:5A:CA:DD:FD:0E:FB:D9:6C:2F:05:7D:8D:5E:D6:C6:63:39
   SHA-256: 54:37:D0:55:22:F7:AD:03:F9:1B:52:A2:C2:A3:69:18:67:9C:C9:B0:CA:EB:43:AF:5E:92:DC:F4:FE:C9:25:00
   ```

   **Upload key** (covers release builds installed directly, e.g. `flutter
   run --release` or a sideloaded AAB-derived APK):
   ```
   SHA-1:   93:B2:1F:EF:C9:95:4E:F0:2F:29:15:F4:93:17:DE:B0:A7:2F:3A:92
   SHA-256: 82:38:CA:78:79:57:1E:38:50:F6:90:36:17:CE:EB:BB:CA:34:F1:A4:A1:E3:52:34:C9:73:A6:08:C1:B1:AF:CD
   ```

   **Play App Signing key** (covers every install from Google Play — the
   one real users hit). Get it yourself:
   - Play Console → HomePerty → **Test and release → Setup →
     App signing** (a.k.a. App integrity)
   - Copy the SHA-1 (and SHA-256) under **App signing key certificate**
     — NOT the upload certificate shown below it (that one is already
     covered above).
   - Add them as fingerprints in Firebase like the others.

## 3. Re-download google-services.json  ⬅ required

The file only contains OAuth clients that existed when it was generated,
so after steps 1–2 it must be refreshed:

1. Same page as step 2 → your Android app → **google-services.json**
   download button.
2. Replace `android/app/google-services.json` with the new file
   (it stays gitignored — never commit it).
3. Sanity check: open it and confirm `"oauth_client"` is **no longer
   empty** — you should see a `"client_type": 3` (web) entry. That web
   client is what the sign-in plugin reads (the Gradle plugin turns it
   into the `default_web_client_id` resource).
4. Rebuild the app (`flutter run`). No code changes needed.

## 4. Test sign-in

- Settings → **Cloud sync** → toggle ON → Google account picker should
  appear → pick account → the account chip shows your name/email with a
  Sign out button. (Same flow on onboarding's "Sync to the cloud?" page
  after a factory reset / fresh install.)
- Works in debug builds thanks to the debug fingerprint; test on a real
  device or an emulator **with Google Play services** (the "Google Play"
  emulator images, not the plain AOSP ones).

**If it fails:**

| Symptom | Cause |
|---|---|
| Picker never appears / instant error, `ApiException: 10` (DEVELOPER_ERROR) | Fingerprint missing or JSON not re-downloaded — redo steps 2–3, make sure the build you're testing is signed with a registered key |
| "Sign-in failed, check connection" snackbar with internet fine | Same as above, or Google provider not enabled (step 1) |
| Error mentions no credentials / no accounts | No Google account on the device, or emulator image without Play services |
| Works in debug, fails when installed from Play | Play App Signing SHA-1 missing (step 2, third key) |

## 5. Enable Cloud Firestore (for cloud backup — required NOW)

**Architecture (final 2026-07-23)**: **automatic versioned cloud
backup** — the app silently uploads compressed backup archives to
Storage at `users/{uid}/backups/{versionId}.hpb` (last 10 versions kept,
older pruned) and maintains one metadata document at
`users/{uid}/backupMeta/current` in Firestore. No user passwords;
security = Firebase Auth + the per-user rules below + TLS + Google's
encryption at rest. Automatic backup is **Pro-gated in the app**;
restore works for any signed-in owner of a backup.

> The full click-by-click version of §5–§6 (with test steps and cost
> notes) is in **`CLOUD_SETUP_GUIDE.md`** — use that one; the rules are
> identical.

1. <https://console.firebase.google.com/project/homeperty-1659d/firestore>
   → **Create database** → **Production mode** → location `eur3 (europe-west)`
   (pick once, can't change later).
2. **Rules** tab → replace with:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid}/backupMeta/{doc} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
   → **Publish**.

## 6. Enable Storage (for the encrypted backup file — required NOW)

1. <https://console.firebase.google.com/project/homeperty-1659d/storage>
   → **Get started** (production mode, same region).
   Note: Storage may ask to upgrade to the **Blaze** (pay-as-you-go) plan —
   there is still a free monthly allowance (5 GB stored, 1 GB/day
   download); you only pay past it.
2. **Rules** tab → replace with (owner-only, 512 MB cap per backup to
   contain abuse):
   ```
   rules_version = '2';
   service firebase.storage {
     match /b/{bucket}/o {
       match /users/{uid}/backups/{allPaths=**} {
         allow get, list, delete: if request.auth != null && request.auth.uid == uid;
         allow create, update: if request.auth != null && request.auth.uid == uid
           && request.resource.size < 512 * 1024 * 1024;
       }
     }
   }
   ```
   → **Publish**.

   Everything outside `users/{uid}/backups/` stays denied by default —
   that is intentional; widen paths only when a future feature needs it.

## 7. Ship checklist for cloud backup

- [x] Play Console **Data safety** form revised — `docs/PLAY_STORE_DATA_SAFETY.md`
      updated 2026-07-23 (accounts + encrypted backup blob)
- [x] `docs/PRIVACY_POLICY.md` cloud backup + GDPR sections updated
      2026-07-23
- [x] `docs/TERMS_OF_SERVICE.md` updated 2026-07-23 (automatic versioned
      cloud backup service description)
- [x] Account deletion path live in-app: Settings → Cloud backup →
      **Delete cloud data & account** (sweeps all Storage objects +
      Firestore doc + the Firebase Auth user, with re-auth retry)
- [ ] Firestore rules published (§5) — **sign-in works without this, but
      any backup attempt will fail with a permission error until done**
- [ ] Storage rules published (§6) — same
- [ ] Smoke test on device (full script in `CLOUD_SETUP_GUIDE.md` §3):
      sign in → edit data → background the app → status shows Synced →
      Restore a version → reinstall & verify the "Backup found" offer →
      Delete cloud data & account

---

**Steps 1–3 are the only blockers for sign-in.** Steps 5–6 can wait until
the sync engine is built, but doing them now costs nothing.
