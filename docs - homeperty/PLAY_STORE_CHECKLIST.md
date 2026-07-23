# Play Store launch checklist — HomePerty

## Store listing drafts

- **App name**: HomePerty — Property Manager
- **Short description** (≤80 chars):
  > Manage rentals: tenants, rent, expenses, documents, reports — all on-device.
- **Full description** (draft):
  > HomePerty is a complete property-management toolkit for landlords and
  > short-term-rental hosts. Track properties, tenants and leases, collect
  > rent, log income and expenses with receipt scanning, run renovations and
  > maintenance, store documents with searchable OCR, and generate
  > professional reports, statements and tax summaries — everything stored
  > privately on your device.
  >
  > • Portfolio dashboard with ROI, cap rate, cash flow and occupancy
  > • Tenants, leases, digital inspections and deposit settlements
  > • Rent schedules, receipts, late fees, ledgers
  > • Short-term rental bookings, pricing calendar, cleaning cycles
  > • Receipt OCR, recurring transactions, mileage, utility bills
  > • 20+ reports (PDF/CSV/Excel), analytics, depreciation, accountant export
  > • Unified calendar and local reminders
  > • PIN + biometric lock, AES-256 encrypted backups
  > • 14 financial calculators
  > • Automatic cloud backup with Pro — sign in with Google and your
  >   data backs up by itself, with the last 10 versions ready to
  >   restore on any device
  >
  > Free for one property — every feature included. Upgrade to Pro for an
  > unlimited portfolio and cloud backup. Everything stays on your device
  > unless you choose otherwise.

## Assets to prepare

- [ ] App icon 512×512 PNG (from `assets/icons/Homeperty icon.png`)
- [ ] Feature graphic 1024×500 (suggested prompt: flat illustration of a house
      + dashboard cards on indigo `#4F46E5` background, "HomePerty" wordmark)
- [ ] Phone screenshots (min 2, 16:9 or 9:16): Dashboard, Property detail,
      Rent calendar, Reports, Calculator, Documents/OCR, Security lock
- [ ] 7-inch/10-inch tablet screenshots (two-pane property view)

## Console setup

- [ ] **App content declarations** (Sign in details, Target audience and
      content, Data safety, Advertising ID, Photo and video permissions):
      exact text and step-by-step answers for all of them in
      `GOOGLE_PLAY_CONSOLE_GUIDE.md` — do that guide before the items
      below that it also covers
- [ ] App created: `com.kingnerd.homeperty` (matches applicationId)
- [ ] Play App Signing enrolled (Google manages the app signing key; our
      keystore is the **upload** key)
- [ ] Content rating questionnaire: Utility/Productivity, no UGC, no ads →
      expect "Everyone"
- [ ] Data safety: answers in `PLAY_STORE_DATA_SAFETY.md` (now covers
      optional Google Sign-In + subscriber Cloud Storage on Firebase)
- [ ] Privacy policy URL — host `docs/PRIVACY_POLICY.md` (e.g. GitHub Pages)
      and paste the URL
- [ ] Terms of Service URL — host `docs/TERMS_OF_SERVICE.md` alongside it;
      link from the app's About/License screen and the subscription
      purchase flow
- [ ] **Account deletion**: Play Console → App content → Data safety now
      requires either an in-app account-deletion path or a web link if
      accounts exist. Provide both: Settings → Cloud backup → Delete cloud
      data & account (in-app, implemented), plus the two hosted pages for
      the form's URL fields (see `GOOGLE_PLAY_CONSOLE_GUIDE.md` §3):
      - Delete account URL: `https://kingnerd88.github.io/kingnerd-legal/homeperty-delete-account.html`
      - Delete data URL: `https://kingnerd88.github.io/kingnerd-legal/homeperty-delete-data.html`
- [ ] Target audience: 18+
- [ ] Ads declaration: contains no ads
- [ ] Countries: start with your market(s), expand later
- [ ] Subscriptions: configure the Pro monthly/yearly products in Play
      Console → Monetize → Subscriptions once `PurchaseGateway` is wired to
      Play Billing (see `ARCHITECTURE.md` extension seams); until then keep
      the store listing's Pro claims limited to what license-key redemption
      already delivers

## Release tracks

1. **Internal testing** — upload AAB, add your own Gmail as tester, verify
   install/upgrade path.
2. **Closed testing** — friends with license keys; run ≥14 days if personal
   account (Play's 20-tester requirement for new personal accounts).
3. **Production** — staged rollout 20% → 100%.

## Versioning rules

- `pubspec.yaml` `version: X.Y.Z+N` → versionName X.Y.Z, versionCode N.
- Bump **N** for every upload (Play rejects duplicates); bump X.Y.Z for
  user-visible releases. `CLAUDE.md`'s "Current version" line is now the
  authoritative source — check there, not this file, for the live number.

## Pre-upload smoke test

- [ ] `flutter analyze` zero issues, `flutter test` green
- [ ] `flutter build appbundle --release` (signed with upload keystore)
- [ ] Install the release build on a device: onboarding → add property →
      add tenant → lease → rent payment → dashboard KPIs update
- [ ] Maps load (MAPS_API_KEY set — for release also add the Play App
      Signing SHA-1 to the key's Android restrictions, or leave unrestricted)
- [ ] Biometric + PIN lock work; backup create/restore round-trip
- [ ] License key redemption against the live sheet
- [ ] Google Sign-In works on a release-signed build (Play App Signing
      SHA-1 registered in Firebase — see `FIREBASE_CONSOLE_CHECKLIST.md`)
- [ ] Cloud backup round-trip on device (script in
      `CLOUD_SETUP_GUIDE.md` §3): sign in → edit data → background app →
      Synced → restore a version → reinstall shows the "Backup found"
      offer → delete cloud data & account. Confirm a free-tier install
      shows the Pro upsell and never uploads
