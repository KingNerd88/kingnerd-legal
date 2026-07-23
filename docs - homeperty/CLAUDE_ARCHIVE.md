# HomePerty Build Orchestrator — ARCHIVED

> **ARCHIVED 2026-07-23.** The v1.0 build (Tasks 0–15) is complete and this
> file is preserved as the audit trail of every task prompt, decision, and
> deviation. It is no longer read as the active work queue — the live
> request board is the new **`CLAUDE.md`** at the repo root. Nothing below
> has been altered since archival.

> This is a **stateful task list** that Claude reads and updates across sessions. Each task has a status. When invoked, Claude works on the next `[TODO]` task, marks it `[DONE]` when complete, and moves it to the "Completed Tasks" archive.

---

## Progress Log

| # | Task | Status |
|---|------|--------|
| 0 | Prerequisite — Create empty Flutter project | `[DONE]` |
| 1 | Project Foundation, Design System & Theming | `[DONE]` |
| 2 | Database Layer, Migrations & Repositories | `[DONE]` |
| 3 | Navigation Shell, Dashboard & Onboarding | `[DONE]` |
| 4 | Properties Module (complete) | `[DONE]` |
| 5 | Tenants, Leases, Applications & Inspections | `[DONE]` |
| 6 | Rent Management + Short-term rental Module | `[DONE]` |
| 7 | Income, Expenses, OCR & Recurring Transactions | `[DONE]` |
| 8 | Renovation, Maintenance & Contractors | `[DONE]` |
| 9 | Document Center, OCR Pipeline & Photo Management | `[DONE]` |
| 10 | Reports, Analytics, Tax Center & Export | `[DONE]` |
| 11 | Calendar, Notifications & Calculators | `[DONE]` |
| 12 | Global Search, Settings, Security, Backup & I/O | `[DONE]` |
| 13 | Licensing System (Google Sheets server) | `[DONE]` |
| 14 | Sample Data, Testing, Android Release & Documentation | `[DONE]` |

**Overall progress:** 15 / 15 tasks complete — v1.0 launch-ready.

---

## How This File Works (Instructions for Claude)

You are working on HomePerty, a commercial-grade Flutter property management app for Android.

### Trigger commands

When the user says any of the following, treat it as an instruction to proceed with the next task:
- `next`
- `next task`
- `continue`
- `run next`
- `proceed`
- `go`
- `start next`

### Task execution protocol

1. **Read** this entire `CLAUDE.md` file at the start of every invocation.
2. **Find** the next task to work on:
   - First, look for any task marked `[IN PROGRESS]` under **Active Tasks**. If one exists, resume it — do NOT skip to a new task. Read the "Resume Instructions" at the bottom of the file and pick up where the previous session left off.
   - If nothing is `[IN PROGRESS]`, find the lowest-numbered task with status `[TODO]` under **Active Tasks**.
3. **Update the status** in two places:
   - The task's header (e.g. `### [TODO] Task 4:` → `### [IN PROGRESS] Task 4:`)
   - The row in the **Progress Log** table at the top of this file
4. **Execute the "Prompt" content** for that task in full. Follow the **Global Conventions** section below on every task. Build all requested files. Run code generation (`dart run build_runner build --delete-conflicting-outputs`) if models were added or changed. Run `flutter analyze` at the end and fix any errors.
5. **When the task is fully complete**:
   - Change the status to `[DONE]` in both the task header and the Progress Log
   - **Move the entire task block** (from `### [DONE] Task N: ...` down to the next `###` heading) from the **Active Tasks** section to the **Completed Tasks** section, appending it at the bottom of Completed Tasks
   - Update the Progress Log row to `[DONE]`
   - Update the "Overall progress" line under the Progress Log
   - Add a one-line completion note to the moved task: `**Completed:** YYYY-MM-DD — <brief summary of what was built>`
6. **Report to the user** in chat:
   - What task was just completed
   - Key files/modules created
   - Any warnings, deferred items, or things the user should verify
   - What the next task is (title and brief scope)

### If a task cannot be finished in one session

If you're running out of context, tokens, or capacity mid-task:
- Leave the status as `[IN PROGRESS]`
- Write a **Resume Instructions** block at the end of the file (or update the existing one) with:
  - Which task you were on
  - Which files were completed
  - Which files or sub-steps remain
  - Any decisions you made that a fresh session should honor
- Report to the user what got done and what remains.

When invoked again with a trigger command, read the Resume Instructions and continue that same task from where you stopped — do NOT restart it, and do NOT skip to the next task.

### Never do these things

- Never skip a `[TODO]` task in favor of a later one unless the user explicitly requests it.
- Never mark a task `[DONE]` if `flutter analyze` reports errors or if build_runner failed.
- Never modify files from a previously-completed task unless you're fixing a bug — and if you do, note it in the chat report.
- Never delete or truncate the prompt text of a task, even after it's moved to Completed Tasks. The archive is the audit trail.
- ~~Never introduce Firebase, any cloud SDK, or any network calls to third-party services. This app is offline-first by hard requirement.~~ **Retired 2026-07-22** — cloud, internet, Google Maps, and third-party SDKs are now permitted (see the Cloud & internet note in Global Conventions). Local-first defaults and graceful offline degradation still apply, but network calls are allowed.

---

## Global Conventions (apply to every task)

### Tech stack (must not deviate)

- Flutter latest stable · Dart 3.x · Material 3
- **State management**: Riverpod (with code generation `@riverpod`)
- **Routing**: GoRouter
- **Database**: Drift (SQLite) with FTS5 for search
- **Models**: Freezed + json_serializable
- **Storage**: `path_provider` for files, `shared_preferences` for lightweight settings, `flutter_secure_storage` for sensitive data
- **UI**: `flutter_svg`, `lucide_icons_flutter`, `fl_chart`, `flutter_animate`, `lottie`, `table_calendar`, `cached_network_image`
- **Media**: `image_picker`, `mobile_scanner`, `google_mlkit_text_recognition`
- **Maps**: `google_maps_flutter` (property location picker + display, photo GPS), `geocoding` (address ↔ coordinates)
- **Docs**: `pdf`, `printing`
- **Utilities**: `intl`, `uuid`, `file_picker`, `crypto`, `archive`, `csv`, `excel`, `share_plus`, `url_launcher`, `permission_handler`, `local_auth`, `package_info_plus`, `device_info_plus`
- **Notifications**: `flutter_local_notifications`
- **Testing**: built-in test + `integration_test` + `mockito`
- **Cloud & internet ARE allowed** (direction change 2026-07-22, Gabriel): the earlier offline-first / no-network / privacy-only mandate is retired. Internet access, Google Maps tiles, geocoding, and cloud SDKs (incl. Firebase) are all permitted. Data still lives locally by default and offline behavior must degrade gracefully, but features may make network calls. Google Maps API key lives in `android/local.properties` as `MAPS_API_KEY` (gitignored); the manifest reads it via a `${MAPS_API_KEY}` placeholder.
- **Planned: Firebase + Google sign-in** (confirmed 2026-07-22, Gabriel): once the core app is built, HomePerty **will** be connected to Firebase with Google-account user login (not now — after the build is done). Because of this, privacy-only / on-device-only guarantees are **not** a requirement anywhere in this app; do not design features around a "data never leaves the device" constraint. When Firebase lands it introduces per-user accounts and cloud sync — build the current data/repository layer so a user id / sync boundary can be threaded through later (the `RepositoryImpl` layer is the intended seam; see Task 15). Do not add the Firebase SDK or auth flows yet; just don't build anything that would block them.

### Architecture

Clean Architecture with these layers per feature:
- `presentation/` — screens, widgets, state notifiers
- `application/` — providers, use cases
- `domain/` — entities (freezed), repository interfaces, value objects
- `data/` — repository implementations (Drift-backed), models, services

Cross-cutting: `core/` (constants, theme, design_system, extensions, utils, errors, router, localization, animations), `shared/` (reusable widgets, providers, mixins).

### Coding standards

- Every entity has: `id` (UUID v4), `createdAt`, `updatedAt`, `archivedAt` (nullable), `notes`, `customFields` (JSON blob).
- All money stored as INTEGER minor units (cents). Use a `Money` value object.
- All dates stored as INTEGER unix ms.
- All strings pass through `l10n` keys (English-only content is fine, but the plumbing must exist).
- All FKs have proper `ON DELETE` behavior; every FK column is indexed.
- Every screen supports light + dark themes and phone + tablet + landscape layouts.
- Cards use radius 16, buttons 12, bottom sheets 20.
- Every list uses `ListView.builder` or equivalent for lazy rendering.
- Every empty state has an illustration slot (Lottie asset path), a friendly message, and a CTA where appropriate.
- Never hardcode strings in UI (use l10n keys).
- Never introduce a new dependency without adding it to `pubspec.yaml` in that same task.

### Per-task exit checklist

Before marking a task `[DONE]`:

- [ ] `flutter pub get` succeeds
- [ ] `dart run build_runner build --delete-conflicting-outputs` succeeds with zero errors
- [ ] `flutter analyze` reports zero errors (warnings acceptable if explained)
- [ ] All new UI screens render without crashing on both a phone viewport (400×800) and a tablet viewport (900×1200)
- [ ] All new providers are properly disposed / kept alive as appropriate
- [ ] Any new database changes include a proper migration (never drop user data)
- [ ] Any new dependencies added are pinned to compatible versions

### Commit convention

After completing a task, remind the user to commit with a tag like `prompt-04-done`. If a git repo is initialized, you may run `git add -A && git commit -m "Task N: <title>"` and suggest tagging, but only if git is already initialized in the workspace.

---

## Active Tasks


## Completed Tasks

### [DONE] Task 0: Prerequisite — Create empty Flutter project

**Completed:** 2026-07-16 — Created Flutter scaffold (Flutter 3.44.2 stable, Dart 3.12.2, org com.gabrielrusu.propertypilot, Android-only). Git repo at workspace root with `.gitignore` excluding `Theta Pilot - EXAMPLE/` and `reference_thetapilot/`. `flutter doctor` clean, `flutter analyze` clean. NOTE: originally scaffolded into a `property_pilot/` subfolder; after Task 1, per Gabriel's request, the whole project was moved to the repo root (pubspec.yaml, lib/, android/ now live at root; package name was `property_pilot` (Dart package renamed to `homeperty` in the 2026-07-16 HomePerty rebrand)).

**Scope:** One-time project bootstrap before any code generation begins.

**Prompt to execute:**

Verify that a Flutter project already exists in this workspace (look for `pubspec.yaml` in the root). If it does not exist, run:

```
flutter create property_pilot --org com.gabrielrusu.propertypilot --platforms=android
```

After creation:
1. Confirm the folder structure (android/, lib/, test/, pubspec.yaml).
2. Initialize a git repository if one is not already present: `git init && git add -A && git commit -m "Initial Flutter project scaffold"`.
3. Verify `flutter doctor` reports no critical issues for Android development.
4. Verify `flutter run` (dry-run analysis, don't launch a device) is possible.
5. Do NOT modify pubspec.yaml, lib/main.dart, or any other files yet — that happens in Task 1.

Report back with the Flutter version, Dart version, and confirmation the project is ready.

---

### [DONE] Task 1: Project Foundation, Design System & Theming

**Completed:** 2026-07-16 — Full foundation delivered: pubspec with all deps resolved, clean architecture folder tree (24 features × 3 layers + core/data/domain/shared), design system (tokens/colors/typography/app_theme, light+dark M3), 17 Pp* widgets, gen-l10n localization (ARB at `lib/core/localization/arb/`, English template), Currency enum (20 currencies) + CurrencyFormatter, theme/locale Riverpod codegen providers persisted to shared_preferences, GoRouter placeholder, strict lints, README stub. build_runner clean, `flutter analyze` zero issues, widget test passing. **Deviations to know:** (1) `custom_lint` NOT added — riverpod_lint 3.1.4 migrated to the native `analysis_server_plugin` system and is incompatible with custom_lint; it's enabled via top-level `plugins:` in analysis_options.yaml instead. (2) `share_plus`/`connectivity_plus`/`package_info_plus`/`device_info_plus` held one major back by `file_picker`'s win32 constraint (Android-irrelevant, documented in pubspec comments). (3) Inter font family referenced in typography but font files not bundled yet (deferred to Task 14 polish; falls back to Roboto). (4) build_runner 2.15 removed `--delete-conflicting-outputs` (now default behavior) — the flag is ignored with a warning.

**Scope:** pubspec dependencies, folder structure, design tokens, theme system, reusable widgets, entry point, localization scaffolding, currency system.

**Prompt to execute:**

You are an expert Flutter architect building HomePerty, a commercial-grade offline-first property management app targeting Google Play. This is Task 1. Everything must be production-quality, not prototype code.

Set up the complete foundation:

1. **pubspec.yaml** with all dependencies pinned to compatible versions:
   `flutter_riverpod, riverpod_annotation, riverpod_generator, go_router, drift, drift_flutter, sqlite3_flutter_libs, path_provider, freezed_annotation, json_annotation, flutter_svg, image_picker, mobile_scanner, google_mlkit_text_recognition, pdf, printing, intl, shared_preferences, flutter_secure_storage, uuid, file_picker, fl_chart, flutter_animate, lottie, lucide_icons_flutter, table_calendar, cached_network_image, permission_handler, local_auth, crypto, archive, csv, excel, share_plus, url_launcher, connectivity_plus, package_info_plus, device_info_plus`. Dev deps: `build_runner, drift_dev, freezed, json_serializable, riverpod_generator, custom_lint, riverpod_lint, flutter_lints, mockito, integration_test`.

2. **Clean architecture folder structure** under `lib/`:
   - `core/` (constants, theme, design_system, extensions, utils, errors, router, localization, animations)
   - `data/` (database, repositories, models, services)
   - `domain/` (entities, repositories interfaces, use_cases, value_objects)
   - `features/` (dashboard, properties, tenants, rent, short_term_rental, income, expenses, renovation, maintenance, contractors, documents, photos, tax, reports, analytics, calendar, notifications, calculators, search, settings, security, licensing, backup, onboarding) — each with `presentation/`, `application/`, `domain/` subfolders
   - `shared/` (widgets, providers, mixins)
   - `main.dart`, `app.dart`

3. **Design system** (`core/design_system/`):
   - `tokens.dart`: spacing (4/8/12/16/20/24/32/40/48/64), radii (8/12/16/20/24/full), elevation, durations, curves
   - `colors.dart`: full palette for light + dark. Primary indigo-based, secondary teal, plus semantic (success, warning, danger, info), surface tiers, income green, expense red, neutral scale
   - `typography.dart`: Material 3 text theme, Inter font family, display/headline/title/body/label scales
   - `app_theme.dart`: full `ThemeData` for light and dark with M3, custom card theme (rounded 16, subtle border in dark), input decoration theme, button themes, chip theme, appbar theme, bottom nav theme

4. **Reusable widgets** under `core/design_system/widgets/`:
   - `PpCard` (rounded, elevation-aware, tappable variant)
   - `PpButton` (primary, secondary, ghost, danger, icon variants — with loading state)
   - `PpTextField` (with prefix/suffix icons, validation, currency mode, date mode)
   - `PpChip` (filter chip variant, tag chip variant with color)
   - `PpEmptyState` (icon + title + subtitle + optional CTA, with Lottie support)
   - `PpLoadingSkeleton` (list, card, dashboard variants)
   - `PpBottomSheet` (rounded top, drag handle, safe area)
   - `PpSectionHeader` (title + optional action)
   - `PpStatCard` (label, value, delta, icon, trend sparkline slot)
   - `PpAvatar` (initials fallback, image support, size variants)
   - `PpConfirmDialog` (destructive variant)
   - `PpDatePicker`, `PpDateRangePicker` wrappers with M3 styling
   - `PpCurrencyInput`
   - `PpTagInput` (chip-based multi-select)
   - `PpAttachmentTile` (photo/pdf/video/audio thumbnails)
   - `PpAnimatedFadeIn` wrapper using flutter_animate

5. **Localization scaffolding**: `intl` setup with English as default, ARB file structure ready for future languages (RO, FR, DE, ES). Do not translate strings yet; use l10n keys.

6. **Currency system**: create a `Currency` enum + formatter service supporting USD, EUR, GBP, RON, and 10+ major currencies with proper locale-aware formatting.

7. **`app.dart`** with `MaterialApp.router`, theme mode provider (system/light/dark), locale provider.

8. **`main.dart`** with `ProviderScope`, Drift setup placeholder, orientation optional (support portrait + landscape), error zone.

9. **`core/router/app_router.dart`** with a minimal GoRouter placeholder (real routes come in Task 3).

10. **`.gitignore`**, **`analysis_options.yaml`** with strict linting (custom_lint + riverpod_lint enabled).

11. **`README.md`** stub with project name, tech stack, and "Setup" section.

Constraints:
- Every file must compile.
- Use freezed for immutable models where relevant.
- Use riverpod code generation (`@riverpod` annotations).
- No hardcoded strings in UI — use l10n keys even if English-only for now.
- Every widget must support both light and dark theme.
- Rounded corners: default 16 for cards, 12 for buttons, 20 for bottom sheets.
- Use `lucide_icons_flutter` for all icons.
- Add TODO comments where future tasks will fill in features.

Deliver the full project skeleton. Run `dart run build_runner build --delete-conflicting-outputs` and ensure no generator errors.

---

### [DONE] Task 2: Database Layer, Migrations & Repositories

**Completed:** 2026-07-16 — Full data layer: 40 Drift tables (shared BaseTable: UUID pk, unix-ms timestamps, archivedAt soft delete, notes, customFields), all FKs indexed with CASCADE/SET NULL (RESTRICT from transactions to categories), FTS5 virtual tables + sync triggers for 7 searchable tables, MigrationStrategy with onCreate/onUpgrade + PRAGMA foreign_keys, app_metadata singleton seeded, Money value object, all enums in domain/entities/enums.dart, 40 freezed entities, 24 repositories + generic Base/ChildDriftRepository, FTS SearchRepository with bm25 ranking, Riverpod providers for db + every repo. 7 DB unit tests (schema/CASCADE/SET NULL/FTS/CRUD/metadata) + widget test all green; flutter analyze zero issues; debug APK builds and runs on the Pixel 9 emulator. **Decisions to honor:** (1) Freezed entities ARE the drift row classes via @UseRowClass(generateInsertable: true) — no separate mapper layer; the toInsertable() extensions are generated. app_database.dart must import all entity files (the generated part file needs them). (2) Monetary columns are int minor units; entities expose int, Money wraps them in the app layer. (3) JSON-ish columns stay raw String at entity level. (4) Soft references (plain text ids, no FK) where hard FKs would cycle: documents.expense/income/maintenance/renovation/contractorId, photos.pairedBeforePhotoId, photo_albums.coverPhotoId, properties.coverPhotoId, document_folders.parentId. (5) expense_transactions gained renovationId/maintenanceTicketId/attachmentIds (Task 8 needs them); maintenance_tickets.createdDate omitted (createdAt covers it). (6) Child tables exposed as ChildRepository members on parent repos (tenant.pets, utility.readings, …) instead of separate named interfaces; extra KeyAccessRepository + NotificationLogRepository added. (7) Android build: AGP pinned to 8.13.2 (mobile_scanner breaks under AGP 9) and kotlin.incremental=false in gradle.properties (Windows cache-close failures in this workspace path).


**Scope:** Drift schema for every entity (40 tables), FTS5 setup, migrations, base repository pattern, Freezed entities with mappers, Riverpod providers.

**Prompt to execute:**

Continuing HomePerty (Task 2). You have the foundation from Task 1. Now build the complete data layer.

Create the Drift database (`data/database/app_database.dart`) with the following tables. Every table gets: `id` TEXT PRIMARY KEY (UUID v4), `createdAt` INTEGER, `updatedAt` INTEGER, `archivedAt` INTEGER nullable, `notes` TEXT nullable, `customFields` TEXT nullable (JSON blob for user-defined fields).

**Tables:**

1. **properties**: name, address, addressLine2, city, region, postalCode, country, latitude, longitude, propertyType (enum: apartment, house, office, garage, land, commercial, duplex, condo), status (owned, sold, under_renovation, for_sale, for_rent, short_term_rental, construction, vacant), bedrooms, bathrooms, sizeSqm, sizeSqft, yearBuilt, purchaseDate, purchasePrice, currentEstimatedValue, currencyCode, mortgageActive (bool), lenderName, loanAmount, loanTermMonths, interestRate, monthlyPayment, mortgageStartDate, insuranceProvider, insurancePolicyNumber, insurancePremiumAmount, insuranceRenewalDate, annualPropertyTax, hoaFeeAmount, hoaFeeFrequency, coverPhotoId, cashReserveTarget, cashReserveCurrent

2. **property_photos**: propertyId FK, filePath, thumbnailPath, caption, isCover (bool), sortOrder, takenAt, latitude, longitude, tags TEXT (json array)

3. **property_documents**: propertyId FK, documentId FK

4. **tenants**: propertyId FK nullable (a tenant may be prospective/unassigned), firstName, lastName, photoPath, phone, phoneAlt, email, dateOfBirth, occupation, employer, employerPhone, monthlyIncome, governmentIdType, governmentIdNumber, moveInDate, moveOutDate, status (prospective, applicant, screening, approved, active, notice_given, moved_out, evicted), applicationDate, screeningScore, notesInternal

5. **tenant_pets**: tenantId FK, name, species, breed, weightKg, color, dateOfBirth, isServiceAnimal (bool), photoPath, vaccinationsUpToDate (bool), petDepositAmount, petRentMonthly, vetContact

6. **tenant_vehicles**: tenantId FK, make, model, year, color, licensePlate, state, parkingSpaceAssigned

7. **tenant_emergency_contacts**: tenantId FK, name, relationship, phone, email

8. **tenant_guarantors**: tenantId FK, firstName, lastName, phone, email, relationship, monthlyIncome, employer

9. **leases**: propertyId FK, primaryTenantId FK, startDate, endDate, monthlyRent, securityDepositAmount, depositHeld (bool), depositRefunded, depositDeductions TEXT (json), rentDueDayOfMonth, lateFeeAmount, lateFeeGraceDays, rentEscalationRate, autoRenew (bool), leaseType (fixed, month_to_month, week_to_week), petAddendum (bool), utilitiesIncluded TEXT (json array), parkingIncluded (bool), status (draft, active, expired, terminated_early, renewed)

10. **lease_tenants**: leaseId FK, tenantId FK (junction, supports multiple tenants per lease)

11. **rent_payments**: leaseId FK, dueDate, amountDue, amountPaid, paidDate, status (paid, pending, late, partial, missed, waived), paymentMethod, referenceNumber, lateFeeApplied, notes

12. **inspections**: propertyId FK, tenantId FK nullable, inspectionType (move_in, move_out, routine, damage, insurance), scheduledDate, completedDate, inspectorName, overallCondition (excellent, good, fair, poor), checklistJson TEXT, photoIds TEXT (json array), signatureTenantPath, signatureLandlordPath

13. **short_term_bookings**: propertyId FK, guestName, guestEmail, guestPhone, platform (listing_site, direct, other), platformReservationId, checkInDate, checkOutDate, numGuests, numAdults, numChildren, numInfants, numPets, nightlyRate, totalNights, subtotal, cleaningFee, platformFeeAmount, taxesCollected, totalPayout, status (inquiry, confirmed, checked_in, checked_out, cancelled, no_show), specialRequests, guestReview TEXT, guestReviewRating INT, hostReview TEXT, cleaningStatus (scheduled, in_progress, complete), cleaningContractorId FK nullable

14. **income_categories**: name, iconName, colorHex, isSystem (bool)

15. **expense_categories**: name, iconName, colorHex, isSystem (bool), taxDeductible (bool)

16. **income_transactions**: propertyId FK nullable, categoryId FK, amount, currencyCode, transactionDate, description, isRecurring (bool), recurrenceRule TEXT nullable, parentRecurringId nullable, tenantId FK nullable, leaseId FK nullable, bookingId FK nullable, attachmentIds TEXT (json array)

17. **expense_transactions**: propertyId FK nullable, categoryId FK, amount, currencyCode, transactionDate, description, vendor, isRecurring (bool), recurrenceRule TEXT nullable, parentRecurringId nullable, splitAcrossProperties TEXT (json map of propertyId → percentage), taxDeductible (bool), mileageKm nullable, receiptDocumentId FK nullable

18. **renovations**: propertyId FK, title, description, category, status (planning, in_progress, completed, cancelled, on_hold), budgetAmount, actualCost, materialsCost, laborCost, contractorId FK nullable, warrantyEndDate, startDate, completionDate, expectedRoiPercent, actualRoiPercent, valueBeforeAmount, valueAfterAmount, beforePhotoIds TEXT (json), afterPhotoIds TEXT (json), timelineJson TEXT

19. **maintenance_tickets**: propertyId FK, unitOrArea, priority (emergency, high, medium, low), status (open, assigned, waiting_parts, in_progress, completed, cancelled), title, description, reportedBy (tenant/landlord/inspection), reportedByTenantId FK nullable, assignedContractorId FK nullable, createdDate, scheduledDate, completedDate, estimatedCost, actualCost, warrantyEndDate, satisfactionRating INT, photoIds TEXT (json), voiceNoteIds TEXT (json)

20. **contractors**: businessName, contactFirstName, contactLastName, trades TEXT (json array — electrician, plumber, builder, painter, hvac, roofer, cleaner, gardener, lawyer, accountant, appliance_repair, locksmith, pest_control, landscaper, general), phone, email, address, website, licenseNumber, insured (bool), insurancePolicy, avgResponseHours, hourlyRate, rating INT (1-5), photoPath, tags TEXT (json)

21. **contractor_jobs**: contractorId FK, propertyId FK, maintenanceTicketId FK nullable, renovationId FK nullable, jobDate, description, amountPaid, satisfactionRating INT, warrantyMonths, invoiceDocumentId FK nullable

22. **documents**: title, documentType (contract, receipt, invoice, mortgage, insurance, tax, government, inspection, lease, warranty, id_document, utility_bill, other), filePath, thumbnailPath, mimeType, sizeBytes, ocrText TEXT, ocrProcessed (bool), issueDate, expiryDate, folderId FK nullable, propertyId FK nullable, tenantId FK nullable, expenseId FK nullable, incomeId FK nullable, maintenanceId FK nullable, renovationId FK nullable, contractorId FK nullable

23. **document_folders**: name, parentId FK nullable (nested folders), colorHex, iconName

24. **photos**: filePath, thumbnailPath, takenAt, latitude, longitude, propertyId FK nullable, albumId FK nullable, sortOrder, caption, tags TEXT (json), pairedBeforePhotoId FK nullable (for before/after), annotationsJson TEXT

25. **photo_albums**: name, propertyId FK nullable, coverPhotoId FK nullable, description

26. **tags**: name, colorHex, entityType (property, tenant, document, transaction, contractor, etc.)

27. **entity_tags**: tagId FK, entityType, entityId (polymorphic junction)

28. **utility_meters**: propertyId FK, meterType (electricity, gas, water, heating, internet), meterNumber, provider

29. **meter_readings**: meterId FK, readingDate, readingValue, photoDocumentId FK nullable, notes

30. **utility_bills**: propertyId FK, meterId FK nullable, provider, billingPeriodStart, billingPeriodEnd, amount, dueDate, paidDate, splitToTenantsJson TEXT, documentId FK nullable

31. **keys_and_access**: propertyId FK, keyLabel, keyType (physical, digital, keypad_code, smart_lock), heldBy (owner/tenant/contractor/office), heldByEntityId, issuedDate, returnedDate, notes

32. **inventory_items**: propertyId FK, category (appliance, furniture, electronics, fixture, other), name, brand, model, serialNumber, purchaseDate, purchasePrice, warrantyEndDate, condition (new, excellent, good, fair, poor), photoIds TEXT (json), receiptDocumentId FK nullable, replacementValue

33. **insurance_claims**: propertyId FK, claimNumber, insurerName, incidentDate, filedDate, incidentType, description, claimAmount, approvedAmount, status (draft, submitted, under_review, approved, denied, paid, closed), photoIds TEXT (json), documentIds TEXT (json), payoutDate

34. **tax_records**: propertyId FK nullable, taxYear, taxType (property_tax, income_tax, capital_gains, other), amountOwed, amountPaid, paidDate, jurisdictionName, documentId FK nullable

35. **calendar_events**: title, description, eventDate, endDate nullable, allDay (bool), category (rent_due, lease_renewal, inspection, maintenance, cleaning, mortgage, insurance, tax, appointment, custom), propertyId FK nullable, tenantId FK nullable, colorHex, notificationOffsetMinutes INT nullable, isCompleted (bool)

36. **notifications_log**: notificationType, entityType, entityId, title, body, scheduledFor, deliveredAt nullable, dismissedAt nullable

37. **mileage_log**: propertyId FK, tripDate, purpose, startLocation, endLocation, distanceKm, ratePerKm, deductibleAmount

38. **lease_templates**: name, description, bodyMarkdown TEXT, clauses TEXT (json array of reusable clauses), isDefault (bool)

39. **nightly_rates**: propertyId FK, date, rate, minStay (for Short-term rental pricing calendar overrides)

40. **app_metadata**: schemaVersion, lastBackupAt, licenseKey (encrypted), first launch timestamp, and any other singleton config.

Also create **FTS5 virtual tables** (via drift raw SQL) for global search across: properties, tenants, documents (ocrText), notes, transactions (description), maintenance_tickets, contractors.

**Additional requirements:**
- Every FK must have proper `ON DELETE` behavior (CASCADE for owned children, SET NULL for references).
- Add indexes on: all FKs, transaction dates, dueDate on rent_payments, checkInDate/checkOutDate on short_term_bookings, expiryDate on documents, scheduledFor on notifications_log, and FTS-mirrored columns.
- Migration strategy: implement `MigrationStrategy` with `onCreate` + `onUpgrade` skeleton supporting schemaVersion in app_metadata. Never destroy user data.
- Repository pattern: create abstract repository interfaces in `domain/repositories/` and Drift-backed implementations in `data/repositories/`. One per entity domain (PropertyRepository, TenantRepository, LeaseRepository, RentRepository, Short-term rentalRepository, TransactionRepository, RenovationRepository, MaintenanceRepository, ContractorRepository, DocumentRepository, PhotoRepository, CalendarRepository, TagRepository, InspectionRepository, InsuranceClaimRepository, TaxRepository, InventoryRepository, UtilityRepository, MileageRepository, LeaseTemplateRepository, MetadataRepository, SearchRepository).
- Each repository must expose: `create`, `update`, `softDelete` (set archivedAt), `hardDelete`, `getById`, `watchById` (Stream), `list` with filters (pagination-ready), `watchList`, `count`.
- Riverpod providers for the database and each repository.
- Freezed data classes for each entity in `domain/entities/`, with Drift ↔ entity mappers.
- All enums defined once in `domain/entities/enums.dart` and reused.
- All monetary fields stored as INTEGER (cents/minor units) — create a `Money` value object to avoid double precision issues.
- All dates stored as INTEGER unix ms.

Do not build any UI in this task. Deliver a fully compiling, indexed, migrated database layer with repositories ready for consumption in Task 3+.

Run build_runner and confirm no errors.

**Note:** This task is unusually large (40 tables). If capacity runs out, split at natural boundary: tables 1–20 (properties through renovations) in one session, tables 21–40 plus all repositories in the next. Update Resume Instructions accordingly.

---

### [DONE] Task 3: Navigation Shell, Dashboard & Onboarding

**Completed:** 2026-07-16 — Full Routes table + StatefulShellRoute app shell (bottom NavigationBar on phone, NavigationRail >720px), More-menu grid (destinations push ComingSoonScreen stubs), 5-page onboarding (PageView, Lottie asset stubs, currency/language pickers, biometric + sample-data offers, shared_preferences completion flag + router redirect), reactive dashboard (greeting bar, PpStatCard grid, upcoming row, 7 fl_chart charts, activity feed, expandable quick actions, empty states with CTA), dashboard StreamProvider aggregations, ResponsiveLayoutBuilder, idempotent SampleDataService (fixed `sample_` ids; Task 7 must reuse its seeded category ids). Widget tests pin a 400px viewport and shut Drift down via `_shutdown` (unmount + elapse fake time + `tester.runAsync(db.close)` — addTearDown(db.close) hangs fake-async). Verified on Pixel 9 emulator: onboarding, dashboard empty state, bottom nav. Same day: **HomePerty rebrand** (see User Notes), launcher icon (white adaptive bg), animated white splash (native seamless launch + SplashGate fade/scale overlay, ~2.1 s hold + 0.5 s fade-out).

**Scope:** Full GoRouter config, bottom nav shell, dashboard with all cards and charts, onboarding flow, sample data service scaffold, responsive layout builder.

**Prompt to execute:**

Continuing HomePerty (Task 3). Foundation and database exist. Now build the app shell and dashboard.

1. **GoRouter configuration** in `core/router/app_router.dart` with all routes as constants in a `Routes` class:
   `/onboarding, /dashboard, /properties, /properties/:id, /properties/:id/edit, /properties/new, /tenants, /tenants/:id, /tenants/:id/edit, /tenants/new, /leases/:id, /leases/new, /rent, /rent/ledger/:leaseId, /short-term-rentals, /short-term-rentals/booking/:id, /short-term-rentals/booking/new, /income, /income/new, /income/:id, /expenses, /expenses/new, /expenses/:id, /renovation, /renovation/:id, /renovation/new, /maintenance, /maintenance/:id, /maintenance/new, /contractors, /contractors/:id, /contractors/new, /documents, /documents/:id, /documents/scan, /documents/folder/:id, /photos, /photos/album/:id, /photos/:id, /calendar, /calendar/event/:id, /reports, /reports/:reportType, /analytics, /tax, /tax/year/:year, /calculators, /calculators/:calcType, /search, /settings, /settings/currency, /settings/language, /settings/security, /settings/backup, /settings/notifications, /settings/about, /settings/license, /licensing/activate`.
   Use `ShellRoute` for the main tabbed area. Include redirect logic for onboarding-not-completed and license-not-activated states (both stubbed for now — real logic in Task 13).

2. **AppShell** widget with a bottom navigation of 5 primary tabs: Dashboard, Properties, Calendar, Documents, More.
   The "More" tab opens a full-screen menu grid with cards for: Tenants, Rent, Short-term rental, Income, Expenses, Renovation, Maintenance, Contractors, Photos, Reports, Analytics, Tax, Calculators, Search, Settings.
   Use `lucide_icons_flutter`. Animate tab transitions.

3. **Onboarding flow** (5 pages, PageView with dots indicator, skippable): welcome + value prop, offline-first & privacy, currency + language selection, biometric setup offer, sample data offer (yes/no). Store completion flag in shared_preferences. Use Lottie animations (placeholder files, ready for real Lottie JSON drop-in later — reference asset paths under `assets/lottie/onboarding_1.json` etc.).

4. **Dashboard screen** (`features/dashboard/presentation/`):
   - Top greeting bar with time-of-day-aware message and property count summary.
   - "Portfolio at a glance" section with `PpStatCard` grid: total portfolio value, monthly income (current month), monthly expenses, net cashflow, occupancy rate, open maintenance count.
   - "Upcoming" horizontal scrollable card row: upcoming rent (with tenant + amount + days-until), lease expirations (within 60 days), insurance renewals (within 90 days), tax deadlines, mortgage payments due, inspection due.
   - Charts section (using `fl_chart`):
     * 12-month income vs expenses (grouped bars)
     * Net profit line chart (12 months trailing)
     * Occupancy rate over time (line)
     * Cashflow waterfall (current month)
     * Portfolio value trend (line with property purchases as markers)
     * Income breakdown pie
     * Expense breakdown pie
   - Recent activity feed: last 10 events (payments received, expenses logged, tickets created, docs scanned) with entity icons and timestamps.
   - Quick actions row (FAB expandable or bottom sheet): Add Property, Log Payment, Log Expense, Scan Document, Add Tenant, New Booking, New Ticket. Use `flutter_animate` for the expand animation.

5. **Dashboard providers** (`features/dashboard/application/`):
   Create Riverpod providers that aggregate from the repositories. Every provider must be reactive (`StreamProvider`) so UI updates when underlying data changes. Cache with keepAlive where appropriate. Handle empty states (no properties → show CTA to add first property, with a Lottie placeholder).

6. **Responsive layout**:
   - Phone portrait: single column stat cards (2-wide grid), charts stacked full-width.
   - Phone landscape / small tablet: 3-wide stat grid, 2 charts per row.
   - Tablet: side rail nav replacing bottom nav when width > 720, 4-wide stat grid, dashboard becomes multi-column.
   Create a `ResponsiveLayoutBuilder` helper.

7. **Empty states** throughout dashboard: friendly illustrations (Lottie asset stubs) + guidance + CTA. Never show blank screens or zeros without context.

8. **Settings entry point** in appbar: gear icon → `/settings`.

9. **Search entry point** in appbar: search icon → `/search` (screen stubbed for now, real impl in Task 12).

10. All numbers formatted through the Currency formatter from Task 1. All dates through `intl` DateFormat.

Do not implement individual entity CRUD screens yet — those come in later tasks. Just make the shell + dashboard fully functional with real data streaming from the repositories (even if empty for a fresh install).

Also add: a simple **SampleDataService** (`data/services/sample_data_service.dart`) that can seed 3 demo properties with tenants, leases, transactions, and photos placeholder paths — triggered from onboarding if user opts in. Do not implement sample photo generation yet, just structured data.

Ensure everything compiles and runs. Test on a small phone viewport and a tablet viewport mentally.

---

### [DONE] Task 4: Properties Module (complete)

**Completed:** 2026-07-17 — Full Properties module under `features/properties/`: list screen (grid 2/3/4-col + list toggle persisted to prefs, status/type/city/has-tenant/needs-attention filter chips, name/date/value/ROI/occupancy sort, search, pull-to-refresh, long-press context menu with Edit/Duplicate/Archive/Export-JSON via share sheet, entry animations, dual empty states); detail screen (SliverAppBar photo carousel + Hero, 12 tabs — Overview/Financials/Tenants/Short-term(conditional)/Maintenance/Renovation/Documents/Photos/Inspections/Inventory/Utilities/Notes-autosave — all streaming live repo data with empty states); 7-step add/edit wizard (validation, unsaved-changes dialog, save-as-draft to prefs for new, mortgage auto-payment calc, m²/sqft toggle, currency picker, cover photo, tags find-or-create + entity_tags, custom-fields editor with 5 field types stored as JSON); `PhotoService` (camera/gallery import, 512px thumbs + EXIF GPS in isolate via `image` pkg, set-cover, captions, delete, before/after pairing stored in photo customFields); pure `PropertyStatisticsCalculator` (ROI/cap/CoC/yield/cashflow/appreciation/occupancy blending leases+bookings with DST-safe day math/vacancy/break-even, null on missing data) + reactive providers; archive flow with hard-delete blockers dialog (active tenants/transactions); two-pane tablet master-detail via ResponsiveLayoutBuilder. Tests: 7 statistics unit tests + list empty-state widget test (16/16 total green). Verified on Pixel 9 emulator (phone + 1600×2400 tablet two-pane). Same session: release-build repairs — javac "source 8 obsolete" silenced via subprojects `-Xlint:-options`, mobile_scanner→7.3.0, `proguard-rules.pro` with mlkit optional-script dontwarn (R8 was failing bundleRelease), *_plus family documented as held by file_picker's win32 pin; release AAB builds clean.

**Scope:** Property list (grid/list toggle), detail with tabs, add/edit multi-step form, photo management, statistics calculations, custom fields, master-detail tablet layout.

**Prompt to execute:**

Continuing HomePerty (Task 4). Shell + dashboard are done. Now build the complete Properties module.

Build under `features/properties/`:

1. **Property List Screen** (`/properties`):
   - AppBar with title, search icon (filters this list), sort dropdown (name, purchase date, value, ROI, occupancy), view toggle (grid/list).
   - Filter chips row: status (owned/for_rent/short-term-rentals/sold/etc.), property type, city, has tenant, needs attention (any missing lease, insurance expiring soon, open maintenance).
   - Grid view: 2 columns phone, 3 tablet portrait, 4 tablet landscape. `PpCard` per property with cover photo (or gradient placeholder with type icon), name, address one-liner, status pill, monthly cashflow, ROI %, occupancy dot indicator.
   - List view: rows with thumbnail, name, address, key stats.
   - Empty state with CTA to add first property.
   - FAB: Add Property → `/properties/new`.
   - Pull to refresh (re-triggers stream).

2. **Property Detail Screen** (`/properties/:id`):
   Use a `CustomScrollView` with `SliverAppBar` containing photo carousel (swipeable, tap for fullscreen) + property name/address. Below the header, a `TabBar` with tabs:
   - **Overview**: key stats (ROI, cap rate, cash-on-cash, monthly cashflow, appreciation, net worth contribution). Purchase & value section. Mortgage summary (if active) with progress bar showing paid vs remaining. Insurance & tax summary. HOA if applicable. Quick actions row.
   - **Financials**: income vs expenses chart (12 months), transaction list (paginated, filterable by date range), running balance line. Split-view: monthly summary table.
   - **Tenants**: current tenant(s) with avatar, lease dates, rent, contact quick actions. Historical tenants section (collapsed). "Add tenant" and "New lease" buttons.
   - **Short-term rental** (only visible if property status includes short_term_rental): bookings calendar + revenue KPIs. Link to full Short-term rental module.
   - **Maintenance**: open tickets list, closed tickets, warranty items expiring.
   - **Renovation**: renovation projects with before/after thumbnails, total invested, ROI.
   - **Documents**: attached documents grouped by type.
   - **Photos**: masonry grid of all photos with album filter.
   - **Inspections**: chronological list.
   - **Inventory**: appliances/furniture list with warranty warnings.
   - **Utilities**: meters + recent readings + bill history.
   - **Notes**: rich text notes field with autosave.

3. **Add/Edit Property Screen** — multi-step form with progress indicator (M3 stepper style):
   Step 1 — Basics: name, property type, status, cover photo.
   Step 2 — Address: full address fields, optional map picker (use static coord input if no map provider; leave a MapProvider abstraction ready).
   Step 3 — Details: bedrooms, bathrooms, size (sqm/sqft toggle), year built.
   Step 4 — Purchase: purchase date, purchase price, current estimated value, currency.
   Step 5 — Mortgage (skippable): lender, loan amount, term, rate, monthly payment, start date. Auto-calculate monthly payment if user provides principal + rate + term.
   Step 6 — Insurance & Taxes: insurance provider, policy, premium, renewal date; annual property tax; HOA.
   Step 7 — Extras: cash reserve target, custom fields (user can add labeled key-value pairs), tags.
   Full form validation, unsaved changes warning, save-as-draft support.

4. **Property statistics calculations** (in `application/property_statistics_provider.dart`):
   Given a propertyId, compute reactively from repositories:
   - ROI = (annual net operating income / total invested) × 100
   - Cap Rate = (annual net operating income / current market value) × 100
   - Cash-on-Cash Return = (annual pre-tax cashflow / total cash invested) × 100
   - Gross Yield = (annual gross rent / current market value) × 100
   - Monthly cashflow = income − all expenses (mortgage, insurance, tax, maintenance, HOA)
   - Appreciation = current value − purchase price (absolute and %)
   - Occupancy rate = occupied days / total tracked days × 100 (blend long-term lease + Short-term rental nights)
   - Vacancy days trailing 12 months
   - Total lifetime income, expenses, profit
   - Break-even monthly rent
   Handle divide-by-zero and missing data gracefully (return null, UI shows "—").

5. **Photo management** for property:
   - Full-screen carousel with pinch-zoom, swipe, share, set as cover, delete, add caption.
   - Add photos: from camera or gallery via `image_picker`, multiple selection.
   - Auto-generate thumbnails (store separately for list performance).
   - Store under app documents dir: `/photos/property_{id}/original/` and `/thumbs/`.
   - Tag photos with location if EXIF present.
   - Before/after pair support: user can link two photos.

6. **Custom fields UI**: an editor allowing user to add labeled fields (text, number, date, boolean, dropdown-with-values) to a property. Persist as JSON in `properties.customFields`. Render on detail screen.

7. **Delete / archive flow**: soft-archive with 30-day recovery hint. Cannot hard-delete a property with active tenants or transactions — show blocked dialog explaining what to resolve first.

8. **Long-press on property card** → context menu: Edit, Duplicate, Archive, Export data (property-scoped JSON).

9. **Responsive layout**: detail screen uses a two-pane layout on tablet (list of properties on left, detail on right). Reuse `ResponsiveLayoutBuilder`.

10. **Entry animations**: cards fade+slide in using `flutter_animate` on list load. Hero transition from list card to detail cover photo.

**Testing**: add basic widget test for the list screen (renders empty state when no properties) and unit tests for the statistics calculations.

All copy through l10n keys. All monetary values via `Money` value object + Currency formatter.

---

### [DONE] Task 5: Tenants, Leases, Applications & Inspections

**Completed:** 2026-07-21 — Full Tenants + Leases + Inspections layer. **Tenants** (`features/tenants/`): list screen (status/open-balance/lease-expiring/has-pets filter chips, search, flat/by-property/by-status grouping, quick contact actions via url_launcher, archive context menu) with a tablet two-pane master-detail split (`embedded`/`selectedId`/`onTenantSelected` on `TenantListScreen`, mirroring the Task 4 Properties pattern) driving `TenantDetailScreen`; detail screen with 10 tabs (Profile w/ masked govt-ID + biometric reveal, Lease, Ledger, Household, Contacts, Communication, Documents, Issues, Inspections, Notes-autosave); 9-step add/edit wizard; application/screening pipeline (`domain/screening.dart` — per-item scoring, aggregate score, rejection reason capture, JSON round-trip) with a convert-to-active action gated on `approved` status. New DB tables `tenant_communications` and `tenant_issues` (complaints + damage incidents) plus `screeningJson`/`rejectionReason` columns on `tenants`. **Leases** (`features/leases/`): create/renew/edit flow with multi-tenant select, escalation preview, lease-template merge (`{{tenant.firstName}}` placeholders), rent-to-income warning (`kRentToIncomeThreshold = 30`), notice-to-vacate + early-termination workflows, deposit-deductions builder shared with the inspection comparison screen. `lease_calculations.dart` (pure, unit-tested): escalated rent compounding, renewal term derivation, due-day month-end clamping (incl. leap Feb), deposit settlement math, rent-to-income %, early-termination penalty, notice-period vacate date. **Inspections** (`features/inspections/`): configurable room/item checklist (`inspection_checklist.dart`) with excellent→missing ratings + photos + notes, hand-rolled canvas `SignaturePad` (tenant + landlord, saved as PNG), move-in vs move-out compare screen that auto-suggests deposit deductions from condition deltas. **PDF services** (`data/services/pdf/`): lease agreement merge, all 6 notice templates (rent reminder, late payment, lease renewal offer, notice to vacate, entry notice, general), application form, deposit settlement statement — all on a shared `pdf_theme.dart`. Tests: `lease_calculations_test.dart` (23 cases), `screening_test.dart` (7 cases), `inspection_checklist_test.dart` (6 cases) — 36 new unit tests, 50/50 total green. `flutter analyze` and `dart run build_runner build` both clean. **Note:** this task's implementation was carried out across sessions without an interim Resume Instructions entry; this pass audited the existing (uncommitted) work against the full spec, fixed the two outstanding lints, and added the missing tablet master-detail split for tenants (item 11) before marking it done.

**Scope:** Tenant CRUD with pets/vehicles/emergency contacts/guarantors, application & screening pipeline, lease management with templates + PDF generation, digital move-in/move-out inspections with signatures, deposit deductions.

**Prompt to execute:**

Continuing HomePerty (Task 5). Properties module done. Now build the complete Tenants + Leases + Inspections layer.

Build under `features/tenants/`, `features/leases/`, and `features/inspections/`:

1. **Tenant List Screen** (`/tenants`):
   - Filter chips: status (prospective, applicant, screening, approved, active, notice_given, moved_out, evicted), has open balance, lease expiring soon, has pets.
   - Search by name/phone/email.
   - Group toggle: by property, by status, flat list.
   - `PpCard` rows: avatar (photo or initials), name, current property + unit, lease dates, status pill, quick contact icons (call/email/message launching url_launcher).
   - FAB: Add Tenant.
   - Empty state with CTA.

2. **Tenant Detail Screen** (`/tenants/:id`) with tabs:
   - **Profile**: photo, full contact block, DOB (age computed), occupation, employer + income, government ID (masked, tap to reveal with biometric confirm if security enabled), status, application date, screening notes.
   - **Lease**: current lease card (dates, rent, deposit, escalation, utilities included, pet addendum) + historical leases list. Buttons: Renew Lease, End Lease, Print Lease.
   - **Rent Ledger**: full payment history with paid/pending/late/partial/missed color coding. Running balance. Export ledger PDF button.
   - **Family & Occupants**: additional adult tenants on lease, children (name + age), pets (with photos, breed, weight, service animal flag, vet contact, pet deposit paid), vehicles (make/model/plate/parking space).
   - **Emergency Contacts** + **Guarantors** sections.
   - **Communication**: timeline log of communications (calls, emails, texts, in-person, notices sent) with type, date, summary, follow-up flag. Add log entry inline.
   - **Documents**: linked docs (lease, ID copies, income proof, etc.).
   - **Complaints & Issues**: log of complaints against or from tenant.
   - **Damage & Deductions**: incidents with photos, cost estimates, resolved status.
   - **Inspections**: move-in, move-out, routine inspections attached to this tenant.
   - **Notes** with autosave.

3. **Add/Edit Tenant screen** — multi-step wizard:
   Step 1 — Personal (name, photo capture via camera or gallery, DOB, government ID with masked display).
   Step 2 — Contact (phone, alt phone, email).
   Step 3 — Employment (occupation, employer, employer phone, monthly income).
   Step 4 — Property assignment (which property + unit, or leave unassigned for prospective).
   Step 5 — Pets (add multiple, each with photo, species, breed, weight, DOB, service animal flag, vet, pet deposit).
   Step 6 — Vehicles (add multiple).
   Step 7 — Emergency contacts (add multiple).
   Step 8 — Guarantor (optional).
   Step 9 — Custom fields + tags.

4. **Prospective Tenant / Application workflow**:
   - New status pipeline: prospective → applicant → screening → approved | rejected → active.
   - Application form (can be filled in on the tenant's behalf, or a shareable data-collection template printed to PDF for offline application).
   - Screening checklist (built-in defaults, customizable): credit check reference, background check reference, prior landlord reference, employment verification, income ratio (rent-to-income), pet policy compliance.
   - Screening score field (0–100) with per-item scoring.
   - Rejection reason capture (for legal record).
   - Convert-to-active button when approved AND lease created.

5. **Lease Management** (`/leases/new`, `/leases/:id`):
   - Create Lease flow: select property → select tenant(s) (multi-select, primary + others) → term (fixed/month-to-month), start/end dates, rent, deposit, rent-due day, late fee amount + grace days, escalation rate (annual %), auto-renew, utilities included checklist, parking, pet addendum with pet deposit + pet rent, custom clauses selection.
   - Lease template picker: choose from `lease_templates` table. Merge tenant/property variables into template body via placeholder syntax `{{tenant.firstName}}` etc.
   - Generate lease PDF using the `pdf` package: cover page, terms, clauses, signature blocks. Save to documents table linked to lease + tenant + property.
   - Rent escalation preview: shows scheduled rent increases across the lease term.
   - Renewal flow: creates a new lease record continuing from prior end date, optionally with rent increase applied per escalation rate.
   - Notice-to-vacate workflow: tenant or landlord initiates, generates notice PDF, tracks required notice period.
   - Early termination workflow with penalty calculation option.

6. **Inspections module** (`/inspections/new/:propertyId` or from tenant):
   - Digital inspection form with configurable checklist templates (Move-In, Move-Out, Routine, Insurance, Damage).
   - Default Move-In/Move-Out checklist: for each room (kitchen, living room, bedrooms, bathrooms, exterior, garage) — walls, floors, ceiling, windows, doors, appliances, fixtures, lighting, plumbing, HVAC condition. Each item gets a rating (excellent/good/fair/poor/damaged/missing) + optional photos + notes.
   - Photo capture inline for each checklist item.
   - Signature capture: two signature pads (tenant + landlord) using a simple canvas-based signature widget. Save as PNG.
   - Generate Inspection PDF with all photos embedded, ratings, signatures, timestamp.
   - Compare Move-In vs Move-Out side-by-side to auto-suggest deposit deductions.

7. **Deposit deductions tool**:
   - After a move-out inspection, present a deductions builder: reason, amount, receipt attachment, photo reference.
   - Total up deductions vs deposit held; compute refund amount.
   - Generate a Deposit Settlement Statement PDF for the tenant.

8. **Communication log entry types + templates**: rent reminder, late payment notice, lease renewal offer, notice to vacate, entry notice (24hr), general. Each generates a PDF suitable for printing/emailing.

9. **Rent-to-income auto-check**: on new lease creation, flag if monthly rent exceeds 30% (configurable in settings) of tenant's monthly income.

10. **Providers + repositories**: fully reactive. Watching a tenant should update instantly when their lease/payment/pet is added.

11. **Responsive tablet layout**: master-detail split.

12. **Tests**: unit tests for lease renewal date calculations, rent escalation math, deposit refund math, screening score aggregation.

All copy via l10n keys.

---

### [DONE] Task 6: Rent Management + Short-term rental Module

**Completed:** 2026-07-21 — Full Rent + Short-term rental (STR) layer, built entirely on the Task 2 data layer (no schema changes) and Task 5's PDF/notice infrastructure. **Rent** (`features/rent/`): dashboard with KPI row, red overdue banner, calendar (table_calendar, dot markers by status)/list/by-property/by-tenant views, per-tenant quick actions (send reminder, log payment, apply late fee); `RentCalculations` (pure, unit-tested) for the ledger running balance, overdue aggregation, KPI roll-up, the late-fee cron check (`evaluateLateChecks` — run on dashboard open, flips overdue `pending` → `late` and applies the lease's fee once), and `allocatePayment` (splits one payment oldest-due-first across multiple periods); log-payment flow supporting single/partial/split; bulk "log all rent paid this month" screen; rent ledger with date-range filter, PDF export, CSV export, and a statement generator that reuses `LeasePdfService.buildRentLedger` on a filtered date range; rent-increase notice reusing `LeasePdfService.buildNotice` (extended `NoticeType` with `rentIncrease` and added an optional `reason` line); new `RentPdfService.buildPaymentReceipt`. **Short-term rental** (`features/short_term_rentals/` — the pre-existing `features/airbnb/` stub folder was empty scaffolding from Task 1 and is superseded by this properly-named tree): dashboard with KPI row (revenue, ADR, occupancy, upcoming check-ins, open cleanings, avg guest rating), property picker, booking calendar, upcoming/current/past lists, trailing-12-month revenue (nightly vs cleaning) + occupancy charts, 52-week seasonality heatmap; `StrCalculations` (pure, unit-tested, DST-safe day math via UTC-normalized date counting) for occupancy rate, ADR, monthly revenue attribution, cleaning-cycle scheduling (checkout date + deep-clean-every-N-stays flag), repeat-guest lookup by email, average guest rating, and the LTR-vs-STR revenue comparison; new-booking flow with live financial recompute and a returning-guest banner; booking detail with Financials/Guest/Cleaning/Reviews/Timeline sections and check-in/out, cancel, mark-cleaned, cleaner-assignment, and review actions; pricing calendar (nightly-rate overrides color-coded by tier, single-day and date-range bulk edit); STR-vs-LTR comparison screen. STR check-in auto-creates the linked payout `income_transaction` via a new `ensureIncomeCategory` helper that matches the sample-data seeder's "Short-term rental" category by name (so a sample-loaded and a fresh install converge on one row). Router: `/rent`, `/rent/ledger/:leaseId`, `/rent/log-payment`, `/rent/bulk-payment`, `/short-term-rentals`, `/short-term-rentals/booking/new`, `/short-term-rentals/booking/:id`, `/short-term-rentals/pricing/:propertyId`, `/short-term-rentals/compare/:propertyId` all wired to real screens (previously stubs). Tests: `rent_calculations_test.dart` (15 cases) + `str_calculations_test.dart` (17 cases) — 32 new unit tests, 82/82 total green. `flutter analyze` and `dart run build_runner build` both clean. **Known gaps / deferred:** editing a lease does not currently regenerate its future rent schedule (no lease-edit UI exists yet to trigger it); `Property` has no "default cleaner" field, so STR cleaner assignment is per-booking only; `ContractorJob`/`Property` have no numeric "host rating of guest" field in the schema, so review aggregation only covers the guest's rating of the stay, not a host-given score; no Android emulator was available this session, so verification relied on `flutter analyze` (0 issues) and `flutter test` (82/82) rather than an on-device walkthrough — Gabriel should smoke-test the new Rent and Short-term rental tabs before proceeding.

**Scope:** Rent calendar, payment tracking, ledger, receipt & statement PDFs, Short-term rental bookings with cleaning cycle, guest reviews, nightly rate override calendar, occupancy analytics.

**Prompt to execute:**

Continuing HomePerty (Task 6). Properties and Tenants/Leases exist. Now build Rent + Short-term rental.

Build under `features/rent/` and `features/short-term-rentals/`:

**## Rent Management**

1. **Rent Dashboard** (`/rent`):
   - Top KPI row: expected this month, collected this month, outstanding this month, late count, upcoming (next 7 days).
   - Rent Calendar view: monthly grid, each day showing dot indicators for payments due (color by status). Tap a day to see list of payments due that day.
   - Toggle views: Calendar / List / By Property / By Tenant.
   - Filter: property, status, month, year.
   - Overdue section highlighted at top (red banner) with total overdue and quick actions per tenant (send reminder, log payment, apply late fee).

2. **Payment logging**:
   - Log Payment flow: pick lease → select which rent_payment record(s) → amount paid, date, method (cash, transfer, check, card, other), reference #, notes.
   - Support partial payment (creates status=partial and leaves remaining balance).
   - Split payment across multiple periods (e.g., a $2000 payment covers two $1000 back-owed months).
   - Auto-apply late fee if configured on lease and payment is after grace period; flag as waived if user opts to waive.
   - Payment receipt: auto-generate a receipt PDF the user can share with the tenant.

3. **Rent Ledger** (`/rent/ledger/:leaseId`):
   - Full chronological ledger for a lease: all charges (monthly rent, late fees, other) and payments.
   - Running balance column.
   - Filter by date range, export to PDF/CSV.
   - Statement generator: pick date range → generates a professional statement PDF.

4. **Auto-scheduling**:
   - When a lease is created, automatically generate `rent_payments` rows for each period from start to end date (or 12 months rolling for month-to-month) based on rent, due day, and escalation rate.
   - Recompute if lease is edited (careful: don't overwrite already-paid records; only regenerate future).
   - Cron-like check on app open: mark any pending record past due-date+grace as late.

5. **Rent increase / notice generation**:
   - Rent Increase Notice generator: pick lease, new amount, effective date, reason (optional), generates PDF using jurisdictional-neutral template.

6. **Bulk payment entry** (for managers): "Log all rent paid this month" flow with per-tenant checkbox and amount pre-filled.

**## Short-term rental Module**

7. **Short-term rental Dashboard** (`/short-term-rentals`):
   - KPI row: this month revenue, avg nightly rate, occupancy %, upcoming check-ins (7 days), open cleanings, review score (avg).
   - Property picker at top (or "All Short-term rental Properties" aggregate).
   - Calendar visualization: month grid with booked-days highlighted (color by platform), gaps as vacancies. Reuse `table_calendar` with custom day builder. Tap a day to see the booking.
   - Booking list below: upcoming, current, past filters.
   - Revenue chart: last 12 months bar chart with cleaning fee vs nightly split.
   - Occupancy chart: last 12 months line.
   - Seasonality heatmap: 52-week calendar heatmap of occupancy or ADR.

8. **New Booking / Reservation flow** (`/short-term-rentals/booking/new`):
   - Property, platform (listing site / direct / other, plus free-text platform name) + platform reservation ID.
   - Guest info (name, email, phone, count breakdown adults/children/infants/pets).
   - Check-in date/time, check-out date/time, nights auto-computed.
   - Financial breakdown: nightly rate × nights (auto), cleaning fee, platform fee (or platform fee %), taxes collected, total payout, currency. Auto-recompute as fields change.
   - Special requests, notes.
   - Cleaning scheduling: schedule cleaning event for checkout day (default) with assigned cleaner (contractor).
   - Save & auto-create linked `income_transaction` on check-in date for total payout (as "Short-term rental — {propertyName} — {guestName}").

9. **Booking Detail Screen** (`/short-term-rentals/booking/:id`):
   - Header with guest name, dates, nights, total payout.
   - Sections: Financials (breakdown), Guest (contact + counts + pets + special requests), Cleaning (status + assigned cleaner + before/after photos), Reviews (host review of guest, guest review of stay + rating), Timeline.
   - Actions: message guest (opens SMS/email), cancel, check-in now (updates status + optionally triggers income transaction), check-out now, mark cleaned.

10. **Short-term rental pricing calendar**:
    - A separate view where user can set custom nightly rates per date (baseRate + overrides using the `nightly_rates` table).
    - Show a monthly grid with color coding by rate tier.
    - Bulk edit: select date range, apply rate.

11. **Guest history / repeat guest detection**:
    - When creating a new booking with an email that matches a past booking, flag "Returning guest" and show past stays.

12. **Short-term rental vs Long-Term comparison**:
    - A comparison view per property: if this property were long-term-rented at X/month vs current Short-term rental performance, show revenue delta trailing 12 months + occupancy tradeoff.

13. **Cleaning cycle management**:
    - Between-stay cleaning: automatic scheduling based on checkout.
    - Deep cleaning cadence (configurable, e.g., every 8 stays).
    - Cleaning contractor assignment with default per property.

14. **Reviews**:
    - Enter host review of guest (1-5 stars + text) + guest review of stay (1-5 stars + text).
    - Aggregate: avg host rating given, avg received.

15. **Providers** reactively drive all dashboards.

16. **Tests**: unit tests for occupancy calculation, ADR, revenue aggregation over date range, cleaning schedule generation.

All monetary math via `Money`. All dates via `intl`.

---

### [DONE] Task 7: Income, Expenses, OCR & Recurring Transactions

**Completed:** 2026-07-21 — Full financial transactions layer under `features/income/`, `features/expenses/`, `features/ocr/`: category management (preseeded income/expense catalogues with 3 sample-data-compatible ids, add/rename/archive/drag-reorder via a `CategoryOrder` customFields-JSON helper, `CategoryListScreen`); freezed `RecurrenceRule` sealed union (daily/weekly/monthly incl. day-of-month & nth-weekday/yearly incl. Feb-29 clamp/custom extension point) + DST-safe `RecurrenceEngine` (idempotent on `(parentRecurringId, scheduledDate)`) + `RecurringMaterializer` running a 12-month horizon on income and expenses; `SplitExpense` (single-row `splitJson`, largest-remainder cent-exact allocation, never double-counted); income + expense transaction screens (recurrence builder, tag find-or-create, attachments, split-across-properties validator, mileage auto-fill, vendor/tax-deductible) and combined list screens (filters, month/week/category grouping with totals, category pie toggle, CSV/Excel/PDF export, search); a scoped-down OCR pipeline (`ReceiptScanService` capture→isolate rotate/contrast→ML Kit text recognition→multi-page PDF combine, `ReceiptFieldExtractor` rule-based vendor/total/tax/date parsing, `OcrCaptureScreen`/`OcrReviewScreen` with an editable extracted-fields panel) plus `mobile_scanner` barcode lookup against `InventoryItem.serialNumber`; a Mileage tab and a Utilities tab (meters, readings, bills, percentage-split and meter-proportional split); reports-linkage provider selectors; router wired for `/income`, `/income/new`, `/income/:id`, `/expenses`, `/expenses/new`, `/expenses/:id`, `/documents/scan` (OCR entry point). Tests: 15 recurrence-engine edge-case tests (leap year, end-of-month clamp, nth-weekday, biweekly), 13 split-expense tests, 17 receipt-field-extractor tests — all new, plus the full existing suite (127 tests total) green; `flutter analyze` zero issues. **Deviations to know:** (1) No live camera view with edge overlay — no `camera` plugin dependency exists in this project, so capture is repeated `image_picker` camera calls (single or multi-page) followed by rotate/contrast only, no interactive crop. (2) The "apply to future only" vs "apply to all unposted" recurring-edit distinction collapses to one behavior (edits only ever affect not-yet-materialized instances) since the data model has no posted/unposted flag to distinguish further. (3) `settings_providers.dart` (a Task 1 file) gained a `DefaultMileageRate` Notifier mirroring `DefaultCurrency`'s shape — needed for the Mileage tab's default rate-per-km. (4) Utilities category is one generic "Utilities-*" set per the original catalogue (Electric/Gas/Water/Internet as separate categories, not a single merged one) — kept as specified. (5) Base transaction streams (`allIncomesProvider`, `allExpensesProvider`) and `allPropertiesProvider`/`allTenantsProvider` all live in `properties_providers.dart`, not in per-feature provider files — future tasks reusing these should import from there directly rather than assuming a `tenants_providers.dart`/`income_providers.dart` origin.

**Scope:** Category management, transaction CRUD, recurring engine, split expenses, camera receipt scanning with OCR autofill, mileage log, utility bill splitter.

**Prompt to execute:**

Continuing HomePerty (Task 7). Now build the complete financial transactions layer.

Build under `features/income/`, `features/expenses/`, and `features/ocr/`:

1. **Category management** (accessed from Settings and inline from transaction forms):
   - Preseeded categories for Income (Rent, Short-term rental, Parking, Storage, Laundry, Solar, Grants, Insurance Payouts, Deposits Kept, Late Fees, Other) and Expenses (Mortgage, Property Tax, Insurance, HOA, Utilities-Electric, Utilities-Gas, Utilities-Water, Utilities-Internet, Cleaning, Maintenance, Repairs, Renovation, Furniture, Appliances, Legal, Accounting, Advertising, Supplies, Travel/Mileage, Property Management, Software, Bank Fees, Depreciation, Other).
   - User can add, rename (except system defaults), assign colors, icons, and tax-deductible flag (expenses).
   - Category list screen with tap-to-edit, drag-to-reorder, archive.

2. **Income transaction screen** (`/income/new`, `/income/:id`):
   - Fields: property (required for property-linked income; optional for portfolio-level), category, amount + currency, date, description, recurring (bool) with recurrenceRule builder (daily/weekly/monthly/quarterly/yearly, interval N, end date or occurrence count), linked entities (tenant/lease/booking), tags, attachments (photos/PDF).
   - Attachment picker: photo, gallery, PDF pick, document scan (opens OCR flow).
   - Save produces one row plus, if recurring, a rule row and future scheduled instances up to a 12-month horizon.

3. **Expense transaction screen** (`/expenses/new`, `/expenses/:id`):
   - Same fields as income, plus: vendor, tax-deductible flag, split-across-properties (JSON map: propertyId → percentage), mileage (km + rate → auto-fill amount for mileage expenses), receipt attachment (photo/PDF/scan).
   - Split validator: percentages must sum to 100%.
   - When linked to a maintenance ticket or renovation project, auto-associate.

4. **Transaction list screens** (`/income`, `/expenses`):
   - Combined and split views. Filter: property, category, date range, min/max amount, has attachment, tax-deductible only, recurring only.
   - Group by month (default), week, or category. Show totals per group.
   - Search transactions by description/vendor/reference.
   - Chart panel toggle: category pie for current filter.
   - Export current view: CSV, Excel, PDF.
   - Bulk actions: select multiple → assign category, add tag, delete, mark tax-deductible.

5. **Recurring transaction engine**:
   - A service that runs on app open (and can be triggered manually from Settings > Data > "Run recurring") which materializes any pending recurring instances up to today into actual transaction rows.
   - Recurrence rule: freezed union — `Daily(interval), Weekly(interval, weekdays), Monthly(interval, dayOfMonth or nthWeekday), Yearly(interval, month, day), Custom(cron-like — leave as extension point)`.
   - Idempotent: never duplicate an instance for a given (parentRecurringId, scheduledDate).
   - When user edits the recurring template, offer: "Apply to future only" or "Apply to all unposted".

6. **Split expense handling**:
   - Recommended model: single row with `splitJson` map, and property-scoped reports compute their share by scanning splits.
   - Alternatively parent+child rows — pick one model and document. Do NOT double-count.

7. **OCR / Document scan flow** (`features/ocr/`):
   - Entry point: from Expense/Income screen → "Scan Receipt". Also standalone: `/documents/scan`.
   - Camera view for document capture with edge overlay. Provide two modes: quick capture (single photo) and multi-page (add pages, reorder, retake).
   - After capture: preview + basic transform (crop, rotate, contrast). Use `image` package for post-processing.
   - Save as JPG(s) or PDF. Multi-page auto-combines to PDF via `pdf` package.
   - Run `google_mlkit_text_recognition` on the captured image(s). Store OCR text in `documents.ocrText`.
   - Parse the OCR text with a rule-based extractor to guess: total amount (regex for currency patterns), date (multiple formats), vendor name (top line heuristic), tax amount. Populate an "extracted fields" panel the user can accept, edit, or ignore.
   - Return the created document + extracted fields to the calling screen so the transaction form pre-fills.

8. **Barcode / QR support**:
   - On the expense flow, allow barcode scan (`mobile_scanner`) to look up an inventory item by serialNumber and pre-fill vendor/model info.

9. **Mileage log**:
   - A tab on Expenses: "Mileage". Add trip: property, purpose, start location, end location, distance (manual or with a placeholder for future GPS auto-track), rate per km (default from settings). Creates an expense transaction linked to a Travel/Mileage category.

10. **Utility bill entry**:
    - When entering a utility expense, offer to link to a `utility_meter` and record a meter reading in one flow.
    - Utility bill splitter: for multi-tenant properties, distribute the bill across tenants by % or by meter reading proportion.

11. **Reports linkage**: expose provider selectors for "expenses for property X in month Y" and similar for report generation.

12. **Providers**: everything StreamProvider so dashboard KPIs stay live.

13. **Tests**: unit tests for recurrence generation (edge cases: end of month, leap year, monthly interval on 31st), split expense math, OCR field extraction on sample text.

All amounts via `Money`. All dates via `intl`.

**Note:** Add the `record` package to pubspec if not already present — will be used in Task 8 for voice notes; add it in this task's pubspec update to keep pubspec changes near the earliest need.

---

### [DONE] Task 8: Renovation, Maintenance & Contractors

**Completed:** 2026-07-21 — Full operations layer under `features/renovation/`, `features/maintenance/`, `features/contractors/`. **Schema v3 migration** (additive, migration-tested path in app_database.dart): new `maintenance_schedules` table (recurring maintenance: frequencyMonths, nextDueDate, defaultContractorId/Priority, `lastGeneratedTicketId` idempotency guard) exposed as `MaintenanceRepository.schedules` ChildRepository; new columns `renovations.{progressPhotoIds,materialsJson,laborJson}` and `maintenance_tickets.activityLogJson`. **Renovation:** list (property/status/budget-range filters, cover thumb, budget progress bar), detail (progress overview bars, Financials with materials/labor/linked-expense/contractor-payment roll-up + "Log expense" deep-link to `/expenses/new?renovationId=`, milestones with gantt-lite dot track, Before/After draggable-divider comparison slider + three galleries via PhotoService into the property gallery, materials/labor line-item editors, linked documents, ROI card, warranty, autosave notes), 5-step form wizard, "Mark completed" flow that computes actualRoiPercent and auto-creates a `contractor_jobs` row. Pure `renovation_calculations.dart` (RenovationMilestone/MaterialItem/LaborEntry/PhotoIdList JSON codecs + `RenovationCalculations.compute`; ROI = (valueAfter − valueBefore − totalCost)/totalCost×100, actualCost override wins else materials+labor+linked expenses). **Maintenance:** kanban board (5 columns, long-press drag between columns gated by `TicketTransitions.allowed`, list toggle persisted to prefs, priority/property/contractor chips, search, emergency banner), ticket form (prefill via `?propertyId=&tenantId=` query params), ticket detail (activity log from `activityLogJson`, assign/status/comment/photo sheets, warranty countdown badge, complete dialog capturing actual cost + satisfaction + warranty months → auto `contractor_jobs` row + schedule roll-forward), voice notes via `record` (m4a under `voice_notes/maintenance_{id}/` + amplitude-waveform `.wave.json` sidecar, live-waveform recorder sheet) with playback via new `audioplayers` dep, recurring schedules screen + `runScheduleCheck()` materializer on board open (14-day window, idempotent). **Contractors:** responsive card grid (trades/min-rating/tag filters, search), detail (call/email/website/map quick actions via url_launcher, trade+tag chips, license/insurance, PpStatCard metrics from pure `ContractorMetrics.fromJobs`, job history cross-linking back to ticket/renovation, manual "Record job" sheet), full add/edit form. Router: all renovation/maintenance/contractor stubs replaced, plus `/renovation/:id/edit`, `/maintenance/:id/edit`, `/maintenance/schedules`, `/contractors/:id/edit`. ~230 new l10n keys (ren*/mnt*/con*). Tests: 3 new suites (renovation calc 13, maintenance logic 15, contractor metrics 6) — 161/161 total green; `flutter analyze` zero issues; debug APK builds. **Notes:** ticket/renovation photos live in the property photo gallery (PropertyPhoto rows) with ids stored in the JSON arrays — no separate photo table; voice-note "ids" are file paths; `audioplayers ^6.5.1` added to pubspec for playback; RECORD_AUDIO permission comes from the record plugin's manifest (explicit declaration still on Task 14's checklist).

**Scope:** Renovation projects with before/after slider, budget vs actual tracking, kanban maintenance ticketing with voice notes, contractor directory with ratings + job history.

**Prompt to execute:**

Continuing HomePerty (Task 8). Now build the operations layer: renovation, maintenance, contractors.

Build under `features/renovation/`, `features/maintenance/`, `features/contractors/`:

**## Renovation**

1. **Renovation list** (`/renovation`):
   - Card per project: cover image (before or after), title, property, status pill, progress bar (materials + labor spent vs budget), start → completion date.
   - Filter: property, status, budget range.
   - FAB: New Renovation.

2. **Renovation detail** (`/renovation/:id`):
   - Header: title + property + status.
   - Progress overview: budget vs actual, timeline progress %.
   - Sections:
     * Financials: budget, actual cost, materials breakdown, labor breakdown, contractor payments, invoices.
     * Timeline: milestones (add/edit) with dates and completion status. Gantt-lite visualization.
     * Photos: separate Before and After galleries. Side-by-side comparison view with a draggable slider (two images overlapped with a divider handle). Additional "In Progress" gallery.
     * Materials list: line items (name, qty, unit cost, total, supplier, receipt link).
     * Labor: hours logged, hourly rate, contractor.
     * Documents: linked invoices, warranty certs, permits.
     * ROI: valueBefore, valueAfter (user-entered), expectedROI, actualROI (auto-computed as `(valueAfter - valueBefore - totalCost) / totalCost × 100`).
     * Warranty: end date with reminder.
     * Notes.

3. **Add/Edit renovation** — multi-step form:
   Step 1 — Basics (title, property, category, description, status).
   Step 2 — Budget & Financials (budget, expected ROI, valueBefore).
   Step 3 — Timeline (start, expected completion, milestones).
   Step 4 — Contractor (optional select from directory).
   Step 5 — Custom fields + tags.

4. **Cost tracking**: link `expense_transactions` to this renovation. When you log an expense with categoryId=Renovation and select a renovationId, it appears in the renovation's Financials.

**## Maintenance**

5. **Maintenance dashboard** (`/maintenance`):
   - Kanban-style board view (default) OR list view toggle: columns for Open, Assigned, Waiting Parts, In Progress, Completed.
   - Filter chips: priority, property, contractor.
   - Emergency banner at top if any emergency-priority open tickets.
   - Search.
   - FAB: New Ticket.

6. **Ticket create/edit** (`/maintenance/new`, `/maintenance/:id`):
   - Fields: property, unit/area (dropdown from property's rooms + free text), priority, title, description, reportedBy (owner/tenant/inspection), tenant reference (if reported by tenant), assigned contractor, scheduled date, estimated cost, photos, voice notes (record with a mic button — store as m4a via `record` package), attachments.
   - When creating from a tenant's screen or property screen, prefill.

7. **Ticket detail**:
   - Full ticket view with activity log (status changes, comments, assignments).
   - Actions: Assign contractor, Update status, Log expense (opens expense form pre-linked), Add photo, Add voice note, Add comment, Mark completed (prompts for actual cost + satisfaction rating + warranty months).
   - Warranty countdown badge.

8. **Voice notes**: record with a simple recorder widget (in-app), waveform preview, playback. Persist under `/voice_notes/maintenance_{id}/`.

9. **Recurring maintenance**:
   - Support scheduled recurring maintenance (e.g., HVAC service every 6 months).
   - Add table `maintenance_schedules` (title, propertyId, frequencyMonths, lastCompletedDate, nextDueDate, defaultContractorId, defaultPriority). Regenerate an open ticket when nextDueDate approaches.

**## Contractors**

10. **Contractor list** (`/contractors`):
    - Grid of cards: photo, business name, primary trade badge, rating stars, response time, city.
    - Filter: trades (multi-select), rating, tags.
    - Search.
    - FAB: New Contractor.

11. **Contractor detail** (`/contractors/:id`):
    - Header: photo + name + rating.
    - Contact block: phone, email, website, address (with call/email/map quick actions).
    - Trades: chips.
    - License & insurance: license #, insured flag, policy details.
    - Job history: chronological list of `contractor_jobs` (property, date, description, amount paid, satisfaction, warranty months, invoice link).
    - Metrics: total jobs, total spent, avg satisfaction, avg response time, avg job cost.
    - Notes.

12. **Add/Edit contractor**:
    - Basic info, trades multi-select, contact, license/insurance, rating, tags, photo, notes.

13. **Recording a job**:
    - From a completed maintenance ticket or renovation, an entry is created in `contractor_jobs` automatically.
    - Also allow manual entry directly on the contractor: "Record job".

14. **Cross-linking**: from a maintenance ticket → tap contractor → contractor detail. From contractor → tap job → back to the ticket/renovation.

15. **Providers, repositories, reactive UI. Empty states with CTAs.**

16. **Tests**: unit tests for renovation ROI calculation, maintenance ticket state transitions, contractor metrics aggregation.

---

### [DONE] Task 9: Document Center, OCR Pipeline & Photo Management

**Completed:** 2026-07-22 — Full Document Center + Photo Management under `features/documents/` and `features/photos/`. **Documents:** `DocumentService` (`data/services/document_service.dart` — file_picker import of PDF/images into `documents/imports/`, thumbnails via shared `core/utils/image_thumbnails.dart` isolate util with PDF first-page raster through `Printing.raster`, OCR via ML Kit incl. PDF-first-page, idempotent default-folder seeding with fixed `folder_default_*` ids, folder CRUD where deleting a folder moves contents up a level, duplicate/rotate/share/print/ZIP-export via `archive`, find-or-create `applyTagByName`, `captureMeterPhoto`); documents shell tab is a real screen now (folder chip strip on phone / persistent left tree pane >720px, type/has-OCR/has-expiry/expiring-60d/property filter chips, title+OCR search, long-press multi-select with bulk move/ZIP/delete, New Folder/Import/Scan actions, "Clean up orphaned files" in overflow); document detail (`/documents/:id` — image InteractiveViewer or `PdfPreview`, editable metadata card incl. type/issue/expiry/folder/links/tags, OCR pane with copy + re-run, rule-based `DocumentAutoTagger` suggestion chips accept/dismiss, expiry countdown card whose "Remind me" creates a `calendar_events` row with `notificationOffsetMinutes` for Task 11 to schedule, rename/move/link/tag/print/duplicate/rotate/delete). Scan flow generalized: `ReceiptScanService.finalize` + `OcrCapture/ReviewScreen` gained optional `documentType`/`folderId` (receipt default preserved); documents screen scans as type=other into the current folder. `ExpiringDocumentsCard` (next 5) added to the main dashboard. **Photos:** `PhotoHubService` (`data/services/photo_hub_service.dart` — camera/gallery into `photos/hub/`, 512px thumbs, captions/tags JSON, annotations, albums incl. auto-cover, before/after pairing, trash = `archivedAt` soft-delete with 30-day purge on hub open, "set as property cover" bridges by re-importing the file through Task 4's `PhotoService`); photo hub `/photos` (month-grouped timeline in a lightweight round-robin `MasonryGrid`, albums grid toggle, property/album/annotated/paired filters, caption+tag search, trash screen); fullscreen viewer `/photos/:id` (PageView over the filtered set, pinch-zoom, annotation overlay toggle, metadata sheet, set-cover/add-to-album/pair/annotate/share/trash); annotation editor (arrow/box/freehand/text in normalized coords via `photo_annotations.dart` codec, painted by `AnnotationPainter` inside an intrinsic-AspectRatio wrapper so viewer and editor align); `/photos/before-after` browser with draggable `BeforeAfterSlider`; album detail `/photos/album/:id`. `StorageCleanupService` walks photos/thumbs/documents/voice_notes subtrees and deletes files no DB row references (1-hour grace for in-flight imports). Utilities tab gained "Record reading" with "Photograph meter" (Document + `photoDocumentId` in one capture). Pure logic in `auto_tagger.dart`, `photo_pairing.dart` (link lives on the AFTER photo's `pairedBeforePhotoId`; dangling ids skipped at resolve time), `image_thumbnails.dart`. Router: documents tab + `/documents/:id`, `/documents/folder/:id`, `/photos`, `/photos/before-after` (new `Routes.photosBeforeAfter`), `/photos/album/:id`, `/photos/:id` all real. ~190 new l10n keys (`doc*`/`ph*` + 4 utility + 4 common). Tests: auto-tagger (9), thumbnails (5), pairing + annotation codec (7) — 183/183 total green; `flutter analyze` zero issues; debug APK builds. **Notes/deviations:** Riverpod 3 has no `valueOrNull` — use `.value`; multi-file import applies folder then batch-OCRs in background (single-file import opens detail for metadata instead of a separate pre-import prompt sheet); PDF OCR covers the first page only (rasterizing every page deferred); GPS shows coordinates text (no map provider by design); per-entity photo capture entry points were already satisfied by the existing `PhotoService`/attachment flows from Tasks 4–8; the storage-cleanup Settings entry lands with the real Settings screen in Task 12 (action lives in Documents overflow meanwhile).

**Scope:** Document folders with nesting, scan (single & multi-page), OCR indexing with auto-tagging, PDF/image viewer, photo timeline, albums, before/after slider, annotations, thumbnail generation.

**Prompt to execute:**

Continuing HomePerty (Task 9). OCR was scaffolded in Task 7 for receipts. Now build the full Document Center and Photo Management systems.

Build under `features/documents/` and `features/photos/`:

**## Document Center**

1. **Document dashboard** (`/documents`):
   - Folder tree on the left (or top on phone, collapsible) rooted at "All Documents". Nested folders supported (`parentId` FK from `document_folders`).
   - Right pane: grid of document tiles for the selected folder. Tile shows thumbnail (PDF first page render, image, or type icon), title, type badge, date, entity link chip.
   - Filter chips: document type, has OCR, has expiry, expiring soon (60 days), linked property, linked tenant.
   - Search bar that searches title AND OCR text.
   - Actions: New Folder, Import (`file_picker` for PDF/images), Scan (opens camera scan flow from Task 7), Bulk Move, Bulk Delete.

2. **Document detail** (`/documents/:id`):
   - Full document preview: image viewer for images, PDF viewer using `printing` or a native viewer.
   - Metadata panel: title (editable), type, issue date, expiry date, folder, linked entities (property, tenant, expense, income, maintenance, renovation, contractor, tax), tags, size, mime type.
   - OCR pane: full extracted text with copy, re-run OCR button.
   - Actions: Rename, Move to Folder, Add Tag, Link to Entity (multi-target picker), Share (`share_plus`), Print (`printing` package), Delete, Duplicate, Rotate (for images).
   - Expiry reminder: if expiryDate set, show countdown and offer to schedule a notification (integrates with Task 11 notification system).

3. **Scan flow reuse**: the `/documents/scan` route reuses the OCR pipeline from Task 7 but starts a general document type instead of a receipt. Multi-page support with reorder and per-page rotate. Save as PDF by default, single-page as JPG optional.

4. **Import flow**:
   - Pick PDF/image via `file_picker`.
   - Show preview, prompt for title, type, folder, linked entities, tags.
   - Run OCR in background (isolate if PDF → rasterize first page and run OCR; for multi-page PDFs, iterate pages or lazily OCR on first search hit).
   - Store under `/documents/` in app documents dir.

5. **Folder management**:
   - Create/rename/move/delete folders. Custom color and icon per folder.
   - Default folders on first launch: Contracts, Receipts, Invoices, Insurance, Tax, Government, Inspections, Warranties, IDs.

6. **Bulk operations**: multi-select in grid, bulk assign folder / tags / delete / export as ZIP.

7. **Auto-tagging heuristics**:
   - When OCR completes, run keyword rules to auto-suggest tags/type (e.g., "invoice" keyword → type=invoice; "policy" + "insurance" → type=insurance; "tax return" → type=tax).
   - Suggestions shown as chips to accept/dismiss.

8. **Expiring documents dashboard widget**: a component (also usable on the main dashboard) listing next 5 expiring documents. Add it to the dashboard.

**## Photo Management**

9. **Photo hub** (`/photos`):
   - Timeline view (default): photos grouped by month, masonry grid within each month. Use a lightweight staggered grid.
   - Album view toggle: album covers grid. Album detail shows the album's photos.
   - Filter: property, album, has annotations, has before/after pair, tags.
   - Search by caption/tag.
   - Empty state with CTA.

10. **Photo detail** (`/photos/:id`):
    - Full-screen viewer with pinch-zoom, swipe navigate.
    - Bottom sheet metadata: caption (editable), date taken, GPS if available (small static map placeholder or just coords), property link, album link, tags.
    - Actions: Set as property cover, Add to album, Link/unlink before/after pair, Annotate, Share, Delete, Move to trash (30-day recovery).
    - Annotations: simple canvas overlay allowing arrows, boxes, freehand, text labels. Save annotation JSON to `photos.annotationsJson`. Toggle annotations on/off in viewer.

11. **Before/After feature**:
    - Standalone Before/After browser at `/photos/before-after` showing all paired sets.
    - Slider comparison widget (draggable divider between two images).
    - Create a pair: from a photo detail, "Pair with…" opens a picker to choose the counterpart.

12. **Albums**:
    - Create album, name, cover photo, description, optional property association.
    - Add/remove photos to album (photos can be in multiple albums).
    - Album card grid with cover + photo count.

13. **Photo capture entry points**: every entity edit screen (property, tenant, maintenance, renovation, inspection) should offer both camera capture and gallery pick, feeding through the same `PhotoService` which handles resizing, thumbnail generation, and storage path.

14. **Thumbnail generation**:
    - On import/capture, resize a copy to max 512px for use in lists. Store under `/thumbs/`.
    - Use isolate for heavy ops.

15. **Metering photo integration**: from utilities module, "Photograph meter reading" launches capture with the meter context and auto-links the photo as a document AND meter reading evidence.

16. **Storage housekeeping**: a Settings action "Clean up orphaned files" walks storage and removes files not referenced by any DB row.

17. **Providers reactive**; efficient list rendering with `ListView.builder` + pagination for large libraries.

18. **Tests**: unit tests for OCR keyword auto-tag rules, thumbnail generation utility, before/after pair integrity.

---

### [DONE] Task 10: Reports, Analytics, Tax Center & Export

**Completed:** 2026-07-22 — Full Reports + Analytics + Tax Center layer, built on a pure, tested aggregation core. **Pure domain (unit-tested):** `features/reports/domain/report_aggregations.dart` (ReportPeriod, totalIncome/Expense with split-expense sharing, monthlyPnl, income/expense-by-category, propertyPerformance, rentRoll/scheduledMonthlyRent, blended lease+STR occupancyRate — 11 tests) and `features/tax/domain/tax_calculations.dart` (straight-line DepreciationCalculator with optional mid-month first-year convention + final-year rounding absorb, TaxAggregator summary with property filter + property-tax category extraction, MileageDeduction, CapitalGains — 12 tests). **Report engine:** a data-driven model — `report_model.dart` (KpiSection/TableSection/NoteSection + RenderedReport with CSV-ready `tables` accessor), `report_types.dart` (21-value ReportType enum with category/requiresProperty/slug metadata), `report_builder.dart` (ReportDataBundle + ReportParams → RenderedReport; real builders for all 21 templates: monthly/yearly P&L, cashflow, property performance, portfolio overview, income/expense breakdown, tenant ledger, rent roll, maintenance/renovation/contractor, occupancy/vacancy, tax summary, depreciation, mileage, short-term, deposit statement, and graceful notes for insurance/utility). Money/date are injected callbacks so the builder is UI-agnostic. `reports_providers.dart` assembles the bundle from every repo via a FutureProvider (flattens contractor jobs, finds property-tax/mortgage categories by name, gathers purchase info for depreciation). **Exports:** `data/services/report_export_service.dart` — PDF (via the shared `PpPdf` theme: KPI→kvTable, tables→dataTable, notes→paragraphs), CSV, Excel (one sheet per table), print, save-to-Documents, and the one-click **accountant ZIP** (income + deductible-expense CSVs, mileage CSV, tax-summary PDF, linked receipt files) via `archive` + share. **Reports UI:** `reports_home_screen.dart` (category-grouped card grid) + `report_view_screen.dart` (period presets this-month/this-year/last-year/custom-range, property picker, live inline preview of sections, export menu). **Analytics:** `analytics/domain/analytics_insights.dart` (deterministic rule-based observations: MoM expense swings, net-loss flips, best/worst property, portfolio margin — 4 tests) + `analytics_screen.dart` (year/lifetime range, reuses the dashboard fl_chart widgets — income-vs-expense bars, net-profit line, income/expense donuts — plus an insights panel and a property-performance table). **Tax Center:** `tax/application/tax_providers.dart` (selected-year notifier, per-property DepreciationConfig persisted in shared_preferences, taxSummary FutureProvider blending deductible aggregation + configured depreciation, taxRecordsForYear stream) + `tax_center_screen.dart` (year/property picker; Overview tab with income/deductible/mortgage/property-tax/depreciation/estimated-taxable; Depreciation tab = live calculator with land-value input, method, useful life, mid-month toggle, and a full schedule table; Records tab = manual tax-payment log with add sheet; export yearly summary PDF). Router: `/reports`, `/reports/:reportType` (slug→ReportType), `/analytics`, `/tax`, `/tax/year/:year` all real screens (were stubs). ~130 new l10n keys (rpt*/an*/tax*). Tests: 3 new suites (report aggregations 11, tax calculations 12, analytics insights 4) — 210/210 total green; `flutter analyze` zero issues; debug APK builds. **Notes/deviations:** reports use a one-shot FutureProvider snapshot (refresh on navigation) rather than live streams — appropriate for on-demand generation; the 21 templates share one generic section renderer/exporter rather than 21 bespoke screens (all produce real data; insurance-claims & utility-consumption show a note until those modules are queried in depth); depreciation land value + method/life are stored per-property in shared_preferences (keeps property.customFields untouched); multi-currency portfolios are still summed naively (no FX conversion — matches the dashboard's current behavior). This task also shipped in the same session as the **Google Maps integration** (see the Cloud & internet note in Global Conventions): `google_maps_flutter` + `geocoding`, reusable `StaticLocationMap` / `MapLocationPicker` wired into the property form, property detail, and photo GPS; INTERNET permission + `MAPS_API_KEY` manifest placeholder (blank in `android/local.properties` until Gabriel adds a key); minSdk 24 / compileSdk 36 with an afterEvaluate subproject override.


**Scope:** 20+ report templates with PDF/CSV/Excel export, custom report builder, accountant package ZIP export, interactive analytics dashboard, tax center with depreciation calculator.

**Prompt to execute:**

Continuing HomePerty (Task 10). Data is fully collected across all modules. Now build the reporting, analytics, and tax layer.

Build under `features/reports/`, `features/analytics/`, `features/tax/`:

**## Reports** (`/reports`)

1. **Reports home**: a grid of report cards. Each card = one report template with icon, name, one-line description.
   Report types to support:
   - Monthly Profit & Loss
   - Yearly Profit & Loss
   - Cashflow Statement
   - Property Performance (one property)
   - Portfolio Overview (all properties)
   - Income Breakdown (by category / by property)
   - Expense Breakdown (by category / by property)
   - Tenant Ledger (one lease)
   - Rent Roll (all active leases current)
   - Maintenance History
   - Renovation History (with ROI)
   - Contractor Costs
   - Occupancy Report
   - Vacancy Report
   - Tax Summary (year)
   - Depreciation Schedule
   - Mileage Log
   - Deposit Statement (one tenant)
   - Inspection Report (one inspection)
   - Insurance Claims Summary
   - Utility Consumption (property, year)
   - Short-term rental Performance (property or portfolio)
   - Custom Report Builder (see below)

2. **Report configuration screen** (`/reports/:reportType`):
   - Parameter form: date range, property picker (single/multi/all), currency (default from settings), grouping (month/quarter/year/category/property), comparison toggle (this year vs last year), include archived (bool), include projections (bool).
   - Preview button renders the report inline.
   - Export button: PDF (via `pdf` package with a branded header, footer, page numbers), CSV, Excel (via `excel` package), print (via `printing`).

3. **Report rendering**:
   - Structured document composed of sections: cover, KPIs, tables, charts, notes.
   - Consistent header/footer with property/portfolio name, date range, generation timestamp, page X of Y.
   - Chart-to-PDF: capture `fl_chart` widgets into images via `RepaintBoundary` + `toImage` for embedding in PDFs.

4. **Custom Report Builder**:
   - Let user assemble a report from blocks: KPI block, Income table, Expense table, Property table, Tenant table, Chart (choose type + data source), Text block. Save as a named template. Support running saved templates on demand.

5. **Accountant Package**:
   - One-click export that bundles:
     * All expenses (tax-deductible flagged) as CSV.
     * All income as CSV.
     * All receipt PDFs.
     * Mileage log CSV.
     * Depreciation schedule PDF.
     * Tax summary PDF.
   - Bundled into a ZIP via `archive` package. Share via `share_plus`.

**## Analytics** (`/analytics`)

6. **Analytics home**: an interactive dashboard with configurable widgets.
   - Time range selector: monthly, quarterly, yearly, lifetime, custom range.
   - Comparison mode: compare periods, properties, or categories.
   - Chart types: bar, stacked bar, line, area, pie, donut, heatmap calendar, scatter, treemap.
   - Available widgets to add/remove/reorder:
     * Income vs Expenses bars
     * Net Profit line
     * Cashflow waterfall
     * Occupancy heatmap
     * Property performance table (sortable)
     * Category breakdown pie
     * ROI comparison bar chart across properties
     * Cap rate comparison
     * Cash-on-cash comparison
     * Portfolio value line chart
     * Expense trend by category (stacked area)
     * Short-term rental ADR + occupancy dual axis
     * Rent collection rate line
     * Maintenance cost per property bar
     * Renovation ROI ranking
   - Drill-down: tap a bar/segment to filter dashboard to that slice.
   - Save current layout as a named view.

7. **Portfolio comparison**:
   - A dedicated screen to compare 2-4 properties side-by-side on all KPIs.

8. **Trends & insights** (rule-based, no AI yet):
   - Automated observations panel: "Property X expenses increased 22% vs last month", "Tenant Y late 3 months in a row", "Renovation Z beat projected ROI", "Vacancy rate at property W is 18% higher than portfolio avg". Compute these deterministically from data.

**## Tax Center** (`/tax`)

9. **Tax home**: year picker at top, property picker (all or one).
   Sections:
   - Deductible expenses total (year) with category breakdown.
   - Rental income total.
   - Mortgage interest paid (auto-computed from mortgage transactions if tagged; user can override).
   - Property tax paid.
   - Depreciation (calculator below).
   - Capital gains (for sold properties).
   - Estimated taxable income = income − deductible expenses − mortgage interest − depreciation.

10. **Depreciation calculator**:
    - For each property, allow setting depreciation method (straight-line default, GDS, ADS, custom), useful life (default 27.5 years residential US, configurable), depreciable basis (purchase price minus land value; land value user-entered).
    - Auto-compute annual depreciation and remaining depreciable value per year.
    - Show a depreciation schedule table for all years since purchase.

11. **Tax records log**: manually enter tax payments (property tax, income tax) with amount, date, jurisdiction, doc link.

12. **Yearly tax summary PDF**: comprehensive one-page summary per property + portfolio total.

13. **Multi-jurisdiction note**: keep tax logic jurisdiction-agnostic; expose configurable defaults (depreciation useful life, land ratio, deduction categories). Do not hardcode any country-specific rules.

14. **Providers reactive** to underlying data.

15. **Tests**: unit tests for straight-line depreciation, tax summary aggregation, mileage deduction math, capital gains basic calc.

Deliverables: production-ready reporting engine, all export formats functional, analytics dashboard with at least 8 chart widgets working with real streamed data.

---


### [DONE] Task 11: Calendar, Notifications & Calculators

**Completed:** 2026-07-22 — Full time-and-money layer. **Calendar** (`features/calendar/`): pure `CalendarAggregator` (`domain/calendar_item.dart`, unit-tested) merges 10 sources — rent due, lease renewals, inspections, maintenance scheduled + warranty, renovation warranty, inventory warranty, STR check-in/check-out, monthly mortgage payment dates, insurance renewals, and manual `calendar_events` (incl. tax category) — into typed `CalendarItem`s carrying origin kind/id so taps deep-link to the owning entity; `CalendarScreen` with Month/Week/Day (table_calendar) + Agenda views, 13-kind multi-select filter chips + property filter, colored day dot markers, day bottom sheet, and a manual-event form sheet (create/edit/delete/mark-complete, all-day, category, property link, reminder offset none→1 week); `TodayCalendarCard` on the dashboard. **Notifications:** `NotificationService` (`data/services/notification_service.dart`) wraps flutter_local_notifications 19 with timezone-aware zonedSchedule (`timezone` + `flutter_timezone` deps), one Android channel per `NotificationChannel` (10 channels), POST_NOTIFICATIONS request, `notifications_log` rows as both audit trail and idempotency store, and a tap-payload broadcast stream the router consumes for deep links; pure `NotificationPlanner` (`features/notifications/domain/notification_plan.dart`, unit-tested) computes all 8 auto-rules (rent lead N-days configurable, rent-late immediate, lease ≤60d, insurance ≤30d, warranty ≤30d across maintenance/renovation/inventory, tax 30/14/7/1, maintenance/inspection day-before at 09:00, backup ≥14d) plus quiet-hours deferral (reminders inside the window shift to its end; midnight-crossing windows handled); `runNotificationScheduler` keepAlive provider fires on router build (fire-and-forget, idempotent); settings screen with master + per-channel toggles, rent-lead slider (0–14d), quiet-hours pickers, and a test button, persisted by the keepAlive `NotificationSettingsStore`. Manifest gained POST_NOTIFICATIONS / RECEIVE_BOOT_COMPLETED / VIBRATE; `main.dart` initializes the service best-effort pre-runApp (UncontrolledProviderScope over a shared container); **`android/app/build.gradle.kts` now enables core-library desugaring** (`desugar_jdk_libs` 2.1.5) — flutter_local_notifications hard-requires it and `assembleDebug` fails without it. **Calculators** (`features/calculators/`): pure `finance_calculators.dart` (mortgage payment + amortize with extra monthly principal, cap rate, cash-on-cash, renovation ROI, cashflow, STR profitability incl. break-even occupancy, appreciation/inflation growth, loan payoff, refinance break-even, 1%/50% quick rules, rent-vs-buy); `calculator_catalog.dart` declares 13 calculators as data-driven `SimpleCalcSpec`s rendered by one generic `SimpleCalculatorScreen` (300 ms debounce, money/percent/years fields, result panel, named scenario save/recall in shared_preferences, share one-pager, `?propertyId` prefill) plus a bespoke `MortgageCalculatorScreen` with a principal-vs-interest amortization LineChart; calculators home grid; routes `/calculators` + `/calculators/:calcType` live. ~143 new l10n keys (cal*/calc*/ntf*). Tests: finance_calculators (incl. the 200 000 @ 4 %/30 y → $954.83 spec fixture), calendar_aggregator, notification_planner (incl. 3 quiet-hours cases) — 250/250 total green; `flutter analyze` zero issues; debug APK builds. **Session note:** the original Task 11 session ended mid-flight without Resume Instructions (code written, but codegen not run, ~143 l10n keys missing, desugaring absent — analyze had ~180 errors and the Android build failed); a follow-up audit session completed codegen + l10n, fixed imports, made the scheduler/settings providers keepAlive (prevents mid-run disposal of the fire-and-forget scheduler), added event mark-complete, implemented the quiet-hours deferral (was configured but unenforced), and fixed the Gradle build. **Deviations to know:** (1) manual events omit endDate / tenant link / custom color (category color is used) and recurrence — `calendar_events` has no recurrence column in the Task 2 schema; add via customFields + a migration only if actually needed. (2) Calculator share is a text one-pager, not PDF; "Apply to Property" is realized as `?propertyId` prefill plus named scenario presets in shared_preferences rather than DB-linked scenario rows. (3) Mortgage supports extra monthly principal but not a dated one-time extra-payments list. (4) Lead-time config covers the rent slider; other channels use the fixed spec windows. Gabriel should smoke-test on device: notification permission prompt + test button, calendar views, and a couple of calculators.

**Scope:** Unified calendar aggregating events from every module, local notifications engine with auto-scheduling, 12+ financial calculators with amortization charts and scenario save.

**Prompt to execute:**

Continuing HomePerty (Task 11). Now build the time-and-money tools layer.

Build under `features/calendar/`, `features/notifications/`, `features/calculators/`:

**## Calendar**

1. **Calendar screen** (`/calendar`):
   - Views: Month, Week, Day, Agenda (list). Toggle via segmented control. Use `table_calendar` for month/week/day; agenda is a chronological list grouped by day.
   - Category filter chips at top: Rent Due, Lease Renewal, Inspection, Maintenance, Cleaning, Mortgage, Insurance, Taxes, Appointment, Custom, Short-term rental Check-in, Short-term rental Check-out. Multi-select.
   - Property filter dropdown.
   - Event markers on days: colored dots (up to 4 visible), overflow count. Color = category color.
   - Tap a day → bottom sheet with that day's events.
   - Tap an event → event detail sheet with actions (Edit, Delete, Mark Complete, Open Related Entity).
   - FAB: New Event.

2. **Event sources** (aggregated automatically, not just manual events):
   - `rent_payments.dueDate` → Rent Due events.
   - Leases with `endDate` within a rolling window → Lease Renewal events (30/60/90 day markers).
   - Inspections `scheduledDate`.
   - Maintenance `scheduledDate`.
   - Short-term rental bookings check-in and check-out.
   - Mortgages monthly payment date.
   - Insurance `renewalDate`.
   - Tax deadlines (user-defined + optional country presets).
   - Warranty expiration (maintenance, renovation, inventory).
   - Manual `calendar_events` entries.
   The calendar UI merges these into a single timeline. Each merged item includes its origin entity so tapping navigates correctly.

3. **New Event flow** (`/calendar/event/new`):
   - Manual events go into `calendar_events` table.
   - Fields: title, description, date, endDate (optional), all-day toggle, category, property link, tenant link, color, notification offset (none/at time/15min/1hr/1day/1week before).
   - Recurring events: reuse the recurrence rule builder.

4. **Widget on Dashboard**: "Today" mini-calendar showing today's events.

**## Notifications**

5. **Local notification engine** (`data/services/notification_service.dart`):
   - Use `flutter_local_notifications` (add to pubspec).
   - Support scheduled, repeating, and instant notifications.
   - Notification channels: rent_due, rent_late, lease_expiring, insurance_expiring, warranty_expiring, tax_reminder, maintenance_reminder, inspection_reminder, backup_reminder, custom.
   - Every scheduled notification is logged in `notifications_log` so we can see history and cancel.

6. **Auto-scheduled notifications** on data changes:
   - When a `rent_payment` is generated, schedule a reminder 3 days before dueDate (configurable in Settings).
   - When a `rent_payment` goes late, fire an alert.
   - When a `lease.endDate` is within 60 days, schedule a lease renewal reminder.
   - When `insurance.renewalDate` is within 30 days, remind.
   - When warranty end within 30 days, remind.
   - When tax due (from `calendar_events` or `tax_records`), remind 30/14/7/1 days out.
   - When maintenance scheduled, remind day before.
   - When no backup has run in 14 days, remind.

7. **Notifications screen** (in Settings > Notifications):
   - Toggle channels on/off.
   - Configure default lead times.
   - Quiet hours configuration.
   - Test notification button.

8. **Handle notification tap** → deep link into the relevant entity via GoRouter.

9. **Permission handling**: request `POST_NOTIFICATIONS` on first schedule (Android 13+). Graceful degrade if denied.

**## Calculators** (`/calculators`)

10. **Calculators home**: grid of cards.
    - Mortgage Calculator
    - ROI Calculator
    - Cash Flow Calculator
    - Cap Rate Calculator
    - Cash-on-Cash Return
    - Break-even Occupancy
    - Short-term rental Profitability
    - Renovation ROI
    - Property Appreciation
    - Inflation-adjusted Value
    - Loan Payoff
    - Refinance Comparison
    - Rent vs Buy (bonus)
    - 1% & 50% Rules Quick Check (bonus)

11. **Each calculator has**:
    - Clean input form with unit hints (currency picker per session), sliders where useful, tooltips explaining terms.
    - Instant recomputation on any field change (debounced 300ms).
    - Result panel with primary output big, secondary outputs beside, and a chart where relevant (e.g., mortgage amortization line chart with principal-vs-interest area).
    - "Apply to Property" button that saves the inputs and results as a scenario linked to a property.
    - Share/export the result as a PDF one-pager.
    - Preset scenarios: save named scenarios for later recall.

12. **Mortgage Calculator specifics**:
    - Inputs: principal, annual rate, term months, start date, extra monthly principal, extra one-time payments (list with dates + amounts).
    - Outputs: monthly payment, total interest, total paid, payoff date, amortization table, principal vs interest chart, savings from extra payments.

13. **Refinance Comparison specifics**:
    - Two-column side-by-side of current loan vs new loan (rate, term, closing costs). Compute break-even months (closing costs / monthly savings).

14. **Short-term rental Profitability specifics**:
    - Inputs: property purchase price, monthly costs (mortgage, tax, insurance, utilities, cleaning, supplies, mgmt), average nightly rate, occupancy %, platform fee %.
    - Outputs: monthly revenue, monthly costs, monthly profit, annual profit, cap rate, cash-on-cash if downpayment provided, break-even occupancy %.

15. **Break-even Occupancy specifics**:
    - Reverse of Short-term rental Profitability: given costs and rates, output the minimum occupancy % needed to break even.

16. **All calculators route params include preset** (`?propertyId=…`) to prefill from a property's data.

17. **Tests**: unit test every calculator against known good outputs (mortgage: 200000 @ 4% for 30y → $954.83/mo etc.).

---

### [DONE] Task 12: Global Search, Settings, Security, Backup & I/O

**Completed:** 2026-07-22 — Full search/settings/security/backup/I-O layer. **Search** (`features/search/`): `search_providers.dart` (recent searches last-10 in prefs, `SearchFilters` — type set / property / date range — and a `searchResults` family provider that runs the Task 2 FTS `SearchRepository` then batch-resolves hits to display rows with title/subtitle/route/propertyId/date) + `search_screen.dart` (autofocus field, 200 ms debounce, type/property/date-range chips, results grouped by entity type with bm25 order + exact-title promotion, FTS `snippet()` `[…]` markers rendered bold, recent-search list, deep links that record history). Quoted phrases and `-` exclusion came free from the Task 2 sanitizer; terms AND by default (an explicit `+` operator is redundant in FTS5 and stripped). **Security** (`features/security/`): pure `pin_hasher.dart` (PBKDF2-HMAC-SHA256, self-describing `pbkdf2-sha256$iter$salt$hash` encoding, constant-time verify, escalating `lockoutFor` 30 s→15 min after 3 failures); `security_providers.dart` (`SecuritySettingsStore` in prefs — lock/biometric/auto-lock 0/1/5/15/60/never/screenshot-protection — plus `SecurityService`: PIN hash + fail counter + lockout in `flutter_secure_storage`, local_auth biometrics, and a `homeperty/secure_screen` MethodChannel whose Kotlin side in **MainActivity.kt** toggles FLAG_SECURE); `pin_pad.dart` (reusable pad + `showPinPadSheet`); `lock_screen.dart` (LockScreen with countdown lockout + auto biometric prompt, and `AppLockGate` — a WidgetsBindingObserver overlay mounted in **app.dart**'s builder that locks on cold start and after the background timeout). **Backup** (`features/backup/` + `data/services/backup_service.dart`): pure `backup_codec.dart` — `.ppb` envelope `PPB1|ver|salt16|iv16|hmac32|ciphertext`, AES-256-CBC, PBKDF2 100 k iterations deriving split AES/MAC keys, encrypt-then-MAC so wrong passphrase/tamper fails before decrypt, plus `BackupMetadata` json; service builds the ZIP (point-in-time DB snapshot via `VACUUM INTO`, typed prefs dump, the four attachment subtrees, metadata.json), writes to `documents/backups/`, shares via sheet, `history()` lists the folder; **staged restore** — decrypted DB written as `.pending-restore`, `applyPendingRestoreIfAny()` in **main.dart** swaps it in (live → `.bak`, WAL/SHM cleared) before Drift opens, attachments+settings applied immediately, restart dialog closes the app; auto-backup (toggle/daily-weekly-monthly/retention prune, passphrase in secure storage) runs fire-and-forget on router build; `backup_screen.dart` covers create/restore/auto-config/history(share/restore/delete). **Import/Export** (`data/services/portfolio_io_service.dart` + settings screens): full-portfolio JSON export = generic `SELECT *` of **every** Drift table (`db.allTables`) into `tables.<name>`; JSON import = schema-checked `INSERT OR IGNORE` merge in one transaction with deferred FKs (existing ids win); CSV/Excel import via pure `csv_import.dart` (per-target field specs for properties/tenants/expenses/income, money parser handling `1,234.56`/`1.234,56`/`$`, multi-format date parser, row mapping with per-row errors) → `csv_mapping_screen.dart` (target picker, header auto-map, per-field column dropdowns, 10-row preview + error list) → entities created with find-or-create categories by name; raw SQLite import validates the file with `sqlite3` (now a direct dep) then stages it through the same pending-restore swap (replace, not row-merge — documented); attachments ZIP export; per-entity CSV exports (properties/tenants/income/expenses); rolling 20-entry import log in prefs. **Settings** (`features/settings/`): `app_settings_providers.dart` (accent color, font scale 85–130 %, currency display symbol/code + separator style feeding the now-settings-driven `currencyFormatter` provider, region date-format/first-day, behavior defaults: default property/rent-due day/grace days/depreciation years/rent-to-income %) — **`PpTheme.light()/dark()` gained an optional `accent`** that re-tints primary/containers; screens: settings home (grouped tiles + RESET-typed app reset wiping DB/attachments/prefs/secure keys + version footer), appearance (RadioGroup theme mode, swatch accent picker, segmented font scale), language (system/English + 6 disabled "coming soon" stubs), currency (live preview, default currency sheet, symbol toggle, separator dropdown), region, behavior, data & storage (`data_tools_screen.dart`: db size + schema version card, orphan cleanup dry-run→confirm, rebuild FTS via the Task 2 `rebuildSearchIndex()`, photo-trash purge, VACUUM, run-recurring-now income+expense, run-notification-scheduler-now, clear caches), import/export, about (version/build, `showLicensePage`, privacy note, mailto support, crash-log export — **main.dart error zone now appends to `crash_log.txt`**, resolving the Task 1 TODO). Router: `/search` + 12 settings routes real (License stays a Task 13 stub); ~185 new l10n keys (srch*/set*/app*/lang*/cur*/reg*/beh*/sec*/bak*/io*/data*/about*). Deps added: `pointycastle` (AES/PBKDF2), `sqlite3` promoted direct. Tests: `pin_hasher_test.dart` (6, incl. RFC vector + lockout curve), `backup_codec_test.dart` (6, round-trip/wrong-pass/tamper/magic/metadata), `csv_import_test.dart` (11, money/date/mapping) — **273/273 total green**; `flutter analyze` zero issues; debug APK builds. **Deviations to know:** (1) "Data & Storage" and "Advanced" share one screen at `/settings/data`. (2) Backups live in app-scoped `documents/backups/` and exit the device via the share sheet (covers the file_picker-save-dialog intent under scoped storage). (3) SQLite import replaces the DB (with `.bak`) rather than row-merging; JSON import is the merge path. (4) Ranking approximates the spec's exact-title>tag>OCR>notes ladder with column-weighted bm25 + exact-title promotion. (5) Accent tints `primary`/containers only (no full M3 re-seed); date-format/first-day-of-week are persisted + exposed as providers but not yet threaded through every existing date render. (6) Per-entity Excel export is served by the existing transactions screens; the Import/Export screen exposes CSV. Gabriel should smoke-test on device: set a PIN + biometric + auto-lock, background/reopen, screenshot protection, create + restore a backup (restart flow), a CSV import, and global search.

**Scope:** FTS-powered global search, complete settings screens, PIN + biometric app lock, encrypted backup with passphrase, restore, import (CSV/Excel/JSON/SQLite), full export.

**Prompt to execute:**

Continuing HomePerty (Task 12). Now build the search, settings, security, and data I/O layer.

Build under `features/search/`, `features/settings/`, `features/security/`, `features/backup/`:

**## Global Search**

1. **Search screen** (`/search`):
   - Full-screen search UI: prominent search field, live results as user types (debounced 200ms).
   - Results grouped by entity type: Properties, Tenants, Leases, Documents (with OCR match snippet), Transactions, Maintenance Tickets, Contractors, Renovations, Photos, Notes.
   - Each result row: entity icon, primary label, secondary context (e.g., property name for a tenant), matched-text highlight.
   - Recent searches history (last 10) persisted in shared_preferences.
   - Filter chips: entity types, date range, property.
   - Empty state with suggested searches.

2. **FTS integration**:
   - Use the FTS5 virtual tables from Task 2.
   - `SearchRepository.query(String query, filters)` returns a fused result set.
   - Rank results by: exact title match > tag match > OCR content match > notes match > older matches.
   - Support quoted phrases and simple + / − operators.

3. **Deep link** every result to its detail screen.

**## Settings**

4. **Settings home** (`/settings`): grouped list.
   - Account & License → `/settings/license`
   - Appearance: theme (system/light/dark), accent color picker, font size scale.
   - Language: locale picker (English default; RO/FR/DE/ES/IT/PT stubs).
   - Currency: default currency, display format (symbol vs code, decimal separator, thousand separator).
   - Region: date format (auto/MM-DD-YYYY/DD-MM-YYYY/YYYY-MM-DD), first day of week.
   - Security → `/settings/security`
   - Notifications → `/settings/notifications`
   - Backup & Restore → `/settings/backup`
   - Import / Export
   - Data & Storage: database size, orphaned files cleanup, rebuild search index, purge trash.
   - Behavior: default property (used when quick-adding), default rent-due day, default late fee grace period, default depreciation useful life, rent-to-income warning threshold, quiet hours.
   - Advanced: developer options (view database size, migration version, run recurring engine now, run notification scheduler now, clear caches).
   - About: version, build number, licenses, privacy note, contact.
   - Reset app (with strong confirmation).

**## Security**

5. **Security screen** (`/settings/security`):
   - Enable App Lock toggle.
   - PIN: set/change/remove (4-6 digits, stored hashed via `crypto` + salt in `flutter_secure_storage`).
   - Biometric: toggle on (uses `local_auth`). Fallback to PIN if biometric unavailable.
   - Auto-lock timeout: immediately / 1 min / 5 min / 15 min / 1 hr / never.
   - Screenshot protection toggle (`FLAG_SECURE` on Android).
   - Sensitive fields reveal requires biometric confirm (already scaffolded on Tenant government ID field).

6. **Lock screen**: shown on cold start and after auto-lock trigger. PIN pad UI or biometric prompt. Fail counter with escalating delays.

**## Backup & Restore**

7. **Backup service** (`data/services/backup_service.dart`):
   - Full backup = SQLite DB copy + attachments folder + settings JSON, packed into a single `.ppb` file (ZIP internally, custom extension).
   - Encryption: AES-256 with user-supplied passphrase (via `crypto` package + `pointycastle` if needed — add pointycastle to pubspec). Derive key with PBKDF2. Store salt in the backup header.
   - Metadata: schemaVersion, appVersion, createdAt, propertyCount, sizeBytes.

8. **Backup UI** (`/settings/backup`):
   - Manual backup: Create Backup now → prompt for passphrase → save via `file_picker` save dialog OR to app external storage `/Documents/HomePerty/backups/`.
   - Restore: pick file → prompt for passphrase → validate schema version → restore transactionally (rename current DB to `.bak` before overwriting).
   - Auto-backup: toggle + frequency (daily/weekly/monthly) + retention (last N backups) + reminder if overdue. Auto-backups run on app open if due.
   - Backup history list with size + date + restore/delete actions.

9. **Import** (from Settings > Import / Export):
   - CSV: pick file → map columns → target entity (properties, tenants, transactions). Preview 10 rows → confirm.
   - Excel: same via `excel` package.
   - JSON: full-file JSON matching our exported schema.
   - SQLite: raw DB merge (advanced; behind confirmation, useful for migrating from a Theta Pilot–style structure).
   - Import log with success/error counts.

10. **Export**:
    - Full portfolio JSON export.
    - Per-entity CSV / Excel exports (already covered in reports; expose here too).
    - Attachments ZIP.

**## Storage Housekeeping**

11. Database optimization: `VACUUM` command runnable from Advanced settings.
12. Orphaned-file cleanup: scan storage, remove files not referenced by any DB row.
13. Rebuild FTS index: for cases where search returns stale results.

**## Tests**

14. Unit tests: PIN hashing round-trip, backup encryption round-trip, CSV import mapping.

Deliverables: functional search across all data, hardened security with PIN + biometric, robust backup/restore, full I/O.

---

### [DONE] Task 13: Licensing System (Google Sheets server)

**Completed:** 2026-07-22 — Full freemium licensing against Gabriel's Google Sheets server. **Domain** (`features/licensing/domain/`, pure + unit-tested): `license_config.dart` (deployed `/exec` URL, appId `homeperty`, app token from the `_Config` tab); `license_logic.dart` — `LicenseState` (tier free/pro, keyCode, activatedAt, lastVerifiedAt, `keyFingerprint` = ••••-last4), hyphen/case-insensitive `classify` (freeMode / factoryMode / regular / malformed), the five `LicenseServerResult` verdicts, `activationOutcome` mapping, `shouldReverify` (Pro-via-key + ≥7 days), `applyReverify` (**'used'/'ok' → refresh timestamp & stay Pro; 'revoked'/'invalid' → downgrade to free; 'error'/offline → state untouched, retried next open** — so the device keeps working offline indefinitely and only an explicit server kill downgrades), `canAddProperty` (free = 1 active property, `kFreeTierPropertyLimit`); `purchase_gateway.dart` — `PurchaseGateway` interface + `StubPurchaseGateway` listing the monthly/yearly Pro plans with a clearly-marked TODO(play-billing) seam. **Service** (`data/services/license_service.dart`): `LicenseClient` posts `{appId, code, token}` with dart:io HttpClient and **manually follows the Apps Script 302 → GET** (HttpClient won't auto-follow POST redirects); every failure path collapses to `error`; `LicenseStorage` persists the state in flutter_secure_storage. `data/services/app_reset_service.dart` extracts the full-wipe (DB + sidecars + pending-restore + all attachment subtrees + backups + crash log + prefs + secure storage `deleteAll`) now shared by Settings > Reset app and the factory key. **Providers** (`licensing_providers.dart`): keepAlive `LicenseController` (async build from storage; `redeemKey` burns a key server-side and activates Pro on 'ok'; `revertToFree`; `reverifyIfDue`), `canAddPropertyProvider`, plus `licenseClient`/`purchaseGateway`. Router runs `reverifyIfDue()` fire-and-forget on app open (≈weekly cadence). **UI** (`license_screen.dart`, serves `/settings/license` **and** `/licensing/activate`): current-plan card (Free/Pro, key fingerprint, activated + last-verified dates), Free-vs-Pro comparison table, Monthly/Yearly Go-Pro buttons through the gateway stub ("subscriptions coming soon"), license-key field with full outcome messaging, support mailto; **special keys handled locally, never sent to the server**: `HOMEPE-FREE-MODE` (confirm → revert to free even from Pro) and `HOMEPE-FACTORY-MODE` (strong confirm → `AppResetService.wipeAllData` → app closes → fresh onboarding). **Enforcement**: `PropertyLimitGate` wraps `/properties/new` (upgrade screen with limit banner when free already has its property) and `ensureCanAddProperty` guards the two property **Duplicate** actions (list + detail); no startup gate — the free tier is the baseline, the old Task-3 license redirect stub was removed and every route is now a real screen (`_stub`/ComingSoonScreen dropped from the router). ~40 new l10n keys (lic*). Tests: `license_logic_test.dart` — 13 cases (classification, verdict parsing, reverify due/apply incl. offline-keeps-Pro, outcome mapping, property gate, fingerprint) — **286/286 total green**; `flutter analyze` zero issues; debug APK builds. **Live server test (this session, PowerShell against the real deployment):** `HOMEPE-ECY3-H88B` first POST → `ok` (key burned, authorized by Gabriel), second POST → `used` (the exact verdict the weekly re-check relies on), wrong token → `error: Unauthorized`, unknown key → `invalid` — the full contract verified end-to-end. **Notes:** the app token is embedded by design of this server (app-level shared secret, not a user credential); revoking a key in the sheet downgrades that device on its next successful weekly check; sample data can seed multiple properties regardless of tier (the limit gates *adding*, existing data always stays visible); subscriptions are UI-ready but inert until Play Billing lands in the `PurchaseGateway` seam. Gabriel should smoke-test on device: redeem a fresh key from the sheet, kill/restore connectivity during redeem, `HOMEPE-FREE-MODE`, `HOMEPE-FACTORY-MODE` (wipes the device data!), and the one-property gate (add property → blocked on second in free mode).

**⚠️ SUPERSEDED 2026-07-22 (Gabriel):** the Theta Pilot pattern below is replaced by Gabriel's **Google Apps Script + Google Sheet licensing server** (multi-app; HomePerty registered with appId `homeperty`). New model:
- POST `{ appId: 'homeperty', code, token }` to the deployed `/exec` URL → `{ result: 'ok'|'used'|'revoked'|'invalid'|'error' }`. Keys **burn** on first redemption ('ok'); re-posting the same key returns 'used' (proof it still exists) or 'revoked'/'invalid' (killed/deleted).
- **Free tier is the baseline** (no 14-day trial, no startup gate): every feature works but the portfolio is limited to **1 property**. Adding a 2nd property requires **Pro** — future monthly/yearly subscription (placeholder UI now via the `PurchaseGateway` stub) or a redeemed key (for friends/Gabriel).
- Special local keys (never sent to the server): `HOMEPE-FREE-MODE` reverts to the free tier even if Pro was unlocked; `HOMEPE-FACTORY-MODE` factory-resets the app (full wipe → onboarding).
- **Weekly re-validation**: while Pro-via-key, re-POST the stored key about every 7 days; only an explicit `revoked`/`invalid` downgrades to free — network failure/offline keeps Pro working indefinitely.
- Server URL + app token live in `lib/features/licensing/domain/license_config.dart` (token from the _Config sheet). Test key `HOMEPE-ECY3-H88B` authorized to be burned during this task's live test.
- Items from the original prompt that still apply: activation/upgrade screen UI quality, Settings > Account & License management, `licenseStateProvider`-style Riverpod state, `PurchaseGateway` abstraction stub, unit tests. Trial logic, gift codes, offline-only validation, clock-rollback anti-tamper, and `reference_thetapilot/` are dropped.

The original (now superseded) prompt is preserved below as the audit trail.

**Scope:** License activation gate, gift code redemption, trial period tracking (14 days), offline validation, clock-rollback and backup-restore anti-tamper, activation UI, settings management, dev-only gift code generator script.

**Prompt to execute:**

Continuing HomePerty (Task 13). Now build the licensing system, mirroring the pattern from my existing Theta Pilot app.

**REFERENCE MATERIAL:** I have attached (or placed in `reference_thetapilot/`) the licensing-related files from Theta Pilot. Read them first and mirror the architecture and validation approach in HomePerty. Preserve the pattern for:
- License key format & validation
- Gift code generation & redemption
- Trial period tracking
- Offline validation (no network calls)
- Any anti-tamper checks
- License states (trial, active, expired, gifted, revoked, unlicensed)

Build under `features/licensing/`:

1. **LicenseService** (`data/services/license_service.dart`):
   - Same key format as Theta Pilot.
   - Store the active license in `flutter_secure_storage` under a namespaced key.
   - Validate on app open. Cache validation result for a session but re-verify integrity fields (hash, expiry) each cold start.
   - Support license states: trial, active, gifted, expired, revoked, unlicensed.
   - Trial: 14 days from first launch (configurable constant). Store first launch timestamp in secure storage.
   - Gift codes: same generation/redemption logic as Theta Pilot's gift system. On redeem, mark license as gifted and set the appropriate expiry (perpetual or time-bounded per code metadata).

2. **License gate**:
   - On app cold start, after unlock (if security enabled), check license state.
   - If unlicensed or expired: route to `/licensing/activate` and block all main navigation.
   - If in trial: allow full access but show a persistent (dismissible per session) banner with days remaining and a "Buy / Activate" CTA.
   - Trial expiry → force to `/licensing/activate`.

3. **Activation screen** (`/licensing/activate`):
   - Clean welcome layout with brand mark.
   - Segmented control: Enter License Key | Redeem Gift Code | Continue Trial (if still eligible).
   - License Key mode: paste field, Activate button, on success show a big check + auto-navigate to `/dashboard`.
   - Gift Code mode: paste field, Redeem, success flow same as license.
   - Continue Trial: only visible if user hasn't started trial yet OR trial has days remaining. Shows remaining days.
   - Restore Purchase button (placeholder for future Play Billing integration — do not implement Play Billing now; leave a clearly labeled TODO with an abstraction interface `PurchaseGateway` that has stub in-memory implementation).
   - Support Contact: displays a `mailto:` link.
   - Error handling: invalid key, revoked key, expired key, malformed input.

4. **License management** (Settings > Account & License):
   - Current status card: type (trial/active/gifted), issued to (if applicable), expiry date, days remaining.
   - Actions: Enter new key, Redeem gift, View license terms, Deactivate on this device (with confirmation — clears secure storage entry).
   - Show license fingerprint (last 4 chars of key) for support reference.

5. **Anti-tamper** (matching Theta Pilot):
   - Detect clock rollback: compare stored `lastKnownEpochMs` vs current; if rollback > 24hr, force re-validation.
   - Detect fresh install with pre-existing DB (someone restored a backup with a used trial): trial start is stored in secure storage AND mirrored in `app_metadata` table; conflict triggers re-activation prompt.
   - Do NOT block on missing network; validation is fully offline.

6. **Gift code generation utility** (dev-only tool):
   - A CLI-style Dart script under `tools/generate_gift_codes.dart` that generates N gift codes with configurable duration/features, output to CSV. Same signing/format as Theta Pilot.

7. **Providers**:
   - `licenseStateProvider` (StreamProvider) that emits state changes.
   - Guards in the router redirect based on this provider.

8. **Copy & UI**:
   - Follow the same tone and layout as Theta Pilot's licensing screens where possible (professional, clean, friendly).
   - Include a small feature comparison card on the activation screen ("Free Trial vs Full Version") — customize the feature list for HomePerty: full trial has everything; expired trial loses ability to add new records but retains read/export.

9. **Tests**:
   - Unit tests: license key parse/validate, gift code redeem, trial expiry logic, clock rollback detection.

Deliverables: fully working license gate that mirrors Theta Pilot's proven pattern, keeping the entire system offline and hardened.

---

### [DONE] Task 14: Sample Data, Testing, Android Release & Documentation

**Completed:** 2026-07-22 (two launch-prep sessions) — Release-ready v1.0. **Session 1 (launch prep):** support email → `kingnerdishere@gmail.com`; **biometric fixed** (`MainActivity` → `FlutterFragmentActivity` + `USE_BIOMETRIC` permission — local_auth needs a FragmentActivity host); **safe-area sweep** — 53 Scaffold bodies wrapped in `SafeArea(top: false)` via scripted transform (Android 15 edge-to-edge put bottom buttons under the system nav; convention now in CONTRIBUTING.md); manifest permissions declared; docs shipped (README rewrite, ARCHITECTURE.md, CHANGELOG.md, CONTRIBUTING.md, LICENSE, `docs/PLAY_STORE_DATA_SAFETY.md`, `docs/PLAY_STORE_CHECKLIST.md`, `docs/PRIVACY_POLICY.md`); release signing completed earlier the same day (JKS upload keystore, `keystore.properties` **UTF-8-BOM trap** documented in the template). **Session 2 (Gabriel's bug batch + policy):** (1) **Photo-picker migration** — `READ_MEDIA_IMAGES`/`READ_MEDIA_VIDEO` removed AND force-stripped from plugin merges via `tools:node="remove"` (Play now restricts them; image_picker uses the system photo picker permission-free; no video anywhere). (2) **Crop-on-import** — new `image_cropper` dep + `UCropActivity` in manifest + `lib/shared/utils/image_crop_helper.dart` (`cropPickedImage`, aspect locked to the target surface): property cover crops at 3:2 (`kCoverAspectRatio`), tenant photo at 1:1. (3) **Cover-photo replace fixed** — `PhotoService.importFile(replaceCover: true)` deletes the old cover (row + files) and promotes the new one via `setAsCover`; property form passes it. (4) **Tenant form fixed** — steps swap in one ListView, so text-field *elements were reused across steps* (contact step's phone keyboard leaked onto the occupation field): ListView now keyed `ValueKey(_step)` (same fix applied to renovation form; property form already had it); `_save` also got first/last-name guard + try/catch (failures used to hang the spinner silently). (5) **Rent dashboard €0.00 fixed** — the calendar's `onPageChanged` never updated `RentListFilter.month`, so the KPI row stayed pinned to the current month; browsing months now drives the KPIs. (6) **Firebase sync prep** — `CloudSyncStore` opt-in (prefs), 6th onboarding page ("Sync to the cloud?") + Settings → Cloud sync toggle (labels honest: "activates with the Google sign-in update"), and `docs/FIREBASE_SETUP.md` — a complete guide (project setup, SHA-1s incl. Play App Signing, google-services.json, Auth/Firestore/Storage rules, packages, the `SyncRepositoryDecorator` + outbox architecture on the Task 2 repository seam, cost notes, pre-ship checklist incl. data-safety revision + account deletion). Version `1.0.5+5`; analyze 0 issues; 286/286 tests; signed AAB built. **Deviations (moved to post-1.0 backlog, below):** sample data stays at the Task 3 three-property seed (spec's 5-property rich generator not built); widget tests remain at the existing 3 (spec's ~12 additional + integration_test + coverage report not built); lottie assets remain path-stubs. These do not block the Play release and live on as the "Post-v1.0 polish backlog" note in User Notes.

**Scope:** Realistic multi-property sample data generator, comprehensive test suite (unit + widget + integration), Android release configuration with signing/icons/splash, README + ARCHITECTURE.md + Play Store readiness docs, final polish pass.

**Prompt to execute:**

Continuing HomePerty (Task 14 — final). Now finish the app: sample data, tests, Android release config, and documentation.

**## Sample Data**

1. Enhance `SampleDataService` (from Task 3) into a realistic seed generator invoked from Onboarding (opt-in) and from Settings > Advanced > Load sample data.
   Content to generate:
   - 5 properties: 2 long-term rentals (apartment + house), 2 Short-term rental (apartment + condo), 1 under renovation.
   - Each property: purchase details, mortgage on 3 of 5, insurance/tax, cover photo (bundled placeholder JPGs under `assets/sample_photos/`), 4-6 gallery photos, 2 documents (mortgage PDF placeholder, insurance PDF placeholder).
   - 6 tenants across the long-term rentals with realistic names, pets on 2 of them (a dog and a cat), vehicles on 3, guarantors on 2, one prospective applicant in screening.
   - Active leases with 12 months of `rent_payments` generated: 9 paid, 1 partial, 1 late, 1 upcoming.
   - 15 Short-term rental bookings across the past 6 months on the two Short-term rental properties with varied nightly rates, cleaning fees, platform mixes.
   - 60+ expense transactions across categories, some recurring (mortgage, insurance, HOA).
   - 30+ income transactions.
   - 3 renovation projects: 1 completed with ROI, 1 in progress, 1 planning.
   - 8 maintenance tickets across statuses and priorities.
   - 4 contractors with job histories.
   - 10+ documents with OCR text populated.
   - 1 completed move-in inspection.
   - Utility meters + a few readings + bills per property.
   - Custom fields examples on 2 properties.
   - Tags applied liberally.
   Bundle all sample photo/PDF placeholders under `assets/sample_data/` and register in pubspec.

**## Testing**

2. **Unit tests** (`test/unit/`):
   - `Money` value object (rounding, formatting, currency conversion via constant rates for testing).
   - Property statistics calculations.
   - Recurring transaction generation edge cases.
   - Late fee application logic.
   - Rent escalation calculation.
   - Depreciation schedule.
   - All calculators (mortgage, ROI, cap rate, etc.).
   - License key parse & validate.
   - Backup encryption round-trip.
   - CSV import mapping.
   - OCR field extraction heuristics on fixture text.
   - Short-term rental ADR and occupancy calc.

3. **Widget tests** (`test/widget/`):
   - Dashboard renders empty state.
   - Property list renders and filters.
   - Property detail loads and shows correct stats.
   - Tenant form validation.
   - Lease creation flow submits correctly.
   - Rent payment logging updates ledger.
   - Booking creation flow.
   - Expense form with split expense percentages validation.
   - Calculator form recomputes on input.
   - Search returns results.
   - Backup/restore flow (mocked storage).
   - Licensing gate blocks unlicensed.

4. **Integration test** (`integration_test/`): a happy-path end-to-end: onboard → skip trial → activate with a test key → add property → add tenant → create lease → log rent payment → confirm dashboard KPIs updated.

5. **Coverage target**: aim for 60%+ on domain + application layers. Report via `flutter test --coverage`.

**## Android Release Config**

6. Update `android/app/build.gradle`:
   - `applicationId com.kingnerd.homeperty` (corrected 2026-07-16: original prompt said com.gabrielrusu.* which is not Gabriel's name; the project was renamed before any release)
   - minSdk 24, targetSdk latest, compileSdk latest.
   - Configure ProGuard/R8 for release (keep rules for Drift, Riverpod codegen, freezed, mlkit, pdf, printing).
   - Signing config: use a `keystore.properties` file (gitignored). Provide `keystore.properties.template`.

7. **App icon**: generate adaptive icon (foreground + background) using `flutter_launcher_icons` (add as dev_dep). Provide a placeholder SVG under `assets/branding/logo.svg` with a clean geometric mark (house-outline + arrow motif) and background color from theme. Configure `flutter_launcher_icons` in pubspec.

8. **Splash screen**: use `flutter_native_splash` (add as dev_dep). Configure with brand logo + background color per theme. Support Android 12+ splash screen API.

9. **Permissions** in AndroidManifest.xml:
   - INTERNET (for future features; declare but never actually use in v1 — mark ready for cloud sync).
   - CAMERA (for scanning + photo capture).
   - READ_MEDIA_IMAGES / READ_MEDIA_VIDEO (Android 13+).
   - POST_NOTIFICATIONS (Android 13+).
   - USE_BIOMETRIC.
   - RECORD_AUDIO (voice notes on maintenance).
   - WRITE_EXTERNAL_STORAGE / MANAGE_EXTERNAL_STORAGE where needed for backups (prefer scoped storage; use SAF via `file_picker` for backup save/restore).

10. **Data safety declarations** helper: a doc file `/docs/PLAY_STORE_DATA_SAFETY.md` pre-filled with our data safety answers: no data shared, all data stays on device, encryption in transit N/A (no transit), encryption at rest for sensitive data yes.

**## Documentation**

11. **README.md** (comprehensive):
    - Project overview and value prop.
    - Screenshots section (placeholder markdown).
    - Feature matrix.
    - Tech stack.
    - Setup: prerequisites (Flutter version, Java, Android SDK), clone, `flutter pub get`, `dart run build_runner watch --delete-conflicting-outputs`, run on device, build release.
    - Project structure (folder tree).
    - Architecture summary (link to ARCHITECTURE.md).
    - Testing (how to run tests).
    - Release build: how to configure signing, build AAB, deploy to Play internal testing.
    - Licensing note.
    - License (proprietary or your chosen license).

12. **ARCHITECTURE.md**:
    - Layer diagram: Presentation → Application → Domain → Data → Infrastructure.
    - Riverpod usage patterns.
    - GoRouter route table.
    - Database schema overview + ER diagram (Mermaid).
    - How to add a new entity end-to-end.
    - How to add a new report.
    - How to add a new calculator.
    - How to add a new language.
    - Extension points for future cloud sync (mark the sync boundary — the RepositoryImpl layer is where a SyncRepositoryDecorator will be inserted).
    - Extension points for future AI features (a `QueryIntent` interface and `NaturalLanguageQueryService` stub — no implementation, just the seam).

13. **CHANGELOG.md**: version 1.0.0 initial release.

14. **CONTRIBUTING.md**: coding conventions, commit style, PR checklist.

15. **LICENSE** file.

**## Play Store Readiness Checklist** (`/docs/PLAY_STORE_CHECKLIST.md`):
   - App name, short description, full description drafts.
   - Screenshots list (which screens to capture, ideal aspect ratios).
   - Feature graphic prompt.
   - Content rating questionnaire notes.
   - Data safety answers.
   - Privacy policy URL (placeholder + template `PRIVACY_POLICY.md`).
   - Testing tracks strategy: internal → closed → open → production.
   - Version code / version name bump rules.

**## Final Polish**

16. **Empty state Lottie files**: create `/assets/lottie/` folder and reference paths from existing empty states. Include a placeholder README in that folder explaining which Lottie animations to source and drop in (each empty state names its file).

17. **Icon set audit**: ensure every entity/section uses a consistent lucide icon and same one everywhere.

18. **Copy pass**: replace any lorem/placeholder strings with real, professional English copy.

19. **Final smoke check**: on a fresh install, walk the entire nav — every route should render without crashes, every empty state should be graceful, every form should validate.

Deliverables: production-ready app with samples, tests, release config, and documentation. Ready to build the signed AAB and upload to Play Console internal testing.

---

---

## Resume Instructions

_This section is written by Claude if a task cannot be completed in a single session. When it is empty, no task is mid-flight and the next trigger command should start the next `[TODO]` task._

---

## Optional Post-v1.0 Tasks (do not run yet)

These are for later iterations, after v1.0 ships. Do not run them as part of the sequential build.

### [IN PROGRESS] Task 15 (Optional): Cloud Sync — **Firebase + Google sign-in** (chosen 2026-07-22)
Connect HomePerty to **Firebase** with **Google-account login** as the auth + backend. Introduce a `SyncRepositoryDecorator` at the `RepositoryImpl` seam that mirrors local data to the signed-in user's cloud store. No offline-first / on-device-only guarantee is required (that mandate was retired 2026-07-22) — offline should still degrade gracefully, but data syncing to the cloud per user account is the goal. Runs after the core build is complete, not before.

**Phase 1 (auth) done 2026-07-23:** Gabriel wired Firebase himself (flutterfire configure → `firebase.json` + `lib/firebase_options.dart`, project `homeperty-1659d`, google-services plugin 4.5.0 in both Gradle files, `google-services.json` gitignored, firebase_core/auth + cloud_firestore + firebase_storage + google_sign_in ^7.x in pubspec). Claude then built the auth layer: `lib/data/services/cloud_auth_service.dart` (`CloudAuthService` — google_sign_in 7.x Credential Manager flow → `signInWithCredential`; cancel returns null; missing idToken throws `CloudAuthConfigException`; plus keepAlive `cloudAuthServiceProvider` + `cloudUserProvider` auth-state stream), fail-soft `Firebase.initializeApp` in main.dart, Settings → Cloud sync (toggle ON runs Google sign-in and only sticks on success; account tile with avatar/name/email + Sign out; sign-in prompt tile when enabled-but-signed-out; OFF signs out, local data untouched), onboarding cloud page (toggle triggers sign-in, shows "Signed in as {email}"), 5 new l10n keys + honest subtitle copy ("automatic sync activates in an upcoming update"). **Blocked on Gabriel:** `google-services.json` has an empty `oauth_client` — Google provider must be enabled in Firebase Authentication and all three SHA-1s (debug/upload/Play App Signing) registered, then the JSON re-downloaded, or sign-in fails at runtime.

**Phase 2 FINAL (2026-07-23, third session): automatic versioned cloud backup** — this REPLACED the same-session password-protected single-slot design (never released; its `cbk*` l10n keys, `cloud_backup_info.dart`, and password UI were deleted). Per Gabriel's "seamless like Google Photos" directive: **no passwords, no Backup-Now/enable/disable buttons, no configuration** — signed-in **Pro** users are backed up automatically. Architecture: plain compressed ZIP archives (`BackupService.buildBackupArchive` — the `.ppb` payload without the passphrase envelope; local `.ppb` exports keep encryption) → Firebase Storage `users/{uid}/backups/{versionId}.hpb`, **last 10 versions** (`kCloudBackupRetention`), single transactional manifest doc `users/{uid}/backupMeta/current` (one read per status check, multi-device safe). **Scheduler** `CloudBackupController` (`features/backup/application/cloud_backup_scheduler.dart`, keepAlive, started on router build): Drift `tableUpdates()` filtered (`app_metadata`/`notifications_log` ignored — prevents churn loops) → 5-min debounce/20-min cap, flush on app-background + connectivity-return + startup catch-up (>24 h), exponential-backoff silent retries; **content-signature dedup** (SHA-256 of DB snapshot + attachment listing, volatile prefs excluded; prefs `cloud.lastSignature`/`cloud.dirty`/`cloud.lastUid` — uid change resets dedup) so unchanged data never re-uploads; skip-empty guard (empty local DB never buries a cloud history with real data). **Restore**: any version, from the minimal Settings → Cloud backup screen (status ✓ Synced/Last backup/Storage used/Retention + Restore + Sign out + subdued Delete cloud data & account) or the automatic post-sign-in "Backup found — Restore/Not now" offer (`cloud_restore_offer.dart`, hooked into onboarding sign-in, cloud screen sign-in, and the **post-key-redemption "Pro includes cloud backup — Connect Google account" prompt** in license_screen.dart). Restore is NOT Pro-gated (only uploads are) — a reinstalling key user recovers data before re-redeeming. Hardening from this session: `CloudAuthService` resolves `FirebaseAuth.instance` **lazily + fail-soft** (eager resolution crashed when Firebase init fail-softed — found via the boot widget tests), connectivity plugin guarded. `cld*` l10n keys; tests `cloud_backup_models_test.dart` (9); **295/295 green, analyze 0**; smoke-verified on the Pixel 9 emulator (screen renders, sign-in launches Credential Manager, scheduler silent). **Remaining for Gabriel: follow `docs/CLOUD_SETUP_GUIDE.md`** (enable Firestore + Storage, paste rules — backups fail softly with permission errors until then) and run its §3 smoke test with a real Google account. All legal docs rewritten again to match (no E2E-encryption claims — security = Firebase Auth + owner-only rules + TLS + Google at-rest encryption; support CAN access backups for authorized support, per privacy policy).

### [TODO] Task 16 (Optional): AI Features
Implement the `NaturalLanguageQueryService` seam. Route natural-language questions to structured queries against the repositories. Local-first with optional cloud model.

### [TODO] Task 17 (Optional): Home Screen Widgets & Wear OS
Home screen widget for today's rent + occupancy. Wear OS companion for quick payment logging on the go.

### [TODO] Task 18 (Optional): iOS Port
Add `ios/` platform, adjust permissions, submit to App Store.

### [TODO] Task 19 (Optional): Multi-user / Property Manager Mode
Multiple owner profiles, role-based access, staff members with permissions.

### [TODO] Task 20 (Optional): Advanced Short-term rental Sync
iCal import for cross-platform calendar sync (still offline-first, just parses `.ics` files the user drops in).

---

## User Notes & Reminders

- **Post-v1.0 polish backlog** (deferred from Task 14, non-blocking): rich 5-property sample-data generator, ~12 additional widget tests + integration_test happy path + coverage report, real Lottie animations for empty states. Pick up in a targeted chat, not by re-running Task 14.
- **Firebase cloud backup**: shipped 2026-07-23 as **automatic, versioned, Pro-only backup** (no passwords, no manual buttons — see the Task 15 note above; `docs/FIREBASE_SETUP.md` is the architecture reference). Blocked on Gabriel following **`docs/CLOUD_SETUP_GUIDE.md`** (enable Firestore + Storage, paste rules) + its on-device smoke test with a real Google account. A live sync engine remains a future option (FIREBASE_SETUP.md §4) and the `users/{uid}` namespace is already shaped for future sharing features.

- **After each task**, remind Gabriel to commit with a tag like `task-04-done`. Suggest `git tag task-04-done` after commit.
- **Task 2 is large** (40 tables). If Fable capacity looks tight, split it: half the tables + entities first session, remaining tables + all repositories second session. Update Resume Instructions accordingly.
- ~~**Task 13 needs Theta Pilot files** placed in `reference_thetapilot/` at the repo root before starting.~~ **Obsolete 2026-07-22** — Task 13 was rebuilt on Gabriel's Google Sheets licensing server instead (appId `homeperty`; server URL + token in `lib/features/licensing/domain/license_config.dart`). Managing keys = the sheet's "Licensing" menu (generate/revoke/stats). Special keys: `HOMEPE-FREE-MODE` (back to free), `HOMEPE-FACTORY-MODE` (full wipe).
- **Post-task verification**: after each `[DONE]`, Gabriel should run the app on device and click through the new module before proceeding.
- **If a task's code needs revision** later, do NOT run the task again from scratch. Instead, open a targeted chat about the specific fix. The task remains `[DONE]`.
- **Application ID history**: Task 0 scaffolded with org `com.gabrielrusu.*` (wrong name) → corrected to `com.gabrielgiurca.homeperty` → changed 2026-07-16 to **`com.kingnerd.homeperty`** (matches the Play Console account used for Theta Pilot; MainActivity moved accordingly). This is the id to type into Play Console. Any future reference to the app id must use the kingnerd form.
- **Proprietary names removed 2026-07-16** (Play-readiness): "Airbnb"/"Booking.com"/"VRBO" no longer appear anywhere in code, copy, or this file. Terminology is now "Short-term rental" (STR): table `short_term_bookings`, entity `ShortTermBooking`, `ShortTermRentalRepository` (+ provider `shortTermRentalRepositoryProvider`), routes `/short-term-rentals[...]` (`Routes.shortTermRentals`, `Routes.shortTermBooking(New)`), l10n `menuShortTermRentals`, files `short_term_entities.dart`/`short_term_tables.dart`. `BookingPlatform` enum is now `{ listingSite, direct, other }` and bookings gained a free-text `platformName` column where the user types the site's name themselves. Sample data category id is `inc_cat_str`. Future tasks must keep proprietary platform names out of shipped code/copy.
- **Rebranded to HomePerty 2026-07-16** (was PropertyPilot): Dart package `property_pilot` → `homeperty` (test imports use `package:homeperty/...`), `applicationId`/`namespace` → `com.kingnerd.homeperty`, launcher label "HomePerty", Drift DB file name `homeperty`, all l10n/docs updated. The `Pp*` design-system widget/class prefix (PpCard, PpTheme, …) is deliberately KEPT — it is the internal design-system prefix and future task prompts reference it. Launcher icon source: `assets/icons/Homeperty icon.png` (white adaptive background, generated via flutter_launcher_icons). Splash: native white launch background + Android 12+ transparent splash icon, then an animated Flutter splash (`lib/shared/widgets/splash_screen.dart`, white→soft-grey gradient, logo fade+scale in, ~2.1 s hold, fade-out into app) mounted via MaterialApp.router `builder` overlay.
