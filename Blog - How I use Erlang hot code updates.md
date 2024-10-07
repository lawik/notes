One of the Erlang ecosystem's spiciest nerd snipes are hot code updates. Because it can do it. In ways that almost no other runtime can.

I use Elixir which builds on Erlang and has the same capabilities.

The standard way of doing Elixir releases via `mix release` does not support Erlang hot code updates. As in, it will not generate the necessary files for you. And if you do want to do it there are several blog posts you need to stitch together or you need to use the Erlang docs in great detail. (TODO: add links, AppSignal, learnyousomeerlang)

Both Bryan Hunter and Chris Keathley will day that they are a tool you can and maybe should use.

Anyone using IEx `r MyModule` or `recompile` are doing hot code updates. But reloading a module in development on you local machine is not super spicy. It is cool and useful but feels like an incremental compile.

I do it with Nerves.