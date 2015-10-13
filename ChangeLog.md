# 1.1.0 #

  * Fix [Issue #4](https://code.google.com/p/gentyref/issues/detail?id=#4): classes declared in a method
  * Add `getTypeParameter` method. Idea from Satoshi Kimura
  * Add `getUpperBoundClassAndInterfaces` method
  * Make defensive copy of returned arrays in `WildcardType`
  * Avoid extra `Object` upperbound in capture types
  * [Issue 6](https://code.google.com/p/gentyref/issues/detail?id=6): Throw an explaining `IllegalArgumentException` when `getExactReturnType` or `getExactFieldType` is called with a method or field that is not a member of the given type
  * [Issue 7](https://code.google.com/p/gentyref/issues/detail?id=7): Add `getExactParameterTypes` method

# 1.0.0 #

Initial release