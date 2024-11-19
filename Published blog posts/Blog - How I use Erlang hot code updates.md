One of the Erlang ecosystem's spiciest nerd snipes are hot code updates. Because it can do it. In ways that almost no other runtime can.

I use Elixir which builds on Erlang and has the same capabilities.

The standard way of doing Elixir releases via `mix release` does not support Erlang hot code updates. As in, it will not generate the necessary files for you. And if you do want to do it there are several blog posts you need to stitch together or you need to use the Erlang docs in great detail. Learn You Some Erlang has [documented much of it](https://learnyousomeerlang.com/relups) of course. AppSignal also has a neat guide on [hot code reloading in Elixir](https://blog.appsignal.com/2021/07/27/a-guide-to-hot-code-reloading-in-elixir.html).

Bryan Hunter and Chris Keathley are both people I listen when they speak because it tends to be interesting, challenging and something I just might not have heard before. Both have described hot code updates as something that people should learn and use. I imagine Whatsapp's initial engineering crew would agree. They did pretty well.

Of course we do use it. But mostly for trivial things.

Anyone using IEx `r MyModule` or `recompile` are doing hot code updates. But reloading a module in development on you local machine doesn't feel all that spicy. It is cool and useful but feels like an incremental compile or watcher/builder type of thing.

I use it constantly [with Nerves](https://underjord.io/unpacking-elixir-iot-embedded-nerves.html). When developing on an embedded Elixir device and needing to tune some numbers, or reworking a module, pasting into the IEx is faster than uploading new firmware and waiting for the reboot. I stop and start parts of the applications or just terminate the relevant GenServer if I need a state reset.

I've also used it for remote devices over [NervesHub](https://github.com/nerves-hub/nerves_hub_web)'s built in web console that offers an IEx prompt. When debugging a misbehaving Real Time Clock it was pretty nice to just paste chunks of utility functions and relevant I2C-calls over to the device to get clear answers about what it was doing.

I'd love to see more tooling for the full-blown delivery of hot code updates on top of Elixir's `mix release` tooling. Or in the vein of the predecessor `distillery`. I don't think there are any shortcuts for doing a correct hot code update. Much like database migrations they require care. And you'll need to know how you dependencies react to hot code updates and many other interesting topics.

This was just a quick one in case people are curious how Erlang's hot code updates are actually used, day-to-day, in Elixir.

If you have comments or questions you can find me on the fediverse via {{< lars_twitter >}} or email me at {{< lars_email >}}.