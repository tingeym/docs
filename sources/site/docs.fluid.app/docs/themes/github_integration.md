# Source: https://docs.fluid.app/docs/themes/github_integration

# GitHub Theme Integration – User Guide

The GitHub Theme Integration lets you create themes in Fluid directly from GitHub repositories and keep them automatically in sync.

This guide explains how to connect GitHub, create themes, and work with publishes and syncs.

---

## What is the GitHub Theme Integration?

The GitHub Theme Integration allows you to:

- Connect a GitHub repository and branch
- Automatically create a new theme from that branch
- Keep the theme synced with GitHub
- Publish changes from Fluid back to GitHub

Each GitHub connection creates a **new theme** in Fluid.

---

## How It Works

- You connect GitHub via a popup
- You select a repository and branch
- Fluid creates a **new theme** in an _Importing_ state
- Files are imported in the background
- GitHub and Fluid stay in sync automatically

You can connect multiple repositories and branches.

---

## Connecting GitHub

### Step 1: Click “Connect to GitHub”

From the Themes page, click **Connect to GitHub**.

- This opens a GitHub popup window
- If the Fluid GitHub App is not installed, GitHub will prompt you to install it
- If already installed, you can select additional repositories

---

### Step 2: Install or Authorize the GitHub App

In the popup:

1. Choose your GitHub account or organization
2. Select which repositories Fluid can access
3. Confirm installation or authorization

You can limit access to specific repositories.

---

### Step 3: Select Repository and Branch

After authorization:

1. Choose a repository
2. Choose a branch
3. Confirm

Fluid will immediately create a **new theme**.

---

## Theme Importing

After connecting:

- A new theme appears with status **Importing**
- Files are downloaded from GitHub in the background
- Once complete, the theme becomes available for editing

You don’t need to wait on the page — import continues automatically.

---

## Publishing Changes

When you publish a template in theme editor:

- A GitHub commit is created

Each publish is recorded in GitHub history.

---

## Deleting Files

When you delete a file or asset in Fluid:

- A GitHub commit is created
- The file is removed from the repository
- The deletion appears in GitHub history

---

## Syncing from GitHub

When changes are pushed to the connected branch:

- Only changed files are updated
- Deleted files are removed
- Renamed files are handled correctly

---

## Multiple GitHub Connections

You can:

- Connect multiple repositories
- Connect multiple branches
- Create multiple themes from GitHub

Each connection creates a separate theme.

---

## Summary

The GitHub Theme Integration allows you to:

- Create themes directly from GitHub
- Publish changes back to GitHub
- Collaborate using Git workflows
- Maintain a clean, auditable history