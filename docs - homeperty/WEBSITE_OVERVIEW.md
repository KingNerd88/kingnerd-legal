# HomePerty — Website Copy & Product Overview

_Source material for kingnerd's product website. Last updated: 2026-07-23._

This is marketing/website copy, not legal text — for the legal document,
see `TERMS_OF_SERVICE.md`; for the privacy story, see `PRIVACY_POLICY.md`.
Pull sections into whatever site builder/CMS is used; headings map roughly
to page sections.

---

## Hero

**HomePerty**
*Run your rental portfolio like a business — from your phone.*

Properties, tenants, rent, expenses, maintenance, documents, taxes, and
reports, all in one Android app. Start free with one property. Everything
stays on your phone unless you choose otherwise.

**[Get it on Google Play]**

---

## Who it's for

- **Landlords** with one property or fifty, tired of spreadsheets and
  shoeboxes full of receipts.
- **Short-term-rental hosts** juggling bookings, cleaning schedules, and
  pricing across platforms.
- **Side-hustle investors** who want real ROI, cap rate, and cash-on-cash
  numbers without hiring a bookkeeper.
- **Anyone who wants their financial and tenant data private by default** —
  HomePerty doesn't require an account, doesn't show ads, and doesn't sell
  data, full stop.

---

## What you can do

**Properties, at a glance.** Track purchase price, current value,
mortgage, insurance, taxes, HOA, and custom fields for every property.
See ROI, cap rate, cash-on-cash return, occupancy, and monthly cash flow
computed automatically.

**Tenants & leases, done right.** Applications, screening, pets, vehicles,
guarantors, digital move-in/move-out inspections with signatures, and
automatic deposit-deduction math.

**Rent that collects itself (almost).** Auto-generated payment schedules,
partial and split payments, late fees, receipts, and full ledgers per
lease.

**Short-term rentals, covered.** Bookings, a pricing calendar, cleaning
cycles, ADR/occupancy analytics, and a side-by-side comparison against
long-term renting.

**Money, organized.** Income and expenses by category, a recurring-
transaction engine, split expenses across properties, mileage tracking,
utility bill splitting, and receipt scanning with automatic OCR field
extraction.

**Renovations & maintenance.** Before/after photo comparisons, budget vs.
actual tracking with ROI, a maintenance kanban board with voice notes, and
a contractor directory with job history and ratings.

**Documents that find themselves.** Folders, scanning, full-text OCR
search across every document, photo albums, and annotation tools.

**Reports that impress your accountant.** 20+ report templates, an
analytics dashboard, a tax center with a depreciation calculator, and a
one-click "accountant package" export.

**Security you'd expect from a bank app, not a spreadsheet.** PIN and
biometric app lock, AES-256 encrypted local backup exports, and (see
below) automatic versioned cloud backup for Pro users.

**14 financial calculators.** Mortgage amortization, refinance comparison,
cap rate, cash-on-cash, break-even occupancy, rent-vs-buy, and more —
each with instant recompute and shareable results.

---

## Plans

### Free
Every feature above, for **one property**, forever. No account, no trial
countdown, no nagging. Your data lives on your device — nothing leaves it
except the things you explicitly choose to share (a backup, an exported
report).

### Pro
Everything in Free, **unlimited properties**, plus automatic Cloud
Backup. Available as:
- A one-time **license key**, or
- A **subscription** (monthly/yearly, via Google Play).

### Cloud Backup — included with Pro
Connect your Google account once, and HomePerty quietly keeps your
entire portfolio backed up — **automatically**, with no buttons, no
passwords, and nothing to remember. The last **10 versions** are kept,
so even an accidental deletion from last week can be undone. Get a new
phone? Sign in and HomePerty finds your backup and offers to restore it.
Backups are stored in your own private space on Google's Firebase
infrastructure, encrypted in transit and at rest, accessible only to
your account. Prefer 100% local? Simply never sign in — everything else
works exactly the same.

---

## Why local-first still matters to us

Most property-management apps assume you want a cloud account from day
one. HomePerty doesn't. The app was built local-first, and it still
defaults that way for everyone — no account, no server, no data leaving
the device except for narrow, disclosed cases (Google Maps for the
location picker, license validation). Cloud Backup exists because some
Pro users *want* a safety net on a lost or new phone — but it's encrypted
so only they can read it, and it's an addition on top of a
private-by-default app, not a replacement for one. See our full
[Privacy Policy](PRIVACY_POLICY.md).

---

## FAQ

**Do I need an account to use HomePerty?**
No. The app works fully offline/local with no sign-in of any kind. An
account is only needed for Cloud Backup.

**What does signing in with Google actually do?**
It connects the Google account your backups are stored in. With Pro,
backups then run automatically; without Pro, signing in uploads nothing
(you can still restore a backup you made while Pro on an old phone).

**Do I have to remember a backup password?**
No. There's nothing to set up and nothing to lose — sign in once and
backups just happen. If you ever need help, support can assist after
verifying you own the Google account.

**What if I mess up my data — can I go back?**
Yes. HomePerty keeps your last 10 backup versions. Open Settings → Cloud
backup → Restore backup and pick a version from before the mistake.

**If I lose Pro (cancel a subscription), do I lose my data?**
No. Your on-device copy is never affected. Your cloud backups are kept
for a short grace period in case Pro is restored, then deleted — see the
Privacy Policy for exact terms.

**Where is cloud data actually stored?**
On Google Firebase (Cloud Storage + Firestore for metadata) — Google's
cloud infrastructure, not a server we run ourselves. Everything travels
over encrypted connections, is encrypted at rest, and is locked to your
account by server-side security rules.

**Can I delete my cloud data?**
Yes, any time, from Settings → Cloud backup → Delete cloud data &
account. That removes every backup version and the account itself in one
action.

**Is my data sold or used for ads?**
No. HomePerty has no ad SDKs, no analytics-for-advertising, and we do not
sell user data to anyone.

**What platforms are supported?**
Android today. (Check the Play Store listing for the current minimum OS
version.)

---

## Footer / legal links

- [Privacy Policy](PRIVACY_POLICY.md)
- [Terms of Service](TERMS_OF_SERVICE.md)
- Support: kingnerdishere@gmail.com
- © 2026 KingNerd. HomePerty is proprietary software — see `LICENSE`.
