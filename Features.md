# Introduction #

This library allows you to
  * Check if two types are supertypes of eachother.
  * Get the erasure of a type
  * Calculate the "exact supertype" of a given class. For example, for `class X<T> implements List<List<T>> {...}` and `class Y extends X<String>`, the exact supertype for interface `List` of `Y` is `List<List<String>>`.
  * Get the exact return type or parameter types of methods and types of fields (taking type parameters into account).
  * Apply capture conversion

With full support for wildcards, array types, raw types and captured types.