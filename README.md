# ha-shared

Shared Home Assistant blueprints and dashboard copies for two homes:

| Site | Name | Home Assistant |
|------|------|----------------|
| AZ | MFOITS (Fountain Hills) | HA Green |
| HI | MFIITS (Puako) | HA Green |

This repo is the **source of truth for blueprints**. Each Home Assistant keeps a
local copy under `blueprints/automation/local/` that is refreshed from here.

## Layout

```
blueprints/automation/summitak/
  lighting_guard.yaml               Lighting Guard (dusk/dawn, daylight sweep, max on-time, occupancy, failsafes)
  zooz_smart_switch.yaml            Zooz Smart Switch Suite (ZEN71/72/76/77/32/35 config + paddle actions)
  ZEN32-control-track-fix1.yaml     ZEN32/ZEN35 scene controller with LED tracking (local fork of rwalker777)
  hvac_door_open_protection.yaml    HVAC door-open AC lockout (Ecobee) - HI only for now
dashboards/
  az/lights-control.json            AZ "Lights" dashboard (storage-mode config)
  hi/lights-control.json            HI "Lights" dashboard (storage-mode config)
```

Each blueprint carries a `source_url` pointing at its file here, so Home Assistant
can pull updates with its built-in **Re-import blueprint** command.

## Updating a blueprint

1. Edit the file here (or ask Claude to) and bump the version in its description.
2. Commit to `main`.
3. At **each** site: Settings -> Automations & Scenes -> Blueprints -> the blueprint's
   menu (three dots) -> **Re-import blueprint**. Every automation using it reloads
   with the new version; their own inputs are kept.

Claude can also push the new version to both sites directly through the HA-MCP
connections.

> The HA copies only gain `source_url` (and the Re-import option) the next time the
> blueprint is saved from this repo. Until then, updates are pushed by Claude.

## Dashboards

Dashboards are **site-specific** (entity IDs differ between AZ and HI), so they are
kept here as version-controlled copies rather than synced files. Change a dashboard
in Home Assistant or here, then keep the copy in step. To restore or roll back, the
JSON is applied with the HA-MCP `ha_config_set_dashboard` tool (full config).

Avoid the "Git pull" app for this repo: it replaces the whole `/config` folder,
and the two sites' configurations are not identical.

## Conventions

- On/off light loads are exposed as `light.*` entities via **Change device type of a
  switch** (`switch_as_x`); the original `switch.*` is hidden.
- Zooz switch breaker panel/circuit is recorded in each Smart Switch Suite automation,
  not in entity or device names.
- No-load (REM/NL) switch entities are kept off dashboards.
- **Areas are physical rooms only.** Software-only devices and integrations get no area.
  Hub hardware (HA Green, ZWA-2, network gear) goes in the room it physically sits in.
- **`System` label** (grey, `mdi:server`, same at both sites) marks infrastructure:
  OS/Core/Supervisor/Host, Backup, apps, radio controllers, network gear,
  Marketplace/HACS items and software integrations (AI, TTS, MCP, HomeKit bridge).
- **Site labels** `Arizona` (orange) and `Hawaii` (indigo) exist at both sites.
- **Shared Ecobee account:** both Home Assistants see all nine Ecobee devices. Every
  Ecobee device is named with its site prefix (`AZ ...` / `HI ...`) identically at both
  sites and carries its site label. At each site, the other site's Ecobee devices are
  disabled and have no area.
- **Per-site cloud accounts:** iAqualink and Tesla (Teslemetry) are integrated only at
  the site they serve.
