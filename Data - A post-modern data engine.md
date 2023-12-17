Streaming and batch. Sure.

Python legacy support? Probably.

Promote and focus on building a flow that gains simplicity and efficiency through minimal transformation, optimal performance and adhering to standards optimized for data and processing.

This also serves to optimize CPU and GPU performance.

- Apache Arrow by default
- Ideally Producer/Source nodes are ADBC-backed or Arrow Flight backed or otherwise produces Arrow resource batches but otherwise they can transform or produce their raw format
- Arrow Flight as the protocol for communication between stages
- GenStage for Source/Sink/Filter
- Library wrapper for making a GenStage into a dynamically scaling Arrow Flight service
	- No additional requirements on the GenStage, it takes config as usual
	- Mechanisms for discovery
		- over cluster
		- Others
	- Wrap in supervisor much like Flow
	- Demand produces back pressure, starts at end of pipeline
	- RemoteFlow.through_available("stage-service-name")
	- RemoteFlow.partition() ??


If any lang can be used to define a step in processing we need a common interface for it. If the assumption is arrow as data format we should optimise for zero-copy. The interface between steps should be semantically compatible with Arrow Flight. But for a local (same host) pipeline or a series of operations in the same language we want to be able to collapse them into the same process or at least not introduce copying when it isn't needed.

Input is a set of named streams. Output is a set of named streams.
Anything that goes over the named streams is Arrow data. Depending on circumstance this could be done with Arrow Flight RPC (ideal for network hop) or if we want to get fancy via IPC shared memory (receive read only, produce read only). Local Arrow Flight is probably a good start for that