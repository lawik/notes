Elixir was built on Erlang. Erlang was built to provide "consistently low latency" and a few other very audacious goals. Note, this is not a hard realtime constraint. It is soft, squishy and yet, important and real.

Soft realtime. This part of the Unpacking Elixir series will really benefit from having read [Unpacking Elixir - Concurrency](/unpacking-elixir-concurrency.html) as it covers a lot about how concurrency works in Erlang and with that it covers Processes and Schedulers.

Erlang's claim for consistently low latency predates the multi-core concurrency approach a fair bit. From my understanding it ran a single scheduler in the olden times and the pre-empting was there to ensure this fair distribution of computational resources. It would prevent single pieces of CPU-intensive work from holding up concurrent, faster, pieces of work. It ensures progress is made on all work in short order.

This was telecom and the work was about routing phone-calls without introducing delay.