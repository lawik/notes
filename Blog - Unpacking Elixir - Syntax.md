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

    # private function with an optional default argument
	defp private_function(arg1, arg2 \\ 5) do
	  # do something
	end

	defp short_function(x), do: x + 1
	defp no_args, do: 1
end
```

Modules are Pascal case, functions and bindings of values are snake_case. Docstrings are multi-line text strings and also compile-time attributes. ExDoc can make very nice documentation with these. Both types of docs can also contain doctests which are a nice way of making simple tests, testing your sample code in the docs and encouraging code samples in the documentation.

Any do/end block can be replaced with `, do:`  for one-liners. If a function takes no args you can skip the parentheses. Functions return the value of the last expression. There is no early return without branching and you do not make the return explicit.

Quickly some values and types:

```
integer = 1
float = 1.0
boolean = true # true and false are both atoms
null_value = nil # nil is also an atom
atom = :foo # aside from true/false/nil you reference atoms with a colon :
string = "lawik"
binary = <<108, 97, 119, 105, 107>> # equivalent to string above

tuple = {:ok, 5} # multiple values grouped, not limited to two
list = [1, 2, 3, 4, 5]
map_of_strings = %{"username" => "lawik", "site" => "underjord.io"}
map_of_atoms = %{username: "lawik", site: "underjord.io"}
struct = %MyStruct{username: "lawik", site: "underjord.io"} # very special map

# erlang compatibility
keyword_list = [username: "lawik", site: "underjord.io"] # the old-school Erlang map
same_but_in_lists_and_tuples = [{:username, "lawik"}, {:site, "underjord.io"}]
charlist = 'lawik' # the old-school Erlang string
charlist_in_lists_and_numbers = [108, 97, 119, 105, 107]
```

Interesting sugar around these types.

Strings are binaries that fit within the constraints of a UTF-8-encoded binary. String and binary concatenation is `"foo" <> "bar"`. String interpolation is `"Hello #{name} and welcome."` where `name` is a value or expression that converts to a string.

Lists have a syntax convenience for prepending. `new_list = [ new_value | old_list ]` will do it. It will return when we talk pattern matching. Lists are implemented as linked lists (not ideal, it is known) so appending is much less performant. List concatenation is `a ++ b` . Most operations are found in either the `List` or `Enum` modules.

Maps have a syntax convenience for updating an existing field. `new_map = %{ old_map | atom_key: new_value}` makes for a pretty simple process. Also works for structs. Overall map operations live in the `Map`  and `Enum` modules.

Keyword lists are very common in Erlang for options. Elixir added some nice sugar for that purpose. A list of tuples can be created with `[key1: 5, key2: 6]` and when calling a function which takes options as a Keyword list as the last argument you can drop into what feels like a python kwargs situation: `my_function("regular value", force: true, timeout: 553)` No double splat to speak of though.

Pipes are kind of neat:

```
defmodule MyModule.Pipage do
  def new do
    %{}
  end

  def add_defaults(thing) do
    thing
    |> Map.put(:timeout, 5000)
    |> Map.put(:weather, "rainy")
    |> Map.put
  end

  def set_name(thing, name) do
    Map.put(thing, :name, name)
  end

  def do_all_of_it do
    new()
    |> add_defaults()
    |> set_name("lawik")
  end
end
```

So pipes allow passing a value through and changing it. They don't deal with failure or anything like that but in functional life there are many cases where this just becomes much more readable. Let's make an example that is more scripting-oriented and uses the Elixir standard library.

```
"~"
|> Path.expand()
|> Path.join(".config/my_app")
|> File.mkdir_p!()
|> File.ls!()
```

This shows one of two interesting conventions for Elixir function names. The `!` at the end of a function indicates a function that will raise an error on failure. Usually they have a sibling function without the bang that will return a tuple of `{:ok, result}` or `{:error, reason}`. The other one is `?` at the end of a function to indicate it typically provides a boolean result.

Why these result tuples? We don't have static typing and Result types, monads or what have you in Elixir. The ok/error tuple is a convention from Erlang and is essentially an informal Result type. Elixir didn't upend the convention which means interop with Erlang code feels normal.

The way they are generally used is with pattern matching. To extract the value and throw a MatchError if it does not you can use a bare pattern match inline in a function. 

```
{:ok, status} = File.stat(my_path)
```

Or you can do a case statement:

```
case File.stat(my_path) do
  {:ok, %{type: :directory}} -> # do thing
  {:ok, _unused} -> # do other thing
  {:error, :enoent} -> # specific error
  _ -> # any other result
end
```

You can also do nice things using the `if` macro, the `with` statement and a bunch of other things. I won't attempt to cover all the syntax. The `for` macro comprehension thing is wildly feature-filled. One very nice place to use pattern matching is in function heads. Elixir supports overloading of a sort.

```
defmodule MyApp.MyModule do
	def eat_value(nil) do
		{:error, :hates_nil}
	end

	def eat_value(%{style: :tasty}), do: {:ok, :tasty}
	def eat_value(%{flavor: any_flavor}), do: {:ok, any_flavor}

	def eat_value(%{} = any_map_or_struct) do
		{:ok, :tasted_fine}
	end

	def eat_value([first_value, | _rest_of_list]) do
		{:ok, first_value}
	end

	def eat_value(num) when is_integer(num) or is_float(num) do
		{:ok, "I will consider it a #{num}."}
	end

    # Convention dictates _ to ignore a value in a match
	def eat_value(_), do: {:ok, :nothing_special}
end
```

This fundamentally compiles down to a case statement in a single function and if none of the cases match it will raise a specific error. You can also apply guards for numeric ranges and such. This example has matching for; Specific value `nil`. Value of a key in a map. Value of a key in a map to a binding and using it later. Head of a list. 

The `with is_integer(..` stuff is a guard clause. It also shows the convention of `_` marking unused bindings, either with a name like `_bla` or just the plain `_`.

Anonymous/lambda functions are an important thing in most functional programming and we certainly have those.

```
# We can bind an existing function to a value with this syntax
referenced_function = &File.stat/1

# Anonymous function, full syntax
anon = fn arg1, arg2 ->
	arg1 + arg2
end

# Anonymous function, short syntax
anon = & &1 + &2

# Anonymous function, multiple clauses
anon = fn
  {:ok, result} -> result + 5
  {:error, reason} -> {:error, :bad, reason}
  other -> other
end

# Calling an anonymous function
anon.(my_first_arg)
```

These are commonly used in pipelines for things like the very important Enum module:

```
File.ls!()
|> Enum.map(fn filename ->
  Path.join(my_path, filename)
end)
```

Something that is not Elixir syntax so much as something you run into using libraries within Elixir are macros and DSLs (domain-specific-languages) built from macros. Elixir does not allow a ton of hiding things in normal code. The code is explicit and mostly goes in a straight line. To avoid some things becoming very unwieldy, such as the Phoenix web framework's routing and the Ecto database library's schema/migration definitions they have built small sets of macros that generate the relevant functions for you. This is one of few places where I find things often feel a bit woo-woo and magical. But it is almost exlusively where the alternative would be worse, either for a technical reason or for a human reason.

So when you run into things that don't feel like normal Elixir code. Odds are you are dealing with macros. They can define custom blocks and they can define custom keywords that are mostly like functions but actually expanded at compile-time.

Example of an Ecto database schema:

```
defmodule MyApp.MyUser do
	use Ecto.Schema

	schema "users" do
		field :username, :string
		field :password, :string, redact: true
		timestamps()
	end
end
```

This generates a model with everything the database layer needs to know about the schema. Your `MyApp.MyUser` module will have a `__schema__()` function. `use Ecto.Schema` is what brings that magic into your module and your subsequent calls to the macro `schema`, `field` and `timestamps` do the rest.

This brings us to one thing that is a bit messy but learnable. `alias`, `import`, `require` and `use`. This is [also covered in the official guide](https://elixir-lang.org/getting-started/alias-require-and-import.html) which is where I learned Elixir mostly.

- `alias` shortens the name of a module or lets you change the name you use for a module.
- `require` makes macros available from a particular function.
- `import` brings in functions AND macros.
- `use` lets the specific module inject any code functions through macro madness.

This is already a bunch to go through. I have not covered all syntax in the language but I think I've given it a fair shake. I hope this is beneficial to you if you are curious to try the language or figure out if it is something you might enjoy.

---

If you have questions, comments or concerns you can reach me by my email at {{< lars_email >}} or on the socials {{< lars_twitter >}}.
