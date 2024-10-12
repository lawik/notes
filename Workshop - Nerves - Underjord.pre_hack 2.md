The Raspberry Pi Zero W should be loaded with a firmware that starts a WiFi AP. It will have a name that includes the 4 characters on the box. If your box says `12xy` the AP will say `Setup nerves-12xy.local`. You can connect that way or should be able to plug in USB. With a connection in place you should be able to visit `nerves-12xy.local` in the browser.

## Get your stuff

- Collect a box with:
	- Raspberry Pi Zero W
	- Micro SD Card (in Pi Zero)
	- USB-A to Micro-USB cable
- Nerves T-shirt

## Get connected

- Check box for 4 characters. Example: `12ab`
- Plug in USB or connect to WiFi AP `Setup nerves-12ab.local`
- Visit `nerves-12ab.local` in a browser on the connected device.
- Enter WiFi information:
	- SSID: `Nerves Workshop`
	- PSK: `nervescloud`

## Get on NervesCloud

- Details on: https://underjord.io/nervescloud-welcome.html
- The default firmware has a way of onboarding your device, most convenient via smartphone. Optional :)
## Things to try

- Deploy a new firmware over SSH

```
git clone git@github.com:lawik/nerves_cloud_kiosk.git
cd nerves_cloud_kiosk
export MIX_TARGET
```
  
- Deploy a new firmware using NervesCloud
- Change firmware to Nerves Livebook
- Disable on-screen keyboard
- Change the Phoenix application
- 