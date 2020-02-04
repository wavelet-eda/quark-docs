Types are Constraints
=====================

Quark makes heavy use of type parameters to make function implementations
more generic. When writing type parameters, you should think of parameter types
as constraints on the set of values which a signal can have. Concrete types
are very specific and totally constrain the set of possible values. Traits
act as partial constraints and unqualified parameters act as no constraint.

At compile time, Quark always determines the concrete type and size of each
signal. Thus, it's not correct to think of a generic function with an
unqualified type parameter as taking *any* type. Instead, think of the
function as adding no constraints to the type.

This reflects the way Quark reaches type consensus. Every use of a signal adds
a constraint to the type of the signal. This allows Quark to always find the
minimal correct width of a signal without any help from the hardware enginner.

Example
-------

To best understand how type constraint solving works, we'll walk through a
simple example.

.. code-block::
    :linenos:

    //Assume all Bit-vectors implement this trait
    trait !(type LHS) Addable!(type RHS, type RETURN) {
        def operator+(LHS x, RHS y): RETURN;
    }

    //Swaps items in a tuple
    def swap((T, Q) x): (Q, T) {
        first, second = x;
        return second, first;
    }

    def double(T x): T when T has Addable!(T, T) {
        return x + x;
    }

    def myFunction(T x, Q y): Q when Q has Addable!(Q, Q) {
        var myTuple = x, y;
        var swapedTuple = swap(myTuple);
        var z = double(myTuple[0])

        return z;
    }

    module myModule(): Bit {
        var x = 'd200;
        var y = 'hC8;

        var res = myFunction(x, y);
        return res > 'hC8;
    }

As you can see, this example contains no explicit type names. We've left type
resolution complete to Quark's compiler. Assuming ``myModule`` is the top level
module for this project, let's walk through each step of type resolution.

#. ``line 47 -> typeof(x) > Bit[log2up(200)]`` "Bit[log2up('d200)] must be
   assignable to typeof(x)"
#. ``line 48 -> typeof(y) > Bit[log2up(200)]`` "Bit[log2up('hC8)] must be
   assignable to typeof(y)"
#. ``line 50 -> typeof(y) has Addable!(typeof(y), typeof(y))`` "since
   myFunction can only take a signal which has Addable, typeof(y) must have an
   implementation of Addable to typeof(y) which returns a typeof(y)"
#. ``line 50 -> typeof(res) = typeof(y)`` "since the second argument of
   my function must be the same type as the return signal"
#. ``line 51 -> typeof(operator>(typeof(res), Bit[log2up('hC8)] < Bit``
   "The result of res > 'hC8 must be assignable to Bit"

Now that all the constraints are recorded, we can find a solution. Since the
only direct constraints on x and y are that Bit[log2up('d200)] is can be
assigned to them, we let typeof(x) = typeof(y) = Bit[log2up('d200)]. Because
of constraint 4, we also get typeof(res) = typeof(y) = Bit[log2up('d200)].

Next, we check that this is valid when the other constraints are taken into
account. Since all Bit types have the Addable!(Bit) trait, constraint 3 is
satisfied by this assumption. Furthermore, since all Bit types implement
comparison operators which return a single wide Bit vector, constraint 5 is
also met.

Thus, the type-checker would report success and assign
```typeof(x) = typeof(y) = typeof(res) = Bit[log2up('d200)]```.

If either constraint 4 or 5 were not met, the type checker would fail. This
is because the only direct constraints on the typeof(x) and typeof(y) are
Bit[log2up('d200)]. Without any further direct constraints, the type checker
would have no other types to try.

This is a simplification in some ways. In general, the type checker can
propogate constraints up the hierarchy when necessary.

**Improve Your Understanding**: Walk through this example again but instead
assume that ``y`` is declared as a ``SafeBit[8]``. Will the type check pass?
