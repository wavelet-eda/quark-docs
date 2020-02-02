Enum Types
==========

Enums in Quark are much more powerful than enum in Verilog. For those type
system nerds out there, Quark enums are proper
`Sum Types <https://en.wikipedia.org/wiki/Tagged_union)>`_!
Essentially, Quark enums are tagged unions whith an extra touch of compiler
saftey checks.

Declaring Enums
---------------

Before we get into the details of using enums, we should discuss declaring
them. A simple enum in Quark looks like this:
::

    enum ClockEdge {
        Rising;
        Falling;
    }

Quark's compiler automatically decides how to pack an enum signal.
In the future, you may be able to use Quark Extensions to inform
the compiler the desired width of an enum signal. In Quark, each
case of an enum (e.g. `Rising` or `Falling`) in this example is called a
*Constructor*. This is because you create a signal of the enum type
(e.g. `ClockEdge`) by constructing it with one of the case options.

Unlike in Verilog, you **cannot** typecast an enum to a Bit vector.
This prevents numerous bugs.

What makes Quark enums so much more powerful than enums in Verilog is that
enums in Quark can contain signals. This means that a bit of runtime data can
be associated with each enum constructor. For example, imagine we can a state
machine where some states were associated with data and some states were not.
In Verilog, we might represent this by using an enum and a struct register in a
module. This can be prone to bugs, however. If the struct containing the data
is read when the FSM is in a state which does not define the value of the
struct, you might have incorrect results. In Quark, you can use enum
constructor fields to ensure this can never happen:
::

    enum State {
        Idle;
        State0(Bit[32] stored_value);
        State1(Bit some_flag);
    }

This way, the approiate data is only available when in the correct state.
Furthermore, it is impossible to accidently enter the state without assigning
the correct values.

Just like structs, enums can be parameterized with Value Parameters or
Type Parameters:
::

    enum Foo!(type BAR, Bit[32] WIDTH) {
        HasBar(BAR);
        HasBit(Bit[WIDTH]);
    }

Using Enums
-----------

Talk about pattern matching...

Standard Library Enums
----------------------

The standard library provides serveral generic enums which may be useful in
your hardware.

Maybe
-----

The `Maybe` enum is useful in cases where a signal may or may not be available.
`Maybe` fills a similar role to `null` in software languages while ensuring
there are no undefined states. Maybe is defined like this:
::

    enum Maybe!(type T) {
        Just(T);
        Nothing;
    }

Thus, a `Maybe` signal is either nothing or just a `T` signal. This is
especially useful as an output of modules or functions which sometimes produce
valid output and sometimes do not.
