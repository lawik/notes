We can start with basic values:

numbers can be integers or floating point, subject to much the same rules as in most programming languages

1
2
4000
60

0.5
33.33

And we can perform operations on these values.

5 + 2
3.3 - 1.2
2 * 7
4 / 2

Binaries are used for .. binary data, like these four bytes:

<<0xff, 0x01, 0x16, 0x00>>

Strings are used for text and are a special case of binaries that are compatible with the UTF-8 encoding:

"Hello world"
"En mördad björn"
"💩"

We can concatenate any binary for example:

"Hello" <> " " <> "world"

Atoms are values that are only themselves. They don't combine or come apart. Where you want a value that is a clear piece of information. Numeric status codes are weird because they are numbers which mean you can do a ton of things to them. A string invites dynamic values. Atoms fill a role and are atomic.

:ok
:error
:not_found
nil
true
false

Oh, yeah, there is some sugar where you can skip the colon for some atoms. Specifically nil. Which is of course a cursed value indicating a non-value or nothing, commonly called null in many languages. It is not more or less cursed in Elixir really. But it is worth noting that it is an atom. Interestingly Erlang doesn't have a nil/null equivalent.

And the same sugary sweetness of skipping the colon is used for true and false. The two sides of a boolean value.

How do we work with values? Well, we can give them names, called bindings. Some languages would call it a variable. But that's in contrast to a constant and we have neither. We just bind names to values.

first_name = "Lars"
real_job = :developer
year = 2025
birth_year = 1984

We can do operations on them and get new values.

age = year - birth_year

We can rebind a variable with a new value. This changes what value is referred to by the name but doesn't change the value itself in any way. This is Elixir's flavour of immutability.

Some values belong together and various ways of grouping values is a pretty important part of how we build data structures in programming. Elixir has a bunch.

The simplest is the tuple. It is an ordered grouping of several values. Useful for x/y coordinates, ok/error results or just managing a few values together in a minimal way.

point = {5, 3}
instruction = {2, 4, 6, 8, :motorway}
result = {:ok, :published}
result = {:error, :not_found}

These are not intended for a sequence of values that are inherently a varying number or many. Especially not many of the same type of value. Anything like that and you'll typically be dealing with a list.

even = \[2, 4, 6, 8, 10, 12, 14, 16\]
languages = \[
  "Elixir",
  "Erlang",
  "Ruby"
\]

The list is kept in the specified order. Unlike our next contestant. The map is a key/value data structure meaning it has some number of keys that reference values.

%{
  "my_key" => "my_value"
}

That's the normal syntax. But since it is very common to use atoms for keys there is a special syntax for that:

%{
  my_key: "my_value"
}

though you can do

%{
  :my_key => "my_value"
}

if you are a pervert.
But you can use anything as a key to a map:

%{
  {5, 3} => :secret_hiding_spot,
  {4, 3} => :trap
}

Keys are unique, you can't have duplicates and maps are unordered so you must not rely on the order you set the keys.

There are also two odd composite values. The first is a char list. A list of numbers that fall within the range of valid code points will be considered a list of characters, aka a char list. This was how strings worked in Erlang for a long time and that has consequently made them necessary in Elixir.

Meanwhile keyword lists are lists that contain tuples where the first item is always an atom and the tuple only has two total values.

\[
  {:first_name, "Lars"},
  {:last_name, "Wikman"}
\]

Ugly right? Well there is a nice syntax for it:

\[
  first_name: "Lars",
  last_name: "Wikman"
\]

They are often used for options and when dealing with functions there is some nice syntactic sugar supporting that use.

Now we come to another fundamental part. Modules. A module is a part of your software. It serves as a namespace and a unit of compilation. A module exposes a set of public functions. To reference a specific function clearly enough to call it you need 3 pieces of information. The module, the atom for the function name and the arity. Arity is a fancy word for the number of arguments. This is often represented as a three-tuple, tripple, thruple, thripplet. A Module, Function, Arity sometimes called an MFA.

{MyModule, :my_function, 2}

A different arity is fundamentally a different function.

Defining a module is simple enough.

defmodule MyModule do
  def my_function(x, y) do
    other_function(x, y)
  end

  defp other_function do
    IO.inspect(x * y)
  end
end

It all starts with a `defmodule` and the scope of the module is started at the `do` and ends at the final `end`. This is how blocks work in Elixir. Do. End. The `def` marks the definition of a public function named my_function with two arguments, so an arity of two. We also define a private function using `defp` which is only available within this module.

We call a function from the IO module called inspect that has an arity of 1.

Oh, and we don't explicitly define return-values. The last expression executed in a function determines the return value. There is no early return aside from using a conditional and I haven't told you about any conditionals yet.

There are two other common ways of defining functions, both are for defining anonymous functions, AKA lambdas.

my_function = fn my_arg ->
  IO.inspect(my_arg)
end

my_function.(:foo)

The first is the regular way. The fn keyword, args, an arrow and close it with an end. If you have a binding that references a function you can call it with a special dot syntax. This distinguishes it from a normal function call.

The other way is a nice way of building one-liners for certain uses.

my_function = & IO.inspect(&1)

This defines the same function and it is invoked in the same way. It is more terse which is either great or terrible for readability. Use it with care.

Elixir can also do significant work at compile-time. The simplest is setting or calculating a constant, called a module attribute.

\# 5 minutes in milliseconds
@my_timeout 5 * 60 * 1000

And any reference to @my_timeout will be replaced with the calculated value at compile-time. It will be inlined, so there are cases where it is not ideal if the constant is very large. For typical constants it is spot on.

And then we have macros. The magical but scarier bits of Elixir. Mostly scary because people sometimes try to be too clever with them. They are bits of code that are evaluated at compile-time. Anything you write in Elixir will be turned into AST form by the compiler. The Abstract Syntax Tree is then turned into Erlang Abstract Format and off we go into the Erlang world. Before then we can manipulate this AST. Macros allow us to manipulate what AST is generated using quote and unquoute.

quote takes an expression you write and turns it into the AST. unquote lets us evaluate an expression inside of a quote. This allows us to generate a dynamic set of functions at compile-time:

defmodule Foo do
  for i <- 1..5 do
   def unquote(String.to_atom("my_fun_#{i}"))(x) do
      IO.inspect(x)
   end
  end
end

Foo.my_fun_3(:foo)

And the `defmacro` .. macro allows writing what feels like functions but they are actually replaced with other code at compile-time and that's what runs inside the function that uses them. It is mildly mind-bending but incredibly useful.

I've yet to see a really clarifying succinct example for a defmacro. Just know that they exist.

A lot of things in Elixir are defined as macros. `def` itself is implemented as a `defmacro`. I don't know how I feel about that.

Back to something reasonable. This is a Functional Programming mainstay that has been seeping into the wells that all modern languages draw from as they try to no longer look and feel like Java and C++. Pattern matching.

Provide a pattern for what you expect the data structure to look like. It either matches or it doesn't. This can be used in a few ways.

Assume we call a function and we don't know what it will return, we could match directly and hope it is an ok-tuple:

{:ok, my_value} = MyModule.my_function()

If it is not a two-item tuple that starts with the atom :ok it will raise an error. If it does match it will bind the second item of the tuple to the name my_value. Not sure we have the time to cover the Erlang philosophy summarized as Let It Crash. But this is certainly that. This is success or hard fail.

We can also match on other data structures:

%{firstname: foo} = MyModule.my_function()

This would match any map which has the atom key firstname. And it would bind the value of that key to foo for later use.

List are interesting. As the Erlang list is a linked list they are fairly efficient to operate on in one direciton and not in the other. So you can plop the head off of the list trivially and we have pattern matching syntax for that.

\[ head | tail \] = MyModule.my_function()

The head binding would hold the first item of the list and the remainder ends up in the tail binding.

Binaries have pattern matching as well and this is a super-power for networking protocols, decoding binary protocols and many other fun things one ocassionally does.

<<0xff,0xd8,0xff, ::binary>> = my_jpeg = MyModule.my_function()

This would check the first three bytes of the returned binary and match if they are the specific bytes. In this case the magic bytes for a jpeg. then we bind the entire value to my_jpeg because we don't want to strip the header. Going beyond this, you can read individual bits, specify integer size to interpret as well as endianness. And it is just as easy to actually build binaries.

With this functionality the bitwise operations and bitshifting you see in other languages when dealing with binary data is almost a code-smell. It is available but rarely needed.

Erlang was forged in telecom and slinging binaries has been important for a long time.

Now crashing on a failed match is not a great pattern for the day-to-day activity of programming. So we have conditionals. And we mostly need one in Elixir.

The case statement.

Imagine if a switch statement didn't suck so much.

case MyModule.my_function() do
  {:ok, value} ->
    IO.inspect(value, label: "success")
  {:error, reason} ->
     IO.inspect(value, label: "failure")
end

This would work for the intended cases and would crash only if nothing matched. Because binding is just pattern-matching with a wide-open pattern that accepts anything we can do a full wildcard case.

case MyModule.my_function() do
  %{first_name: "Sofi", last_name: last_name} ->
     IO.inspect(last_name, "A Sofi with last name")
  other_data ->
     IO.inspect(other_data)
end

We can unfurl and unpack data structures and get very specific about what shapes they are, what is in them and determine the branches of our program based on that. Without many nested if statements. And handling the fallback if our conditions fail is a single thing. For lists this is interesting:

case MyModule.my_function() do
  \[\] ->
    IO.puts("an empty list")
  \[item\] ->
     IO.puts("a single item list")
  \[item_1, item_2] ->
     IO.puts("a two-item list")
  \[head | \_\] ->
     IO.puts("The first item of any other list")
end

The name pattern-matching is apt. We write patterns for our data structures and check for matches.

You can also match for patterns in function-heads.

def my_function(:person, name) do
..
end

def my_function(:city, name) do
..
end

We can match based on an argument. This all compiles down to a case statement but it really does help tease apart code that would otherwise get a bit tangled.

It does also work for anonymous function but is less commonly used:

fn
  :person, name ->
    ..
  :city, name ->
     ..
end

There is another conditional that leans heavily on pattern matching. The `with` statement.

with
  {:ok, user} <- MyModule.get_user(),
  {:ok, food} <- MyModule.get_food(user),
  {:ok, shoes} <- MyModule.get_shoes(user, food) do
  IO.inspect(shoes, label: "shoes")
else
  other_outcome ->
    IO.inspect(other_outcome, label: "failure")
end

The else-clause is optional but can also be a full on matching-frenzy. The trick here is that if any match fails the with statement will return the non-matching value or fall into the else-clause if one is available. This allows writing the happy-path in a pretty convenient way.

There are some conditionals that are not quite so pattern-matchy.

cond do
  MyModule.my_function() == 5 ->
    IO.puts("it was five")
  true ->
    IO.puts("fallback")
end

A cond statement will execute the first branch condition that evaluates as true. A catch-all can be provided using the literal true at the end.

Oh, and we have `if` statements like any other language. But they are just a macro over case with some conveniences for dealing with `nil` values.

So how do we loop? while? for? foreach? Well, `for` does exist but the fundamental way to do iteration in functional programming is through recursion. In many imperative or object-oriented languages a function that calls itself is risky as it can blow the call-stack.