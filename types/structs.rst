Struct Types
============

Structs are the simplest type in Quark. Basic structs look exactly the same in
Quark as they do in Verilog.


Declaring Structs
-----------------

Here's an example of a struct representing a mathematical 3D Vector:
::

    struct Vector3 {
        Bit[32] x;
        Bit[32] y;
        Bit[32] z;
    }

This is all well and good, but we may need `Vector3` to accept variable bit
widths for each field. We can solve this just like we would in verilog by
adding a param to the struct:
::

    struct Vector3!(Size WIDTH) {
        Bit[WIDTH] x;
        Bit[WIDTH] y;
        Bit[WIDTH] z;
    }

Notice how params are declared using a bang than parens (`!(...)`). Also notice
how params are always `UPPER_SNAKE_CASE`. Quark requires that param names
contain no lower-case letters.

What if we wanted our Vector3 to support fields of any type? For example,
what if we have a custom representation of fixed precision decimals using
the following struct:
::

    struct Decimal!(Size Q, Size F) {
        Bit[Q] int_part;
        Bit[F] frac_part;
    }

We could of course pass a `WIDTH` value to `Vector3` equal to `Q + F` and
use type-casts whenever necessary, but this would potentially cause errors.
Using type-casts means the type-checker cannot ensure your code is correct.
It also hideners the readability of your code as the types of each variable can
inform a reader what the intent of the code is. Luckily, Quark allows us to
parameterize structs (and any type, for that matter) with Type Parameters.
This allows us to rewrite `Vector3` like this:
::

    struct Vector3!(type T) {
        T x;
        T y;
        T z;
    }

Now we can simply pass a `Decimal` as a param when we use our `Vector3` and
the fields will have the correct type. Better yet, if we ever need to a
`Vector3` of a new type we can simply reuse the struct we've already written!

Using Structs
-------------

Structs can be used anywhere any other type can be used. For example, we can
make a function which returns our `Vector3`:
::

    def newZeroVector(): Vector3!(type Bit[32]) {
        return Vector3 {x: 0, y: 0, z: 0};
    }


In the [Type Inference](type-inference.md) chapter you will learn how to use
type inference to make these kinds of parameterized statements less verbose.
