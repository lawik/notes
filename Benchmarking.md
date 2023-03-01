I have a few things I want to do that I should keep apart:

- measure assorted hardware's performance on the beam (workstation, Macs, Steam deck, dedicated servers vs VPS)
- benchmark web app capacity for REST API, renderes page, LiveView

Web app test workloads should be both synthetic (fake IO delay, simplistic CPU tasks, mixed) and simulating real scenarios.

Simulating real scenarios will be annoying implementation-wise. Essentially a set of operations:

- light crypto for auth
- database reads
- database writes
- joins

Accessed through:

- REST API
- Static pages
- LiveView (probably simplify and benchmark Channels)

Measuring:

- requests/s or operations/s
- concurrent connections that can be sustained in LiveView
