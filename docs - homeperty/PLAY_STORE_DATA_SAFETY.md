# Play Console — Data safety questionnaire answers

Prepared for HomePerty (com.kingnerd.homeperty). Update if features change.

**Revision note (2026-07-23, automatic cloud backup)**: HomePerty
supports optional Google Sign-In (Firebase Authentication) and — for Pro
users — **automatic, versioned Cloud Backup** stored on Firebase.
Free-tier users who never sign in are unaffected: the app remains fully
local for them.

## Summary of actual behavior

- All user data (properties, tenants, finances, documents, photos) is
  stored **locally on the device** in a SQLite database and app-private
  storage, for every user, always. The local database is the source of
  truth; the cloud holds backup copies only.
- **Accounts are optional.** No feature other than Cloud Backup requires
  sign-in.
- If a user signs in (Google, via Firebase Authentication), we collect
  their name, email, profile photo, and account identifier (UID).
- For **Pro** users with a connected account, the app **automatically**
  uploads compressed backup archives (database + settings + attachments)
  to Firebase Storage under `users/{uid}/backups/`, keeping the **last
  10 versions** and deleting older ones. A small Firestore document per
  user lists the versions (dates, sizes, app version, device model).
- Server-side Firebase Security Rules restrict all reads/writes to the
  owning authenticated account. The developer administers the Firebase
  project and accesses backup content only for user-requested support,
  abuse prevention, or legal compliance.
- The app makes network calls for these purposes:
  1. **Google Maps / geocoding** — map tiles and address lookup.
  2. **License key redemption / weekly re-validation** — POSTs the
     entered license key + an app-level token to the developer's
     licensing endpoint. No personal data beyond the key.
  3. **Google Sign-In / Firebase Authentication** — only if the user
     opts to sign in.
  4. **Cloud Backup upload/restore/delete** — Pro + signed-in users only.
  5. No analytics, no ads, no crash-reporting SDKs.
- Local backup exports are encrypted (AES-256, user passphrase) and only
  leave the device when the user explicitly shares them.

## Questionnaire answers

**Does your app collect or share any of the required user data types?**
→ **Yes, conditionally.** For users who never sign in (the default), no
personal data is collected. For users who sign in, we collect account
identifiers. For Pro users, the app additionally uploads their app
content as backup archives. Nothing is **shared** with third parties
beyond Google acting as our hosting/service provider.

Recommended declarations:

| Data type | Collected | Shared | Purpose | Optional |
|---|---|---|---|---|
| Name | Yes (sign-in only) | No | Account management | Yes |
| Email address | Yes (sign-in only) | No | Account management | Yes |
| User photo | Yes (sign-in only) | No | Account management (avatar) | Yes |
| Approximate/precise location | Yes (Maps SDK, on-device use) | No | App functionality (map picker) | Yes |
| Photos/videos | Yes (Pro cloud backup only) | No | App functionality (backup/restore) | Yes |
| Files/docs | Yes (Pro cloud backup only) | No | App functionality (backup/restore) | Yes |
| Financial info | Yes (Pro cloud backup only) | No | App functionality (backup/restore) | Yes |
| Audio (voice notes) | Yes (Pro cloud backup only) | No | App functionality (backup/restore) | Yes |
| Device or other IDs | No | No | — | — |

Declare data types as collected because the *capability exists*; the
"Optional" column reflects that every one of them is genuinely avoidable
(never sign in).

**Is all user data encrypted in transit?** → Yes (Maps, licensing,
Firebase Authentication, and Storage/Firestore all use HTTPS/TLS).

**Is data encrypted at rest?** → Yes.
- On-device: sensitive values (PIN hash, license) live in Android
  Keystore-backed encrypted storage; local backup exports are AES-256
  encrypted with a user passphrase.
- In the cloud: Firebase Storage and Firestore encrypt all data at rest
  with Google-managed encryption.

**Do you provide a way for users to request data deletion?** → Yes.
- Local data: Settings → Reset app (or uninstalling).
- Cloud data + account: Settings → Cloud backup → **Delete cloud data &
  account** (deletes every backup version, the metadata, and the
  Firebase Auth account, in-app, no support contact needed). Also
  available on request via the support email.

**Account deletion** (Play requirement once accounts exist): the in-app
path above deletes both the data and the account in one action. For the
Play listing's "account deletion" URL field, link the hosted privacy
policy section describing it, plus the support email as fallback.

**Target audience**: adults (property management / finance tools). Not a
children's app.
