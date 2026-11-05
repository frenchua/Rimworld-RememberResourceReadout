# About
A small mod to the video game RimWorld to remember the Resource readout (the list of resources in the upper left corner of the screen), so that it remembers if each entry is expanded or collapsed either on a
per-save or global basis (assuming the resource readout is set to sort itself by category).

It also adds a right-click context menu to the resource readout so that you can easily expand/contract a certain category (including all of it's subcategories), or expand/contract everything.

## Screenshots
<img width="244" height="170" alt="Screenshot 2026-09-01 005459" src="https://github.com/user-attachments/assets/76ed8e71-2d72-460e-9957-aa1044c32cf1" />
<img width="920" height="186" alt="Screenshot 2026-09-01 005110" src="https://github.com/user-attachments/assets/5812071e-56a3-4f31-ac1e-6a61496d0da8" />


# Installing the mod
1. Clone the repository into the `Mods` directory of your RimWorld install (on windows with Steam this is often `C:\Program Files (x86)\Steam\steamapps\common\RimWorld`)
2.  Run `dotnet build` from within this directory (or open the `.csproj` file in visual studio and build if from there) and you should be good to go.

# TODO
- [x] Make `.csproj` file work on all platforms
- [x] Add option to remember resource readout expansion state between game loads.
- [x] Add mod settings dialog.
- [ ] Add Version 1.7 support (including targeting .NET 10)
- [ ] Upload to Steam workshop
- [ ] Upload binary .zip releases
- [x] Github CI/CD integration to auto upload to workshop

# Releasing

Pushing to `main` runs `.github/workflows/steam-workshop.yml`, which builds both RimWorld
versions, stages a clean mod folder (`About/`, `1.6/Assemblies`, `1.7/Assemblies`, `README.md`)
and updates the Workshop item named in `About/PublishedFileId.txt`. The workflow can also be
started by hand from the Actions tab, with an optional changenote.

## One-time setup

Two repository secrets are needed, both from a Steam account that owns RimWorld and the
Workshop item:

1. `STEAM_USERNAME` — the account name.
2. `STEAM_CONFIG_VDF` — a SteamCMD login session, so CI never sees the password:
   - run `steamcmd +login <username> <password> +quit` locally and enter the Steam Guard code,
   - run `steamcmd +login <username> +quit` again to confirm it no longer asks,
   - paste the contents of SteamCMD's `config/config.vdf` into the secret.

   If the session later expires, run `steamcmd +set_steam_guard_code <code>` locally and
   replace the secret with the new `config.vdf`. Never commit `config.vdf` — it is a credential.

## Limitations

- SteamCMD cannot set Workshop tags. Existing tags survive an update, but new ones (such as
  the `1.7` version tag) have to be added once by hand on the Workshop page.
- The preview image is not re-uploaded; changing `About/Preview.png` still means updating the
  Workshop page manually.

# Building without RimWorld 1.7 installed

The `net10.0` (RimWorld 1.7) target normally compiles against the game's own
`Assembly-CSharp.dll` and `UnityStubs.dll`. When those are not on disk — on CI, or on a machine
without 1.7 — the build falls back to the prerelease `Krafs.Rimworld.Ref` package and emits a
warning saying so. Point it at a real install with `-p:RimWorld17Dir=<folder>`.
