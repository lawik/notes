Minimize JS payload balanced with full local-first support and offline-support.

I am thinking of using Automerge JS libraries to achieve consistency.

A Service Worker is installed that abstracts storage of changes.

A change to the "document" is done with Automerge. And then sent as an update to the service worker. If there is an online backend available it will write to both local and remote.

A read of some new data goes through the service worker it will always read from local first but also trigger a pull from any online backends when available. That should get updates.

Occasionally checking for updates seems reasonable.

Every browser session that auths can be given an identifier from the server. So the server can tie together the identity of the user and the many instances of their work and produce a merged session that everyone can consume. Every session keeps their own vector clock (number) to let them know what to write next.

The browser session can keep a presigned URL around for writing the next chunk: "users/5/session/abcd4321/todo/$chunkId"

With wildcard presign prefixes this would get even better.

Whether directly against an Object Storage or just an application server the point is to try and stay quite close to HTTP and HTML semantics.

I don't think HTMX and similar tools make sense for this as they are very server-side-rendering-focused. And our client needs to know how to render.

This would be buildable in JS+Node to get reusable code but I despise using Node overall, personally. Rust with WASM or Go with WASM could do it. My favorite candidate suddenly though is Gleam.

Or more likely I don't need to share code between frontend and backend. And I can do vanilla JS in browser without buildstep and Elixir on the server. Accessing Automerge on the server will require either shelling to JS or a Rust NIF. Or some other way of running WASM?

Yes, WASM seems good. [wasmex](https://github.com/tessi/wasmex) should let me run this straight up. Does bring in Rust NIF stuff but that seems ~fine.

