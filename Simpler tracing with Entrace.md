- Demo the steps to perform a trace with erlang primitives
	- Raise some questions about the API
	- Note the single-receiver limitation
	- Note the risk
	- Note common solutions: redbug, recon
		- Show a trace with redbug
		- Show a trace with recon
	- Note Elixir option, recon_ex
		- Show a trace with recon_ex
- Show Entrace
- Bonus? Sequential tracing
- Bonus? Triggering specific traces from wide simpler traces


## Redbug

```
redbug:start("erlang:demonitor")
```

```
redbug:start("erlang:demonitor->return,stack",[{msgs,2}])
```

## recon

## edbg

https://github.com/etnt/edbg#tracing