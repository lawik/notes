Streaming and batch. Sure.

Python support is a requirement.

Promote and focus on building a flow that gains simplicity and efficiency through minimal transformation, optimal performance and adhering to standards optimized for data and processing.

This also serves to optimize CPU and GPU performance.

- Apache Arrow by default
- All integrations that produce data to be processed and worked on should operate on the Arrow format. Meaning they pull from ADBC, Arrow Flight or Arrow Flight SQL to obtain Arrow Record Batches. Integrations where the original source cannot produce Arrow should invest the overhead of transforming to Arrow format.
- Communication between stages is Arrow format. The orchestrator could determine whether that is done in Arrow Flight, in-process or via IPC (between local processes).
- The orchestrator/engine takes DAG definitions, turns them into execution plans and distributes workloads to achieve it.
- Elixir's current libraries don't really tackle this. They stay in the Elixir world and do Elixir things.
- What does Elixir bring to the table for building out an orchestrator
	- Trivial to build out a built-in live UI via LiveView
	- Easy to do low-latency status and observability
	- Built-in coordination and clustering for the work of the engine
	- Low latency
	- High level and expressive
	- Experimental idea, represent all active steps with their own Erlang process. This is probably pointless.

Dealing with supporting any Arrow-capable language for processing means we probably want to start the processing steps in Docker-style containers. Just for convenient packaging.

Levels of Fancy, in terms of optimizing running of workloads:

1. Everything runs in a container and speaks Arrow Flight RPC over TCP+TLS to communicate with previous and next stages.
2. Stages that are scheduled to occur locally to each other, host-wise, are set to communicate over Arrow Flight on a Unix Domain Socket.
3. Stages defined in the same language on the same versions, and that are not partitioned or distributed, are collapsed to run in the same process. Serially when appropriate. This is probably not worth it in most cases. Process isolation is good. The overhead of the container is probably not too bad.

Input is a set of named streams. Output is a set of named streams.
Anything that goes over the named streams is Arrow data. We could release the data developer from the need to define Arrow services with a bit of nice abstraction. This would need to be repeated for every applicable environment. Probably just start with Python.
Essentially they can name their streams and indicate what they do when they come in and how they leave and we can expose the right endpoints.

How do you do demand-driven processing with Arrow Flight?

## Where to start

### Python proving parts

Build out some of the stages in Python to pin down the concepts and the Arrow Flight interconnects.

Probably SQLite via ADBC (podcast index 4Gb and static wikipedia 40Gb datasets give us some bulk to work with) that we can pretend is streaming. And then multiple processing steps.
Do some crunching. Maybe wordcount? Classic demo.

Make a small abstraction if we can, so that we can swap the interconnect and serialization.
Time it end-to-end.

- how many entities get processed over a set period of time?
- How long does it take to process X number of total entries at 1/1000 batch size?
- What is the min/avg/med/max/p99 latency for 1/10/100/1000/10.000/100.000 entities for the entire pipeline?

JSON VS Parquet VS Arrow

This should indicate the gains in terms of end-to-end performance as well as running latency for stream processing.

Use Fly app with process groups for the demo.

### Show a GPU case

Run SentenceTransformers on the text. Compare differences. Expecting:

- GPU is more well-utilized producing slightly more results over a shorter period of time
- CPU usage may go down as we reduce transformation work. It may go up as we feed the GPU more actively.
- There may be other bottlenecks that become evident

First test: workstation
Second test: Scaleway or Fly GPU instance

This should show the wins/gains.

### LiveView app showing status

Ingest OTel status information. Expose additional information about progress.

### Orchestrate workloads
Use Fly Machines as a backend much like Apache Beam does with Dataflow.

API conceptually like Flow but probably defined in config.

```
Stage: "load podcast feeds"
Image: load-podcast@v5
Output: [ ]
```