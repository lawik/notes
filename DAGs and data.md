
Need a general way of turning client libraries into operator/provider/connector stuff.
Dynamic dispatch through protocols by each provider being a struct. Enforce good library practices by having that lib impose a base structure.

Oban Workflow Worker can do DAG work.

Airbyte: sources & destinations (overall name connectors)
They derive some connectors from Yaml. Don't love that. But some stuff is Java. It is a mess of assorted things.

José Valim brings the bomb: GenStage is a producer/consumer or source/sink API. In the standard library. Any library that exposes one or more Stages provides demand-driven performant and reasonable staged execution.

