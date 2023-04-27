## Splash
Title: Lively systems with Membrane & LiveView

(use purple-backed headings)


## Trying to do things since the 90s

- Self-taught developer, grew up doing web dev
- Mixing technically interesting work with visually interesting work
- Driven by realizing ideas, implementation, not theory
- Are cool things naturally hard to do? Or are hard things simply cool?
- Always new trouble in execution

## Why Elixir?

- Great for dealing with inputs.
- Simple ways of dealing with state.
- Does not require a bunch of extra infrastructure
- LiveView made it incredible for building expressive outputs.
- Wiring inputs to outputs could not be easier.

Images: Elixir logo, Erlang logo, Phoenix logo

## Inputs & Outputs

- Projects I've done in recent years
	- Calendar eInk screen
	- Macro pad controlling lights
	- Stream Deck control software
	- Telegram bots
- Inputs / Sources
	- APIs
	- Calendar URLs
	- Chat bots (Telegram/Slack)
	- Hardware controls
	- Webhooks
- Outputs / Sinks
	- APIs
	- Web pages (LiveView)
	- Desktop apps (wx)
	- Hardware (lights, displays)
	- Chat bots (Telegram/Slack)

## Why Membrane?

- Provides new Inputs and Outputs with audio and video
- Traditionally difficult mediums to work with
- Performs operations in Elixir, minimize shelling out
	- ffmpeg is rough to integrate with
	- Makes the results practical to use in Elixir
	- Make the actual process accessible in Elixir
- More information than you might think

Images: Membrane logo

## Transformations

- Taking text from one place and putting it in another place is easy enough
- Libraries like Image make manipulating pictures very powerful
- Membrane enables transformations for audio and video.

## ML transforming unlikely formats

- Not a big ML enthusiast but see some utility there
- Allows transforming between messy formats
	- Text to image, weirdly through diffusion
	- Image to text, OCR quite well, object classification quite poorly
	- Audio to text, transcription
	- Text to audio, voice synthesis
- Bumblebee makes this a tool box for builders like me

## Managing complexity

- No extra infrastructure, all in a BEAM application
- Media handling well abstracted
- Machine learning well abstracted
- Live web UI also well abstracted
- Communication and coordination, thoroughly available
- The most complicated code is the math to calculate duration from bitrate
- Oh, and the SVG for the waveform was confusing

## What is this presentation doing?

- Measure audio levels to produce a waveform using Membrane
- Low-latency poor-quality, near-instant transcription 
- Slower better transcription using a longer section of speech
- Interpreting transcript to of

## Trash

## Who am I?

Note: NEVER START ON LOGISTICS

- Lars Wikman
- Writes and publishes at Underjord.io, blog and newsletter
- Makes videos and livestreams on YouTube
- Podcasts with BEAM Radio
- Hacks on projects for fun and profit

Images: logo, beam radio logo


Outline

- Membrane in one slide
- LiveView in one slide
- Membrane provides a lot of potential signal
	- Visual
		- Picture
		- Dimensions
		- Framerate
		- Bitrate
	- Audio
		- Sound
		- Waveform
	- Transformed with ML?
		- Facial tracking
		- Transcribed audio
- Any signal you can capture can be visualized in LiveView
- The more types of information we can get, the richer the presentation
- We can do everything in one Phoenix/Membrane app
- ?Membrane is not limited to video or audio, streaming other transformations

## Prototype Talk, synopsis for Krakow meetup

**Going beyond video with Membrane?**

Audio and video are some of the most difficult formats to work with. As you probably know, that's what Membrane does.
Live web frontends can also be a decent challenge. Fortunately, we have Phoenix LiveView.
This presentation is an exploration into getting more out of your media and how to present it to your users. Let's get fancy.
