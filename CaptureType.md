# Introduction #

In Java, `Class` implements `Type`, and there are 4 other subinterfaces of `Type`:
  * `ParameterizedType`: for example `List<String>`
  * `TypeVariable`: for example `E` in `interface List<E>`
  * `WildcardType`: for example `? extends Number`
  * `GenericArrayType`: for example `List<String>[]`

In Gentyref, we introduce a fifth subinterface, do deal with capture conversion: `CaptureType`.

Here we explain why this was needed.

# Reason 1: Wildcard versus capture: _any_ versus _unknown_ #
For example: take the following class:
```
class Foo<T> {
	List<? extends Number> listWildcard;
	List<T> listT;
}
...
	Foo<? extends Number> foo = new Foo<Integer>();
```

  * A wildcard represents _any_ type that matches a given bound. For example, `foo.listWildcard` has type `List<? extends Number>`. That means it can contain any object that implements `List` with as type parameter a subtype of `Number`. So it's ok to write `foo.listWildcard = new ArrayList<Long>();`.

  * A capture represents an _unknown_ type that matches a given bound. In our example, `foo.listT` is a variable of type `List<capture of ? extends Number>`. That means it can only contain objects that implement `List` with as type parameter a type that we don't know, but we do know that it is a subtype of Number.
Although a naive replacement of the type variable `T` by its value `? extends Number` would make you think so, it is **not** `List<? extends Number>`. Because then you could do `foo.listT = new ArrayList<Long>()`. And remember that `foo` is actually an instance of `Foo<Integer>`, so for this instance `listT` should only be allowed to be subtypes of `List<Integer>`.

Take the type of our variable foo:
```
Type fooWildcard = new TypeToken<Foo<? extends Number>>(){}.getType();
```
And the type of its two fields:
```
Type listWildcardFieldType = GenericTypeReflector.getExactFieldType(Foo.class.getDeclaredField("listWildcard"), fooWildcard);
Type listTFieldType = GenericTypeReflector.getExactFieldType(Foo.class.getDeclaredField("listT"), fooWildcard);
```
`listWildcardFieldType` is `List<? extends Number>`. So, we can assign `new ArrayList<Long>()` to it:
`GenericTypeReflector.isSuperType(listWildcardFieldType, new TypeToken<ArrayList<Long>>(){}.getType()) == true`
`listTFieldType` is `List<capture of ? extends Number>`, so we cannot assign that:
`GenericTypeReflector.isSuperType(listTFieldType, new TypeToken<ArrayList<Long>>(){}.getType()) == false`

# Reason 2: bounds of type variables #

For example, take the following class:
```
class Bar<T extends Number> {
	T t;
}
```

The field `t` for a `Bar<?>` is a `T`, so it has `Number` as its supertype. For example this code is valid:
```
Bar<?> bar = new Bar<Integer>();
Number n = bar.t;
```

Without capture conversion the type would be `?`, and that's not a subtype of `Number`.

```
Type barType = new TypeToken<Bar<?>>(){}.getType();
Type captureType = GenericTypeReflector.getExactFieldType(Bar.class.getDeclaredField("t"), barType);
```
==> `GenericTypeReflector.isSuperType(Number.class, captureType)) == true`.

Bounds can also refer to each other. For example with `FooBar<T, S extends T>`, then for a `FooBar<?, Number>` the `T` also extends `Number`. That's why capture conversion can only be applied to a complete parameterized type, and not for type parameters individually.