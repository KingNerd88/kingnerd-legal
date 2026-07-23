# HomePerty Privacy Policy

_Last updated: 2026-07-23_

HomePerty ("the app", "we", "us") is published by KingNerd (Gabriel), an
individual developer. Contact: kingnerdishere@gmail.com.

## The short version

- **By default, everything stays on your phone.** No account, no cloud,
  no analytics, no ads — ever.
- **Pro includes automatic Cloud Backup**: sign in with Google once, and
  the app keeps the last 10 versions of your data backed up to your own
  private space in our Firebase (Google Cloud) project — automatically,
  with nothing to configure. It exists for backup, restore, device
  migration and disaster recovery.
- Backups are **encrypted in transit (TLS) and at rest** on Google's
  infrastructure, and security rules ensure **only your signed-in
  account can access your backups**.
- If you'd rather your data never leave your phone, simply don't sign
  in — the app is fully functional without it.
- We never sell your data and never use it for advertising.

## Data the app stores locally

Everything you enter — properties, tenants, leases, finances, documents,
photos, voice notes, calendar entries, notes — is stored locally on your
device in the app's private storage. The local database is always the
source of truth; the cloud only ever holds backup copies of it. If you
never sign in (the default), the device copy is the only copy that
exists; we never receive or see it.

## Accounts & Google Sign-In

An account is **optional**. You only need one for Cloud Backup. Signing
in currently uses **Google**, via **Firebase Authentication**; other
sign-in options may be added in a future update, and this policy will be
revised when that happens.

When you sign in, we (via Firebase Authentication) receive:

- Your name, email address, and profile photo, as provided by Google.
- A unique account identifier (UID) used to key your backups so that
  only your account can access them.

Signing in by itself uploads none of your app data (automatic backup
additionally requires Pro). Using Google Sign-In also means Google
processes your authentication request under its own terms — see Google's
Privacy Policy: https://policies.google.com/privacy.

## Cloud Backup (included with Pro)

When you have Pro and a connected Google account, the app automatically
backs up your data — after meaningful changes, when you leave the app,
and at most daily as a safety catch-up. There are no backup passwords,
no buttons, and no settings to maintain.

- **What is stored**: compressed backup archives of your app data —
  database, settings, photos, documents, and voice notes — plus a small
  metadata record per backup (date, size, app version, property count,
  device model).
- **Versioning**: the **last 10 backup versions** are retained; older
  versions are deleted automatically. You can restore any retained
  version from Settings → Cloud backup (protection against accidental
  deletions or mistakes).
- **Where it's hosted**: Firebase Cloud Storage and Cloud Firestore
  (metadata only), on Google Cloud infrastructure in the region
  configured for our Firebase project (EU, `europe-west`).
- **Security**: transferred exclusively over HTTPS/TLS and encrypted at
  rest by Google-managed encryption. Server-side Firebase Security Rules
  restrict every read and write to your own authenticated account — no
  other user can ever access your backups.
- **Who can access it**: you, from any device where you sign in with the
  same Google account. We (the developer) technically administer the
  Firebase project and can access backup files **only** for customer
  support you request, abuse prevention, or legal compliance — never for
  marketing, profiling, or any other purpose. Support-assisted recovery
  is possible after we verify you control the account.
- **How long we keep it**: up to 10 versions, rolling, while your
  account exists. If Pro lapses, existing backups are retained for a
  grace period (up to 30 days) in case Pro is restored, after which they
  may be deleted. We may also delete backups of long-inactive accounts
  (with notice where feasible).
- **Deleting it**: Settings → Cloud backup → **Delete cloud data &
  account** permanently removes all backup versions, their metadata, and
  the sign-in account itself, in one in-app action. **Uninstalling the
  app does not delete your cloud backups** — use the in-app deletion
  first, or email us.

## Data that leaves your device

1. **Google Maps** — map tiles and geocoding requests go to Google when
   you use map features. Google's privacy policy applies.
2. **License keys** — redeeming or re-validating a Pro license key sends
   the key string over HTTPS to our licensing endpoint. No personal
   information accompanies it.
3. **Google Sign-In** — only if you choose to sign in, as described
   above.
4. **Cloud Backup** — only for Pro users with a connected account: the
   backup archives and metadata described above.
5. **Backups and exports you share** — local encrypted backups, PDFs,
   CSVs and ZIPs leave the device only through the Android share sheet,
   to a destination you choose.

## Permissions

- **Camera / photos** — scanning receipts and documents, property photos.
- **Location** — only to show your position on the map picker (optional).
- **Microphone** — voice notes on maintenance tickets.
- **Notifications** — local reminders (rent due, lease expiry, backups).
- **Biometrics** — unlocking the app lock; biometric data never leaves
  the Android system APIs.

## Data processing & your rights (GDPR / similar laws)

For users in the EU/EEA, UK, and jurisdictions with comparable laws:

- **Controller**: KingNerd (Gabriel), contact kingnerdishere@gmail.com,
  is the data controller for the account data and cloud backups
  described above. For data that never leaves your device, no processing
  by us takes place at all.
- **Processor**: Google Ireland Limited / Google LLC (Firebase, Google
  Cloud) processes account data and stores backups on our behalf under
  the [Google Cloud Data Processing Addendum](https://cloud.google.com/terms/data-processing-addendum).
  Data is hosted in the EU (`europe-west`); where Google transfers data
  internationally, it relies on the EU Standard Contractual Clauses and
  equivalent safeguards.
- **What we process and why**:
  | Data | Purpose | Legal basis |
  |---|---|---|
  | Google account name, email, photo, UID | Operate your sign-in and key your backups to your account | Performance of a contract (Art. 6(1)(b)) |
  | Backup archives (your app content) | Provide the automatic backup/restore service included with Pro | Performance of a contract (Art. 6(1)(b)) |
  | Backup metadata (dates, sizes, device model) | Show backup status; version management; retention | Performance of a contract (Art. 6(1)(b)) |
  | License key string | Validate Pro entitlement | Legitimate interest (Art. 6(1)(f)) — anti-fraud |
- **Your rights**: access, rectification, erasure, restriction, data
  portability, and objection. Most are self-service: your data is
  readable and exportable in-app (portability), and erasure is Settings
  → Cloud backup → Delete cloud data & account. For anything else, email
  us; we respond within 30 days. You also have the right to lodge a
  complaint with your supervisory authority.
- **No profiling, no automated decision-making, no marketing use.**
  Backup contents are processed only as opaque archives to store and
  return them to you.

## Data deletion — summary

- **Local data**: uninstall the app, or Settings → Reset app.
- **Cloud backups + account**: Settings → Cloud backup → Delete cloud
  data & account (or email us). Uninstalling alone does not delete the
  cloud copies.

## Children

HomePerty is a financial/productivity tool intended for adults (18+).

## Changes

We may update this policy; material changes will be reflected in the
app's About screen and this document's date. See also our
[Terms of Service](TERMS_OF_SERVICE.md) for subscription and account
terms.
