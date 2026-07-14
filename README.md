# King Nerd Software Legal Center

Source for the King Nerd Software legal and support website. The published site is intended to provide company-wide legal documents and an app-specific legal page for every released Windows or Android application.

## Publishing changes from VS Code

This repository is connected to `https://github.com/KingNerd88/kingnerd-legal.git`. The local `main` branch tracks `origin/main`.

1. Make and save your changes in VS Code.
2. Open **Source Control**, review the changed files, write a commit message, and click **Commit**.
3. Click **Sync Changes** (or the cloud/sync icon). VS Code pulls incoming changes, then pushes your commit to GitHub.

`Sync Changes` only sends committed changes. Resolve any merge conflict shown by VS Code before syncing again. Do not include private keys, customer data, or app signing files in this repository.

## Adding an application

1. Copy `app-template.html` to a short, lowercase filename such as `my-app.html`.
2. Replace every `[placeholder]` and remove sections that do not apply.
3. Confirm the data, permissions, third-party services, store, support route, and any domain-specific warning against the released app.
4. Add a card and link for the app to `index.html`.
5. Commit and sync the changes.

The company-wide pages set the baseline. An app page supplies the details for that application and prevails where it differs. Have a qualified Romanian/EU lawyer review the documents before release, especially when an app handles tenant, health, financial, or regulated-equipment information.
