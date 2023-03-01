The Streamdeck Plus is a fun device. 8 keys that are also displays, an 800x100 touchscreen display and then 4 twisty knobs that can also be pressed as buttons.

I managed to implement the full functionality of the hardware with [an Elixir library](https://github.com/lawik/streamdex).

What to do with it?

The screen has a decent rate of updates. Less than 60hz when doing full-redraws but only slightly.

Current experimental implementation:

- Key turning off and on the keylight.
- Touchscreen to display calendar events. Pulling the data using the implementation I had in my calendar_gadget project.
- Knob to adjust brightness of keylight. Pressing toggles on or off.
- Knob to adjust temperature of keylight. Pressing resets to a good default.

Things I want to do:

- Show keylight brightness indicator.
- Show volume output volume indicator. Adjust with knobs. Press to toggle mute.
- Show micropohone input indicator. Adjust with knobs. Press to toggle mute.