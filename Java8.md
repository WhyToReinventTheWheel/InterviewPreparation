# Major New Features in Java8 
- Java 8 lambda expressions and Interfaces
- Stream API and Collectors
- Date and Time API
- Strings, I/O and otherbits and pieces
- Nashorn, a new Javascriptenginefor the JVM

# Lambda expressions and Interfaces
## The lambda syntax
`anonymousclass`
```
FileFilterfileFilter= newFileFilter() {
@Override
  public booleanaccept(File file){
  returnfile.getName().endsWith(".java");
  }
};
```
```
FileFilterfilter= (File file) -> file.getName().endsWith(".java");

Comparator<String> c =
  (String s1, String s2)->
    Integer.compare(s1.length(), s2.length());

Runnabler = () -> {
  for(inti = 0; i < 5; i++) {
    System.out.println("Hello world!");
  }
};
```

## Functionalinterfaces
Whatisthe type of a lambda expression?
Answer: a functionalinterface
- A functionalinterface isan interface withonlyone abstractmethod
```
public interfaceRunnable{
  run();
};

public interfaceComparator<T> {
  intcompareTo(T t1, T t2);
};

```
- How to make funtion interface
```
@FunctionalInterface
public interfaceMyFunctionalInterface{
  someMethod();
  /**
  * Somemore documentation
  */
  equals(Object o);
};
```

- 4 categories
- Supplier
```
@FunctionalInterface
publicinterfaceSupplier<T> {
  T get();
}
```
- Consumer
```
@FunctionalInterface
publicinterfaceConsumer<T> {
  void accept(T t);
}
```
- Predicate
```
@FunctionalInterface
publicinterfacePredicate<T> {
  boolean test(T t);
}
```

## Methodreferences
