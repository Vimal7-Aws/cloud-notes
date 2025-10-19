# Java Collections — Cheat Sheet

| Type         | Class           | Ordered | Duplicates | Null allowed | Description                                                                                           |
|---------------|----------------|----------|-------------|--------------|-------------------------------------------------------------------------------------------------------|
| List          | ArrayList      | Yes      | Yes         | Yes          | Resizable array, fast random access, slower for insertions/removals.                                 |
| List          | LinkedList     | Yes      | Yes         | Yes          | Doubly-linked list, faster insertions/removals, slower access by index.                              |
| List          | Vector         | Yes      | Yes         | Yes          | Synchronized, resizable array, similar to ArrayList but thread-safe.                                 |
| List          | Stack          | Yes (LIFO) | Yes       | Yes          | Subclass of Vector, Last-In-First-Out (LIFO) operations.                                              |
| Set           | HashSet        | No       | No          | Yes          | Unordered, uses a hash table for fast lookups, allows one null element.                              |
| Set           | LinkedHashSet  | Yes      | No          | Yes          | Maintains insertion order, slightly slower than HashSet.                                              |
| Set           | TreeSet        | Yes (Sorted) | No       | No           | Sorted set, backed by a Red-Black tree, does not allow null elements.                                 |
| Queue         | PriorityQueue  | No       | Yes         | No           | Elements ordered based on natural order or comparator.                                                |
| Queue/Deque   | LinkedList     | Yes (FIFO) | Yes       | Yes          | Can act as both Queue and Deque, supports both FIFO and LIFO.                                         |
| Deque         | ArrayDeque     | Yes      | Yes         | No           | Resizable array implementation, faster than LinkedList for deque operations, no null elements allowed. |
