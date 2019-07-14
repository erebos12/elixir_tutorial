# Elixir

## Specifics

* _**Elixir data structures are immutable. One advantage of immutability is that it leads to clearer code. You can freely pass the data around with the guarantee no one will mutate it in memory - only transform it.**_

## Basic Types

### Atoms

An atom is a constant whose value is its own name. An atom is defined by a
leading `:`:  

    iex> :apple
    :apple

Often they are used to express the state of an operation, by using values such as `:ok` and `:error`.

The booleans true and false are also atoms:

    iex> is_atom(false)
    true

Finally, Elixir has a construct called aliases. Aliases start in upper case and are also atoms:

    iex> is_atom(Hello)
    true


### Strings

You can print a string using the `IO.puts/1 `function from the IO module:

    iex> IO.puts "hello\nworld"
    hello
    world
    :ok
Notice that the `IO.puts/1` function returns the atom `:ok` after printing.


Strings in Elixir are represented internally by binaries which are sequences of bytes:

    iex> is_binary("hello")
    true
    byte_size("hello")
    5
    String.length("hello")
    5

String concatenation is done with `<>`:

    `iex> "foo" <> "bar" --> "foobar"`

The String module contains a bunch of functions that operate on strings as defined in the Unicode standard:

    iex> String.upcase("hello")
    "HELLO"

See also [String Module](https://hexdocs.pm/elixir/String.html)

## Basic Operators

Also have look to [Elixir Operators](https://hexdocs.pm/elixir/operators.html)


### Comparision Operators

Elixir  provides `==, !=, ===, !==, <=, >=, <` and `>` as comparison operators:

The difference between == and === is that the latter is more strict when comparing integers and floats:

    iex> 1 == 1.0 --> true
    iex> 1 === 1.0 --> false


### Match Operator

In Elixir, the `=` operator is actually called the _match operator_. You cannot only assign values, you can also compare values by the _match operator_:

    iex> x = 1
    1
    iex> x
    1
    iex> 1 = x
    1
    iex> 2 = x
    ** (MatchError) no match of right hand side value: 1

But a variable can only be assigned on the left side of `=`.

The match operator is not only used to match against simple values, but it is also useful for destructuring more complex data types. For example, we can pattern match on tuples:

    iex> {a, b, c} = {:hello, "world", 42}
    {:hello, "world", 42}
    iex> a
    :hello
    iex> b
    "world"

The example below asserts that the left side will only match the right side when the right side is a tuple that starts with the atom `:ok`:

    iex> {:ok, result} = {:ok, 13}
    {:ok, 13}
    iex> result
    13

    iex> {:ok, result} = {:error, :oops}
    ** (MatchError) no match of right hand side value: {:error, :oops}

We can pattern match on lists:

    iex> [a, b, c] = [1, 2, 3]
    [1, 2, 3]
    iex> a
    1



### PIN Operator

Use the pin operator `^` when you want to pattern match against an existing variable’s value rather than rebinding the variable:

    iex> x = 1
    1
    iex> ^x = 2
    ** (MatchError) no match of right hand side value: 2
    iex> {y, ^x} = {2, 1}
    {2, 1}
    iex> y
    2
    iex> {y, ^x} = {2, 2}
    ** (MatchError) no match of right hand side value: {2, 2}

The last example could also have been written as:

    iex> {y, 1} = {2, 2}
    ** (MatchError) no match of right hand side value: {2, 2}

### Anonymous functions

Anonymous functions can be created inline and are delimited by the keywords `fn` and `end`:

    iex> fn a, b -> a + b end
    #Function<12.71889879/2 in :erl_eval.expr/5>
    iex> (fn a, b -> a + b end).(1, 2)
    3

Anonymous functions are “first class citizens” in Elixir, meaning **_they can be assigned to variables, and passed as arguments to other functions_** in the same way as integers and strings.

    iex> is_function(fn a, b -> a + b end)         # pass to function
    true
    iex> add = fn a, b -> a + b end                # assign to a variable
    #Function<13.91303403/2 in :erl_eval.expr/5>
    iex> add                                  
    #Function<13.91303403/2 in :erl_eval.expr/5>
    iex> add.(1, 2)                                # use it by variable
    3

Note that a dot (.) between the variable and parentheses is required to invoke an anonymous function. The dot ensures there is no ambiguity between calling the anonymous function matched to a variable add and a named function add/2. In this sense, **_Elixir makes a clear distinction between anonymous functions and named functions_**.

Anonymous functions are closures and as such they can access variables that are in scope when the function is defined.
Let’s define a new anonymous function that uses the add anonymous function we have previously defined:

    iex> double = fn a -> add.(a, a) end
    #Function<6.71889879/1 in :erl_eval.expr/5>
    iex> double.(2)
    4

## Lists

Elixir uses square brackets to specify a list of values. Values can be of any type:

    iex> [1, 2, true, 3]
    [1, 2, true, 3]
    iex> length [1, 2, 3]
    3

Two lists can be concatenated or subtracted using the ++/2 and --/2 operators respectively:

    iex> [1, 2, 3] ++ [4, 5, 6]
    [1, 2, 3, 4, 5, 6]
    iex> [1, true, 2, false, 3, true] -- [true, false]
    [1, 2, 3, true]

List operators never modify the existing list. Concatenating to or removing elements from a list returns a new list. We say that Elixir data structures are immutable.

The head is the first element of a list and the tail is the remainder of the list. They can be retrieved with the functions `hd/1` and `tl/1`.

    iex> list = [1, 2, 3]
    iex> hd(list)
    1
    iex> tl(list)
    [2, 3]


## Charlists

_**Charlists**_ are quite common when interfacing with existing Erlang code. Whenever you see a value in iex and you are not quite sure what it is, you can use the `i/1` to retrieve information about it:

    iex(11)> i 'hello'
    Term
      'hello'
    Data type
      List
    Description
      This is a list of integers that is printed as a sequence of characters delimited by single quotes because all the integers in it represent printable ASCII characters. Conventionally, a list of Unicode code points is known as a charlist and a list of ASCII characters is a subset of it.
    Raw representation
      [104, 101, 108, 108, 111]
    Reference modules
      List
    Implemented protocols
      Collectable, Enumerable, IEx.Info, Inspect, List.Chars, String.Chars


ATTENTION: 'hello' and "hello" is NOT the same. _Single quotes are charlists, double quotes are strings_:

    iex> 'hello' == "hello"
    false

## Tuples

Tuples can hold any value:

    iex> {:ok, "hello"}
    {:ok, "hello"}

You can get elements by index:

    iex> elem(tuple, 1)
    "hello"

It is also possible to put an element at a particular index in a tuple with put_elem/3:

    iex> tuple = {:ok, "hello"}
    {:ok, "hello"}
    iex> put_elem(tuple, 1, "world")
    {:ok, "world"}
    iex> tuple
    {:ok, "hello"}

Notice that `put_elem/3` returned a new tuple. The original tuple stored in the tuple variable was not modified. Like lists, _**tuples are also immutable**_. _**Every operation on a tuple returns a new tuple**_, it never changes the given one.
