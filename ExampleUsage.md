# Introduction #

This page shows some examples of what the gentyref library does.

# Examples #

**Check if a type is a supertype of another type, including type parameters.**

You use `TypeToken` to refer to specific types, and `GenericTypeReflector.isSuperType` to check if a type is a supertype of another.
Given the following interface and classes:
```
	interface Processor<T> {
		void process(T t);
	}
	
	class StringProcessor implements Processor<String> {
		public void process(String s) {
			System.out.println("processing " + s);
		}
	}
	
	class IntegerProcessor implements Processor<Integer> {
		public void process(Integer i) {
			System.out.println("processing " + i);
		}
	}
```

We can check that a certain class is the right kind of Processor:
```
	/*
	 * Returns true if processorClass extends Processor<String>
	 */
	public boolean isStringProcessor(Class<? extends Processor<?>> processorClass) {
		// Use TypeToken to get an instanceof a specific Type
		Type type = new TypeToken<Processor<String>>(){}.getType();
		// Use GenericTypeReflector.isSuperType to check if a type is a supertype of another
		return GenericTypeReflector.isSuperType(type, processorClass);
	}
```
For example
  * `isStringProcessor(StringProcessor.class)` returns true, because `StringProcessor` extends `Processor<String>`.
  * `isStringProcessor(IntegerProcessor.class)` return false, because `IntegerProcessor` doesn't extend `Processor<String>` but `Processor<Integer>`

**Get the exact return type of a method, including resolved generic type variables.**

Given the following classes:
```
	abstract class Collector<T> {
		public List<T> list() {
			...
		}
		public void add(T item) {
			...
		}
	}
	
	class StringCollector extends Collector<String> {
	}
```

The return type of the `Collector.list()` method is `List<T>`. For `StringCollector`, this would be `List<String>`. But, if we simply get the return type from java, we get `List<T>`:
```
	Method listMethod = StringCollector.class.getMethod("list");
	Type returnType = listMethod.getGenericReturnType();
```

Then `returnType` is `List<T>`, not what we want. To get the exact return type, use `GenericTypeReflector.getExactReturnType`:
```
	Type exactReturnType = GenericTypeReflector.getExactReturnType(listMethod, StringLister.class);
```

Now `exactReturnType` is `List<String>`.

**Get the exact types the parameters of a method, including resolved generic type variables.**

Continuing with the sample above, if you ask java, the parameter for the `add` method has type T:
```
	Method addMethod = StringCollector.class.getMethod("add", Object.class);
	
	// returns [T]
	Type[] parameterTypes = addMethod.getGenericParameterTypes();
```

You can get the exact parameter type using `GenericTypeReflector.getExactParameterTypes`:
```
	// returns [String]
	Type[] exactParameterTypes = GenericTypeReflector.getExactParameterTypes(addMethod, StringCollector.class);
```