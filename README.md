# Quark

`*.qk`

Quark is an independent HDL language using a syntax largely inspired by SystemVerilog but with a dramatically improved type system and purely sequential event ordering paradigm. While no behavior across clock cycles is inferred by the language compiler, modern language features like Rust's ownership model help to make hardware programming more flexible and easier to see design intent. The compiler is written in Go and supports a variety of language targets, including simulation code and netlists in a variety of formats.

## examples/

Small examples that showcase different features of Quark and how to use them in scope of a larger design.

## tests/

Focused test cases that can be used for compiler validation, specifically testing part of the language.

## docs/

Text describing how to use Quark and describing the different language features in detail.
