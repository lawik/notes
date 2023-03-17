This should be a decently easy port.

https://github.com/petabite/libsonyapi

I think the order of operations is:

- Connect to their wifi hotspot for control
- Discover endpoint via UDP SSDP (Quinn has built [an SSDP example](https://quinnwilton.com/blog/writing-an-ssdp-directory-in-elixir), thanks, or [this library](https://hex.pm/packages/ssdp))
- Talk HTTP + XML to it, see the Python API above

Old SDK, think everything uses this:
https://developer.sony.com/develop/cameras/

New API/SDK for USB or Wired network?
https://support.d-imaging.sony.co.jp/app/sdk/en/index.html