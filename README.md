# Snipe-IT Monitoring

Using Snipe-IT API to track value changes in Inventarisation system

All thanks to the creators [Snipe-IT (A free open source IT asset/license management system)](https://github.com/snipe/snipe-it)

Developen and tested on v6.1.2 - build 10938 (master)

## Using
Add 2 macro in item:

- `{$SNIPEIT_TOKEN}` -- access token (how to get [here](https://snipe-it.readme.io/reference/generating-api-tokens))
- `{$SNIPEIT_URL}` -- URL for your instance of Snipe-IT

## Metrics
- All assets count
- Users count
- models count
- licenses count

Autodiscovery status labels and count them (like "Ready to Deploy" or "Archived"). Support user-changed categories

No triggers or smth else, just metrics

## TODO
- add categories for "non-deployable"/"ok device"
- maintainces count and mb trigger for fast increases?
- something about activity log
- web http check for service health checking
- app version logging



API wiki -- https://snipe-it.readme.io/reference/api-overview
