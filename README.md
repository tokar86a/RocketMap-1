
# GreatRocketMap

![Python 2.7](https://img.shields.io/badge/python-2.7-blue.svg) ![License](https://img.shields.io/github/license/RocketMap/RocketMap.svg) [![Build Status](https://travis-ci.org/RocketMap/RocketMap.svg?branch=develop)](https://travis-ci.org/RocketMap/RocketMap)

Live visualization of all the Pokémon (with option to show gyms, raids and PokéStops) in your area.

This project is based on [RocketMap](https://github.com/RocketMap/RocketMap) but **enhanced with several unique features.** This doc only lists the changes compared to the original RocketMap. Please also read the main [RocketMap documentation](https://rocketmap.readthedocs.io/en/develop/). 

## GreatRocketMap Features:

* Uses [MrMime API Wrapper Library](https://github.com/sLoPPydrive/MrMime).
* Support for [PGPool](https://github.com/sLoPPydrive/PGPool), an external account pool webservice.
* Support for [PGScout](https://github.com/sLoPPydrive/PGScout), an external, efficient webservice for determining Pokémon IV and stats.
* On-demand gym icon creation that uses [ImageMagick](http://www.imagemagick.org/). *No more waiting for new icons when new Raid Bosses appear.*
* Plays well with the PGTools Edition of [PokeAlarm](https://github.com/sLoPPydrive/PokeAlarm/tree/MIX_6)
* Includes german translations for Pokémon moves.
* Various other tweaks and enhancements.

## Support the Development [![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.me/slop)
If you like **GreatRocketMap** and feel the urgent need to **thank** me, the best way to do it is via **[PayPal](https://www.paypal.me/slop)** or **BitCoin (_1PNdXhzvvz2ytCf8mbFdF9MQaABzpjSbJi_)**  Seriously, that would be so awesome! :-D If you can't or don't want to use PayPal or BitCoin some **level 30+** accounts or **[PokeHash keys](https://talk.pogodev.org/d/51-api-hashing-service-by-pokefarmer)** are always welcome as well. You can find me on various Pokémon related Discords (see below) as "sLoPPydrive".

## Information
* [PGTools Discord](https://discord.gg/Vwe5mTa) for general support for GreatRocketMap and all other PGTools products.
* [PogoDev Discord](https://discord.pogodev.org) for all things Pogo Development, hashing and reverse engineering

## Configuration Changes

### New Options
```
  -sazl SHOW_ALL_ZOOM_LEVEL, --show-all-zoom-level SHOW_ALL_ZOOM_LEVEL
                        Show all Pokemon, even excluded, at this map zoom
                        level. Set to 0 to disable this feature. Set to 19 or
                        higher for nice results. [env var:
                        POGOMAP_SHOW_ALL_ZOOM_LEVEL]
  -rst RARELESS_SCANS_THRESHOLD, --rareless-scans-threshold RARELESS_SCANS_THRESHOLD
                        Mark an account as blind/shadowbanned after this many
                        scans without finding any rare Pokemon. [env var:
                        POGOMAP_RARELESS_SCANS_THRESHOLD]
  -rb, --rotate-blind   Rotate out blinded accounts. [env var:
                        POGOMAP_ROTATE_BLIND]
  -pgpu PGPOOL_URL, --pgpool-url PGPOOL_URL
                        URL of PGPool account manager. [env var:
                        POGOMAP_PGPOOL_URL]
  -hw HIGHLVL_WORKERS, --highlvl-workers HIGHLVL_WORKERS
                        Load this many high level workers from PGPool. This
                        requires --pgpool-url to be set. [env var:
                        POGOMAP_HIGHLVL_WORKERS]
  -gxp, --gain-xp       Do various things to let map accounts gain XP. [env
                        var: POGOMAP_GAIN_XP]
  -gen, --generate-images
                        Use ImageMagick to generate gym images on demand. [env
                        var: POGOMAP_GENERATE_IMAGES]
  -pgsu PGSCOUT_URL, --pgscout-url PGSCOUT_URL
                        URL to query PGScout for Pokemon IV/CP. [env var:
                        POGOMAP_PGSCOUT_URL]
```

### Removed Options
* `-wwht/--webhook-whitelist, -wblk/--webhook-blacklist` - These have been removed in favor of
 one single whitelist file (`-wwhtf/--webhook-whitelist-file`)which contains all Pokémon with names. To exclude a Pokémon just
 prepend a dash in front of the name.


## MrMime API Wrapper

All API calls are now being done by the [MrMime](https://github.com/sLoPPydrive/MrMime) API
wrapper library. It takes care of proper login sequence, timings, stuff like location jittering,
offers support for [GoMan Hashing](https://selly.gg/p/b9416192) and last but not least
can be attached to a running [PGPool](https://github.com/sLoPPydrive/PGPool) instance to constantly update account details.

MrMime library is pretty low level but it has some knobs to configure. These settings are not
being exposed via standard (Great)RocketMap configuration options but instead read from the file
`config/mrmime_config.json`. This is a simple key/value JSON file which may contain the
following entries:

* `goman_proxy_support` (default: _false_) - Patch PGoApi to be able to use GoMan proxies.
* `goman_hashing_rate_limit` (default: _null_) - Artificial rate limiting for GoMan hashing.
* `goman_hashing_max_rpm_count` (default: _null_) - Artificial max RPM for GoMan hashing.
* `parallel_logins` (default: _true_) - Parallel logins increases number of requests.
* `exception_on_captcha` (default: _true_) - Raise CaptchaException if captcha detected
* `dump_bad_requests` (default: _false_) - Requests leading to BAD_REQUEST errors will be dumped to a file
* `jitter_gmo` (default: _true_) - Perform location jitter on GET_MAP_OBJECTS requests
* `login_retries` (default: _3_) - Number of login retries
* `login_delay` (default: _6_) - Delay between login retries
* `full_login_flow` (default: _true_) - Whether login flow requests should be performed or not
* `download_assets_and_items` (default: _true_) - Whether to perform download_asset_digest and download_item_templates requests at all
* `request_retry_delay` (default: _5_) - Number of seconds to wait between request retries. Will be shorter if multiple hash keys and/or proxies are available.
* `scan_delay` (default: _10_) - Wait at least this long between 2 GMO requests
* `debug_log` (default: _false_) - If MrMime should output debug logging
* `log_file` (default: _null_) - If given MrMime also logs into this file
* `pgpool_url` (default: _null_) - URL of PGPool to manage account details
* `pgpool_system_id` (default: _null_) - System ID for PGPool - which system has the account in use
* `pgpool_auto_update` (default: _true_) - Whether MrMime updates PGPool account details automatically
* `pgpool_update_interval` (default: _60_) - Update account details in PGPool after this many seconds

## PGPool Support

## PGScout Support
