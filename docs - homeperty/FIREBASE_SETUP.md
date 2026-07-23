# HomePerty × Firebase — architecture & status

> **Status 2026-07-23 (final for this iteration)** — **Automatic,
> versioned cloud backup shipped in code**, replacing the same-day
> password-protected single-slot design (never released to users). §1
> console work (Google provider, SHA-1s, google-services.json with
> populated `oauth_client`) was completed by Gabriel; sign-in was
> verified end-to-end on a Pixel 9 emulator up to Google's real login
> page (no DEVELOPER_ERROR). **Remaining console work: enable Firestore
> + Storage and publish the rules — follow `CLOUD_SETUP_GUIDE.md`
> (step-by-step, written for Gabriel).** Until then, backup attempts
> fail softly with permission errors and the app retries silently.

## 1. What shipped: automatic versioned cloud backup (Pro)

Invisible, Google-Photos-style backup. No passwords, no buttons, no
configuration — signed-in Pro users are simply backed up.

- **Payload**: a compressed ZIP archive (`BackupService.buildBackupArchive`)
  — SQLite snapshot via `VACUUM INTO`, typed settings dump, and the four
  attachment subtrees + `metadata.json`. Same archive the local `.ppb`
  feature wraps, minus the passphrase envelope. Security relies on
  Firebase's recommended architecture: Firebase Auth + per-user Security
  Rules + TLS in transit + Google-managed encryption at rest. (Local
  `.ppb` exports keep their passphrase encryption — they leave the
  device via the share sheet.)
- **Versioning & retention**: up to **10 versions** per account
  (`kCloudBackupRetention`), oldest pruned automatically. Bounded count
  → predictable worst-case storage cost per user.
- **Layout** (mirrored by the security rules):
  - Storage: `users/{uid}/backups/{versionId}.hpb`
    (`versionId` = sortable UTC timestamp, e.g. `20260723T104205123`)
  - Firestore: `users/{uid}/backupMeta/current` — ONE doc listing all
    versions `{id, createdAt, sizeBytes, schemaVersion, appVersion,
    propertyCount, signature, device}` → status checks cost a single
    read; manifest updates run in a **transaction** (multi-device safe).
- **Smart sync / cost control**:
  - A **content signature** (SHA-256 of the DB snapshot + attachment
    listing; volatile prefs excluded) is computed per build — unchanged
    data is **never re-uploaded**, locally (prefs `cloud.lastSignature`)
    and cross-device (manifest's latest signature).
  - ZIP compression built in; Firestore traffic ≈ 1 read + 1 write per
    real upload.
- **Scheduling** (`CloudBackupController`, keepAlive, started on router
  build): Drift `tableUpdates()` (filtered — `app_metadata` and
  `notifications_log` don't count as meaningful) → 5-min debounce with a
  20-min max-wait; immediate flush on app background; connectivity-return
  flush; startup catch-up when dirty or the newest version is >24 h old.
  Failures are silent with exponential-backoff retries (5 min → 1 h);
  the only surface is the status line in Settings → Cloud backup.
- **Safety guards**: an empty local database never overwrites a cloud
  history that has real data (skip-empty guard); restoring never runs
  concurrently with an upload; account switches reset the dedup
  signature so the new account gets a backup.
- **Restore**: after any interactive sign-in, the app checks the account
  and offers "We found a HomePerty backup from {date}… Restore / Not
  now" (with an explicit replace-warning when local data exists). Any of
  the 10 versions can be restored from Settings → Cloud backup → Restore
  backup. Restores stage the proven `.pending-restore` swap + app
  restart; a backup with a newer schema than the app is rejected with a
  clear message, an older schema migrates on open.
- **Gate**: automatic backup requires **Pro + signed in**. Restore only
  requires being signed in — a reinstalling Pro-via-key user can recover
  their data *before* re-redeeming a key (no data hostage).
- **Seamless connect for key users**: right after a key activates Pro,
  the app offers one-tap "Connect Google account" (then checks for a
  restorable backup). Decline = fully local, offer repeats never;
  Settings → Cloud backup remains the opt-in point.
- **Account deletion** (Play/GDPR): Settings → Cloud backup → *Delete
  cloud data & account* sweeps every Storage object under the user's
  folder (orphans included), the manifest doc, and the Firebase Auth
  user (with re-auth retry on `requires-recent-login`).

### Code map

| Piece | File |
|---|---|
| Domain: manifest, versions, retention, ids | `lib/features/backup/domain/cloud_backup_models.dart` |
| Archive build + signature + zip restore | `lib/data/services/backup_service.dart` (`buildBackupArchive`, `restoreFromArchiveBytes`) |
| Cloud service (upload/prune/restore/delete) | `lib/data/services/cloud_backup_service.dart` |
| Scheduler / status (auto triggers) | `lib/features/backup/application/cloud_backup_scheduler.dart` |
| Settings screen (status + restore + sign out) | `lib/features/backup/presentation/cloud_backup_screen.dart` |
| New-device restore offer | `lib/features/backup/presentation/cloud_restore_offer.dart` |
| Auth (Google via Credential Manager, lazy/fail-soft) | `lib/data/services/cloud_auth_service.dart` |
| Post-key-redemption connect prompt | `lib/features/licensing/presentation/license_screen.dart` |
| Route | `/settings/cloud-backup` (`Routes.settingsCloudBackup`) |
| Tests | `test/unit/cloud_backup_models_test.dart` |

### Reliability notes (edge cases by design)

- **Interrupted upload**: Storage objects appear only on completion; a
  failure between file upload and manifest write leaves at most an
  orphaned file (swept by account deletion) — the next flush uploads a
  fresh version.
- **Multiple devices**: manifest transactions serialize concurrent
  uploads; backup-level last-writer-wins with the version history as the
  safety net for conflicting edits.
- **Auth expiration**: Firebase refreshes tokens automatically; a truly
  dead session surfaces as signed-out and the scheduler idles.
- **Firebase unavailable / init failed**: every entry point degrades to
  "not connected" — the auth service resolves `FirebaseAuth.instance`
  lazily and fail-soft, so a broken Firebase config can never crash or
  block the offline app.
- **Deleted cloud backups**: a manifest entry whose file is missing is
  dropped from the manifest automatically on restore attempt.

## 2. Console prerequisites

Follow **`CLOUD_SETUP_GUIDE.md`** (Firestore + Storage + rules, with
exact clicks). Fingerprint/OAuth setup is documented in
`FIREBASE_CONSOLE_CHECKLIST.md` and already done.

## 3. Costs & scale

Per user: ≤ 10 × archive size in Storage (typical portfolio: a few MB →
tens of MB per user), one manifest doc in Firestore, ~1 write per real
data change session. Scales linearly; at 10k users revisit retention or
photo-resolution caps. Set a budget alert (guide §6).

## 4. Future: shared access & sync (designed-for, not built)

The `users/{uid}/…` namespace and single-manifest pattern extend
naturally to shared properties, family accounts, accountant/manager
access and tenant portals: sharing adds sibling namespaces (e.g.
`shares/{shareId}` with member UID lists checked in rules) without
touching the backup layout. A live per-table sync engine remains
possible later at the `lib/data/repositories/` seam
(SyncRepositoryDecorator + outbox) if real-time multi-device editing is
ever needed — backups and sync can coexist.
