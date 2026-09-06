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
- [ ] Upload to Steam workshop
- [ ] Upload binary .zip releases
- [ ] Github CI/CD integration to auto upload to workshop
