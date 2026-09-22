# Snipe-IT Monitoring

Zabbix template for [Snipe-IT](https://github.com/snipe/snipe-it) — tracks inventory
totals and watches open asset maintenances via the Snipe-IT REST API.
All items are Zabbix HTTP agent checks, nothing is installed on the target.

Developed and tested on Snipe-IT v6.1.2 and Zabbix 7.4.

## Metrics

- Totals: **assets, users, licenses, models, accessories, components,
  consumables, locations, manufacturers, suppliers**
- **Maintenances total** and **open maintenances** (no completion date yet)
- LLD: asset count per **category** and per **status label**
  (supports user-renamed categories and labels)
- LLD: **open maintenances** with their **age in days** per record

## Triggers

Per open maintenance, from the age item:

- **Warning** — maintenance is open for more than `{$SNIPEIT.MAINT.AGE.WARN}`
  days (default 30)
- **High** — maintenance is open for more than `{$SNIPEIT.MAINT.AGE.CRIT}`
  days (default 90). The Warning trigger depends on the High one, so only the
  higher severity is shown

Notes on lifecycle:

- a completed maintenance drops out of discovery; its items, triggers and
  graphs are removed automatically after the discovery lifetime (7 days)
- both triggers support manual close
- threshold values can be changed per host with the macros

## Graphs and dashboard

Template graphs:

- **Snipe-IT: Inventory totals** — assets, users, licenses, models, open maintenances
- **Snipe-IT: Supplies and structure** — consumables, accessories, components,
  locations, suppliers, manufacturers, maintenances total
- per-maintenance **age graph** (created by discovery for every open record)

The **Snipe-IT overview** template dashboard shows the key totals and both
template graphs on the host's Dashboards page.

## Setup

1. In Snipe-IT create an API token (user profile → API Keys).
2. Import `snipeit_monitoring.yaml` into Zabbix (Alerts → Templates → Import).
3. Create a host for the instance (no interface is required), link the template.
4. Set macros on the host:

| Macro | Default | Description |
|---|---|---|
| `{$SNIPEIT_URL}` | — | Base URL, e.g. `https://snipeit.example.com` |
| `{$SNIPEIT_TOKEN}` | — | API token (stored as a secret macro) |
| `{$SNIPEIT.HTTP_TIMEOUT}` | `15s` | Timeout of every API request |
| `{$SNIPEIT.MAINT.AGE.WARN}` | `30` | Warning threshold for maintenance age, days |
| `{$SNIPEIT.MAINT.AGE.CRIT}` | `90` | Critical threshold for maintenance age, days |
| `{$SNIPEIT.MAINT.LIMIT}` | `1000` | Max maintenance records fetched per poll |

## Notes

- All checks verify HTTP 200 and use TLS peer verification (`verify_peer`).
  If the instance uses a self-signed certificate, disable **Verify peer** on the
  items after import or fix the certificate.
- The `total` counters are taken from `$.body.total` of each list endpoint.
- Maintenance monitoring uses `/api/v1/maintenances?limit={$SNIPEIT.MAINT.LIMIT}`:
  records with `completion_date: null` are treated as open; the age is computed
  in Zabbix preprocessing as `now() - start_date`. API times are treated as UTC,
  so age values may be off by a few hours, which is fine for day thresholds.

API reference: https://snipe-it.readme.io/reference/api-overview

## TODO

- consumables below their minimum quantity (`remaining` < `min_amt`)
- activity log monitoring
- web scenario for service health check + version logging
