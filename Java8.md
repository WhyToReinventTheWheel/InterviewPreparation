# Major New Features in Java8 
- Java 8 lambda expressions and Interfaces
- Stream API and Collectors
- Date and Time API
- Strings, I/O and otherbits and pieces
- Nashorn, a new Javascriptenginefor the JVM

# Lambda expressions and Interfaces
### The lambda syntax
`anonymousclass`
```
FileFilterfileFilter = newFileFilter() {
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

### Functionalinterfaces
What is the type of a lambda expression?
Answer: a functional interface
- A functional interface is an interface with only one abstractmethod
```
public interface Runnable{
  run();
};

public interface Comparator<T> {
  intcompareTo(T t1, T t2);
};

```
- How to make funtion interface
```
@FunctionalInterface
public interface MyFunctionalInterface{
  someMethod();
};
```

- 4 categories
- Supplier
```
@FunctionalInterface
public interface Supplier<T> {
  T get();
}
```
- Consumer
```
@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);
}
```
- Predicate
```
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
}
```
- Function
```
@FunctionalInterface
 public interface Function<T, R> {
    R apply(T t);
}
```

## Methodreferences
- Function<String, String> f = s -> s.toLowerCase() `===` Function<String, String> f = String::toLowerCase;
- Consumer<String> c = s -> System.out.println(s)  `===` Consumer<String>c = System.out::println;
- Comparator<Integer> c = (i1, i2) -> Integer.compare(i1, i2)  `===` Comparator<Integer> c = Integer::compare;
