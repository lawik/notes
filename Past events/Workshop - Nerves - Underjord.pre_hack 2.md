The Raspberry Pi Zero W should be loaded with a firmware that starts a WiFi AP. It will have a name that includes the 4 characters on the box. If your box says `12xy` the AP will say `Setup nerves-12xy.local`. You can connect that way or should be able to plug in USB. With a connection in place you should be able to visit `nerves-12xy.local` in the browser.

## Get your stuff

- Collect a box with:
	- Raspberry Pi Zero W
	- Micro SD Card (in Pi Zero)
	- USB-A to Micro-USB cable
- Nerves T-shirt

## Get connected[]()

- Check box for 4 characters. Example: `12ab`
- Plug in USB or connect to WiFi AP `Setup nerves-12ab.local`
- Visit `nerves-12ab.local` in a browser on the connected device.
- Enter WiFi information:
	- SSID: `Nerves Workshop`
	- PSK: `nervescloud`

## Get on NervesCloud

- Details on: https://underjord.io/nervescloud-welcome.html
- The default firmware has a way of onboarding your device, most convenient via smartphone. Optional :)
## Ways to deploy

- Deploy a new firmware via Micro SD card (require card reader)

```
git clone git@github.com:lawik/rpi0kiosk.git
cd rpi0kiosk
export MIX_TARGET=rpi0
mix deps.get
mix firmware
mix burn
```
  
- Deploy firmware via SSH

```
# Confirm SSH connection, may need to burn SD card first to fix keys
ssh nerves-12ab.local
# iex> exit
git clone git@github.com:lawik/rpi0kiosk.git
cd rpi0kiosk
export MIX_TARGET=rpi0
mix deps.get
mix firmware
mix upload nerves-12ab.local
```

- Deploy a new firmware using NervesCloud
	- https://docs.nerves-hub.org/nerves-hub/setup/firmware-signing-keys (TODO: make a smaller one)
	  
## Things to try

In no particular order:

- Change firmware to Nerves Livebook
  
```
# Download .fw from https://github.com/nerves-livebook/nerves_livebook
NERVES_WIFI_SSID='Nerves Workshop' \
NERVES_WIFI_PASSPHRASE='nervescloud' \
fwup nerves_livebook_rpi0.fw
```
  
- Disable on-screen keyboard (oops)
- Manipulate the `/sys/class/leds`
- Change the Phoenix application
- Make a new friend and make an Erlang cluster together
	- Example: https://github.com/lawik/nerves_node
- Set up Ecto and Sqlite (https://github.com/nerves-project/nerves_examples)
- Run Erlang
- Run Gleam
- Set it up to talk to a Phoenix server with Phoenix Channels and Slipstream
- Try hooking up Mobius
- Suggest your own project, enjoy yourself :)