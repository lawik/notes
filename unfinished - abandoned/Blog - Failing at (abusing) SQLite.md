I have lots of ideas. That's fortunate. Not all of them work out though. This is something along those lines.

*This blog post is sponsored by Tigris Data. They are your no-nonsense Object Storage that works gracefully with Fly.io's geo-distributed applications or if you just want your files distributed, CDN style, without the effort. Big thanks to them for sponsoring my streams, blog posts and creative efforts.*

When exploring interesting things to do with object storage and S3-compatible APIs part of the fun is to look at ways in which people have abused them. And of course playing with SQLite is always fun. I had at some point seen extensions for SQLite that allowed reading from a SQLite database using range requests over HTTP. I think I heard of this approach from [this blog post](https://phiresky.github.io/blog/2021/hosting-sqlite-databases-on-github-pages/) initially. I had looked around at this before and [this VFS came up](https://github.com/psanford/sqlite3vfshttp). It looks pretty good. Made in Go, runtime-loadable, pretty straight-forward to use.

So I grabbed my Tigris credentials. Created a public-readable bucket and showed my favorite SQLite database sample up on it. [The Podcast Index](https://github.com/Podcastindex-org/database). Btw, I like that they are making an alternative, a fallback for some inevitable future where Apple stops being an absent benevolent dictator of podcasts. I also appreciate experimenting with adding new stuff to podcasts. But Podcasting 2.0 loses me at the crypto-currency payments and ideals I don't share. Interesting data to toy with regardless. Over 4Gb uncompressed.

I cloned it the VFS repo, built it and had no issues reproducing the example from the docs on how to use it with `sqlite3`. Very cool. Had to be a bit careful to not accidentally query the whole DB though, those queries are just a full 4Gb download which is not what I want.

I then tried getting it going with `exqlite` under Elixir, planning to use it with Ecto. You know my bread and butter stuff. First of all I was brave because I had just made SQL Cipher work (well, [Connor Rigby made SQL Cipher](https://cone.codes/posts/encrypted-sqlite-with-ecto/) work) for me and knew `exqlite` would handle extensions.

I got none of it working. I hacked at it a bit but at best I managed a segfault under Ecto. At worst it just failed to open the database under raw exqlite.

Then [I did a livestream where I kept failing away at it](https://youtube.com/live/L-7K5S_DEXo). Helpful chat-participant Herman kept trying after the stream and replicated the behavior of the `sqlite3` CLI tool and [got it going](https://github.com/elixir-sqlite/exqlite/issues/283#issuecomment-2040257138). Not with Ecto, but at least with exqlite. Rad.

At this point I start my next livestream, [building a Phoenix app](https://youtube.com/live/0s28a8kBIvA) where this is intended to be the starting point. And it goes. And then it goes slower. And slower. Even with my careful queries where I essentially just query slices of what I assume is already ordered data...

```
select id, title from podcasts limit 10 offset 0;
```

So the above query and just keep moving the offset out. And it slows down. More and more. I would have expected that there was something clever that could be done to precisely fetch the byte ranges for an offset slice. But no such luck I'm afraid. And offset of 75000 will mean fetching a lot of data. 
