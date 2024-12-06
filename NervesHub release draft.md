## Major updates
### Extensions

This release combines with [nerves_hub_link v2.6.0](https://github.com/nerves-hub/nerves_hub_link/releases/tag/v2.6.0) to bring the complete Extensions experience to NervesHub. An Extension is a non-critical but useful mechanism that needs to piggy-back on the device's connection to NervesHub. Rather than opening more connections or risking that the critical firmware delivery socket fails we have wrapped both ends of the socket in safeties.

Extensions are only enabled after the essential connection is confirmed and firmware delivery is possible. Each extension is also opt-in at the product level under product settings and possible to opt out at the device level under device settings. These changes can importantly be made at run-time in the UI. We want to ensure that if anything is wrong with an extension or it somehow disrupts critical operation it is trivial to disable immediately. This is to ensure that firmware updates and trouble-shooting come first.

The upshot of this is that we can build some very nice features without touching the core of what NervesHub must do. More on those below.

### Extension: Health

Health reporting lets your device send metrics, metadata and alarms to NervesHub giving you indicators about your device fleet's health. Health information is shown on the device details page when the extension is enabled and as information gets reported.

Default metrics, if supported by the hardware:

- CPU temperature
- CPU utilization
- Load averages (1, 5, 15 min respectively)
- Memory usage
- Disk usage

Adding custom metrics to the default report is a minimal addition in `config.exs` on `nerves_hub_link`:

```
config :nerves_hub_link,
  health: [
    # metrics are added with a key and MFA
    # the function should return a number (int or float is fine)
    metrics: %{
      "battery_percent_remaining" => {MyBattery, :percent_remains, []}
    },
    
```

There are many more features planned for metrics. Filtering, monitoring levels and various types of acting on metrics being out of bounds to provide a good idea about device health.

Alarms are a very nice way to find out if your devices is experiencing issues and uses the Erlang `:alarm_handler` functionality. The default alarm handler is not intended for real use and when it is used we won't report alarms because they don't behave very well. The alarm handler we know well and can recommend is [alarmist](https://hex.pm/packages/alarmist).

The device list can be filtered by devices with any alarms, specific alarms and your navigation bar within a product will indicate devices with alarms helping you quickly spot and find the list of devices with active alarms.

## Extension: Geo

Geo allows your device to report location information. By default it uses a Geo-IP mechanism through an API call to a web service. This gives a rough location. A device with location information can be shown on the Dashboard map which is enabled by setting `DASHBOARD_ENABLED=true`. If you configure a [Mapbox](https://www.mapbox.com/) API key via the environment variable `MAPBOX_ACCESS_TOKEN` it will fetch a local map for the device details page as well.

There are certainly more things we want to do in terms of filtering and useful maps with this geo-location information and this is the starting point.

Your device may well have a better way to get location data, whether LTE, GPS or manually entered coordinates you can modify the device's `config.exs` to include:

```
config :nerves_hub_link,
  geo: [
    # Implement your  `NervesHubLink.Extensions.Geo.Resolver`, just one function
    resolver: MyGPS.Resolver
  ]
```



## Added

- Add the oban integration to Sentry (#1543)
- Add container version tagging (#1549)
- Add device count and estimated device count for Deployment views (#1517)
- Use Chart.js for metrics (#1523)
- Add task for generating randomized device metrics (#1564)
- Add support for a release id for Sentry (#1568)
- Add option for showing updated devices on the map (#1592)
- Clear inflight updates for unhealthy devices (#1620)
- Show helpful message when uploading duplicate firmware (#1627)
- Add support for filtering devices on alarms (#1628)
- Have the calculator use Oban (#1639)
- Save device connection data over time (#1572)
- Add support for OpenTelemetry tracing (#1612)
- Show current alarms on device page (#1648)
- World Map clustering (#1619)

## Changed

- Device channel cleanups (#1546)
- Increase the Repo queue_target (#1545)
- Fix dialyzer warning about use of map where each would do (#1550)
- Use the Endpoint from the socket (#1558)
- Sentry integration tweaks (#1560)
- Remove the NodeReporter metrics logger (#1563)
- Only render connection tooltip when timestamp is present (#1565)
- Logger improvements (foundations) (#1556)
- Allow errors and warnings in test env (#1561)
- Separate logging from metrics (#1571)
- Allow the socket drainer to be configured at runtime (#1577)
- Use the endpoint from the socket (#1579)
- Reintroduce missing custom metrics and metadata to device page (#1578)
- Add y-scale start number as chart parameter (#1580)
- Metrics charts improvements (#1581)
- Only broadcast on terminate/2 if the channel is joined (#1586)
- Display selected option when moving device(s) to other product (#1591)
- Add firmware delta generation back in (#1582)
- Order platform dropdown + add unknown platform as selection (#1599)
- Group by deployment id when counting devices for deployments (#1600)
- Disable console button when device console is not available (#1605)
- Batch insert device metrics (#1613)
- Optimize the queries needed for Accounts.list_org_keys (#1614)
- Increase the Orchestrator timer, and add a jitter (#1615)
- Remove an extra Deployment preload during after_join (#1618)
- Allow publishing docker images from PR (#1626)
- Use PRs HEAD sha for tagging images (#1637)
- Use PR number when looking for publish commit message (#1638)

## Fixed

- Some minor dialyzer fixes (#1553)
- Update mix.exs Elixir version to match .tool-versions (#1621)
- Use the correct endpoint for pubsub broadcasts (#1634)
- Fix an unreachable with/else code path (#1642)
- Use the full module reference for some @specs (#1645)
- Change health section to not show boxes for non-reported default values (#1651)

## Dependencies
- **castore**: 1.0.8 -> 1.0.10
- **ex_aws_s3**: 2.5.3 -> 2.5.5
- **dialyxir**: 1.4.3 -> 1.4.5
- **bcrypt_elixir**: 3.1.0 -> 3.2.0
- **comeonin**: 5.4.0 -> 5.5.0
- **swoosh**: 1.17.1 -> 1.17.3
- **slipstream**: 1.1.1 -> 1.1.2
- **telemetry_metrics_statsd**: 0.7.0 -> 0.7.1
- **ecto**: 3.12.3 -> 3.12.4
- **ecto_sql**: 3.12.0 -> 3.12.1
- **telemetry_metrics**: 0.6.2 -> 1.0.0
- **ecto_psql_extras**: 0.8.1 -> 0.8.2
- **ex_aws**: 2.5.5 -> 2.5.7
- **x509**: 0.8.9 -> 0.8.10
- **postgrex**: 0.19.1 -> 0.19.3
- **floki**: 0.36.2 -> 0.36.3
- **crontab**: 1.1.13 -> 1.1.14
- **sentry**: 10.7.1 -> 10.8.0
- **phoenix_ecto**: 4.6.2 -> 4.6.3
- **gettext**: 0.24.0 -> 0.26.2
- **phoenix_live_dashboard**: 0.8.4 -> 0.8.5
- **bandit**: 1.5.7 -> 1.6.0
- **opentelemetry_bandit**: 0.2.0-rc.1 -> 0.2.0
- **opentelemetry_phoenix**: 2.0.0-rc.1 -> 2.0.0
- **open_telemetry_decorator**: 1.5.7 -> 1.5.8

From `assets/`:
- **webpack**: 5.45.1 -> 5.95.0
- **braces**: 3.0.2 -> 3.0.3

Full Changelog - https://github.com/nerves-hub/nerves_hub_web/compare/v2.0.0...v2.1.0