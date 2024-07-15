With our efforts for [NervesCloud](https://nervescloud.com) (coming soon™️) we have been hacking hard on NervesHub to make it better in a variety of ways. I'll cover a number of the most recent bits and also re-hash a few that people might have missed that are important. Also maybe tease a bit of what's in the pipeline :)

Eric Oestrich put in a ton of work to make NervesHub 2.0 simpler than the AWS-oriented solution that came before it. And Josh Kalderimis made it easy to get up and running on Fly and other Docker-friendly hosts.

We would also encourage people that want to get their feet wet with a real Phoenix project, or poke around with Nerves to get involved. The primary repos are [nerves_hub_web](https://github.com/nerves-hub/nerves_hub_web/issues) for the hostable system, [nerves_hub_link](https://github.com/nerves-hub/nerves_hub_link/issues) for the on-device client and [nerves_hub_cli](https://github.com/nerves-hub/nerves_hub_cli) for the mix tasks and in-project CLI.

Now we're dialing up the polish.

## The New
### LiveView all the things

NervesHub has a lot of real-time aspects to it in terms of the state of devices and LiveView is really a blessing here. Josh put in a lot of work to get us into LiveView across the app letting it be much more interactive, responsive and snappy.

### Testing the UI

German Velasco built [PhoenixTest](https://hex.pm/packages/phoenix_test/) as a tool to make testing Phoenix UI easy and consistent. Josh has spoken very well of his experience using it during this process. And it deserves a shout-out here as a lot of testing was done with this tool.

## The Recent

### Shared Secret Device Authentication

NervesHub originally supported the best-practice secure element approach of device certificates. Ideally via [NervesKey](https://github.com/nerves-hub/nerves_key) (Microchip ATECC devices). This was not the most approachable for initial onboarding, hobbyist use, experiments and lower security needs.

Josh and Jon (Carstens of Nerves Core team) worked through this functionality to enable a key + secret approach to make onboarding a lot simpler. No Certificate Authorities, no provisioning. No single-use burning of soldered chips.

Just:

```
config :nerves_hub_link,
 host: "devices.nervescloud.com",
 shared_secret: [  
   product_key: "my-key",  
   product_secret: "my-secret"
 ]
```

### Scalability work

This was a big hunt that Jon [covered well in his NervesConf talk](https://youtu.be/lHcC9gwk_rg?t=1535). Lots of work by Eric and I'm sure others at SmartRent to address problems they had with the Thundering Herd. Now I suggest watching the bit of the talk I linked above because the punchline is quite something. It lands as a fix in a different Elixir project that a lot of us use. You may be benefiting right now, at least if you update your deps..

But due to trying to mitigate thundering herd problems as well as fixing the underlying actual problem, NervesHub is now incredibly well suited to running a ridiculous amount of devices as far as connections are concerned. And it shrugs off restarts with hundreds of thousands of devices wanting to get in at the same time.

We are eyeing some unexpected memory usage levels. If you like that stuff, there are things to poke at. Reach out :)

## Next up

These are brief and will get more detail when they actually ship. Very excited about it though.

- **UI & UX overhaul**: So excited to share, but I mustn't yet :)
- **Device health reporting:** What's the health metrics and checks you want from your device to know it operates correctly. How do you know when it doesn't?
- **Geo-location out of the box:** Whether you do LTE, GPS or can only really do GeoIP we'll do what we can to help you put your device on the map.
- **Onboarding:** A lot of thought and effort will land in getting folks started and onto NervesHub when they want it. Beyond the great new shared secrets.

Feel free to holler with questions and curiosity. Happy to try to clarify anything or address any concerns.