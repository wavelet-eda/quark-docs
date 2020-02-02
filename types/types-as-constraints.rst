Types *are* Constraints
====================

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
