Backend components for getting information into a system and affecting the world outside the system.

Types of components:

- Source (fetches information/triggers events with data)
- Sink (consumes commands with data and changes the world)
- Filter/Transmuter (transforms data between formats, adapts data)

All components can hold state. All components join process groups of related processes.

Design of the system ends up similar to Membrane with formats, source, sinks, filters. Try to keep the API simpler. Membrane's API is a bit annoying.

Pluggability:

- 