# Google Play Console — App Content declarations guide

Step-by-step answers, with exact text to paste, for every declaration
under **Play Console → [your app] → App content**. Written from the
current codebase (verified 2026-07-23 against a real build's merged
manifest — see the notes under each section for how). Play's exact
field labels/step order shift occasionally; if something doesn't match
pixel-for-pixel, match by meaning — the answers below are what matters.

Related docs, already written and referenced from here rather than
duplicated: `PRIVACY_POLICY.md`, `TERMS_OF_SERVICE.md`,
`PLAY_STORE_DATA_SAFETY.md`, `PLAY_STORE_CHECKLIST.md`.

**Before you start**: host `PRIVACY_POLICY.md` and `TERMS_OF_SERVICE.md`
somewhere public (GitHub Pages is fine) — several forms below need a
live URL, not a repo path. These are already hosted, along with two
dedicated deletion-instructions pages, at:
- Privacy Policy → `https://kingnerd88.github.io/kingnerd-legal/homeperty-privacy.html`
- Terms of Service → `https://kingnerd88.github.io/kingnerd-legal/homeperty-terms.html`
- Delete account URL → `https://kingnerd88.github.io/kingnerd-legal/homeperty-delete-account.html`
- Delete data URL → `https://kingnerd88.github.io/kingnerd-legal/homeperty-delete-data.html`

---

## 1. Sign in details

_Play's own note on this form: "If parts of your app are restricted
based on sign-in details... provide instructions on how to access
them." Reviewers cannot create accounts or use their own paid
credentials — but **can** use their own Google account for a standard
federated sign-in like ours, since HomePerty doesn't issue its own
username/password system for that._

HomePerty needs **no login at all** for its core, reviewable
functionality — the free tier is every feature for one property, no
account required. The only things gated are (a) the Pro property limit,
unlockable with a license key, and (b) optional Cloud Backup, which uses
Google's own Sign-In.

**Name** *(required, ≤60 chars)*
```
HomePerty Pro review access
```

**Username, email address, or phone number** — leave blank (not
applicable; access is via a license key + Google Sign-In, not app
credentials).

**Password** — leave blank (not applicable).

**Any other information required to access your app** *(≤500 chars —
trim if needed, this is slightly over; shorten the middle paragraph if
Play truncates it)*:
```
HomePerty requires no account or login for its core features. The free
tier includes every feature of the app for one property.

To review Pro (unlimited properties), open Settings > Account & License
> Enter license key, and enter this test key: <PASTE-YOUR-KEY-HERE>.

Pro also includes optional Cloud Backup. To test it, go to Settings >
Cloud backup > Sign in with Google — this is Google's standard Sign-In
(Firebase Authentication); reviewers may use their own Google test
account, no HomePerty-specific credentials exist for it.

Please do not enter HOMEPE-FREE-MODE or HOMEPE-FACTORY-MODE in the
license key field — these are internal maintenance keys unrelated to
reviewing the app, and the second one erases local app data.
```

**Before you paste this**: generate a real key from your Sheets
licensing server (the sheet's **Licensing** menu → generate) — label it
something like "Play Store reviewer" in the sheet's notes column so you
remember not to revoke it while under review, and drop the real value
into the placeholder above.

**Checkbox** — *"Sign in details in this declaration provide full
access to all the features and content within this app, including
premium or paid content and in-app purchases"* → **check it.** The key
+ Google Sign-In together give a reviewer full access to everything the
app currently offers (no live in-app purchases exist yet — Play Billing
isn't wired up, see `PLAY_STORE_CHECKLIST.md`).

---

## 2. Target audience and content

**Target age groups** — check **only** "18 and over". Leave every
under-18 bracket (5 & under, 6–8, 9–12, 13–15, 16–17) unchecked.

**Does your app appeal to children even if it's not targeted at them?**
→ **No.** (Financial/property-management tool, professional branding,
no cartoon characters, no child-directed marketing.)

**Is your app a mixed-audience app (appeals to both children and
adults)?** → **No.**

**Ads targeting** (if asked) → not applicable, HomePerty has no ads.

**Store listing content** (violence, sexual content, gambling,
user-generated content, etc.) → answer **No / None** to every category.
HomePerty has no user-generated public content, no chat between users,
no gambling, no violence.

This declaration is separate from the **Content rating questionnaire**
(IARC) under App content → Content ratings — see
`PLAY_STORE_CHECKLIST.md` ("Utility/Productivity, no UGC, no ads →
expect Everyone"); fill that one out with the same "no objectionable
content" answers and it should return an "Everyone" or "Everyone 10+"
rating.

---

## 3. Data safety

This maps directly onto `PLAY_STORE_DATA_SAFETY.md` — the answers below
are that document translated into the exact wizard steps.

### Step 2 — Data collection and security

- **Does your app collect or share any of the required user data
  types?** → **Yes.**
- **Is all of the user data collected by your app encrypted in
  transit?** → **Yes.**
- **Independent security review / UPI Payments verified** (badges) →
  leave unanswered / skip — not applicable.

### Step 3 — Data types

Check **only** the rows below; leave everything else (Health & fitness,
Messages, Web browsing, Calendar, Contacts, App activity, Device or
other IDs, etc.) unchecked — none of it is collected.

| Category → sub-type | Collected? | Shared? | Ephemeral? | Required/Optional | Purpose |
|---|---|---|---|---|---|
| Location → Approximate location | Yes | No | Yes | Optional | App functionality |
| Location → Precise location | Yes | No | Yes | Optional | App functionality |
| Personal info → Name | Yes | No | No | Optional | Account management |
| Personal info → Email address | Yes | No | No | Optional | Account management |
| Personal info → User IDs | Yes | No | No | Optional | Account management |
| Photos and videos | Yes | No | No | Optional | App functionality |
| Files and docs | Yes | No | No | Optional | App functionality |
| Financial info → Other financial info | Yes | No | No | Optional | App functionality |
| Audio → Voice or sound recordings | Yes | No | No | Optional | App functionality |

Notes on judgment calls:
- **Location** is marked ephemeral because it's used only to show the
  "my location" dot on the map picker (`ACCESS_FINE_LOCATION` /
  `ACCESS_COARSE_LOCATION`) and is never stored or transmitted — a
  property's saved address/coordinates is user-entered content, not
  device-collected location data.
- **Photos/Files/Financial info/Audio** are only collected at all for
  **Pro users who connect Google account** (Cloud Backup); "Shared: No"
  because Firebase is a service provider processing data on our behalf
  to run the feature, not a third party we share it with for their own
  purposes — Play's own Data Safety help documents this distinction.

### Step 4 — Data usage and handling

This step is where Play's "Data safety" form (App content → Data safety
→ Data collection and security) actually asks for the deletion URLs
shown in its own screenshot: a required **"Delete account URL"** field,
plus an optional **"Do you provide a way for users to request that
some or all of their data is deleted, without requiring them to delete
their account?"** question with its own **"Delete data URL"** field.
Answer them like this:

- **Add a link that users can use to request that their account and
  associated data is deleted** → **Delete account URL:**
  ```
  https://kingnerd88.github.io/kingnerd-legal/homeperty-delete-account.html
  ```
  This page names HomePerty/KingNerd, gives the exact in-app steps
  (Settings → Cloud backup → Delete cloud data & account), the email
  fallback, and a table of what's deleted vs. retained — matching
  Play's three bullet requirements for this field (refers to the app,
  features the deletion steps prominently, and specifies what's
  deleted/kept and any retention period).

- **Do you provide a way for users to request that some or all of
  their data is deleted, without requiring them to delete their
  account?** → **Yes**, then **Delete data URL:**
  ```
  https://kingnerd88.github.io/kingnerd-legal/homeperty-delete-data.html
  ```
  This is true for HomePerty: users can delete individual records
  in-app, wipe all local data via Settings → Reset app while staying
  signed in, or email support to have only their cloud backups removed
  while keeping the account. The hosted page documents all three paths.

- **Do you provide a way for users to request that their data is
  deleted?** (the general Step-4 question, distinct from the two URL
  fields above) → **Yes.**
  - Link: either hosted deletion page works here, but the
    `homeperty-delete-account.html` URL above is the more complete
    answer since it also links onward to the data-only page.
- **Is your data collection and handling described in a privacy
  policy?** → **Yes** → paste the hosted Privacy Policy URL:
  ```
  https://kingnerd88.github.io/kingnerd-legal/homeperty-privacy.html
  ```

### Step 5 — Preview

Review the generated summary against the table above, then **Submit**.

---

## 4. Advertising ID

**Does your app use advertising ID?** → **No.**

Verified directly, not assumed: `pubspec.yaml` has no ad or analytics
SDK (no AdMob, no Firebase Analytics, no Facebook/AppLovin/Unity Ads),
and dumping the actual built release APK's permissions
(`aapt dump permissions app-release.apk`) confirms
`com.google.android.gms.permission.AD_ID` is **not** present — so "No"
here won't conflict with anything in the manifest (Play cross-checks
this and flags a mismatch).

---

## 5. Photo and video permissions

**Read this first — this declaration may already be stale for you.**
The manifest source (`android/app/src/main/AndroidManifest.xml`)
explicitly force-removes `READ_MEDIA_IMAGES`/`READ_MEDIA_VIDEO` via
`tools:node="remove"`, and dumping the actual built release APK
confirms neither permission — nor `READ_EXTERNAL_STORAGE` nor
`MANAGE_EXTERNAL_STORAGE` — is present in the final manifest; only
`CAMERA` is. If Play is flagging this declaration, it's almost
certainly tied to an **older AAB** you uploaded to a track before the
photo-picker migration (documented in `docs/CLAUDE_ARCHIVE.md`, Task 14
session 2). **Upload a fresh build to the track under review first** —
this declaration will very likely clear on its own once Play re-scans
the new artifact (can take a few hours).

If it still asks for a justification after a fresh upload (e.g. because
`CAMERA` alone triggers a lightweight version of the form on your
account), use:

```
HomePerty's core functionality includes scanning receipts and documents
(with on-device OCR) and capturing photos for properties, tenants,
maintenance tickets, and inspections. The app uses the device Camera
(CAMERA permission) to capture these photos directly, and the Android
system Photo Picker to attach existing images. HomePerty does not
request READ_MEDIA_IMAGES, READ_MEDIA_VIDEO, or any broad gallery/
storage permission — photo selection goes through Android's
privacy-preserving Photo Picker, which requires no permission grant.
```

---

## Other App content items (for completeness — not shown as pending)

- **News apps declaration** → not applicable, decline/skip.
- **COVID-19 contact tracing and status apps** → not applicable, decline/skip.
- **Government apps** → not applicable, decline/skip.
- **Financial features declaration**: this only appears if you set the
  Play Store **category** to Finance (or similar) — HomePerty doesn't
  lend money, exchange currency/crypto, or link bank accounts, so keep
  the category as **Business** or **Productivity** (matches the
  "Utility/Productivity" content rating already planned in
  `PLAY_STORE_CHECKLIST.md`) and this declaration should never trigger.
- **Ads declaration** (separate from Advertising ID) → "No, my app does
  not contain ads."

---

## Order of operations

1. Host `PRIVACY_POLICY.md` + `TERMS_OF_SERVICE.md` publicly; get URLs.
2. Generate a labeled Play-reviewer license key from the Sheets server.
3. Fill in **Sign in details** (§1) with that key.
4. Fill in **Target audience and content** (§2).
5. Fill in **Data safety** (§3), pasting the Privacy Policy URL twice.
6. Fill in **Advertising ID** (§4) — No.
7. Build and upload a fresh AAB to your active track, *then* check
   **Photo and video permissions** (§5) — likely auto-clears.
8. Run the Content ratings (IARC) questionnaire per
   `PLAY_STORE_CHECKLIST.md`.
9. Confirm all 5 (or fewer, if #5 auto-cleared) declarations show green
   under App content before submitting for review.
