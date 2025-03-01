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