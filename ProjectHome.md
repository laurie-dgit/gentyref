A small library for reflection related to generic types in Java.

Java provides some reflection for generic types. There is the `java.lang.reflect.Type`, implemented by `Class`, and with subinterfaces `ParameterizedType`, `GenericArrayType`,...
These can represent complex types like `List<? extends String>[]`.

But Java does not provide anything beyond the introspection needed to get these types. There is no way to check if two types are supertypes. Or, for example, given a type that implements `List`, get its type parameter for that interface.
That's where this library comes in. It allows you to do arithmetics on Types.

  * [Example Usage](ExampleUsage.md)
  * [API documentation](http://gentyref.googlecode.com/svn/tags/1.1.0/docs/apidocs/index.html)
  * [Download](Download.md)
  * [ChangeLog](ChangeLog.md)