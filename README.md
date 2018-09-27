# jdk8_juggling
#### Lambda

1. Sort with Lambda, [Doc](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)

   ```java
   Comparator<Person> ageComparator = (t1, t2) -> t1.getAge().compareTo(t2.getAge());
   // parameter type is optional
   list.sort((t1, t2) -> t1.getAge()-t2.getAge());
   // reverse order
   list.sort(ageComparator.reversed());
   // simple writing
   list.stream().map(Person::getAge()).sort(Comparator.naturalOrder());
   ```

2. Group by, [Doc](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)

   ```java
   // group for counting , output: {12:1, 34:3}
   list.stream().map(Person::getAge).collect(Collectors.groupingBy(Function.identity(), Collectors.counting));

   // equals to
   list.stream().collect(Collectors.groupingBy(Person::getAge, Collectors.counting()));
   // group for sum
   list.stream().collect(Collectors.groupingBy(Person::getAge, Collectors.summingInt(Person::getQty)));
   // use 'mapping' to convert Map<Integer, Set<String>> output: {12=[chen], 23=[nehc]}
   list.stream().collect(Collectors.groupingBy(Person::getAge, Collectors.mapping(Person::getName, Collectors.toSet())));
   // sort a Map by value
   Stream<Map.Entry<K,V>> sorted =
       map.entrySet().stream()
          .sorted(Map.Entry.comparingByValue());
   ```

3. Primitive arrays to list

   ```java
   // e.g. int array, Arrays.stream
   IntStream intStream1 = Arrays.stream(intArray);
   // use flatMapToInt, Stream.of(intArray) return Stream<int[]>
   IntStream intStream2 = Stream.of(intArray).flatMapToInt(Arrays:stream);
   ```

4. Reuse a Stream

   ```java
   // use Supplier, get() will return new Stream
   Supplier<Stream<Integer>> streamSupplier = () -> Stream.of(array);
   streamSupplier.get().forEach...
   ```

5. Duplicated Key Map, [Doc](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)

   ```java
   // if the key duplicated, prefer oldKey
   Collectors.toMap(Person::getName, Person::getAge, (oldValue, newValue) -> oldValue);
   ```

6. use Predicate for generic

   ```java
   <K, V> Map<K, V> filterByValue(Map<K, V> map, Predicate<V> predicate) {
       return map.entrySet()
                   .stream()
                   .filter(x -> predicate.test(x.getValue()))
                   .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
   }
   ```

   ​