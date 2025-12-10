# Automatic Sync Webhook Setup

This document explains how to set up automatic syncing from source repositories.

## Overview

The `sync-addons.yml` workflow now supports automatic triggering via `repository_dispatch` webhooks. When you push changes to source repositories (printernizer-ha, Prusa-Connect-RTSP-HA), they can automatically trigger a sync in this add-ons repository.

## Setup Instructions

### 1. Create Personal Access Token (PAT)

You need a GitHub PAT with `repo` scope to trigger workflows in this repository.

1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Click "Generate new token (classic)"
3. Name it: `homeassistant-addons-sync-trigger`
4. Select scope: `repo` (full control of private repositories)
5. Click "Generate token"
6. **Copy the token** (you won't see it again!)

### 2. Add Secret to Source Repositories

For each source repository (`schmacka/printernizer-ha` and `schmacka/Prusa-Connect-RTSP-HA`):

1. Go to repository Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `ADDONS_REPO_TRIGGER_TOKEN`
4. Value: Paste the PAT from step 1
5. Click "Add secret"

### 3. Add Webhook Workflow to Source Repositories

Create `.github/workflows/trigger-addons-sync.yml` in each source repository:

**For printernizer-ha repository:**
```yaml
name: Trigger Add-ons Sync

on:
  push:
    branches:
      - master
    paths:
      - 'printernizer/**'

jobs:
  trigger-sync:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger homeassistant-addons sync
        run: |
          curl -X POST \
            -H "Accept: application/vnd.github.v3+json" \
            -H "Authorization: token ${{ secrets.ADDONS_REPO_TRIGGER_TOKEN }}" \
            https://api.github.com/repos/schmacka/homeassistant-addons/dispatches \
            -d '{"event_type":"sync-request","client_payload":{"repository":"printernizer-ha"}}'
```

**For Prusa-Connect-RTSP-HA repository:**
```yaml
name: Trigger Add-ons Sync

on:
  push:
    branches:
      - main
    paths:
      - 'prusa_connect_rtsp/**'

jobs:
  trigger-sync:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger homeassistant-addons sync
        run: |
          curl -X POST \
            -H "Accept: application/vnd.github.v3+json" \
            -H "Authorization: token ${{ secrets.ADDONS_REPO_TRIGGER_TOKEN }}" \
            https://api.github.com/repos/schmacka/homeassistant-addons/dispatches \
            -d '{"event_type":"sync-request","client_payload":{"repository":"Prusa-Connect-RTSP-HA"}}'
```

## How It Works

1. You push changes to source repository (e.g., `printernizer-ha/printernizer/config.yaml`)
2. The source repository's workflow triggers
3. It sends a `repository_dispatch` webhook to this repository
4. This repository's `sync-addons.yml` workflow runs automatically
5. Latest changes are synced within seconds!

## Fallback

If webhooks fail or aren't set up:
- Scheduled sync runs every 6 hours as fallback
- Manual sync can be triggered from Actions tab

## Testing

After setup, test by:
1. Making a small change in source repository
2. Push to the configured branch
3. Check Actions tab in both repositories
4. Verify sync completes successfully

## Troubleshooting

**Webhook not triggering:**
- Check that PAT has `repo` scope
- Verify secret name is exactly `ADDONS_REPO_TRIGGER_TOKEN`
- Check source repository Actions tab for trigger workflow status
- Verify paths match in source workflow

**Sync fails after trigger:**
- Check this repository's Actions tab for error logs
- Verify `.addons.yml` configuration is correct
- Ensure source files exist at configured paths
