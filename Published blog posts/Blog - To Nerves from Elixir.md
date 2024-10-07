I adore [Nerves](https://nerves-project.org). I recently joined the core team. And I'll be doing my best to help people get along with this lovely way to co-mingle hardware and massively concurrent reliable software.

## Getting started

Assuming you have a comfortable Elixir & Erlang installed, preferrably the most recent. Most conveniently through asdf/mise, but I don't judge. Instructions are lifted from [the official docs](https://hexdocs.pm/nerves/installation.html) but I will try to keep it leaner as I assume you are comfortable with Elixir.

Install deps for your OS:
### MacOS + Homebrew

```
brew install fwup squashfs coreutils xz pkg-config
```

### Linux + Ubuntu/Debian

```
sudo apt install build-essential automake autoconf git squashfs-tools ssh-askpass pkg-config curl libmnl-dev

```


Have a Raspberry Pi computer (not the Picos, those are microcontrollers), with SD-card, available. It will assume a Raspberry Pi Zero, ie. using `rpi0` for referring to the target system. You can replace that in any command with [another supported system](https://hexdocs.pm/nerves/systems.html#compatibility):

```
# Install the Nerves tooling called nerves_bootstrap
mix archive.install hex nerves_bootstrap

# Use the Nerves tooling to create a new Elixir + Nerves project
mix nerves.new my_thing
# it will ask to install deps, you can yes, otherwise: mix deps.get

cd my_thing
export MIX_TARGET=rpi0
# Getting deps with the target set will fetch the base linux system
mix deps.get
mix firmware
mix burn
```

The `mix burn` should prompt you for which SD-card you want to flash the firmware to.

Then start the device with that SD card in it. Give it power and it should turn on. If you have it connected with a data-cable it should show up via USB networking which is convenient. It should be announcing as `nerves.local` and have picked out one of your SSH public keys to embed in the firmware by default. Try `ssh nerves.local` to see if it is up. If not you may have to adapt a bit further and set up WiFi.

If you have networking the next update is still a `mix firmware` to compile but the deploy becomes:

```
mix uplad nerves.local
```

That's ridiculous.

You can also update specific modules by just pasting them over the ssh connection into IEx which is surprisingly practical.
## Set up Wi-Fi

Top of the generally debunked hierarchy of needs is of course Wi-Fi. You can edit `config/target.exs` to edit the config for hardware devices. `host.exs` is for running the project locally on your computer.

The networking libraries for Nerves are known as VintageNet and we can configure the VintageNetWiFi technology like this by replacing the default `"wlan0"` entry:

```
{"wlan0",
      %{
        type: VintageNetWiFi,
        vintage_net_wifi: %{
          networks: [
            %{
              key_mgmt: :wpa_psk,
              ssid: "my_network_ssid",
              psk: "a_passphrase_or_psk",
            }
          ]
        },
        ipv4: %{method: :dhcp},
      }
    }
```

Re-burn the SD-card and with a little bit of time your Pi should connect to your wireless network. If you have one of the larger Pis and an ethernet cable that also works great.

## Nerves Livebook

If you are a bit lost on what to do and inspiration hasn't sparked immediately you can instead get some neat tutorials going from [Nerves Livebook](https://github.com/nerves-livebook/nerves_livebook). There you get pre-baked firmware with special environment variables for setting up Wi-Fi. Note that it starts a bit slowly, especially on something like a Pi Zero. So be patient. Follow the instructions linked because they are friendly and practical.

## Going further with Phoenix

The Nerves documentation has a part about UI where we show how to do a so-called Poncho project (less bad than an umbrella project, but it's still raining). Redwire Labs published their approach which I know others have also done and [dubbed it naked Phoenix](https://nerves.redwirelabs.com/runbooks/naked-phoenix). I like this approach so if you want to integrate Phoenix. Try it.

## All just Elixir *

If you've dealt with your fair share of Mix projects and Elixir applications Nerves is fairly straightforward because you are just building an Elixir thing. Thing can get a little bit spicier if you need some special dependencies but usually that's actually fairly simple. Either the library pulls it in for you or you need to [install packages via buildroot](https://hexdocs.pm/nerves/customizing-systems.html#buildroot-package-configuration). It is more involved than you might be used to for apt. Pretty much a slightly old-school Docker image situation. The nuisance is the build time and that's the flip side of building a tiny dense BEAM-centric linux from scratch instead of shipping Ubuntu and hoping for the best. I can tell you I've had more luck reviving a Nerves-project 2 years down the line than I've had when I've hacked together something on top of Raspberry Pi OS.

There are some things that have fancy deps that aren't possible to cross-compile or aren't ready for it. But I've seen most of Membrane compile without issue. I've installed SQL Cipher as an encrypted alternative to SQLite. There's some nuance there but it is the same that lives in your Dockerfile plus a bit of architectural nuance.

You also get some cool stuff you usually don't poke at on a server. VintageNet for wrangling network interfaces. NervesTime for both Real-Time Clocks, ntpd and so on. The Nerves heart variant of the Erlang heart. A/B firmware partitions and firmware validation.

Things that are more fun on-device than on your average server is stuff like connecting a camera and using [evision](https://github.com/cocoa-xu/evision). I've added a Coral TPU over USB and had some fun via [tflite_elixir](https://github.com/cocoa-xu/tflite_elixir).

Find some devices to talk to, you now have a little server that can pull down your calendar, parse it and control things based on that, or based on a public RSS feed you like. There are so many possible things. I have hacked on [my Elgato Keylights](https://github.com/lawik/keylight) and my [Elgato StreamDeck](https://github.com/lawik/streamdex). 

And you can get incredible mileage out of OTP functionality like GenServers and the binary pattern matching to do things with hardware. You really get to do the stuff Erlang was made for.

## More Nerves

Things to read further on:

- [NervesHub](https://www.nerves-hub.org/) 
  Really fancy firmware updates, including binary delta updates, for Nerves devices. Me, Josh and the Nerves team are making them cooler by the day. Includes [nerves_hub_link](https://github.com/nerves-hub/nerves_hub_link) and [nerves_hub_cli](https://github.com/nerves-hub/nerves_hub_cli).
- [NervesKey](https://github.com/nerves-hub/nerves_key) 
  Opinionated abstractions on top of hardware encryption using the ATECC608-series from Microchip. This gives your device an identity and I've [been doing stuff with it](https://www.youtube.com/watch?v=Yd-qWQ65eQ8).
- [Mobius](https://hexdocs.pm/mobius/readme.html)
  On-device metrics, I want to look into this one and try to combine it with Explorer in some neat fashion.
- [Scenic](https://hexdocs.pm/scenic/welcome.html)
  Not currently the liveliest project but it does allow you to make UI all in Elixir and is used in production.
- [Sensors & peripherals](https://elixir-circuits.github.io/)
  There are many devices supported to be used directly from Elixir that you can pick up off of Adafruit or Pimoroni and just have fun with. I've implemented the [Inky eInk display](https://hex.pm/packages/inky) and the [VCNL4040](https://hex.pm/packages/vcnl4040) ambient light and proximity sensor.

## exit

That's it for now. Try Nerves and let me know if you run into things that feel confusing or weird about Nerves and I'm happy to try and clarify, improve and generally help you succeed with Nerves.

I am reachable on email {{< lars_email >}} and on the fedi {{< lars_twitter >}}.