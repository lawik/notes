https://github.com/nerves-networking/vintage_net/blob/main/docs/notes/Wired-802.1X-PEAP-MSCHAPv2.md

Frank: https://discord.com/channels/269508806759809042/592366299573649440/1216760917656600586
```
I'd put the code for this in `vintage_net_ethernet` or create a `vintage_net_802dot1x` if it needs to pull in a lot of dependencies.  I never implemented 802.1X since I didn't have a way to test, but if you need help adding it, let me know. Having working config files is most of what you need since the implementation will be to tell VintageNet what's in them. If there are notifications from `wpa_supplicant` that are important to bubble up to Elixir, then I think we should factor out the supplicant communication code in `vintage_net_wifi`.
```

bo0tzz: https://discord.com/channels/269508806759809042/592366299573649440/1216652477340975146
```
<@162694898062065664> I'm not familiar enough with vintage_net to see if it's got an escape hatch to only configure 802.1X, but you should be able to skip it and just write the config directly a la https://github.com/nerves-networking/vintage_net/blob/main/docs/notes/Wired-802.1X-PEAP-MSCHAPv2.md, then bring up the interface [like so](https://github.com/nerves-networking/vintage_net/blob/main/docs/notes/Static-Wired-Ethernet.md#tested). I don't know what you're missing out on there by not using vintage_net though
```