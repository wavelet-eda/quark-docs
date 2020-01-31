# The Quark Type System

Quark's goals are to bring high level abstractions to hardware development with
zero costs. To acheive this goal, Quark has a sophisticated type system which
allows polymorphic programming. Quark's type system draws inpiration from other
zero cost type systems like Rust and Haskell.

## Types

Quark has 4 basic catagories of types, `struct`, `enum`, `trait`, and `module`.
Future versions of Quark will support a `function` type. If you've written
verilog before, you are familiar with the basic concepts of structs, enums,
and modules. The rest of this section will discuss the type system in
greater detail.

## Parameters

Like in Verilog, Quark has two types of variables: Parameters and Signals.
Unlike Verilog, Quark considers Parameters to be part of the type system.
This means that a `Bit[32]` is a different type than `Bit[16]`. This helps
ensure the compiler can catch incorrect code before the expensive synthesis
step.

Quark also has two kinds of parameter: _Type Paramteres_ and _Value
Parameters_. Value Parameters are very similar to Parameters in verilog.
Type Parameters are more similar to generics in software languages. Type
Parameters allow structs, enums, functions, and modules to be written to
be generic accross multiple signal types. This allows code to be reused 
more often.

Importantly, Value Parameters also have types. This means that the type
checker can ensure that Parameter code is correct before parameter
evaluation occurs.

Taken together, Quark's parameter system shares many similarities to
Verilog's but adds a few features to ensure better code reusability.
This allows Quark hardware libraries (including the standard library)
to be useful in a much greater number of use cases.