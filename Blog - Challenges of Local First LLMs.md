
*TLDR: the tricky part is "Large". "Language" and "Model" seems manageable. But largeness has all sorts of trouble associated with it for mobile devices. Not insurmountable, but challenging. Also makes for finicky development.*

To start off, I am not an AI expert. I am not an ML engineer. I'm a web and systems dev that mostly works in Elixir these days. Through the Elixir community I got connected with [Electric SQL](TODO: Add video link) and have done some collaboration with them. *This post is done as part of my work with them and is paid work. I'll probably let them read it before publishing but this is all me and my perspectives. They just pay me to endure the Javascript ;)*

Electric SQL are firmly in the local first ecosystem. Their open source project/product gives you a way to get eventual consistency between a server-side Postgres and a wherever-you-want SQLite. You handle the data model from the Postgres side with your normal tooling. Whenever your clients have a chance to reach the sync service/postgres proxy you'll get schema updates and whatever new data the server might have to your local thing. Anyway. Cool stuff and I hope to end up making an app with a decent demo off it. On to the local first inference!

I really am not enjoying React Native here. Part of that is almost definitely that I'm poking around the immature ragged edge of what people have launched, like [llama.rn](TODO: link) (based on [llama.cpp](TODO: link)) and [the outdated example for transformers.js](TODO: oof link). There are no real paths and I'm not deep enough on mobile dev to patch up the holes well myself. I also don't know enough C/C++ to ship custom sqlite's with vector extensions and whatnot. Might get there but am not there.

I know there are people who have made models run locally. I am not blazing a trail. But it is also not particularly mature as a space.

So the current approaches I see for getting LLMs to work on phones is mostly quantization which to my understanding is the process of reducing the precision of the numbers and calculations to save heavily on space and memory usage. Taking [Llama2 7B](https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGUF) you have this 13.5Gb model at 16bit floating point. Quantizing it with llama.cpp down to "Q2 K" I believe uses 2bit integers. Pretty lossy. But it brings it down to 2.6Gb and it can actually be run on a modern iPhone.

Now there are also efforts to rework models to be smaller fundamentally and then you have something like [TinyLlama-1.1B](https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v0.3) ([GGUF](https://huggingface.co/TheBloke/TinyLlama-1.1B-Chat-v0.3-GGUF)). This model is miniscule (relatively speaking) at 731Mb. It also produces unrelated stuff or the wrong language almost all of the time for me. Keep at it folks. I hope you get something good out of it.

The crunched up Llama has produced semi-relevant responses for me but it has also been much harder to run. A 731Mb asset is perhaps on the larger side. But whether it goes over wifi or the lightning cable `npx react-native@latest run-ios` is not impressively fast when slinging a 2.6Gb Llama over the wire (Nullsoft would have loved this era of computing. WinAmp should return as an LLM I guess).


