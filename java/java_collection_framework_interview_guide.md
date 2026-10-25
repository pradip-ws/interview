# Java Collection Framework Interview Questions & Speakable Answers

## Senior Java Developer / Senior Software Engineer --- Java 8, 11, 17 and 21

**How to use this guide:** Practise each answer aloud. Aim for 45--90
seconds for core concepts and explain the implementation, complexity,
trade-offs, and use case. Complexity figures are typical and depend on
implementation details and assumptions.

> **Version note:** Unless stated otherwise, internal implementation
> notes refer to mainstream OpenJDK Java 8+ implementations. Avoid
> presenting implementation details as universal API guarantees.

## Contents

1.  Collection Framework fundamentals
2.  List implementations
3.  Set implementations
4.  Map implementations and HashMap internals
5.  Queue, Deque and PriorityQueue
6.  Iterators and traversal
7.  `equals()`, `hashCode()`, Comparable and Comparator
8.  Generics and collection APIs
9.  Java 8+ Streams and collection methods
10. Thread safety and concurrent collections
11. Complexity and performance
12. Coding and scenario-based questions
13. Rapid revision checklist

------------------------------------------------------------------------

# 1. Collection Framework Fundamentals

### Q1. What is the Java Collection Framework?

**Speakable answer:** The Java Collection Framework is a set of
interfaces, implementations, and algorithms for storing and manipulating
groups of objects. Interfaces such as `List`, `Set`, `Queue`, and `Map`
define behaviour, while classes such as `ArrayList`, `HashSet`, and
`HashMap` provide implementations. It reduces the need to build data
structures from scratch and gives us consistent APIs, generics,
iterators, sorting, and utility algorithms.

### Q2. What is the difference between `Collection` and `Collections`?

**Speakable answer:** `Collection` is the root interface for most
collection types, including `List`, `Set`, and `Queue`. `Collections` is
a utility class with static methods such as `sort`, `reverse`,
`shuffle`, `frequency`, and wrappers such as `synchronizedList` and
`unmodifiableList`. `Map` is part of the framework but does not extend
`Collection`.

### Q3. Explain the main collection hierarchy.

**Speakable answer:** `Iterable` allows an object to be used in an
enhanced `for` loop. `Collection` extends `Iterable` and is the parent
of `List`, `Set`, and `Queue`. `Deque` extends `Queue`. `Map` is a
separate hierarchy because it stores key-value mappings rather than
individual elements. Common implementations include `ArrayList`,
`LinkedList`, `HashSet`, `TreeSet`, `PriorityQueue`, `HashMap`, and
`TreeMap`.

### Q4. What is the difference between `List`, `Set`, `Queue`, and `Map`?

**Speakable answer:** A `List` preserves positional order and permits
duplicates. A `Set` represents unique elements. A `Queue` is designed to
process elements according to a policy such as FIFO or priority. A `Map`
associates unique keys with values. I choose based on the required
behaviour: ordered duplicates, uniqueness, processing order, or
key-based lookup.

### Q5. Why does `Map` not extend `Collection`?

**Speakable answer:** A `Collection` represents individual elements,
while a `Map` represents key-value pairs with unique keys. Treating a
map as a collection of individual elements would be ambiguous: should
iteration return keys, values, or entries? Instead, `Map` exposes
separate `keySet()`, `values()`, and `entrySet()` views.

### Q6. What is the difference between an array and a collection?

**Speakable answer:** Arrays have a fixed length and can store
primitives or references. Collections are object-based containers with
APIs for resizing, searching, iteration, and algorithms. Arrays can be
more compact and efficient for fixed-size data, while collections are
more flexible. Generic collections cannot directly store primitive
values, so primitives use wrapper types such as `Integer`.

### Q7. What are the advantages of the Collection Framework?

**Speakable answer:** It provides reusable data structures, consistent
interfaces, type safety through generics, standard iteration, sorting
and searching algorithms, and interoperability between implementations.
It also lets us program to an interface and change the implementation
when requirements change, for example from `ArrayList` to a concurrent
collection.

### Q8. What is the difference between mutable, unmodifiable, and immutable collections?

**Speakable answer:** A mutable collection supports changes through its
API. An unmodifiable collection rejects mutations through that
reference, but if it is a view over another collection, changes to the
backing collection may still appear. A truly immutable collection does
not expose a way to change its collection state. Also, an unmodifiable
collection does not make mutable objects stored inside it immutable.

### Q9. What is a shallow copy versus a deep copy?

**Speakable answer:** A shallow copy creates a new collection structure
but copies the same element references. Changing the collection
structure does not affect the original, but mutating a shared element
can be visible through both collections. A deep copy also copies the
contained objects, to the required depth. Java collections do not
automatically deep-copy their elements.

### Q10. What are fail-fast and weakly consistent iterators?

**Speakable answer:** A fail-fast iterator may throw
`ConcurrentModificationException` if it detects structural modification
outside the iterator during iteration. This is a best-effort
bug-detection mechanism, not a thread-safety guarantee. Weakly
consistent iterators, used by some concurrent collections, can tolerate
concurrent updates and may reflect some of them without throwing that
exception.

------------------------------------------------------------------------

# 2. List Implementations

### Q11. What is the difference between `ArrayList` and `LinkedList`?

**Speakable answer:** `ArrayList` uses a resizable array, so indexed
access is O(1) and appending is amortized O(1), while inserting or
deleting in the middle generally requires shifting elements and is O(n).
`LinkedList` is a doubly linked list; accessing by index is O(n), but
adding or removing at an end is O(1). In most everyday cases, I prefer
`ArrayList` because of lower memory overhead and better cache locality.

### Q12. How does `ArrayList` work internally?

**Speakable answer:** `ArrayList` stores references in an internal
array. When the array is full, it allocates a larger array and copies
the existing references. This makes most appends O(1) amortized,
although a resizing append can take O(n). It supports fast indexed
access, preserves insertion order, and allows duplicates and nulls.

### Q13. What is the initial capacity of an `ArrayList`?

**Speakable answer:** The no-argument constructor in common OpenJDK
implementations uses a shared empty array initially and allocates
storage on the first addition. The API does not require a particular
internal capacity strategy. If I know the approximate number of
elements, I can provide an initial capacity to reduce resizing and
copying.

### Q14. How does `ArrayList` grow?

**Speakable answer:** In common OpenJDK implementations, when the
backing array lacks capacity, `ArrayList` allocates a larger array and
copies the existing elements. The commonly used growth policy is roughly
1.5 times the old capacity, subject to minimum capacity and maximum
array-size rules. The exact growth policy is an implementation detail,
not an API guarantee.

### Q15. What is the time complexity of `ArrayList` operations?

**Speakable answer:** `get(index)` and `set(index)` are O(1). Appending
is amortized O(1), though resizing can make an individual append O(n).
Searching by value is O(n). Inserting or removing at an arbitrary index
is O(n) because elements may need to shift.

### Q16. How does `LinkedList` work internally?

**Speakable answer:** `LinkedList` stores nodes, and each node
references its previous and next nodes. It supports efficient additions
and removals at the ends. However, finding a node by index requires
traversal, so indexed access is O(n). Each node also uses extra memory
for links, which is one reason it is not automatically faster for
frequent insertions.

### Q17. When would you choose `LinkedList` over `ArrayList`?

**Speakable answer:** I would consider `LinkedList` when the workload
needs frequent additions or removals at the ends and the list-specific
behaviour is useful. If I need random access or general-purpose storage,
`ArrayList` is usually a better default. For stack and queue operations,
I would normally evaluate `ArrayDeque` as well.

### Q18. What is the difference between `Vector` and `ArrayList`?

**Speakable answer:** Both are resizable arrays. `Vector` synchronizes
individual methods and is a legacy class, while `ArrayList` is not
synchronized by default. Synchronizing individual calls does not
automatically make a multi-step operation atomic. For concurrent
workloads, I choose an appropriate concurrent collection or explicit
synchronization rather than selecting `Vector` by default.

### Q19. What is the difference between `Stack` and `ArrayDeque`?

**Speakable answer:** `Stack` is a legacy class that extends `Vector`,
whereas `ArrayDeque` is a resizable-array implementation of `Deque`.
`ArrayDeque` generally offers a better modern choice for single-threaded
stack operations using `push`, `pop`, and `peek`. It does not allow null
elements and is not thread-safe by default.

### Q20. How do you remove duplicates from a list while preserving insertion order?

**Speakable answer:** I can pass the list into a `LinkedHashSet` and
then create a new list, because `LinkedHashSet` enforces uniqueness
while retaining insertion order. For example,
`List<T> unique = new ArrayList<>(new LinkedHashSet<>(items));`. With
streams, `items.stream().distinct().toList()` is another option on newer
Java versions; it returns an unmodifiable list in current Java APIs.

### Q21. How do you sort a list of custom objects?

**Speakable answer:** I use `list.sort(comparator)` or
`Collections.sort(list, comparator)`. The comparator defines the
ordering, for example by employee salary or name. For multiple fields, I
can chain comparators with `Comparator.comparing()` and
`thenComparing()`. I prefer an explicit comparator when the sorting
requirement varies by use case.

### Q22. How do you convert between an array and a list?

**Speakable answer:** `Arrays.asList(array)` creates a fixed-size list
backed by the array. To get a resizable list, I use
`new ArrayList<>(Arrays.asList(array))`. To convert a list to an array,
I usually use `list.toArray(new String[0])` or an appropriately typed
array. For primitive arrays, `Arrays.asList(intArray)` treats the whole
`int[]` as one element, not a list of integers.

### Q23. What is `subList()` and what are its pitfalls?

**Speakable answer:** `subList(from, to)` returns a view of a range in
the original list, with the end index excluded. Changes through the view
affect the backing list and vice versa. Structural changes to the
backing list outside the view can make subsequent behaviour invalid or
cause `ConcurrentModificationException`. If I need an independent list,
I create `new ArrayList<>(list.subList(from, to))`.

### Q24. What is `RandomAccess`?

**Speakable answer:** `RandomAccess` is a marker interface indicating
that indexed access is generally fast. `ArrayList` implements it, while
`LinkedList` does not. Algorithms can use this marker to choose an
appropriate traversal strategy, such as indexed access versus
iterator-based traversal.

------------------------------------------------------------------------

# 3. Set Implementations

### Q25. What is a `Set`?

**Speakable answer:** A `Set` is a collection that does not contain
duplicate elements according to its equality or ordering rules.
`HashSet` is generally used for fast membership checks without a
guaranteed iteration order, `LinkedHashSet` preserves insertion order,
and `TreeSet` maintains sorted order.

### Q26. Compare `HashSet`, `LinkedHashSet`, and `TreeSet`.

**Speakable answer:** `HashSet` is hash-based and typically provides
average O(1) add, remove, and contains. `LinkedHashSet` adds predictable
insertion-order iteration with some extra memory overhead. `TreeSet` is
tree-based and maintains sorted order, with O(log n) basic operations. I
select based on whether I need speed, insertion order, or sorted order.

### Q27. How does `HashSet` work internally?

**Speakable answer:** In common Java implementations, `HashSet` is
backed by a `HashMap`. Each set element is stored as a map key with a
shared dummy value. The hash determines the bucket, and equality checks
determine whether the element already exists. Adding an equal element
does not create a second entry.

### Q28. How does `HashSet` prevent duplicates?

**Speakable answer:** It uses the element's hash code to find a
candidate bucket and then uses equality checks to determine whether an
equal element is already present. Correct implementations of `equals()`
and `hashCode()` are therefore essential. If those methods violate their
contracts, duplicate detection and lookup can behave unexpectedly.

### Q29. Can `HashSet` contain null? Can `TreeSet`?

**Speakable answer:** `HashSet` permits one null element. A
natural-order `TreeSet` generally does not accept null because it cannot
compare null with ordinary values. A custom comparator can define a null
ordering, but the comparator must consistently support the values
supplied.

### Q30. How does `TreeSet` maintain sorted order?

**Speakable answer:** `TreeSet` is backed by a `TreeMap`, which uses a
balanced tree in common Java implementations. Elements are ordered by
their natural ordering or a supplied comparator. Basic operations such
as add, remove, and contains are O(log n). The comparison result also
determines whether elements are treated as duplicates.

### Q31. How do you create a set of custom objects correctly?

**Speakable answer:** For `HashSet`, I implement `equals()` and
`hashCode()` consistently using the fields that define logical identity.
For `TreeSet`, I provide a valid `Comparable` implementation or a
`Comparator`. I also avoid changing fields that participate in equality,
hashing, or ordering while the object is stored in the set.

### Q32. What happens if a mutable object is stored in a `HashSet`?

**Speakable answer:** If a field used by `hashCode()` or `equals()`
changes after insertion, the set may no longer find the object in the
expected bucket. A later add can even allow a logically duplicate object
to be inserted. I prefer immutable keys and set elements, or at least
stable equality fields.

### Q33. When should you use `LinkedHashSet` instead of `HashSet`?

**Speakable answer:** I use `LinkedHashSet` when I need uniqueness and
predictable insertion-order iteration, such as removing duplicates while
preserving the original sequence. If order does not matter and I want
the simpler hash-based structure, `HashSet` is usually sufficient.

------------------------------------------------------------------------

# 4. Map Implementations and HashMap Internals

### Q34. Compare `HashMap`, `Hashtable`, and `ConcurrentHashMap`.

**Speakable answer:** `HashMap` is not thread-safe and allows one null
key and null values. `Hashtable` is a legacy synchronized map that does
not allow null keys or values. `ConcurrentHashMap` is designed for
concurrent access, does not allow null keys or values, and generally
scales better than synchronizing every operation on one map-wide lock. I
choose `HashMap` for single-threaded or externally synchronized use and
`ConcurrentHashMap` for shared concurrent access.

### Q35. How does `HashMap` work internally?

**Speakable answer:** A `HashMap` stores entries in a bucket array. It
computes a hash from the key's `hashCode()`, derives a bucket index, and
stores or finds the entry there. If multiple keys map to one bucket, it
handles the collision by comparing keys and storing multiple entries in
that bucket, using a linked structure or a tree in applicable cases. On
lookup, it uses the hash and then equality checks to find the key.

### Q36. What happens when you call `put()` on a `HashMap`?

**Speakable answer:** The map computes the key's hash, finds the bucket,
and checks the entries there. If an existing key matches by hash and
equality, its value is replaced. Otherwise, a new entry is added. If
adding entries crosses the resize threshold, the map may grow its table.
A null key is supported by `HashMap`, with special handling.

### Q37. How is the bucket index calculated?

**Speakable answer:** In common OpenJDK implementations, the hash is
spread using a bit operation and the bucket index is calculated
approximately as `(n - 1) & hash`, where `n` is the table capacity. This
works efficiently when the capacity is a power of two. The exact
internal hashing implementation is not a public API guarantee.

### Q38. What is a hash collision?

**Speakable answer:** A collision occurs when different keys map to the
same bucket. They may have the same hash code or different hashes that
lead to the same bucket index. A collision is normal; the map resolves
it by checking candidate entries and comparing keys with `equals()`.

### Q39. How are collisions handled in Java 8 and later?

**Speakable answer:** In common OpenJDK implementations, a bucket
initially uses a linked list. When the bucket becomes sufficiently large
and the table is large enough, it may be converted to a red-black tree.
Tree bins improve lookup behaviour for heavily collided buckets, though
the exact thresholds and conditions are implementation details.

### Q40. When does a bucket convert into a red-black tree?

**Speakable answer:** In common OpenJDK implementations, the
treeification threshold is 8 entries, but conversion also depends on
table capacity. If the table is below a minimum capacity of 64, resizing
is generally preferred before treeification. The constants are
implementation details, so I explain the principle rather than treating
them as API guarantees.

### Q41. What are `TREEIFY_THRESHOLD` and `UNTREEIFY_THRESHOLD`?

**Speakable answer:** In common OpenJDK implementations,
`TREEIFY_THRESHOLD` is 8 and `UNTREEIFY_THRESHOLD` is 6. They guide when
a bucket may be converted to a tree or back to a linked representation.
The precise conditions include other implementation details, so these
numbers should be understood as OpenJDK implementation constants, not
promises made by the `Map` API.

### Q42. What are capacity, size, and load factor?

**Speakable answer:** Capacity is the number of buckets in the hash
table, size is the number of mappings currently stored, and load factor
determines how full the table may become before resizing is triggered.
In a typical `HashMap`, the default load factor is 0.75. The resize
threshold is roughly capacity multiplied by load factor.

### Q43. What are the default initial capacity and load factor of `HashMap`?

**Speakable answer:** The commonly documented defaults are an initial
capacity of 16 and a load factor of 0.75. In current OpenJDK
implementations, the backing table allocation can be deferred until the
first insertion. When discussing this in an interview, I distinguish the
logical default capacity from the moment memory is actually allocated.

### Q44. What happens when the resize threshold is exceeded?

**Speakable answer:** The map expands its bucket table, commonly
doubling the capacity in OpenJDK implementations, and redistributes
entries into the new table. This costs work proportional to the number
of entries being processed. Resizing is why an individual insertion can
be expensive even though `put()` is typically O(1) on average over many
operations.

### Q45. What is the average and worst-case complexity of `HashMap`?

**Speakable answer:** `get()` and `put()` are typically O(1) on average
with a good hash distribution. Heavy collisions can make performance
worse. Java 8+ tree bins improve many collision-heavy cases, often to
O(log n) within a tree bin, but exact worst-case behaviour depends on
key comparison and implementation details. I would not describe all
`HashMap` operations as unconditionally O(1).

### Q46. What is the role of `equals()` and `hashCode()` in `HashMap`?

**Speakable answer:** `hashCode()` helps determine the bucket, while
`equals()` identifies whether a candidate key is logically the same key.
Equal objects must have equal hash codes. Unequal objects can share a
hash code, so the map must still distinguish them using equality.
Overriding one without the other correctly can break lookups.

### Q47. What happens if two keys have the same hash code?

**Speakable answer:** They may land in the same bucket. `HashMap` checks
the candidate entries and uses equality to distinguish them. If the keys
are not equal, they can coexist as separate keys. If they are equal
according to the map's key-matching rules, a new `put()` updates the
existing mapping instead of adding another one.

### Q48. What if `equals()` and `hashCode()` are not overridden correctly?

**Speakable answer:** Logically equivalent objects may be stored as
separate keys or a lookup using an equivalent object may fail. In a
`HashSet`, duplicate prevention can also fail. I follow the contract: if
two objects are equal, they must have equal hash codes, and equality
must be reflexive, symmetric, transitive, and consistent.

### Q49. What happens if a key's hash code changes after insertion?

**Speakable answer:** The map does not automatically move the existing
entry to the bucket that corresponds to the new hash. A lookup may
search the new bucket and fail to find the entry. This is why mutable
objects are risky map keys when their equality or hash fields can
change. I prefer immutable keys.

### Q50. Can `HashMap` contain null keys and values?

**Speakable answer:** Yes. `HashMap` allows one null key and multiple
null values. This can be useful, but a null result from `get()` is
ambiguous because it can mean either that the key is absent or that the
key maps to null. I use `containsKey()` if I need to distinguish those
cases. `ConcurrentHashMap` does not allow null keys or values.

### Q51. Why is `HashMap` not thread-safe?

**Speakable answer:** Its operations do not coordinate concurrent
access. Multiple threads modifying it can observe inconsistent
application-level state, lose updates, or interfere with compound
operations. For shared concurrent access, I choose `ConcurrentHashMap`,
a synchronized wrapper, or another design appropriate to the required
atomicity.

### Q52. Compare `HashMap` and `LinkedHashMap`.

**Speakable answer:** `HashMap` does not guarantee iteration order.
`LinkedHashMap` maintains a linked order, usually insertion order, and
can also be configured for access order. That order costs extra memory
and some overhead. Access-order mode is useful when building structures
such as an LRU cache.

### Q53. Compare `HashMap` and `TreeMap`.

**Speakable answer:** `HashMap` provides average O(1) lookup and does
not guarantee key order. `TreeMap` keeps keys sorted according to
natural ordering or a comparator and typically provides O(log n) lookup,
insertion, and removal. I use `TreeMap` when sorted traversal, range
views, or nearest-key operations are needed.

### Q54. How does `TreeMap` work internally?

**Speakable answer:** In common Java implementations, `TreeMap` uses a
red-black tree to maintain keys in sorted order. Its core operations are
O(log n). It supports range-oriented methods such as `subMap`,
`headMap`, `tailMap`, `floorKey`, and `ceilingKey`, which are useful for
ordered queries.

### Q55. What is `IdentityHashMap`?

**Speakable answer:** `IdentityHashMap` compares keys using reference
identity, effectively `==`, instead of logical equality via `equals()`.
It is useful for specialised cases such as object-graph transformations
where two distinct objects that compare equal must remain distinct. It
is not a general replacement for `HashMap`.

### Q56. What is `WeakHashMap`?

**Speakable answer:** `WeakHashMap` stores weak references to keys,
allowing a key to be garbage-collected when no strong references to it
remain elsewhere. Its entry may then disappear automatically. It can
help with certain metadata maps, but it is not a deterministic cache
eviction policy because garbage collection timing is not predictable.

### Q57. What is `EnumMap`?

**Speakable answer:** `EnumMap` is a specialised map whose keys are
values from a single enum type. It is compact and efficient because it
can use the enum's ordinal-based structure internally. It maintains enum
declaration order and is often a good choice when all keys belong to one
enum.

### Q58. Compare `put()`, `putIfAbsent()`, and `computeIfAbsent()`.

**Speakable answer:** `put()` stores a value and replaces an existing
mapping. `putIfAbsent()` inserts only when the key is absent or mapped
to null, according to the map's contract. `computeIfAbsent()` computes a
value using a function only when the key is absent or mapped to null,
then stores a non-null result. With concurrent maps, these atomic map
methods can help avoid separate check-then-act races.

### Q59. What is the difference between `get()` and `getOrDefault()`?

**Speakable answer:** `get()` returns the mapped value or null if no
mapping exists, which can be ambiguous if null values are permitted.
`getOrDefault(key, defaultValue)` returns the default when there is no
mapping, but returns null if the key is explicitly mapped to null. It
does not insert the default value into the map.

### Q60. How do you sort a map by keys or values?

**Speakable answer:** For key ordering, I can copy entries into a
`TreeMap` if its ordering matches the requirement. For value ordering, I
stream the entries and sort them with a comparator on
`Map.Entry::getValue`, then collect or display the results in the
required order. A normal `HashMap` does not promise to retain a sorted
iteration order.

### Q61. How do you count element frequencies using a map?

**Speakable answer:** I use a map from each element to its count. For
each element, I can call `map.merge(element, 1, Integer::sum)`. This
inserts 1 when the key is absent and increments the current count
otherwise. It is concise and usually takes O(n) average time for n
elements with a hash-based map.

### Q62. How do you merge two maps?

**Speakable answer:** I create a destination map and define what should
happen when both maps contain the same key. `putAll()` lets later values
overwrite earlier ones. `merge()` lets me combine colliding values with
a function, such as summing counts. The correct approach depends on the
business rule for duplicate keys.

------------------------------------------------------------------------

# 5. Queue, Deque and PriorityQueue

### Q63. What is the difference between `Queue` and `Deque`?

**Speakable answer:** A `Queue` typically supports insertion at one end
and removal from the other, often FIFO. A `Deque`, or double-ended
queue, supports insertion and removal at both ends. `Deque` can
implement both queue and stack behaviour. `ArrayDeque` is a common
choice for non-concurrent stack or queue operations.

### Q64. What is the difference between `Queue` and `PriorityQueue`?

**Speakable answer:** A standard FIFO queue processes elements in
arrival order. A `PriorityQueue` processes the least or highest-priority
element according to its comparator or natural ordering; Java's default
head is the least element. Its iterator does not guarantee sorted
traversal. `offer()` and `poll()` are typically O(log n), while `peek()`
is O(1).

### Q65. How does `PriorityQueue` work internally?

**Speakable answer:** In common Java implementations, `PriorityQueue`
uses a binary heap stored in an array. The head is the least element
under natural ordering or the supplied comparator. Insertion restores
heap order by sifting an element upward, and removal of the head
restores order by sifting downward. It is useful for scheduling, top-K
problems, and graph algorithms.

### Q66. What is the difference between `add()` and `offer()`?

**Speakable answer:** Both try to insert an element into a queue.
`add()` may throw an exception if insertion is not possible under the
queue's capacity rules, while `offer()` returns false when it cannot
accept the element. For bounded queues, `offer()` is often useful when I
want to handle capacity pressure without an exception.

### Q67. What is the difference between `remove()` and `poll()`?

**Speakable answer:** Both remove and return the queue head. `remove()`
throws `NoSuchElementException` if the queue is empty, while `poll()`
returns null. I use `poll()` when an empty queue is an expected
condition that the code should handle normally.

### Q68. What is the difference between `element()` and `peek()`?

**Speakable answer:** Both inspect the queue head without removing it.
`element()` throws `NoSuchElementException` if the queue is empty, while
`peek()` returns null. The choice depends on whether an empty queue
should be represented as a normal result or an exceptional condition.

### Q69. Why is `ArrayDeque` often preferred over `Stack`?

**Speakable answer:** `ArrayDeque` implements `Deque` and supports stack
operations such as `push`, `pop`, and `peek` without the legacy
inheritance and synchronization overhead of `Stack`. It is usually a
better default for a local, non-thread-safe stack. For concurrent
access, I choose a suitable concurrent design instead.

### Q70. Does `PriorityQueue` iterate in sorted order?

**Speakable answer:** No. Its iterator traverses the heap's internal
representation, which is not a fully sorted sequence. Only the head is
guaranteed to be the least element under the queue's ordering. If I need
sorted output, I can repeatedly poll from a copy or sort a separate
collection.

### Q71. How do you implement a stack using `Deque`?

**Speakable answer:** I declare `Deque<T> stack = new ArrayDeque<>()`,
then use `push()` to add to the top, `pop()` to remove and return the
top, and `peek()` to inspect it. These operations give the familiar
last-in, first-out behaviour. `ArrayDeque` does not permit null
elements.

### Q72. How do you implement a queue using two stacks?

**Speakable answer:** I maintain an input stack and an output stack.
Enqueue pushes onto the input stack. On dequeue, if the output stack is
empty, I move all input elements to the output stack, reversing their
order, and then pop from the output stack. Each element moves at most
once between stacks, so enqueue is O(1) and dequeue is amortized O(1).

### Q73. When would you use a blocking queue?

**Speakable answer:** I use a `BlockingQueue` in producer-consumer
workflows when producers and consumers need coordination. Operations
such as `put()` can wait for capacity, and `take()` can wait for an
element. Bounded queues also provide backpressure, preventing producers
from creating unbounded amounts of pending work.

------------------------------------------------------------------------

# 6. Iterators and Collection Traversal

### Q74. How does an `Iterator` work?

**Speakable answer:** An `Iterator` provides a standard way to traverse
a collection using `hasNext()` and `next()`, and optionally remove the
last returned element using `remove()` if supported. It hides
implementation details, so the same traversal pattern can work with
different collection types.

### Q75. What is `ConcurrentModificationException`?

**Speakable answer:** It commonly occurs when a fail-fast iterator
detects structural modification of a collection outside the iterator's
permitted mutation methods. For example, removing directly from an
`ArrayList` inside an enhanced `for` loop may trigger it. The exception
is a best-effort diagnostic, not a concurrency guarantee.

### Q76. What is `modCount` in `ArrayList`?

**Speakable answer:** In common OpenJDK implementations, `modCount`
tracks structural modifications and is used by iterators to detect
unexpected changes. An iterator compares the expected modification count
with the collection's count and may throw
`ConcurrentModificationException` when they differ. It is an internal
implementation detail, not a public synchronization mechanism.

### Q77. What is a structural modification?

**Speakable answer:** A structural modification changes the collection's
size or otherwise changes its structure in a way the iterator tracks.
Adding or removing elements is typically structural, while replacing an
existing element with `set()` in an `ArrayList` is generally not. The
exact definition depends on the collection's contract.

### Q78. Why can removing elements in an enhanced `for` loop fail?

**Speakable answer:** The enhanced `for` loop uses an iterator under the
hood. Removing directly from the collection changes its structure
without updating that iterator's expected modification state, so a
fail-fast iterator may throw `ConcurrentModificationException`. I use
the iterator's own `remove()` method or `removeIf()` when appropriate.

### Q79. How can you safely remove elements while iterating?

**Speakable answer:** With an iterator, I call `iterator.remove()` after
`next()` when supported. For a list or collection, `removeIf(predicate)`
is often simpler. If concurrent updates are required, I select a
collection whose concurrency semantics suit the task rather than relying
on fail-fast behaviour.

### Q80. What is the difference between `Iterator.remove()` and `collection.remove()` during iteration?

**Speakable answer:** `Iterator.remove()` removes the last element
returned by that iterator and keeps the iterator's state consistent when
supported. Calling `collection.remove()` separately can structurally
modify the collection without updating the iterator, potentially causing
a fail-fast exception. The iterator method can only be called in the
valid state after `next()` and not repeatedly without another `next()`.

### Q81. How does `CopyOnWriteArrayList` iteration work?

**Speakable answer:** A `CopyOnWriteArrayList` iterator traverses a
snapshot of the backing array captured when the iterator was created. It
does not see later changes and does not throw
`ConcurrentModificationException` for those updates. This is useful when
reads and iterations greatly outnumber writes, because writes copy the
array and can be expensive.

### Q82. Does a fail-fast iterator always detect concurrent modification?

**Speakable answer:** No. Fail-fast behaviour is best effort and must
not be used as a correctness or synchronization mechanism. It may detect
many accidental modifications, but an application must use proper
synchronization or concurrent collections to ensure thread safety.

### Q83. What is a `Spliterator`?

**Speakable answer:** A `Spliterator` supports traversing and
potentially partitioning elements, especially for the Stream API. Its
`tryAdvance()` processes one element, `trySplit()` attempts to split off
part of the work, and `estimateSize()` estimates remaining elements.
Characteristics such as `ORDERED`, `SIZED`, and `SORTED` describe the
source and can help stream processing.

### Q84. How do you iterate through a `Map` efficiently?

**Speakable answer:** If I need both keys and values, I normally iterate
over `entrySet()` because each entry exposes its key and value directly.
If I need only keys or only values, I use `keySet()` or `values()`. For
example, `for (Map.Entry<K,V> e : map.entrySet())` is a clear and
efficient general pattern.

### Q85. What is the difference between `forEach()` and an enhanced `for` loop?

**Speakable answer:** An enhanced `for` loop provides straightforward
iteration and works with `Iterable`. `forEach()` accepts a consumer,
making lambda-based code concise. Neither automatically makes the
collection thread-safe. If order, checked exceptions, early termination,
or mutation matters, I choose the construct that expresses those
requirements most clearly.

------------------------------------------------------------------------

# 7. equals(), hashCode(), Comparable and Comparator

### Q86. What is the contract of `equals()`?

**Speakable answer:** `equals()` should be reflexive, symmetric,
transitive, and consistent, and it must return false when compared with
null. It expresses logical equality rather than necessarily checking
whether two references point to the same object. If I define equality
using business fields, I implement the method carefully and keep it
consistent with the class's identity model.

### Q87. What is the contract of `hashCode()`?

**Speakable answer:** If two objects are equal according to `equals()`,
they must have the same hash code. Unequal objects may share a hash
code. The hash code should remain consistent during repeated calls as
long as the information used by equality has not changed. This contract
enables hash-based collections to locate candidate entries efficiently.

### Q88. Why must equal objects have equal hash codes?

**Speakable answer:** Hash-based collections use the hash code to locate
a bucket before checking equality. If two equal objects have different
hash codes, a lookup using one object may never examine the bucket where
the other was stored. Therefore, overriding `equals()` normally requires
overriding `hashCode()` consistently.

### Q89. Can two unequal objects have the same hash code?

**Speakable answer:** Yes. A hash code is an integer, while the set of
possible object states is much larger, so collisions are unavoidable. A
collision does not mean the objects are equal; `equals()` distinguishes
them when the collection compares candidate keys.

### Q90. What happens if only `equals()` is overridden?

**Speakable answer:** The default `Object.hashCode()` may return
different hash codes for objects that the custom `equals()` considers
equal. Those objects can be placed in different hash buckets, so
`HashMap` lookup and `HashSet` duplicate detection may fail. I override
both methods as a consistent pair.

### Q91. What happens if only `hashCode()` is overridden?

**Speakable answer:** The objects still use the inherited identity-based
`equals()` unless it is also overridden. Matching hash codes only bring
objects into the same candidate bucket; they do not make the objects
equal. As a result, logically equivalent objects may still be stored
separately.

### Q92. What is the difference between `==` and `equals()`?

**Speakable answer:** For object references, `==` checks whether both
references point to the same object. `equals()` checks logical equality
according to the class implementation. The default `Object.equals()`
behaves like identity comparison, but classes such as `String` override
it to compare content.

### Q93. What is the difference between `Comparable` and `Comparator`?

**Speakable answer:** `Comparable` defines a class's natural ordering
through `compareTo()`. `Comparator` defines an external ordering through
`compare()`, allowing multiple sort orders without changing the class.
For example, an employee might have a natural order by ID but be sorted
by salary or name using different comparators.

### Q94. What is the difference between `compareTo()` and `compare()`?

**Speakable answer:** `compareTo()` is an instance method from
`Comparable` and compares the current object with another object.
`compare()` is a method on a `Comparator` and accepts two objects. Both
return a negative number, zero, or a positive number to indicate
ordering, not necessarily exactly -1, 0, or 1.

### Q95. How do you sort by multiple fields?

**Speakable answer:** I compose comparators. For example,
`Comparator.comparing(Employee::getDepartment).thenComparing(Employee::getSalary)`
sorts by department and then salary. I can reverse an ordering with
`reversed()` and define null handling with methods such as
`nullsFirst()` or `nullsLast()`.

### Q96. What is an ordering consistent with `equals()`?

**Speakable answer:** An ordering is consistent with `equals()` when
`compare(a, b) == 0` exactly when `a.equals(b)` is true. `TreeSet` and
`TreeMap` use comparison results to determine key uniqueness. If
comparison returns zero for objects that are not equal, the sorted
collection can treat them as the same set element or map key.

### Q97. What can go wrong with a bad comparator?

**Speakable answer:** A comparator that violates transitivity or
consistency can cause incorrect ordering and may lead sorting algorithms
to throw an exception. If it returns zero for distinct values
unexpectedly, `TreeSet` or `TreeMap` may discard a distinct element or
mapping. I ensure comparator logic is transitive, stable for the data
being stored, and handles nulls and edge cases intentionally.

------------------------------------------------------------------------

# 8. Generics and Collection APIs

### Q98. What are generics in Java?

**Speakable answer:** Generics provide compile-time type safety for
classes, interfaces, and methods. For example, `List<String>` restricts
elements to strings at compile time and reduces explicit casting. Java
implements generics primarily through type erasure, so generic type
parameters generally do not remain as distinct runtime classes.

### Q99. What are bounded type parameters?

**Speakable answer:** A bounded type parameter restricts which types can
be used. For example, `<T extends Number>` accepts `Number` and its
subclasses. Bounds let a method use operations guaranteed by the bound,
such as numeric methods on `Number`, while still remaining generic.

### Q100. Explain `? extends T` and `? super T`.

**Speakable answer:** `? extends T` means an unknown subtype of T and is
useful when reading values as T, but generally prevents adding non-null
T values because the exact subtype is unknown. `? super T` means an
unknown supertype of T and is useful for writing T values. This is
summarised by PECS: Producer Extends, Consumer Super.

### Q101. What is the PECS principle?

**Speakable answer:** PECS means Producer Extends, Consumer Super. If a
structure produces values for me to read, I use `? extends T`; if it
consumes values I want to write, I use `? super T`. For example, a
method that copies from a source list to a destination list can accept
`List<? extends T>` as the source and `List<? super T>` as the
destination.

### Q102. Why can't you directly create a generic array such as `new T[10]`?

**Speakable answer:** Java arrays are reified, so they know their
component type at runtime, while generic type parameters are erased.
Creating `new T[10]` would create a mismatch between runtime array
checks and erased generic types. Generic collections such as
`ArrayList<T>` are the usual alternative.

### Q103. What is type erasure?

**Speakable answer:** Type erasure is the process by which the compiler
removes most generic type information from runtime representations,
inserting casts and bridge methods when necessary. It maintains
compatibility with older Java bytecode. One consequence is that
`List<String>` and `List<Integer>` share the same runtime raw class.

### Q104. What is the difference between `List<Object>` and `List<?>`?

**Speakable answer:** `List<Object>` is a list that can accept any
object, while `List<?>` is a list of some unknown element type. I can
safely read elements from `List<?>` as `Object`, but I cannot generally
add arbitrary values because its exact type is unknown. Generics are
invariant, so `List<String>` is not a subtype of `List<Object>`.

### Q105. What is a raw type?

**Speakable answer:** A raw type uses a generic class without specifying
its type argument, such as `List` instead of `List<String>`. It exists
for backward compatibility but bypasses much of generic type checking
and can cause runtime `ClassCastException`. I use parameterised types in
modern Java code.

### Q106. What is a generic method?

**Speakable answer:** A generic method declares its own type parameter,
such as `<T> T first(List<T> items)`. The type parameter can be inferred
from the arguments or supplied explicitly. This allows type-safe
reusable methods without tying the method to one concrete element type.

### Q107. Why can't you add an element to `List<? extends Number>`?

**Speakable answer:** The list might actually be a `List<Integer>`,
`List<Double>`, or another subtype list. Adding a general `Number` could
violate its real element type. The compiler therefore restricts adding
values other than null. I use `? super Number` when the list must accept
Number values.

### Q108. What do `List.of()`, `Arrays.asList()`, and `ArrayList` do differently?

**Speakable answer:** `List.of()` returns an unmodifiable list and
rejects null elements. `Arrays.asList()` returns a fixed-size list
backed by the supplied array: `set()` is allowed, but adding and
removing are not. `ArrayList` is resizable and supports add, remove, and
set. I use `List.of()` for unmodifiable data, `Arrays.asList()` for
fixed-size array-backed views, and `ArrayList` for dynamic lists.

### Q109. What is the difference between `Arrays.asList()` and `List.of()`?

**Speakable answer:** `Arrays.asList()` is backed by the input array,
permits nulls, and allows replacing elements with `set()`, but it cannot
change size. `List.of()` does not permit nulls and rejects element
replacement as well as additions and removals. Both are unmodifiable
with respect to size, but only `List.of()` rejects `set()`.

### Q110. What is the difference between `Collections.unmodifiableList()` and `List.of()`?

**Speakable answer:** `Collections.unmodifiableList(list)` returns an
unmodifiable view of the supplied list, so changes made through another
reference to the backing list can still appear. `List.of()` creates an
unmodifiable list whose collection contents cannot be changed through a
backing collection reference. Neither guarantees that mutable elements
themselves cannot change.

### Q111. What is the difference between `Collections.synchronizedList()` and a concurrent collection?

**Speakable answer:** `synchronizedList()` wraps a list so individual
method calls use synchronization. Iteration still requires synchronizing
on the wrapper according to its contract. A concurrent collection is
designed with concurrency-specific behaviour, such as weakly consistent
iteration or copy-on-write snapshots. I choose based on the required
read/write pattern and consistency guarantees.

### Q112. How do collection factory methods `List.of()`, `Set.of()`, and `Map.of()` behave?

**Speakable answer:** They were introduced in Java 9 and create
unmodifiable collections. They reject null elements, keys, and values,
and set or map factories reject duplicate elements or keys rather than
silently overwriting them. They are concise for fixed data, but I use
mutable implementations when later updates are required.

------------------------------------------------------------------------

# 9. Java 8+ Streams and Collection Methods

### Q113. What is the Stream API?

**Speakable answer:** The Stream API provides a declarative way to
process data from collections and other sources. A pipeline can filter,
transform, sort, group, or reduce elements. Intermediate operations are
generally lazy, and a terminal operation triggers evaluation. A stream
is not a collection and should generally be consumed only once.

### Q114. What is the difference between `stream()` and `parallelStream()`?

**Speakable answer:** `stream()` normally processes elements
sequentially, while `parallelStream()` can split work across multiple
threads, commonly using the fork-join common pool. Parallel streams can
help with sufficiently large, independent CPU-bound tasks, but overhead,
shared state, blocking I/O, and ordering requirements can make them
slower or unsafe.

### Q115. What is the difference between intermediate and terminal stream operations?

**Speakable answer:** Intermediate operations such as `filter`, `map`,
and `sorted` build a pipeline and return another stream. Terminal
operations such as `collect`, `count`, `reduce`, and `forEach` trigger
processing and produce a result or side effect. A stream cannot normally
be reused after a terminal operation.

### Q116. What is the difference between `map()` and `flatMap()`?

**Speakable answer:** `map()` transforms each element into one result,
so mapping an element to a list produces a stream of lists. `flatMap()`
transforms each element into a stream and flattens those streams into
one stream. It is useful when each input contains multiple values, such
as converting a list of departments into one stream of employees.

### Q117. What is the difference between `filter()` and `map()`?

**Speakable answer:** `filter()` keeps or discards each element based on
a predicate, so the element type remains the same. `map()` transforms
each element into another value and can change the stream's element
type. For example, I filter employees by active status and map the
remaining employees to their names.

### Q118. How do you group objects using `groupingBy()`?

**Speakable answer:** I use `Collectors.groupingBy()` with a classifier
function. For example,
`employees.stream().collect(Collectors.groupingBy(Employee::getDepartment))`
creates a map from department to employee list. I can add downstream
collectors to count employees, sum salaries, or collect only selected
fields.

### Q119. How do you count elements using streams?

**Speakable answer:** If I need the total number of elements, I use
`stream().count()`. If I need a count per category, I use
`Collectors.groupingBy(classifier, Collectors.counting())`. For simple
frequency counting, a map with `merge()` can also be a clear and
efficient approach.

### Q120. What is `partitioningBy()`?

**Speakable answer:** `partitioningBy()` divides stream elements into
two groups based on a predicate, returning a map keyed by Boolean
values. For example, I can partition employees into active and inactive
groups. Unlike general grouping, the classifier always produces one of
two Boolean categories.

### Q121. How do you convert a list into a map using `toMap()`?

**Speakable answer:** I use `Collectors.toMap(keyMapper, valueMapper)`.
For example, I can map each employee ID to the employee object. If
duplicate keys are possible, I supply a merge function to define which
value to keep or how to combine them; otherwise, duplicate keys cause an
`IllegalStateException`.

### Q122. What happens when `toMap()` encounters duplicate keys?

**Speakable answer:** The two-argument `toMap()` collector throws
`IllegalStateException` if the generated keys collide. I use the
overload with a merge function, such as
`(oldValue, newValue) -> oldValue`, or combine values according to the
business requirement. I do not silently choose a merge policy without
understanding the data.

### Q123. How do you find the maximum or minimum element?

**Speakable answer:** I can use `stream().max(comparator)` or
`min(comparator)`, which return an `Optional` because the stream may be
empty. For a collection, I can also use `Collections.max()` or `min()`
with a comparator. The comparator defines the meaning of maximum and
minimum.

### Q124. What does `distinct()` do?

**Speakable answer:** `distinct()` removes duplicates according to
equality semantics. For ordered streams, it preserves the encounter
order of the first occurrence. For custom objects, the result depends on
correctly implemented `equals()` and `hashCode()`. It is useful for
simple de-duplication pipelines.

### Q125. Compare `Stream.toList()` and `Collectors.toList()`.

**Speakable answer:** `Stream.toList()` was introduced in Java 16 and
returns an unmodifiable list. `Collectors.toList()` does not guarantee a
particular concrete list type or mutability, even though common
implementations often return an `ArrayList`. If I specifically need a
mutable `ArrayList`, I use
`collect(Collectors.toCollection(ArrayList::new))`.

### Q126. What is the difference between `forEach()` and `forEachOrdered()` on streams?

**Speakable answer:** `forEach()` does not guarantee encounter order for
parallel streams. `forEachOrdered()` preserves encounter order when the
stream has one, which can reduce parallel performance. I use ordered
processing only when the order matters and avoid side effects in stream
pipelines where possible.

### Q127. When can parallel streams make performance worse?

**Speakable answer:** Parallel streams add splitting, scheduling, and
combining overhead. They can be slower for small collections, tasks that
are cheap per element, blocking operations, shared mutable state, or
workloads limited by memory bandwidth. I measure performance with
realistic data rather than assuming parallel execution is faster.

### Q128. What do `removeIf()`, `replaceAll()`, and `sort()` do?

**Speakable answer:** `removeIf()` removes elements matching a
predicate, `replaceAll()` transforms each element using a unary
operator, and `sort()` orders a list using natural order or a
comparator. They make common list operations concise. Whether a
collection supports these mutations depends on its implementation and
mutability.

------------------------------------------------------------------------

# 10. Thread Safety and Concurrent Collections

### Q129. Compare synchronized collections and concurrent collections.

**Speakable answer:** Synchronized wrappers generally coordinate access
through a common lock and require extra care for iteration and compound
operations. Concurrent collections are designed for concurrent access
with more specialised locking, non-blocking techniques, snapshots, or
weakly consistent iteration. I select based on throughput, ordering,
atomicity, and iteration requirements.

### Q130. How does `ConcurrentHashMap` work in Java 8 and later?

**Speakable answer:** In Java 8+ OpenJDK implementations,
`ConcurrentHashMap` combines volatile reads, compare-and-set operations,
and synchronisation at a bucket level when needed. It does not use one
global lock for every operation. This allows many reads and updates to
proceed concurrently while preserving thread-safe map operations. The
exact implementation can change between JDK versions.

### Q131. Why is `ConcurrentHashMap` generally better than `Hashtable` for concurrency?

**Speakable answer:** `Hashtable` synchronizes its methods on the map
instance, which can cause contention between unrelated operations.
`ConcurrentHashMap` is designed for greater concurrency and supports
atomic operations such as `putIfAbsent`, `compute`, and `merge`. It also
provides weakly consistent iterators and does not allow null keys or
values.

### Q132. How does `ConcurrentHashMap` handle concurrent reads and writes?

**Speakable answer:** Reads are generally non-blocking in common
implementations, while updates use atomic operations and targeted
synchronisation when necessary. Its internal design allows unrelated
buckets to be updated concurrently in many cases. Application code
should still use atomic map methods for compound logic rather than
splitting a check and update into separate calls.

### Q133. Why does `ConcurrentHashMap` not allow null keys or values?

**Speakable answer:** Null values would make it difficult to distinguish
a missing mapping from a mapping whose value is null during concurrent
retrieval. By disallowing null keys and values, the map keeps retrieval
semantics unambiguous. This simplifies concurrent use and the
interpretation of methods such as `get()`.

### Q134. Compare `ConcurrentHashMap` and `Collections.synchronizedMap()`.

**Speakable answer:** `synchronizedMap()` protects operations with a
common wrapper lock, and iteration requires external synchronization on
the wrapper. `ConcurrentHashMap` is designed for concurrent workloads
and typically allows better parallel access. It has different semantics,
including no null keys or values and weakly consistent iterators, so I
choose based on the contract required.

### Q135. Compare `CopyOnWriteArrayList` and `synchronizedList()`.

**Speakable answer:** `CopyOnWriteArrayList` creates a new backing array
for mutations and gives iterators a snapshot. It works well for
read-heavy, write-light workloads. A synchronized list uses a lock to
protect operations and can be more suitable when writes are frequent,
but iteration requires synchronizing on the wrapper. Both choices have
trade-offs.

### Q136. When should you use `CopyOnWriteArrayList`?

**Speakable answer:** I use it when reads and iterations greatly
outnumber updates, such as a list of registered listeners that changes
infrequently. Iterators see a stable snapshot and do not need to
coordinate with writers. It is a poor choice for frequent updates to a
large list because each write may copy the array.

### Q137. What is `CopyOnWriteArraySet`?

**Speakable answer:** It is a set implementation backed by a
copy-on-write array structure. It provides thread-safe operations and
snapshot-style iteration, making it suitable for small, read-heavy sets
such as listener registries. Membership checks and writes can be
relatively expensive compared with hash-based sets.

### Q138. What is `BlockingQueue`?

**Speakable answer:** `BlockingQueue` is a thread-safe queue designed
for producer-consumer coordination. It supports operations that wait for
an element or available capacity, plus non-blocking alternatives. A
bounded implementation can limit queued work and provide backpressure.
It is widely used in worker pools and asynchronous processing pipelines.

### Q139. Compare `ArrayBlockingQueue` and `LinkedBlockingQueue`.

**Speakable answer:** `ArrayBlockingQueue` is a bounded queue backed by
an array, with fixed capacity. `LinkedBlockingQueue` is node-based and
can be bounded or effectively unbounded when no capacity is supplied.
Their locking strategies and memory characteristics differ. I set an
intentional capacity when controlling memory and producer pressure
matters.

### Q140. Compare `ConcurrentLinkedQueue` and `LinkedBlockingQueue`.

**Speakable answer:** `ConcurrentLinkedQueue` is a non-blocking
concurrent queue and does not wait for elements or capacity.
`LinkedBlockingQueue` supports blocking `put` and `take` operations and
can be bounded. I use the former for non-blocking concurrent FIFO access
and the latter when producers and consumers need coordination.

### Q141. What are `SynchronousQueue` and `DelayQueue`?

**Speakable answer:** `SynchronousQueue` has no storage capacity; each
insertion must rendezvous with a consumer taking the element.
`DelayQueue` holds elements until their delay has expired, with elements
implementing `Delayed`. They solve specialised coordination and
scheduling problems rather than acting as ordinary FIFO queues.

### Q142. What are `ConcurrentSkipListMap` and `ConcurrentSkipListSet`?

**Speakable answer:** They are concurrent, sorted map and set
implementations based on skip-list structures. They provide expected
O(log n) operations and ordered traversal while supporting concurrent
access. They are useful when I need both sorted or range-based
operations and concurrency, unlike `ConcurrentHashMap`, which does not
maintain sorted order.

### Q143. What is weakly consistent iteration?

**Speakable answer:** A weakly consistent iterator tolerates concurrent
modification and does not generally throw
`ConcurrentModificationException`. It may reflect some changes made
after iteration begins, but it does not promise a fixed snapshot or
necessarily show every concurrent update. I rely on the collection's
documented semantics, not on assumptions about exactly which updates
appear.

### Q144. How do `compute()`, `merge()`, and `putIfAbsent()` help with concurrency?

**Speakable answer:** These methods express compound map operations as a
single API call. For `ConcurrentHashMap`, their documented atomicity
helps avoid races that can occur with separate `get()` and `put()`
calls. The mapping functions should be short and should not recursively
modify the same map in problematic ways.

### Q145. What is the limitation of compound operations on synchronized collections?

**Speakable answer:** Even if individual methods are synchronized, a
sequence such as "check whether a key exists, then insert it" is not
automatically atomic across both calls. Another thread can interleave
between them. I use external synchronization around the whole sequence
or a concurrent map's atomic methods.

### Q146. How would you implement a thread-safe frequency counter?

**Speakable answer:** For independent key counts, I can use
`ConcurrentHashMap<T, LongAdder>` and update with
`computeIfAbsent(key, k -> new LongAdder()).increment()`. `LongAdder` is
useful for high-contention counters. If I need a consistent atomic
snapshot or more complex invariants across keys, I need a stronger
coordination strategy.

------------------------------------------------------------------------

# 11. Complexity and Performance

### Q147. What are the time complexities of common collections?

**Speakable answer:** `ArrayList` provides O(1) indexed access,
amortized O(1) append, and O(n) arbitrary insertion or deletion.
`HashMap` and `HashSet` are typically O(1) on average for basic
operations. `TreeMap` and `TreeSet` provide O(log n) operations.
`PriorityQueue` has O(1) peek and O(log n) offer and poll. I always
state assumptions, because workload and implementation affect
performance.

### Q148. What is the time complexity of `HashSet.contains()`?

**Speakable answer:** It is typically O(1) on average with a good hash
distribution. Under heavy collisions, performance can degrade; tree bins
in modern OpenJDK implementations can improve collision-heavy cases. I
also consider the cost of `hashCode()` and `equals()`, because those
methods execute as part of lookup.

### Q149. How does initial capacity affect `HashMap` performance?

**Speakable answer:** If the capacity is too small for the expected
number of entries, the map may resize repeatedly, requiring extra work.
If it is excessively large, it can waste memory and increase traversal
costs in some operations. When I know the expected size, I choose a
reasonable initial capacity to reduce resizing without overallocating.

### Q150. How does load factor affect memory and collisions?

**Speakable answer:** A lower load factor tends to resize sooner, which
uses more table memory but can reduce collisions. A higher load factor
saves table space but can increase collisions and lookup work. The
default 0.75 is a practical balance for general use, but tuning should
be based on measured workload and memory constraints.

### Q151. Why can a poor `hashCode()` hurt performance?

**Speakable answer:** If many keys return the same or poorly distributed
hashes, they concentrate in a small number of buckets and require more
equality comparisons. Tree bins can mitigate some cases, but a good hash
distribution remains important. I avoid simplistic hash implementations
and use well-designed immutable key fields.

### Q152. What is amortized complexity?

**Speakable answer:** Amortized complexity averages the cost across a
sequence of operations, not just one operation. For example, an
`ArrayList` append is usually O(1), but a resize may copy O(n)
references. Because resizing happens occasionally, the average append
cost across many operations is amortized O(1).

### Q153. How would you choose a collection for millions of records?

**Speakable answer:** I start from access patterns and correctness
requirements: do I need ordered iteration, uniqueness, key lookup, range
queries, or concurrent updates? I estimate memory overhead, choose an
appropriate initial capacity, and benchmark with representative data.
For millions of entries, object allocation, key size, hash quality, and
the cost of retaining references can matter as much as algorithmic
complexity.

### Q154. How would you investigate high memory use caused by collections?

**Speakable answer:** I would inspect a heap dump and use a profiler or
memory analysis tool to identify the largest retained collections and
their retaining paths. I would check for unbounded caches, static
references, duplicate data, unexpectedly large capacities, and objects
that are not released. Then I would validate a targeted fix with memory
and load tests rather than changing collection types blindly.

### Q155. When would you use primitive-specialised collections?

**Speakable answer:** Standard Java collections store objects, so
numeric primitives are boxed into wrappers such as `Integer`, which adds
object and allocation overhead in some workloads. For very large
primitive datasets, specialised libraries can reduce memory and improve
performance. I would introduce such a dependency only when profiling
demonstrates a meaningful benefit.

------------------------------------------------------------------------

# 12. Coding and Scenario-Based Questions

## Q156. Remove duplicates from a list without using a Set.

**Speakable answer:** I can use a `LinkedHashMap` as a uniqueness
tracker, but that is still a map-based structure. If the requirement
literally prohibits sets and maps, I can build a new list and check
`contains()` before adding each element. That simple approach is O(n²)
in the worst case. If order and scale matter, I would clarify whether a
hash-based helper is allowed.

``` java
List<Integer> unique = new ArrayList<>();
for (Integer value : input) {
    if (!unique.contains(value)) {
        unique.add(value);
    }
}
```

## Q157. Find duplicate elements in an array.

**Speakable answer:** I use a `HashSet` to track values already seen and
another set to store values encountered more than once. For each value,
if `seen.add(value)` returns false, it is a duplicate. This takes O(n)
average time and O(n) extra space.

``` java
Set<Integer> seen = new HashSet<>();
Set<Integer> duplicates = new LinkedHashSet<>();

for (int value : values) {
    if (!seen.add(value)) {
        duplicates.add(value);
    }
}
```

## Q158. Find the first non-repeating character in a string.

**Speakable answer:** I count each character in a `LinkedHashMap` so
insertion order is preserved. Then I iterate through the entries and
return the first character whose count is one. This is O(n) time and
O(k) space, where k is the number of distinct characters.

``` java
Map<Character, Integer> counts = new LinkedHashMap<>();
for (char c : input.toCharArray()) {
    counts.merge(c, 1, Integer::sum);
}
Character result = null;
for (var entry : counts.entrySet()) {
    if (entry.getValue() == 1) {
        result = entry.getKey();
        break;
    }
}
```

## Q159. Count word frequencies in a paragraph.

**Speakable answer:** I normalise the text according to the
requirements, split it into tokens, and increment each token's count
with `Map.merge()`. The basic algorithm is O(n) average time for n
tokens, excluding text-normalisation details. In production, I would
explicitly handle punctuation, case, Unicode, and tokenisation rules.

``` java
Map<String, Integer> frequency = new HashMap<>();
for (String word : words) {
    frequency.merge(word.toLowerCase(Locale.ROOT), 1, Integer::sum);
}
```

## Q160. Sort a map by values.

**Speakable answer:** I stream the map entries and sort by value. If I
need a result that retains the sorted iteration order, I collect into a
`LinkedHashMap`. I must define tie-breaking when multiple keys have the
same value.

``` java
Map<String, Integer> sorted = map.entrySet().stream()
    .sorted(Map.Entry.comparingByValue())
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        Map.Entry::getValue,
        (a, b) -> a,
        LinkedHashMap::new
    ));
```

## Q161. Find the top K most frequent elements.

**Speakable answer:** First, I count frequencies using a hash map. Then
I maintain a min-heap of size K ordered by frequency, replacing the
minimum when a more frequent element appears. This is typically O(n + m
log k), where n is the input length and m is the number of distinct
elements, with O(m + k) space. Sorting all distinct entries is simpler
but usually costs O(m log m).

## Q162. Group employees by department.

**Speakable answer:** I use `Collectors.groupingBy()` with the
department as the classifier. It gives me a map from department to
employee list. If I only need counts, I use a downstream `counting()`
collector to avoid retaining all employee objects in each group.

``` java
Map<String, List<Employee>> byDepartment =
    employees.stream().collect(
        Collectors.groupingBy(Employee::getDepartment)
    );
```

## Q163. Find the highest-paid employee in each department.

**Speakable answer:** I group employees by department and use a
downstream `maxBy()` collector based on salary. This produces one
maximum employee per department, represented as an `Optional` in the
downstream result type. I also define how ties should be handled if
multiple employees share the maximum salary.

``` java
Map<String, Optional<Employee>> highestPaid =
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.maxBy(Comparator.comparing(Employee::getSalary))
    ));
```

## Q164. Convert a list of employees into a map keyed by employee ID.

**Speakable answer:** I use
`Collectors.toMap(Employee::getId, Function.identity())`. Before
choosing the collector, I confirm whether employee IDs are unique. If
duplicates can occur, I provide a merge function to reject, retain, or
combine duplicates according to the business rule.

``` java
Map<Long, Employee> byId = employees.stream()
    .collect(Collectors.toMap(
        Employee::getId,
        Function.identity(),
        (first, second) -> first
    ));
```

## Q165. Merge two lists and remove duplicates.

**Speakable answer:** If I need to preserve the first occurrence order,
I can concatenate both lists into a `LinkedHashSet` and then create a
list. This uses equality and hashing to define duplicates and typically
takes O(n + m) average time. If the lists are already sorted, a
merge-based solution may be more appropriate.

``` java
List<T> merged = new ArrayList<>(
    new LinkedHashSet<>(firstList) {{
        addAll(secondList);
    }}
);
```

**Note:** In production code, avoid double-brace initialisation. Prefer
a normal local set:

``` java
Set<T> set = new LinkedHashSet<>(firstList);
set.addAll(secondList);
List<T> merged = new ArrayList<>(set);
```

## Q166. Find common and distinct elements between two lists.

**Speakable answer:** I build a `HashSet` from one collection and scan
the other to find membership efficiently. I clarify whether the result
should preserve order and whether duplicates matter. For set-style
results, intersection and difference are typically O(n + m) average
time; multiset-style results need frequency counts instead.

## Q167. Reverse a list without `Collections.reverse()`.

**Speakable answer:** I use two pointers, one at the beginning and one
at the end, and swap their elements until the pointers meet. This is
O(n) time and O(1) extra space for a mutable random-access list. For a
linked list, iterator-based reversal may be more appropriate than
repeated indexed access.

``` java
int left = 0, right = list.size() - 1;
while (left < right) {
    T temp = list.get(left);
    list.set(left, list.get(right));
    list.set(right, temp);
    left++;
    right--;
}
```

## Q168. Implement an LRU cache using `LinkedHashMap`.

**Speakable answer:** I use a `LinkedHashMap` in access-order mode and
override `removeEldestEntry()` to evict the least recently accessed
entry when capacity is exceeded. A read updates access order, and
insertion or access moves an entry toward the most-recent end. This is a
compact single-threaded LRU implementation; I add synchronisation or a
dedicated cache library if concurrent access or advanced eviction is
required.

``` java
class LruCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    LruCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}
```

## Q169. Design a bounded producer-consumer queue.

**Speakable answer:** I use a bounded `BlockingQueue`, such as
`ArrayBlockingQueue`, so producers block or receive a failure signal
when capacity is exhausted, and consumers wait for available work. This
limits memory growth and provides coordination. I also define shutdown,
interruption handling, queue capacity, and overload behaviour.

``` java
BlockingQueue<Task> queue = new ArrayBlockingQueue<>(500);
// Producer: queue.put(task);
// Consumer: Task task = queue.take();
```

## Q170. Safely update a shared map from multiple threads.

**Speakable answer:** If each key can be updated independently, I use
`ConcurrentHashMap` and atomic operations such as `merge()` or
`compute()`. I avoid a separate `get()` followed by `put()` when the
pair must be atomic. If the invariant spans several keys or external
state, I may need explicit locking or a transaction-like coordination
strategy.

``` java
ConcurrentHashMap<String, Integer> counts = new ConcurrentHashMap<>();
counts.merge("orders", 1, Integer::sum);
```

## Q171. Choose a collection for a leaderboard with frequent score updates.

**Speakable answer:** If I need quick lookup by player and ranking by
score, I may maintain a map for player scores plus a sorted structure
for ranking. I must handle score updates by removing the old ranking
entry and inserting the new one atomically. For a production leaderboard
with ties, pagination, and high update volume, I would evaluate a
dedicated indexed or distributed data structure rather than relying on
one basic collection.

## Q172. Choose a collection for a cache that needs insertion order.

**Speakable answer:** `LinkedHashMap` is a straightforward choice when I
need key-value lookup and predictable insertion order. If the cache
should evict least-recently-used entries, I can enable access order. For
concurrent production caches, I would evaluate concurrency, expiration,
size limits, metrics, and whether a dedicated caching library is more
suitable.

## Q173. Choose a collection for sorted range queries.

**Speakable answer:** I would consider `TreeMap` or `TreeSet` because
they maintain sorted order and expose range methods such as `subMap`,
`tailSet`, and `ceilingKey`. Their operations are typically O(log n),
with range traversal proportional to the number of returned entries. If
concurrent sorted access is required, I would evaluate
`ConcurrentSkipListMap` or `ConcurrentSkipListSet`.

## Q174. Debug a `ConcurrentModificationException` in production.

**Speakable answer:** I first identify which collection is being
structurally modified and which iterator is active. I check for direct
add or remove calls inside loops, callbacks that mutate the same
collection, and shared access across threads. Then I use iterator
removal, `removeIf()`, a copy, appropriate locking, or a concurrent
collection based on the actual requirement. I do not simply catch and
ignore the exception.

## Q175. How would you benchmark two collection implementations fairly?

**Speakable answer:** I use a benchmark harness such as JMH rather than
relying on one timing measurement. I test representative data sizes,
read/write ratios, key distributions, warm-up, allocation, and
concurrency. I keep the workload equivalent and avoid dead-code
elimination. I then compare throughput, latency, and memory while
accounting for the application's real requirements.

------------------------------------------------------------------------

# 13. Additional High-Value Interview Questions

### Q176. Can two different keys coexist in a `HashMap` if their hash codes are equal?

**Speakable answer:** Yes. The hash code locates a bucket, but equality
determines whether the keys represent the same mapping. If the keys are
not equal, both can coexist in the same bucket. If they are equal,
inserting the second key updates the existing mapping.

### Q177. Does `HashMap` preserve insertion order?

**Speakable answer:** No. `HashMap` makes no guarantee about iteration
order. If I need insertion order, I use `LinkedHashMap`; if I need
sorted key order, I use `TreeMap`.

### Q178. Can a collection contain duplicate objects?

**Speakable answer:** A `List` can contain duplicates. A `Set` prevents
duplicates according to its equality or ordering rules. A `Map` cannot
contain duplicate keys, but it can contain duplicate values. The exact
behaviour depends on the interface and implementation.

### Q179. Why should I program to interfaces?

**Speakable answer:** Declaring a variable as `List`, `Set`, or `Map`
decouples client code from a specific implementation. I can switch from
`ArrayList` to another list implementation without changing callers that
only rely on the interface. I still choose the concrete implementation
deliberately based on performance and semantics.

### Q180. How do you choose the right collection in a real application?

**Speakable answer:** I start with the access pattern and required
semantics: duplicates, ordering, sorting, key lookup, concurrency, null
handling, and range operations. Then I compare complexity, memory
overhead, and iteration behaviour. I choose a simple implementation that
satisfies the contract and benchmark it if performance is important.

### Q181. Are Java collections thread-safe by default?

**Speakable answer:** No. Common classes such as `ArrayList`, `HashMap`,
and `HashSet` are not thread-safe for concurrent mutation. Java provides
synchronized wrappers and concurrent implementations such as
`ConcurrentHashMap`, `CopyOnWriteArrayList`, and `BlockingQueue`. The
correct choice depends on how threads read, update, and iterate.

### Q182. What is the difference between an unmodifiable collection and a defensive copy?

**Speakable answer:** An unmodifiable wrapper blocks mutations through
the wrapper, but it can still reflect changes made through another
reference to the backing collection. A defensive copy creates a separate
collection structure so later structural changes to the original do not
affect the copy. If elements are mutable, both collections can still
share the same element objects unless I copy those too.

### Q183. Can `Collections.unmodifiableList()` guarantee deep immutability?

**Speakable answer:** No. It prevents structural changes through the
returned wrapper, but it does not freeze the underlying list if another
reference can mutate it, and it does not make contained objects
immutable. Deep immutability requires controlling the backing state and
the mutability of contained objects.

### Q184. Why should mutable objects generally be avoided as hash keys?

**Speakable answer:** If a key changes in a way that affects its hash
code or equality, it may become unreachable through normal lookup
because it remains stored according to its old hash. Immutable key types
such as `String`, records with immutable components, or carefully
designed value objects reduce this risk.

### Q185. What are the key Java 8, 9, 16, and 21 collection-related features to know?

**Speakable answer:** Java 8 introduced major collection-related
capabilities such as streams, lambdas, `Map` default methods, and
`List.sort()`. Java 9 introduced collection factory methods such as
`List.of()`, `Set.of()`, and `Map.of()`. Java 16 added `Stream.toList()`
and records, which can be useful for immutable data carriers. Java 21
includes sequenced collection interfaces such as `SequencedCollection`,
`SequencedSet`, and `SequencedMap`, adding consistent APIs for
encounter-order operations to relevant collections.

### Q186. What are sequenced collections in Java 21?

**Speakable answer:** Java 21 introduced `SequencedCollection`,
`SequencedSet`, and `SequencedMap` to provide a common API for
collections with a defined encounter order. Depending on the interface,
methods include access to the first or last element and reversed views.
They make ordered collection operations more consistent across
implementations such as lists and linked hash collections. Not every
collection has a defined encounter order.

------------------------------------------------------------------------

# 14. Rapid Revision Checklist

Before an interview, make sure you can explain each item without reading
notes:

-   [ ] Collection vs Collections; Collection vs Map
-   [ ] List vs Set vs Queue vs Map
-   [ ] ArrayList internals, resizing, amortized complexity
-   [ ] ArrayList vs LinkedList vs Vector
-   [ ] HashSet internals and duplicate detection
-   [ ] HashMap bucket calculation, collision handling, resizing, load
    factor
-   [ ] HashMap treeification and implementation-specific thresholds
-   [ ] `equals()` and `hashCode()` contracts
-   [ ] Mutable keys and incorrect hash implementations
-   [ ] HashMap vs LinkedHashMap vs TreeMap
-   [ ] HashMap vs Hashtable vs ConcurrentHashMap
-   [ ] Comparable vs Comparator and comparator contracts
-   [ ] Queue methods: add/offer, remove/poll, element/peek
-   [ ] PriorityQueue heap internals and iteration order
-   [ ] Iterator, ListIterator, Spliterator and fail-fast behaviour
-   [ ] Safe removal during iteration
-   [ ] Synchronized wrappers vs concurrent collections
-   [ ] CopyOnWriteArrayList and BlockingQueue use cases
-   [ ] PECS, wildcards, type erasure, raw types
-   [ ] `List.of()` vs `Arrays.asList()` vs `ArrayList`
-   [ ] Streams: map, flatMap, groupingBy, partitioningBy, toMap
-   [ ] `Stream.toList()` vs `Collectors.toList()`
-   [ ] Time complexity and amortized analysis
-   [ ] Frequency counting, duplicates, top K, LRU cache
-   [ ] Real-world collection selection and concurrency scenarios

## Final interview strategy

For each answer, follow this structure:

1.  **Definition:** State what the interface, class, or method does.
2.  **Internal working:** Explain the relevant data structure or
    algorithm.
3.  **Complexity:** Give average or worst-case complexity where it
    matters.
4.  **Trade-off:** Explain a limitation or alternative.
5.  **Example:** Connect it to a practical Java application.

Do not memorise every sentence word-for-word. Understand the idea and
practise explaining it naturally. For senior roles, interviewers often
follow a correct definition with "How does it work internally?", "What
is the complexity?", and "What would you choose in production, and why?"
