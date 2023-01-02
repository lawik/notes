# The premise

- 10 Mango Pi MQ Pro (RISC-V SBC)
- 10 Micro SD Cards
- 0 extra cables :/
- Nerves Livebook

# To test beforehand

- Single-file Phoenix LiveView
- Evision (do any inference smart cells work okay on it?)
- Anything else?
- Precook a custom Nerves Livebook with deps I want:
	- evision
	- nerves_node (my cluster thingie)
	- req?, decent http client 
	- delux, https://github.com/smartrent/delux
	- telegram bot library?
- Pre-cook wifi credentials :)


# The fun

- Nerves Livebook, preloaded on the cards, with deps and wifi creds
- Control the onboard light with Delux (https://github.com/smartrent/delux). Apparently you can do some [good fun that way](https://fosstodon.org/@fhunleth@mastodon.social/109593877017233380).
- Set up a Single file Phoenix app
- Set up a Single file LiveView
- Upload images from mobile phone camera and use evision on them?
- Telegram bot for input (build smart cell?)
	- Demo Secrets to control bot