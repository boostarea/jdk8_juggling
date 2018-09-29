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

3. Primitive arrays to list, IntStream, LongStream  [Doc](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html)

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

7. use flatMap to Stream, [Doc](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#flatMap-java.util.function.Function-)

   ```java
   // convert {{1,2}, {3,4}} -> flatMap -> {1,2,3,4}
   Stream<String[]> temp = Arrays.stream(doubleArray);
   Stream<String> stringStream = temp.flatMap(x -> Arrays.stream(x));
   ```

8. Optional.map or filter, [Doc](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)

   ```java
   Optional.ofNullable("a").map(String::toUpperCase).get();  //"A"
   // deep map: flatMap
   Optional.ofNullable("a").map(m -> m.map(String::toUpperCase)).get();  //"A"
   Optional.ofNullable("a").filter(g -> g.equals("b")); //Optional.empty
   ```

9. Collectors.joining 

   ```java
   //{a,b}
           String result = list.stream().map(x -> x.getName())
   			.collect(Collectors.joining(", ", "{", "}"));
   ```

10. convert a primitive array to list

   ```java
   // resolve boxing issue, Arrays.asList(number) reutrn List<int[]>
   int[] number = {1,2,3};
   Arrays.stream(number).boxed().collect(Collectors.toList());
   ```

   ​

   #### Java8 Date API

   1. Why do we need a new date and time library](https://www.oracle.com/technetwork/articles/java/jf14-date-time-2125367.html)

      ###### In old days, we use the following classic Date and Calendar APIs to represent and manipulate date.

      - `java.util.Date` – date and time, print with default time-zone.
      - `java.util.Calendar` – date and time, more methods to manipulate date.
      - `java.text.SimpleDateFormat` – formatting (date -> text), parsing (text -> date) for date and calendar.

      ###### In Java 8, a new series of date and time APIs ([JSR310](https://jcp.org/en/jsr/detail?id=310) and inspired by Joda-time) are created in the new `java.time` package.

      - `java.time.LocalDate` – date without time, no time-zone.
      - `java.time.LocalTime` – time without date, no time-zone.
      - `java.time.LocalDateTime` – date and time, no time-zone.
      - `java.time.ZonedDateTime` – date and time, with time-zone.
      - `java.time.DateTimeFormatter` – formatting (date -> text), parsing (text -> date) for java.time
      - `java.time.Instant` – date and time for machine, seconds passed since the Unix epoch time (midnight of January 1, 1970 UTC)
      - `java.time.Duration` – Measures time in seconds and nanoseconds.
      - `java.time.Period` – Measures time in years, months and days.
      - `java.time.TemporalAdjuster` – Adjust date.

   2. ​