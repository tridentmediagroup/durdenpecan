# Theme Development & Version Control Guide

This document outlines how version control is managed for this Shopify theme.
Because this project does **not** use the Shopify–GitHub integration, all updates
to the live theme must be synced manually using the Shopify CLI and Git.

---

## 🔧 Overview

- The **`main`** branch mirrors the **currently published** theme on Shopify.
- All development work occurs on **feature branches**.
- Git **tags** are used to mark each version that has been synced with the live theme.

---

## 🧭 Example Workflow

1. **Sync the live theme**
   
Pull the latest version of the live theme to make sure your local `main` branch matches what's currently published.

```bash
shopify theme pull
git add .
git commit -m "Sync from live store"
```

2. Tag live version (Marks sync point)
```bash
git tag v1.x-live
git push origin --tags
```

3. Start new feature (Work safely in isolation)
```bash
git checkout -b feature/...
```

4. Develop and preview changes:
```bash
shopify theme dev
shopify theme push --theme <dev-theme-id>
```

5. Periodically sync merchant edits

If the merchant makes updates in the live store (via Theme Editor or Code Editor), you'll want to keep main up to date:

```bash
git checkout main
shopify theme pull
git add .
git commit -m "Sync merchant edits from live"
git tag v1.x-live
git push origin --tags
```

6. Then, rebase your feature branch to include those edits to ensure it's up to date and free of conflicts when you go to open a PR:
```bash
git checkout feature/...
git fetch origin
git rebase origin/main
```

Resolve conflicts if any, test again, and continue working.

7. Open a PR on Github

8. Review and merge PR into `main` branch

9. Pull the updated `main` locally and tag it:
```bash
git pull origin main
git tag v1.x
```

10. Final live sync check.

Before deploying, confirm that no additional merchant edits were made since your last pull:

```bash
shopify theme pull
git add .
git commit -m "Final sync before deployment"
```

This ensures you don't overwrite any Theme Editor changes.

11. Deploy to live

Push the updated `main` (now tagged and verified) to the live theme:

```bash
shopify theme push --theme <live-theme-id>
```

12. Tag the deployed version

Record this as your official live release:

```bash
git tag v1.x-live
git push origin --tags
```

## 🏷️ About Tags
Tags are immutable markers for released versions.

View them at any time:
```bash
git tag
```

Compare versions:
```bash
git diff v1.1-live v1.2-live
```

Revert to a prior release:
```bash
git checkout v1.1-live
shopify theme push --theme <live-theme-id>
```

## 🧠 Best Practices
- Never develop on main: Keep main a clean mirror of the live store.
- Tag every live deployment: Tags are your safety net.
- Commit before every pull from Shopify: This makes it easy to see exactly what changed on the live store.
- Write meaningful commit messages: ("Sync from live after merchant edits" is better than "update.")
- Optional: Maintain a CHANGELOG.md summarizing each tagged version.

## ✅ Example Tag History

- v1.0-live: Initial theme sync
- v1.1-live: Merchant made text edits
- v1.2-live: Footer redesign deployed
- v1.3-live: New homepage hero added
