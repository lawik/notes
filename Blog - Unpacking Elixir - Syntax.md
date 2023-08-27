Elixir is a language with syntactical roots in Ruby. Ruby is an object-oriented language, Elixir is functional language. Overall I would say the Elixir syntax is pretty approachable and reasonable to learn.

This is another piece of my series on "Unpacking Elixir". The other piece right now is [concurrency](/unpacking-elixir-concurrency.html).

My background is Python so I wasn't familiar with Ruby before-hand and ran into all these Ruby-isms with some confusion. Overall it was still a pretty smooth ride for me. I had done PHP, Javascript, Python, some C/C++, C#. It is a slightly new style but it didn't scare me.

The thing that people will probably most find unnecessary coming from C-style languages or very verbose coming from Python is how a block is defined:

(TODO: elixir markings)
```
do
  # block contents go here
end

if foo? do
	# true
else
    # false
end

def my_function(arg1) do
  # function body
end
```

It is fair that `end`  has a lot more characters than `}` but I do think it comes across as more human. A less dense and symbol-filled syntax tends to be more approachable.

Let's write a basic module:

```
defmodule MyModule.SampleThing do
	@moduledoc """
	This is module documentation. Typically written as Markdown.
	"""
	
	# compile-time attribute, define-style
	@my_attribute 1000
	
	@doc """
	This is function documentation.
	"""
	def public_function(arg1, arg2) do
	  # do thing
	  new_arg = arg1 + arg2
	  # return the last thing done
	  new_arg
	end
	
	defp private_function(arg1, arg2) do
	  # do something
	end

	defp short_function(x), do: x + 1
	defp no_args, do: 1
end
```

Modules are Pascal case, functions and bindings of values are snake_case. Docstrings are multi-line text strings and also compile-time attributes. ExDoc can make very nice documentation with these. Both types of docs can also contain doctests which are a nice way of 