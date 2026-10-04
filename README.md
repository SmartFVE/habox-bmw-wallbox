# bmw_wallbox (HABOX patched build)

Fork of [JoaoPedroBelo/bmw-wallbox-ha](https://github.com/JoaoPedroBelo/bmw-wallbox-ha) **v1.7.6** (MIT, see LICENSE)
with a small patch for BMW Wallbox (Delta Gen4, FW 01.20.06.71) whose internal clock runs ~50 s behind.

Repository root = Home Assistant integration folder (`custom_components/bmw_wallbox`), pulled by the HABOX
deploy (component_pull, lock `habox/sync/components/bmw_wallbox.lock.yml`) only for clients that enable it
in `client.conf.yml` `component_overrides`.

## Patch
- `SetChargingProfile.startSchedule` = now - 5 min (was now). With the wallbox clock behind, profiles starting
  "now" became active only ~50 s later; a controller changing the limit every 15 s never got an active profile
  and the car charged at hardware maximum.
- `currentTime` in BootNotification / Heartbeat responses sent as RFC 3339 UTC with `Z`
  (was `datetime.utcnow().isoformat()` without timezone).

Verified live (Koukal Solax 2026-10-04): 6 A applied immediately (4.18 kW), 6 -> 9 A mid-session in ~20 s
(6.0 kW), pause effective within 20 s.

## Updating
Rebase on a newer upstream tag, re-apply the two changes above, bump nothing else, push `main`.
