# Temperature Overview Bars Card

[Deutsch](README.de.md)

A Home Assistant dashboard card that shows room temperatures as gradient bars (styled after the weather forecast card).

- Default scale **15–25 °C**
- Gradient: blue below 19 °C, **green 19–21 °C**, red above, with smooth transitions
- A dot marks the current temperature, the value is shown left of the bar
- Rooms are organized in **groups** (e.g. heated, unheated, outdoor), separated by a thin divider. Every group can have its **own scale**
- Each room has a **prioritized sensor list**: the first sensor with a valid value is shown (e.g. Zigbee first, Homematic as fallback)
- Clicking a row opens the more-info dialog of the sensor in use
- Fully configurable with the **visual editor**, nothing is pre-filled
- The card and editor UI is German if your Home Assistant user language is German, English otherwise

## Installation via HACS

1. HACS → menu (three dots) → **Custom repositories**
2. Enter the repository URL and choose type **Dashboard** (formerly "Lovelace")
3. Download "Temperature Overview Bars Card" and hard-reload the browser (Ctrl+F5)

HACS adds the resource automatically. Manually it would be `/hacsfiles/<repo-name>/temperature-bars-card.js` as type *JavaScript module*.

To update, click "Update" in HACS and hard-reload the browser.

## Setup

1. Edit dashboard → Add card → **Temperature Overview Bars Card**
2. Use **+ Add group** to create a group (the group name is only used in the editor). Optionally enter a custom scale; empty fields use the default scale of the card.
3. Use **+ Add room** inside a group and pick the HA area. The display name is optional, the area name is used otherwise.
4. Use **+ Sensor** to pick sensors. By default only the temperature sensors of the selected area are offered; a checkbox shows all temperature sensors.
5. **Add the area's sensors** adds all temperature sensors of the area at once, Homematic sensors last. Sensors, rooms and groups can be reordered with ▲ ▼.

## YAML example

```yaml
type: custom:temperature-bars-card
title: Temperatures
min: 15                    # default scale for groups without their own values
max: 25
green_from: 19
green_to: 21
decimals: 1
colors:                    # optional
  low: "#3b82f6"
  ok: "#4ade80"
  high: "#ef4444"
groups:
  - name: heated           # only used in the editor, not displayed
    rooms:
      - area: living_room  # HA area ID, optional
        name: Living room  # optional, area name otherwise
        sensors:           # priority from top to bottom
          - sensor.living_room_zigbee_temperature
          - sensor.living_room_radiator_temperature
  - name: outdoor
    min: -10               # own scale for this group
    max: 35
    green_from: 10
    green_to: 25
    rooms:
      - area: garden
        sensors:
          - sensor.garden_temperature
```

A configuration with `rooms:` at the top level (without groups) still works and is shown as one group.

## Options

| Option | Default | Description |
| --- | --- | --- |
| `title` | – | Card title |
| `min` / `max` | 15 / 25 | Default bar scale in °C |
| `green_from` / `green_to` | 19 / 21 | Default green comfort range |
| `decimals` | 1 | Decimal places |
| `colors.low/ok/high` | blue/green/red | Gradient colors |
| `groups[].name` | – | Group name, only visible in the editor |
| `groups[].min/max/green_from/green_to` | default scale | Own scale of the group |
| `groups[].rooms[].sensors` | – | Sensor list in priority order |
| `groups[].rooms[].area` | – | HA area, filters the sensor choice in the editor |
| `groups[].rooms[].name` | area name | Display name |

A room without a valid sensor value (all `unavailable`/`unknown`) is shown dimmed with "–".

## Releases

A GitHub Action (`.github/workflows/release.yml`) automatically creates a release `vX.Y.Z` when `CARD_VERSION` in `temperature-bars-card.js` is raised and pushed to `main`. HACS then shows the new version as an update. If the release already exists, nothing happens.

To test a change without bothering other users, set `CARD_VERSION` to a pre-release version such as `0.2.0-beta.1`. It is published as a GitHub pre-release, which HACS only shows to users who enabled **Show beta versions** for this repository. Once it works, publish the stable version (e.g. `0.2.0`).
