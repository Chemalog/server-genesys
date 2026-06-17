# Genesis Modpack

Packwiz pack for the Genesis server. This repo is the **source of truth** for the launcher — every time a player clicks PLAY, the launcher reads this pack and syncs their mods automatically.

## Requirements

- [packwiz CLI](https://github.com/packwiz/packwiz/releases) — download and add to PATH
- Git

---

## How to update mods

### 1. Update your mods in CurseForge App

Add, remove, or update mods normally in the CurseForge app for your profile.

### 2. Export from CurseForge

In the CurseForge App:
`My Modpacks → right click your profile → Export Profile → Export`

Save the `.zip`, extract it. You'll get a folder with `manifest.json` inside.
Place (or replace) it at: `../genesis-neo/manifest.json`

### 3. Import into packwiz

```bash
cd genesis-modpack
packwiz curseforge import ..\genesis-neo\manifest.json
```

This updates all `.pw.toml` files to match your CurseForge export.

### 4. Refresh the index

**This step is mandatory.** Without it, the launcher won't detect the changes.

```bash
packwiz refresh
```

### 5. Push to GitHub

```bash
git add .
git commit -m "chore: update mods from CurseForge export"
git push origin main
```

That's it. The next time any player clicks **PLAY**, the launcher pulls the updated pack and downloads only what changed.

---

## Adding a single mod manually

```bash
# From CurseForge (by project ID — found in the mod's URL on curseforge.com)
packwiz curseforge add --project-id 394468

# From Modrinth (by slug or ID)
packwiz modrinth add sodium

# Then always refresh and push
packwiz refresh
git add .
git commit -m "feat: add <mod name>"
git push origin main
```

## Removing a mod

```bash
# Delete the .pw.toml file
del mods\sodium.pw.toml

packwiz refresh
git add .
git commit -m "chore: remove <mod name>"
git push origin main
```

---

## What happens on the player side

```
Player clicks PLAY
   └─ Launcher reads pack.toml + index.toml from GitHub (raw)
   └─ For each mod: checks local hash against index
       ├─ Hash matches → skip (already up to date)
       └─ Hash differs or missing → download
   └─ Minecraft launches with all mods in sync
```

No manual action needed from players. Everything is automatic.

---

## Pack structure

| File | Purpose |
|---|---|
| `pack.toml` | Pack metadata (MC version, NeoForge version) |
| `index.toml` | Master list of all files with SHA256 hashes |
| `mods/*.pw.toml` | One file per mod — download URL and hash |
| `config/` | Config files distributed to all players |
