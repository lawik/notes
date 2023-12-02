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