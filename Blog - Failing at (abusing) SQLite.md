I have lots of ideas. That's fortunate. Not all of them work out though. This is something along those lines.

*This blog post is sponsored by Tigris Data. They are your no-nonsense Object Storage that works gracefully with Fly.io's geo-distributed applications or if you just want your files distributed, CDN style, without the effort. Big thanks to them for sponsoring my streams, blog posts and creative efforts.*

When exploring interesting things to do with object storage and S3-compatible APIs part of the fun is to look at ways in which people have abused them. And of course playing with SQLite is always fun. I had at some point seen extensions for SQLite that allowed reading from a SQLite database using range requests over HTTP. I think I heard of this approach from [this blog post](https://phiresky.github.io/blog/2021/hosting-sqlite-databases-on-github-pages/) initially. I had looked around at this before and [this VFS came up](https://github.com/psanford/sqlite3vfshttp). It looks pretty good. Made in Go, runtime-loadable, pretty straight-forward to use.

So I grabbed my Tigris credentials. 

I cloned it, built it and had no issues reproducing the example in 