connect the difficulty of the work, the skill required to effortlessly focus on exploration and execution. How the difficulty is everywhere as you start and that makes everything cookis everywhere as you start and that mak

- Custom command per slide title so I can shift slide naturally at the end of every slide by leading into the next one. (https://hexdocs.pm/elixir/1.14/String.html#jaro_distance/2 probably, or bag_distance)
- Adapt slide display to first show title and then slide in content, leads into it a bit better
- Seems like it won't work without an extra serving which isn't worth. But try if Whisper will translate some swedish.
- Add more about buidings skill and that what is hard and thus what is cool also depends on where you are in your skill progressio, your understanding. Share understanding that it takes years of active practice to play the guitar effortlessly and similarly with code, it only becomes effortless with practice. But the creative exploration happens at all levels. For me, this presentation was a fun challenge but not exceptionally difficult. Some would call it impossible, some other would consider it trivial.
- Bring StreamDeck for demo? Waveform on the deck?
- Webcam? Don't show constant feed. Just process image in background only show the feed if the face was visible within last X ms. 
	- Commands for "video on" / "video off"
	- Commands for "left eye", "right eye" ideally animate panning over with CSS transitions
- Make an alternate audiometer for generating waveforms over time?
- Code updates
	- Show two pages of code for different SVG renderings, hit them to load the new module and replace
	- Or should I build it up during the talk?
		- Have the pipeline and transcode running as a GenServer
		- LiveView receives notifications but doesn't do anything with them
		- All activities are delegated to a stub module to allow replacing ad hoc.
		- Indicate update process somehow cleanly 
		- Build a module that can build the code from a series of function strings. Don't want code snippets to have to include the whole module. Map of function names to code and then prefix module name should be all the data structure needed. Keep the module data structure in the LV
		- Code build-up 

