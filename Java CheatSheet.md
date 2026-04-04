# ☕ Java Collections & Stream API — Quick Cheatsheet
### For C++ Developers using Spring Boot

> **Mental model:** Think of Java Collections as STL containers, and Stream API as `std::ranges` + functional pipelines. No raw pointers, no manual memory.

---

## 📦 C++ STL → Java Collections Mapping

| C++ STL | Java Equivalent | Interface | Notes |
|---|---|---|---|
| `std::vector<T>` | `ArrayList<T>` | `List<T>` | Dynamic array, O(1) random access |
| `std::deque<T>` | `ArrayDeque<T>` | `Deque<T>` | Double-ended queue |
| `std::list<T>` | `LinkedList<T>` | `List<T>`, `Deque<T>` | Doubly linked list |
| `std::set<T>` | `TreeSet<T>` | `Set<T>` | Sorted, no duplicates |
| `std::unordered_set<T>` | `HashSet<T>` | `Set<T>` | Unordered, O(1) avg |
| `std::map<K,V>` | `TreeMap<K,V>` | `Map<K,V>` | Sorted by key |
| `std::unordered_map<K,V>` | `HashMap<K,V>` | `Map<K,V>` | O(1) avg, no ordering |
| `std::stack<T>` | `Deque<T>` (as stack) | `Deque<T>` | No dedicated Stack class (use Deque) |
| `std::queue<T>` | `LinkedList<T>` / `ArrayDeque<T>` | `Queue<T>` | Use `offer/poll` |
| `std::priority_queue<T>` | `PriorityQueue<T>` | `Queue<T>` | Min-heap by default |
| `std::pair<A,B>` | `Map.Entry<K,V>` or custom class | — | No direct pair; use records/POJO |

---

## 🏗️ Creating Collections

```java
// ===== LIST =====
List<String> list = new ArrayList<>();           // mutable
List<String> fixed = List.of("a", "b", "c");    // IMMUTABLE (Java 9+)
List<String> copy  = new ArrayList<>(fixed);     // mutable copy of fixed

// ===== SET =====
Set<String> set   = new HashSet<>();
Set<String> fixed = Set.of("x", "y", "z");      // IMMUTABLE, no duplicates

// ===== MAP =====
Map<String, Integer> map = new HashMap<>();
Map<String, Integer> fixed = Map.of("a", 1, "b", 2);  // IMMUTABLE (up to 10 pairs)
Map<String, Integer> big   = Map.ofEntries(            // IMMUTABLE (any size)
    Map.entry("a", 1),
    Map.entry("b", 2)
);

// ===== DEQUE as Stack =====
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);   // push to top
stack.pop();     // remove from top
stack.peek();    // view top

// ===== QUEUE =====
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);  // enqueue (returns false if full, never throws)
queue.poll();    // dequeue (returns null if empty, never throws)
queue.peek();    // front element
```

---

## 🔑 List — Common Operations

```java
List<String> list = new ArrayList<>(List.of("banana", "apple", "cherry"));

list.add("date");               // append
list.add(1, "avocado");        // insert at index
list.get(0);                   // random access → "banana"
list.set(0, "blueberry");      // replace at index
list.remove(0);                // remove by index
list.remove("apple");          // remove by value (first occurrence)
list.contains("cherry");       // → true
list.size();                   // → count
list.isEmpty();                // → false
list.indexOf("cherry");        // first index, -1 if not found

Collections.sort(list);                              // sort in-place (natural order)
Collections.sort(list, Comparator.reverseOrder());   // sort descending
list.sort(Comparator.naturalOrder());                // same, instance method

Collections.shuffle(list);
Collections.reverse(list);
Collections.frequency(list, "apple");               // count occurrences
```

---

## 🗺️ Map — Common Operations

```java
Map<String, Integer> scores = new HashMap<>();

scores.put("Alice", 90);
scores.put("Bob", 85);
scores.get("Alice");                    // → 90, null if key missing
scores.getOrDefault("Eve", 0);         // → 0 (safe get)
scores.containsKey("Bob");             // → true
scores.containsValue(85);             // → true
scores.remove("Bob");                  // remove by key
scores.size();

// Iterate map entries
for (Map.Entry<String, Integer> e : scores.entrySet()) {
    System.out.println(e.getKey() + " = " + e.getValue());
}

// putIfAbsent, computeIfAbsent (very common in Spring)
scores.putIfAbsent("Alice", 100);     // won't override existing
scores.computeIfAbsent("Charlie", k -> 75); // compute only if missing

// merge — add to existing value or put new
scores.merge("Alice", 5, Integer::sum); // Alice's score += 5

// forEach
scores.forEach((k, v) -> System.out.println(k + ": " + v));
```

---

## 🌊 Stream API — The Functional Pipeline

> **Think of it as:** `collection | filter | transform | collect`
> Streams are **lazy** — nothing runs until a terminal operation is called.

### Anatomy of a Stream

```
source → intermediate ops (lazy) → terminal op (triggers execution)

list.stream()
    .filter(x -> x > 0)     // intermediate
    .map(x -> x * 2)        // intermediate
    .collect(toList());     // terminal → executes everything
```

---

## 🔧 Creating Streams

```java
// From collection
list.stream()
set.stream()
map.entrySet().stream()

// From values
Stream.of("a", "b", "c")

// Primitive streams (avoid boxing overhead)
IntStream.range(0, 10)        // 0..9 (exclusive end, like C++ for loop)
IntStream.rangeClosed(1, 10)  // 1..10 (inclusive)
LongStream.of(1L, 2L, 3L)
DoubleStream.of(1.5, 2.5)

// Infinite stream (use with limit!)
Stream.iterate(0, n -> n + 1)
Stream.generate(Math::random)
```

---

## 🔍 Intermediate Operations (Lazy — return Stream)

```java
List<String> names = List.of("Alice", "Bob", "Charlie", "anna", "Dave");

// FILTER — like std::copy_if
names.stream()
     .filter(s -> s.startsWith("A"))
     // → ["Alice", "anna"] (case sensitive)

// MAP — like std::transform (1-to-1 transform)
names.stream()
     .map(String::toUpperCase)
     // → ["ALICE", "BOB", "CHARLIE", "ANNA", "DAVE"]

// FLAT_MAP — 1-to-many (flatten nested collections)
List<List<Integer>> nested = List.of(List.of(1,2), List.of(3,4));
nested.stream()
      .flatMap(Collection::stream)
      // → [1, 2, 3, 4]

// SORTED
names.stream().sorted()                              // natural order
names.stream().sorted(Comparator.reverseOrder())    // reverse
names.stream().sorted(Comparator.comparingInt(String::length)) // by length

// DISTINCT — remove duplicates (uses equals/hashCode)
Stream.of(1, 2, 2, 3, 3).distinct()  // → [1, 2, 3]

// LIMIT & SKIP — like slicing
names.stream().limit(3)      // first 3 elements
names.stream().skip(2)       // drop first 2, take rest

// PEEK — side-effect for debugging (don't use in prod logic)
names.stream()
     .peek(s -> System.out.println("Before: " + s))
     .filter(s -> s.length() > 3)
     .peek(s -> System.out.println("After: " + s))

// MAP to primitive (avoid boxing)
names.stream().mapToInt(String::length)    // → IntStream
names.stream().mapToLong(String::length)
names.stream().mapToDouble(String::length)
```

---

## 🏁 Terminal Operations (Eager — trigger execution)

```java
List<Integer> nums = List.of(3, 1, 4, 1, 5, 9, 2, 6);

// COLLECT — most common terminal op
List<Integer> result = nums.stream()
    .filter(n -> n > 3)
    .collect(Collectors.toList());           // → [4, 5, 9, 6]

// COUNT
long count = nums.stream().filter(n -> n > 3).count();  // → 4

// FIND
Optional<Integer> first = nums.stream().filter(n -> n > 5).findFirst();  // → Optional[9]
Optional<Integer> any   = nums.stream().filter(n -> n > 5).findAny();    // any match

// MATCH (short-circuits like && in C++)
boolean anyMatch  = nums.stream().anyMatch(n -> n > 8);   // → true
boolean allMatch  = nums.stream().allMatch(n -> n > 0);   // → true
boolean noneMatch = nums.stream().noneMatch(n -> n > 10); // → true

// MIN / MAX
Optional<Integer> max = nums.stream().max(Comparator.naturalOrder()); // → Optional[9]
Optional<Integer> min = nums.stream().min(Comparator.naturalOrder()); // → Optional[1]

// REDUCE — fold/accumulate (like std::accumulate)
int sum  = nums.stream().reduce(0, Integer::sum);         // → 31
int prod = nums.stream().reduce(1, (a, b) -> a * b);

// FOR_EACH — consume (no return value, like range-for with side effects)
nums.stream().forEach(System.out::println);

// TO_ARRAY
Integer[] arr = nums.stream().toArray(Integer[]::new);
int[]     arr = nums.stream().mapToInt(i -> i).toArray();  // primitive

// SUM / AVERAGE / STATS (on primitive streams)
int total = nums.stream().mapToInt(i -> i).sum();
OptionalDouble avg = nums.stream().mapToInt(i -> i).average();
IntSummaryStatistics stats = nums.stream().mapToInt(i -> i).summaryStatistics();
stats.getMin(); stats.getMax(); stats.getSum(); stats.getAverage(); stats.getCount();
```

---

## 📥 Collectors — Collecting into Collections/Maps

```java
import static java.util.stream.Collectors.*;

List<String> names = List.of("Alice", "Bob", "Charlie", "Anna", "Dave");

// TO LIST / SET
List<String> l = names.stream().collect(toList());
Set<String>  s = names.stream().collect(toSet());

// JOINING — concatenate strings
String joined  = names.stream().collect(joining(", "));         // "Alice, Bob, ..."
String joined2 = names.stream().collect(joining(", ", "[", "]")); // "[Alice, Bob, ...]"

// TO MAP
Map<String, Integer> nameLengths = names.stream()
    .collect(toMap(
        name -> name,         // key function
        String::length        // value function
    ));

// GROUP BY — most common in Spring! Returns Map<K, List<V>>
Map<Integer, List<String>> byLength = names.stream()
    .collect(groupingBy(String::length));
// → {5=["Alice", "Anna", "Dave"], 3=["Bob"], 7=["Charlie"]}

// GROUP BY with downstream collector
Map<Integer, Long> countByLength = names.stream()
    .collect(groupingBy(String::length, counting()));

Map<Integer, String> joinedByLength = names.stream()
    .collect(groupingBy(String::length, joining(", ")));

// PARTITION BY — splits into true/false groups
Map<Boolean, List<String>> partitioned = names.stream()
    .collect(partitioningBy(s -> s.length() > 3));
// → {true=["Alice", "Charlie", "Anna", "Dave"], false=["Bob"]}

// COUNTING
long count = names.stream().collect(counting()); // same as .count()

// SUMMARIZING
IntSummaryStatistics stats = names.stream()
    .collect(summarizingInt(String::length));
```

---

## 🧩 Optional — Null Safety (No More NPE!)

> Java's answer to `std::optional<T>`. Forces you to handle the "missing" case.

```java
Optional<String> opt = Optional.of("hello");
Optional<String> empty = Optional.empty();
Optional<String> nullable = Optional.ofNullable(someNullableValue);

opt.isPresent()          // → true
opt.isEmpty()            // → false (Java 11+)
opt.get()                // → "hello" (throws if empty, avoid bare get())
opt.orElse("default")    // → value or default
opt.orElseGet(() -> computeDefault())  // lazy default
opt.orElseThrow()        // throw NoSuchElementException if empty
opt.orElseThrow(() -> new RuntimeException("missing!"))

opt.map(String::toUpperCase)          // → Optional["HELLO"]
opt.flatMap(s -> Optional.of(s + "!")) // → Optional["hello!"]
opt.filter(s -> s.length() > 3)      // → Optional["hello"] (passes filter)

// Common pattern in Spring repositories
userRepo.findById(id)
    .map(User::getName)
    .orElseThrow(() -> new NotFoundException("User not found"));
```

---

## ⚡ Parallel Streams

```java
// Just add .parallel() — use only for CPU-intensive, large datasets
list.parallelStream()
    .filter(x -> heavyComputation(x))
    .collect(toList());

// Or convert existing stream
list.stream()
    .parallel()
    .map(...)
    .collect(toList());

// ⚠️  Avoid for: small lists, I/O, ordered operations, shared mutable state
// ✅  Good for: large datasets, pure functions, embarrassingly parallel tasks
```

---

## 🌱 Spring Boot — Common Patterns

### Repository → Service Layer

```java
// Find and transform
List<UserDTO> dtos = userRepo.findAll().stream()
    .filter(User::isActive)
    .map(userMapper::toDTO)
    .collect(toList());

// Group users by role
Map<Role, List<User>> byRole = userRepo.findAll().stream()
    .collect(groupingBy(User::getRole));

// Check existence
boolean hasAdmin = userRepo.findAll().stream()
    .anyMatch(u -> u.getRole() == Role.ADMIN);
```

### Null-safe with Optional

```java
// Spring Data returns Optional<T>
public UserDTO getUser(Long id) {
    return userRepo.findById(id)
        .map(userMapper::toDTO)
        .orElseThrow(() -> new EntityNotFoundException("User " + id));
}
```

### Counting and Stats

```java
// Order stats per customer
Map<Long, Long> ordersPerCustomer = orderRepo.findAll().stream()
    .collect(groupingBy(Order::getCustomerId, counting()));

// Total revenue per product
Map<String, Double> revenueByProduct = orderRepo.findAll().stream()
    .collect(groupingBy(
        o -> o.getProduct().getName(),
        summingDouble(Order::getAmount)
    ));
```

### Flat mapping nested collections

```java
// All unique tags across all posts
Set<String> allTags = postRepo.findAll().stream()
    .flatMap(post -> post.getTags().stream())
    .collect(toSet());
```

---

## 📊 Comparator Quick Reference

```java
// Natural order
Comparator.naturalOrder()
Comparator.reverseOrder()

// By field
Comparator.comparing(User::getName)
Comparator.comparingInt(User::getAge)
Comparator.comparingDouble(Product::getPrice)

// Chaining (sort by age, then by name)
Comparator.comparingInt(User::getAge)
          .thenComparing(User::getName)

// Reverse a custom comparator
Comparator.comparing(User::getName).reversed()

// Null-safe
Comparator.comparing(User::getName, Comparator.nullsFirst(Comparator.naturalOrder()))
Comparator.comparing(User::getName, Comparator.nullsLast(Comparator.naturalOrder()))

// In streams
users.stream().sorted(Comparator.comparingInt(User::getAge).reversed())
users.stream().min(Comparator.comparingDouble(Product::getPrice))
users.stream().max(Comparator.comparingInt(String::length))
```

---

## 🗂️ Choosing the Right Collection

```
Need ordered, indexed access?     → ArrayList<T>
Need fast insert/delete at ends?  → ArrayDeque<T>
Need uniqueness?                  → HashSet<T> (unordered), TreeSet<T> (sorted)
Need key-value lookup?            → HashMap<K,V> (fast), TreeMap<K,V> (sorted keys)
Need thread-safe map?             → ConcurrentHashMap<K,V>
Need sorted key-value?            → TreeMap<K,V>
Need a stack?                     → Deque<T> as ArrayDeque (push/pop/peek)
Need a queue?                     → Queue<T> as ArrayDeque (offer/poll/peek)
Need priority queue?              → PriorityQueue<T> (min-heap, use reverseOrder for max)
```

---

## ⚠️ Common Gotchas for C++ Devs

| C++ Assumption | Java Reality |
|---|---|
| `==` compares values | `==` compares **references**; use `.equals()` for value comparison |
| Containers hold values | Collections hold **object references** (use `int[]` or `IntStream` for primitives) |
| No null by default | Java references can be `null`; use `Optional<T>` to signal absence |
| `std::sort` is in-place | `list.sort()` is in-place; `stream().sorted()` returns a **new stream** |
| Iterator invalidation on modify | Same risk in Java; don't modify a collection while iterating (use `removeIf`) |
| Streams are reusable | Java Streams are **consumed once** — calling terminal op again throws `IllegalStateException` |
| `auto` infers types | Use `var` (Java 10+) for local type inference |

```java
// ❌ Wrong — reference comparison
"hello" == "hello"  // may be true by luck (string pool), never rely on it
new String("a") == new String("a")  // → false

// ✅ Correct — value comparison
"hello".equals(otherString)
Objects.equals(a, b)  // null-safe equals

// ❌ Stream already consumed
Stream<String> s = list.stream().filter(...);
s.count();       // OK
s.collect(...);  // throws IllegalStateException — stream already consumed!

// ✅ Rebuild the stream each time
list.stream().filter(...).count();
list.stream().filter(...).collect(...);
```

---

## 🚀 One-Liner Recipes

```java
// Sum of all values in list
int sum = list.stream().mapToInt(i -> i).sum();

// Max value in list
int max = list.stream().mapToInt(i -> i).max().getAsInt();

// Remove nulls
list.stream().filter(Objects::nonNull).collect(toList());

// Deduplicate preserving order
list.stream().distinct().collect(toList());

// Flat list of all order items across orders
orders.stream().flatMap(o -> o.getItems().stream()).collect(toList());

// Map list of User to list of names
users.stream().map(User::getName).collect(toList());

// Comma-separated string from list
String csv = list.stream().collect(Collectors.joining(", "));

// Check if list has any element matching condition
boolean found = list.stream().anyMatch(s -> s.startsWith("A"));

// Convert list to map by ID
Map<Long, User> userById = users.stream()
    .collect(toMap(User::getId, u -> u));

// Sort strings by length, then alphabetically
list.stream()
    .sorted(Comparator.comparingInt(String::length).thenComparing(Comparator.naturalOrder()))
    .collect(toList());

// First match or throw
User user = users.stream()
    .filter(u -> u.getEmail().equals(email))
    .findFirst()
    .orElseThrow(() -> new RuntimeException("Not found"));

// Partition list into chunks of N (Java doesn't have built-in, use IntStream)
int chunkSize = 3;
List<List<Integer>> chunks = IntStream.range(0, (list.size() + chunkSize - 1) / chunkSize)
    .mapToObj(i -> list.subList(i * chunkSize, Math.min((i + 1) * chunkSize, list.size())))
    .collect(toList());
```

---

*Java 17+ with Spring Boot 3.x assumed. `import java.util.*; import java.util.stream.*; import java.util.stream.Collectors.*;`*
