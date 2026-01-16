# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Sebastian's Home Assistant Add-ons** is a Home Assistant add-on repository that aggregates multiple add-ons for easy installation in Home Assistant.

**Repository Type**: Home Assistant add-on collection/aggregator
**Maintainer**: Sebastian (schmacka)
**URL**: https://github.com/schmacka/homeassistant-addons

## Purpose

This repository serves as a **distribution point** for Home Assistant add-ons. It does not contain the actual add-on source code - instead, it pulls pre-built add-ons from their individual repositories.

### Add-ons Included

1. **Printernizer** - 3D printer fleet management system
   - Source: https://github.com/schmacka/printernizer-ha
   - Branch: `master`
   - Path: `printernizer`

2. **RTSP to Prusa** - RTSP camera streaming for Prusa printers
   - Source: https://github.com/schmacka/Prusa-Connect-RTSP-HA
   - Branch: `main`
   - Path: `prusa_connect_rtsp`

## Architecture

This repository uses the **Home Assistant Add-on Repository** structure:

### Key Files

- **`.addons.yml`** - Defines which add-ons to include and where to pull them from
- **`repository.json`** - Repository metadata for Home Assistant
- **`README.md`** - User-facing documentation

### Directory Structure

```
homeassistant-addons/
├── .addons.yml              # Add-on aggregation config
├── repository.json          # HA repository metadata
├── README.md               # User documentation
├── printernizer-ha/        # Printernizer add-on (auto-synced)
├── rtsp-to-prusa-ha/       # RTSP to Prusa add-on (auto-synced)
└── .github/
    └── workflows/          # GitHub Actions for auto-sync
```

## Important: Do NOT Modify Add-on Code Here

⚠️ **CRITICAL**: This repository is an **aggregator only**.

- **Do NOT modify code in `printernizer-ha/` or `rtsp-to-prusa-ha/` directories**
- These directories are automatically synced from their source repositories
- Any changes made here will be overwritten

### To Make Changes to Add-ons

1. **Printernizer**: Edit in https://github.com/schmacka/printernizer (main repo)
2. **RTSP to Prusa**: Edit in https://github.com/schmacka/Prusa-Connect-RTSP-HA

Changes will automatically sync to this repository via GitHub Actions.

## How the Aggregation Works

### `.addons.yml` Configuration

The `.addons.yml` file defines the add-on sources:

```yaml
channel: stable
addons:
  printernizer-ha:
    repository: schmacka/printernizer-ha
    branch: master
    source_path: printernizer
    target: printernizer-ha
  rtsp-to-prusa-ha:
    repository: schmacka/Prusa-Connect-RTSP-HA
    branch: main
    source_path: prusa_connect_rtsp
    target: rtsp-to-prusa-ha
```

### GitHub Actions

- `.github/workflows/` contains automated sync workflows
- Syncs run periodically or on manual trigger
- Pulls latest changes from source repositories

## Common Tasks

### Adding a New Add-on to the Repository

1. Create or identify the source repository for the add-on
2. Update `.addons.yml` with the new add-on configuration:
   ```yaml
   addons:
     new-addon-name:
       repository: username/repo-name
       branch: main
       source_path: addon_folder
       target: new-addon-name
   ```
3. Update `README.md` with add-on information
4. Trigger the sync workflow or wait for automatic sync

### Removing an Add-on

1. Remove the add-on entry from `.addons.yml`
2. Update `README.md` to remove add-on documentation
3. Optionally delete the add-on directory (will be removed on next sync)

### Updating Repository Metadata

Edit `repository.json`:
```json
{
  "name": "Sebastian's Home Assistant Add-ons",
  "url": "https://github.com/schmacka/homeassistant-addons",
  "maintainer": "Sebastian"
}
```

## Installation (for Users)

Users add this repository to Home Assistant:

1. Navigate to **Supervisor** → **Add-on Store**
2. Click menu (⋮) → **Repositories**
3. Add: `https://github.com/schmacka/homeassistant-addons`
4. Install individual add-ons from the store

## Development Workflow

### Making Changes to This Repository

Only modify these files:
- `.addons.yml` - Add-on configuration
- `repository.json` - Repository metadata
- `README.md` - User documentation
- `.github/workflows/` - Sync automation

### Testing Changes

1. Fork the repository
2. Make changes to configuration files
3. Test the sync workflow
4. Create a pull request

## Support and Issues

- **Repository issues**: https://github.com/schmacka/homeassistant-addons/issues
- **Printernizer issues**: https://github.com/schmacka/printernizer-ha/issues
- **RTSP to Prusa issues**: https://github.com/schmacka/Prusa-Connect-RTSP-HA/issues

## Related Repositories

- **Printernizer Main**: https://github.com/schmacka/printernizer
- **Printernizer HA Add-on**: https://github.com/schmacka/printernizer-ha
- **RTSP to Prusa**: https://github.com/schmacka/Prusa-Connect-RTSP-HA

---

**Key Takeaway**: This is an aggregator repository. For add-on code changes, always work in the source repositories, not here.
