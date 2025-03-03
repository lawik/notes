Notes:
- Try llama_cpp for Elixir.
- Try setting up a small app on the machine at home that can run a heavier model.
- Try FLAME to outsource the LLM. Kind of a fun Membrane element.


Presentation outline:

- Full demo with the pretty LiveView:
	- "OK, computer. I'm at Code BEAM America in San Francisco. What do you want to tell all these lovely people?"
	- my query should show up fairly quickly
	- wait ... a significant amount of time for SmolLM2 to work
	- show response and ideally they also hear it
	- Interrupt it at some appropriate point. Stop button.
	  "See! The power of AI. Revolutionary!"
	  
	  That's the demo. That's as good as it gets. But I should explain what we are doing here. The nutritional contents of the talk. We will talk about the Machine Learning, then we will get into the crunchy bits about how we actually sling media around effectively.
- The hardware
	- This is a MacBook Air with 24Gb of RAM. On any given day it indicates that it has about 11Gb to spare.
	- It has a GPU, it has Apples Neural Engine, it has a surprisingly capable CPU.
	- It has no fan, no active cooling.
	- Way stronger than most embedded devices I deal with. Way weaker than my Linux workstation. Leagues weaker than a typical GPU server.
- Local first and open models
	-  First off. Everything is local. That is why the LLM is so terrible. It has to fit into less than 11 Gb of memory.
	- All the models are open-ish. Open weights, usable licenses. Currently the best LLMs and particularly LLM services are definitely the proprietary ones. But a Llama3 would do a much better job than the one I'm using but it won't fit.
	- Why not just use OpenAI or Anthropic? Do you just put your compute at AWS and store your files in S3. Geez. No. No sport in that.
- Mixture of Idiots - Multiple simpler models
	- Introducing our incredibly dumb models. In order of weight, heavy first.
	- SmolLM2-135M-Instruct which is a chat model published by HuggingFace that I think could fit on a Raspberry Pi given enough RAM. I don't think I own an 8Gb Pi, if you do, try running this thing under Bumblebee and see if it can. I think that'd be cool.
	- OpenAI's Whisper model is my work-horse. I've used this model before in a few talks. I just love that I can turn audio into text. It is not massively heavy.
	- Kokoro - This is the new fun for me. Doing the reverse of Whisper and turning text into audible speech. It sounds good. Not exceptional but good and it runs pretty quickly on most hardware.
	- Silero VAD - VAD stands for Voice-Acticity Detection. This is the most lightweight. The model determines whether a chunk of audio is speech or not and this is a fantastic building block for a voice assistant style pipeline. It runs at essentially real-time on Raspberry Pi 4. I've used it for this at home. It does a great job of rejecting keyboard clacks, background noise and even determining speech over a really bad microphone with a super high noise floor. It doesn't clean up the speech for you but it will tell your it is happening.
	- We are using 4 different machine learning models of various heft. And I think that's an important bit. They aren't all LLMs and the special purpose ones are actually quite capable and useful. As building blocks the non-LLM ones are actually a fair bit easier to reason about and they of course complement each other. The VAD means we don't have to get Whisper involved until someone is actually speaking. Whisper tends to do weird things with background noise sometimes.
- How I run them
	- I use a few different techniques. Since I'm a ride-or-die Elixir extremist I prefer Nx-based ML models. They slot into supervision nicely, they can do a bunch of cool clustering type stuff. And a minimal set of weirdo dependencies. I run both SmolLM2 and Whisper this way using the higher-level abstraction of Bumblebee. I'm actually utilizing the new support for Apple hardware. EMLX instead of EXLA. It works, they are certainly faster on the GPU than EXLA ran them on the CPU.
	- And for the smaller models I use Ortex which is a library that provides the Rust-based ONNX Runtime bindings called Ort into Elixir via Rustler. ONNX is a format and runtime for producing portable machine learning models. The ONNX file will contain both the architecture, the config and the weights you need to run it. There are accelerated execution providers for Ortex but I haven't started digging into that. This just runs on the CPU and is plenty quick.
	- Aside from the LLM which is going to be quite slow on most CPUs all of these will run trivially on your machine. This particular repo is built for a Mac. One line of config to change though.
	- As always, thanks to Paulo Valente and the Nx folks for helping me get over various hurdles.
- Limitations
	- Elixir's machine learning support is not at the leading edge for running LLMs in local resource constraints. If you look at the project llama.cpp they have their own format for models called GGUF and you can find heavily quantized versions of the big LLMs. I've been able to fit a Llama 2 on an iPhone Pro. I believe it took the model from about 13Gb to about 2Gb. They lose precision with quantization so you lose quality. But it allows you to adapt the model to the hardware you have. Some quantisation is available with Axon but it is a bit early.
- What is actually happening?
	- Initial input: Microphone via PortAudio, produces audio out
	- Peakmeter 1, measure amplitudes, audio in, audio out
	- Silero VAD filtering for only voice, audio in, audio out
	- Peakmeter 2, measure amplitudes, audio in, audio out
	- Whisper, transcribe audio, audio in, text out
	- SmolLM2, add transcript to messaging thread, text in, text out
	- Kokoro, generate speech for the LLM output, text in, audio out
	- FFMPEG swresample, adjust audio bitrate, audio in, audio out
	- Final destination: Audio output via PortAudio
- Membrane Framework
	- Some people haven't heard of it. Media framework for Elixir. I really like it.
	- Built by Software Mansion who keep doing cool stuff like WebRTC, video composition and more.
	- Wraps common tools like ffmpeg and portaudio into Elixir abstractions that operate using Supervision trees and more.
	- You build a pipeline of elements. Essentially a DAG.
- Fair warning about media formats
	- Membrane makes a lot of things simpler but tries to expose the correct amount of power.
	- Audio and especially Video are more complicated than anyone wants them to be.
	- Encodings, decodings, containers, bitrates, color spaces. It is never just a series of images.
- Raw audio is actually kind of straightforward but can seem a bit scary.
	- Channels, typically 1 or 2.
	- Format, signed/unsigned/floating point, bit-depth, endianness. This all uses f32le, floating point 32-bit little endian.
	- Sample rate, you've probably seen it, measures in Hertz meaning events per second. 16KHz, 24kHz are common. 16000 hertz is good for voice. Why? Because 15kHz is about how much of the audio-spectrum a human can hear and the absolute majority of voice happens in within that range. We might lose a few harmonics up towards 17kHz.
	- If you have an arbitrary chunk of bytes and know these specifics, which is common in Membrane, you can convert that to a duration. It took me until writing this to realize there is of course a utility function for that in Membrane.
	- libmad will turn an MP3 into raw audio for you
	- lame will turn raw audio into MP3
- I made up my own text format so Membrane can complain at me if I don't send the right thing. Just a list of Elixir strings because it maps pretty closely to how the models like it.
- My membrane pipeline does a few things.
	- Specifies children, the pipeline itself.

---
- Membrane pipelines
- Throttling in pipeline to desired ~fps
- Collect amplitude and emit interval ticks with info from pipeline, even empty ones
	- Helps LiveView to draw a cohesive track with roughly correct spacing
	- amplitudes pre & post
	- text from whisper
	- text from LLM
- LiveView displays in interesting ways
	- Collected amps pre-VAD as waveform
	- Collected amps post-VAD as waveform
	- Transcript text as user
	- Collected amps from TTS as waveform?
	- LLM text as assistant
- Explain the original pipeline for voice in, transcript out used in my original talk
- Show reproducible examples with some speech and some keyboard clatter, maybe some noise
- Drop-down to select various pipelines, start and stop dynamically.
- Visualize the children of the Membrane Pipeline from the data structure.
- Building up a pipeline:
	- Run with just the input, into Whisper. Output the text.
		- File -> Whisper -> Text :: LiveView
	- Run with just the input, into Whisper. Output in LiveView.
		- File -> Whisper -> Text ::  LiveView
	- Run with Audiometer. Output in LiveView.
		- File -> AudioMeter 1 -> Whisper -> Text
		- LiveView, text and amp1, vad-notif
	- Run with Silero VAD, into Whisper. Output the text.
		- File -> AudioMeter 1 -> VAD filter -> AudioMeter 2 -> Whisper -> Text
		- LiveView, text and amp1, amp2, vad-notif
	- Add Kokoro, mangla á la dubbel Google Translate
		- File -> AudioMeter 1 -> VAD filter -> AudioMeter 2 -> Whisper -> Text -> Kokoro -> PortAudio output
		- LiveView, text + text response, response sound playback and amp1, amp2, vad-notif, borde vara kul :)
	- Add SmolLM2
		- File -> AudioMeter 1 -> VAD filter -> AudioMeter 2 -> Whisper -> Text -> SmolLM2 -> Text -> Kokoro -> PortAudio output
		- LiveView, text + text response and amp1, amp2, vad-notif
	- Use microphone
		- PortAudio input -> AudioMeter 1 -> VAD filter -> AudioMeter 2 -> Whisper -> Text -> SmolLM2 -> Text -> Kokoro -> PortAudio output
		- LiveView, text + text response, response sound playback and amp1, amp2, vad-notif
	- How bad is this as a voice assistant?
	- Future topics: Tool use,  SmolLM2 tool use is very inconsistent.
	- Maybe add VAD interrupt to SmolLM2 feed


Demo spiel:

"Hmmm... *tapping fingers on desk* I wonder about this, whatsitcalled, *pen on paper*, Elixir thing. *keyboard clatter* You think it has legs?"