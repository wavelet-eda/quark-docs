Type Inference
==============

Quark has a sophisticated type inference system. This helps Quark code be
conscise and expressive without becoming difficult to understand.

Quark uses an iterative solver to first resolve Param types and then resolve
signal types. Type inference in Quark works globally.

Using ``var`` for Full Type Inference
-------------------------------------

The most basic form of type inference in Quark uses the ``var`` keyword. When
declaring a new signal or param variable, the type of the variable can be
replaced with the ``var`` keyword. When Quark encounters ``var``, it deduces
the type of the variable from the type of the expression being assigned to it.
This is similar to using ``auto`` types in C++.

Here's an example:
::

    def AddBits(Bit[32] a, Bit[32] b): Bit[32] {
        var result = a + b; //identical to "Bit[32] result = a + b;"
        return result;
    }

This construct helps keep code consice. Since type inference works globally,
it's best to only use ``var`` in situations where the variable type is clear to
a reader.

Inferable Params & Explicit Params
----------------------------------

As you've seen previously, Params in Quark go in the Parameter list (which uses
the ``!(...)`` syntax). Params which are declared in the Parameter List are
called *Explicit Params*. Explicit Params are considered part of the type name
or function name. Explicit Params must always be fulfilled wherever the type
name is written.

However, this is not the only way to use Params in Quark. Sometimes you don't
want to clutter the Parameter List with type and value params which are obvious
from their usage. This is especially useful for type parameters.

Imagein the following scenario:
::

    def foobar!(type T)(T a): T {
        return a;
    }

In the function ``foobar``, there are no constraints to what real type ``T``
can take on. Thus, any invocation of ``foobar`` is valid regardless of what
type is provided in the parameter list. That makes invocation somewhat verbose:
::

    def someOtherFunction() {
        Bit[32] a = 5;
        Bit[32] b = foobar!(type Bit[32])(a);
    }

In this example, you have to write ``Bit[32]`` 3 times. To solve this problem,
you should use an implicit parameter for ``T``:
::

    def implicitFoobar(T a): T {
        return a;
    }

Here, the compiler notices that ``T`` is not a declared type name and instead
treats it as a type parameter. However, there is no way for an invocation of
``implicitFoobar`` to explicitly pass the parameter. Instead, the type
inference system will deduce the correct types. Invocations of
``implicitFoobar`` look like this:
::

    def someOtherFunction() {
        Bit[32] a = 5;
        Bit[32] b = implicitFoobar(a);
    }

This cuts down on the verbosity of the invocation significantly.

In general, implicit parameters can be used whenever the value of the parameter
would be explicitly written in the arguments list or the return list. The value
of an implicit parameter cannot be determined by a parameter calculation. For
example:
::

    def concatBits(Bit[WIDTH] x, Bit[WIDTH] y): BIT[OUT_WIDTH] {
        Size OUT_WIDTH = WIDTH + WIDTH;
        Bit[OUT_WIDTH] return_value = {x, y};
        return return_value;
    }

would fail to compile. This is because OUT_WIDTH is calculated in a param
expression. Notably,
::

    def concatBits(Bit[WIDTH] x, Bit[WIDTH] y): BIT[OUT_WIDTH] {
        var return_value = {x, y};
        return return_value;
    }

would compile because ``OUT_WIDTH`` is constrained only by declarative type
constraints.

Similar if the parameter that appears in the input list has a function applied
to it, you must use an explicit param:
::

    def foobar!(Size MAX)(Bit[clog2Up!(MAX)]) {
        //...
    }

In this example there's no way for Quark to know how to calculate the inverse
of ``clog2Up``.

Another case where implicit parameters cannot be used is when a parameter value
depends on the outcome of a branch:
::

    def foobar(Bit[IN_SIZE] a): Bit[SIZE] {
        if IN_SIZE > 16 {
            return 32'b0;
        } else {
            return 16'b0;
        }
    }

This example fails to compile as type inference could cause confusing execution
order changes. Instead, the output size of the function should be determined
directly by an explicit param.

In summary, if you need complex param calculations or non-declarative param
constraints you must use explicit params. Otherwise, use implicit params. Since
type params can only ever have declarative constraints and cannot appear in
expressions, it is almost always possible to make type params implicit. Value
params often must be explicit.
