# Central thermostat for Sonoff TRVZB (ZHA)

A Home Assistant automation blueprint that lets one central thermostat drive any
number of Sonoff TRVZB radiator thermostats, using an external room sensor.
Everything is configured in the UI - no entity IDs in YAML.

What it does:

- The target temperature of the central thermostat is copied to every selected
  TRV. Nothing else: the TRVs' mode is never changed, and each TRV opens and
  closes its valve by itself.
- The room sensor's temperature is written to each TRV's
  **External temperature sensor value**, on every change and every N minutes
  (so the TRV never falls back to its internal sensor).
- Each TRV's **External temperature sensor** switch is kept on.
- The external temperature entities are found automatically from the TRV's
  device; offline TRVs are skipped.

Requires Home Assistant 2024.10 or newer.

## Setup (once per room)

1. **Install the blueprint** (only the first time): click

   [![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FDurakovicM%2Fhass-central-thermostat-trvzb%2Fblob%2Fmain%2Fcentral_thermostat_trvzb.yaml)

   or go to *Settings -> Automations & scenes -> Blueprints -> Import blueprint*
   and paste this URL:

   `https://github.com/DurakovicM/hass-central-thermostat-trvzb/blob/main/central_thermostat_trvzb.yaml`

   To update later: on the blueprint's ⋮ menu choose *Re-import blueprint*.
2. **Dummy switch**: *Settings -> Devices & services -> Helpers -> Create helper
   -> Toggle*, e.g. "Living room heating dummy". It is required by the generic
   thermostat but controls nothing.
3. **Central thermostat**: *Create helper -> Generic thermostat*
   - Name: e.g. "Living room"
   - Heater: the dummy toggle from step 2
   - Temperature sensor: your room sensor
4. **Automation**: *Settings -> Automations & scenes -> Blueprints ->
   "Central thermostat for Sonoff TRVZB (ZHA)" -> Create automation*, then pick
   - Central thermostat: the helper from step 3
   - Radiator thermostats: all TRVZB climate entities in the room
   - Room temperature sensor
   - Refresh interval (default every 15 minutes)
5. **Dashboard**: add a standard **Thermostat** card for the central thermostat.

For another room, repeat steps 2-5.

## Checking it works

- Change the target on the central thermostat -> all TRVs follow within seconds.
- On a TRV's device page, **External temperature sensor** is on and
  **External temperature sensor value** matches the room sensor.

## Notes

- Changes made directly on a TRV are not copied back; the central thermostat
  wins on its next change, and on every Home Assistant restart.
- Turning the central thermostat off does not turn the TRVs off. To turn
  heating off, lower the target temperature (or switch the TRVs off directly).
- External temperature is limited by the TRV to 0-99.9 °C.
