# Time and Space Complexity — DSA Day 1

For product-company interviews, **time complexity tells you how an algorithm's running time grows as the input size increases. Space complexity tells you how its memory usage grows.**

Let's understand both using Java examples.

## 1. Time complexity

Suppose `n` is the number of elements in an array.

```java
int[] arr = {10, 20, 30, 40, 50};

for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
```

The loop executes `n` times. If the array contains 5 elements, it runs 5 times; if it contains 1,000 elements, it runs 1,000 times.

Therefore, the time complexity is **O(n)**.

## 2. Common time complexities

### O(1) — Constant time

The number of operations doesn't grow with input size.

Example: `arr[0]`

### O(log n) — Logarithmic time

The search space shrinks by a factor each step.

Example: Binary search.

### O(n) — Linear time

Work grows proportionally to the input size.

Example: One loop over an array.

### O(n log n) — Linearithmic time

Common in efficient sorting algorithms.

Example: Merge sort.

### O(n²) — Quadratic time

Work often grows with every pair of elements.

Example: Two nested loops.

The general order from more scalable to less scalable is:

\[
O(1) < O(\log n) < O(n) < O(n\log n) < O(n^2)
\]

## 3. How to calculate time complexity in Java

### Example A: One loop — O(n)

```java
void printArray(int[] arr) {
    for (int num : arr) {
        System.out.println(num);
    }
}
```

The loop processes every element once.

- **Time complexity:** `O(n)`
- **Auxiliary space complexity:** `O(1)` — no additional data structure grows with the input.

### Example B: Two nested loops — O(n²)

```java
void printPairs(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        for (int j = 0; j < arr.length; j++) {
            System.out.println(arr[i] + ", " + arr[j]);
        }
    }
}
```

For every element, the inner loop processes all `n` elements.

Total operations: \(n \times n = n^2\).

- **Time complexity:** `O(n²)`
- **Auxiliary space complexity:** `O(1)`

### Example C: Consecutive loops — O(n)

```java
void process(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        System.out.println(arr[i]);
    }

    for (int i = 0; i < arr.length; i++) {
        System.out.println(arr[i]);
    }
}
```

The total work is \(n+n=2n\).

Big-O ignores constant factors, so the answer is **O(n)**, not O(2n).

### Example D: Binary search — O(log n)

```java
int binarySearch(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) {
            return mid;
        }

        if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1;
}
```

Each iteration eliminates approximately half of the remaining search space.

- **Time complexity:** `O(log n)`
- **Auxiliary space complexity:** `O(1)`

**Important:** Binary search requires sorted data or another structure with a suitable monotonic search condition.

## 4. Space complexity

Space complexity describes memory growth as input size increases. In interviews, distinguish **total space** from **auxiliary space** (extra memory used by the algorithm, excluding the input itself).

### Example A: Constant extra space — O(1)

```java
int sum(int[] arr) {
    int total = 0;

    for (int num : arr) {
        total += num;
    }

    return total;
}
```

Only one additional variable, `total`, is needed regardless of array size.

- **Time:** `O(n)`
- **Auxiliary space:** `O(1)`

### Example B: Linear extra space — O(n)

```java
int[] copyArray(int[] arr) {
    int[] copy = new int[arr.length];

    for (int i = 0; i < arr.length; i++) {
        copy[i] = arr[i];
    }

    return copy;
}
```

The new array grows with the input size.

- **Time:** `O(n)`
- **Auxiliary space:** `O(n)`

### Example C: HashMap — O(n) extra space

```java
Map<Integer, Integer> frequency = new HashMap<>();

for (int num : arr) {
    frequency.put(num, frequency.getOrDefault(num, 0) + 1);
}
```

In the worst case, the map stores up to `n` distinct elements.

- **Expected time:** `O(n)` total under normal hash-distribution assumptions.
- **Auxiliary space:** `O(n)`.

## 5. Practice questions

Try identifying the time or space complexity of each snippet before checking the answers.

### Question 1

What is the time complexity of a single loop from `0` to `n - 1`?

**Answer:** `O(n)`

Why: The loop performs work proportional to `n`.

### Question 2

What is the complexity of two consecutive loops, each running `n` times?

**Answer:** `O(n)`

Why: The work is `n + n = 2n`, which simplifies to `O(n)`.

### Question 3

What is the time complexity of two fully nested loops, each running `n` times?

**Answer:** `O(n²)`

Why: The inner loop runs `n` times for each of the `n` outer iterations.

### Question 4

An algorithm repeatedly halves the search range. What is its time complexity?

**Answer:** `O(log n)`

Why: The number of halvings needed to reduce `n` to `1` is proportional to `log n`.

### Question 5

You create a new array of size `n`. What is its auxiliary space complexity?

**Answer:** `O(n)`

Why: The additional array requires memory proportional to `n`.

## 6. How to answer in a product-company interview

When an interviewer asks for complexity, say something like:

> “The algorithm traverses the array once, so the time complexity is O(n). It uses only a constant number of additional variables, so the auxiliary space complexity is O(1).”

For nested loops, explain how many times the inner work executes. For recursion, also consider the recursion stack.

## Key takeaways

- **Time complexity** describes how running time scales as input size increases.
- **Space complexity** describes how memory usage scales.
- One loop over `n` items is usually `O(n)`.
- Two fully nested loops over `n` items are usually `O(n²)`.
- Consecutive loops add their costs; drop constant factors in Big-O.
- Binary search is `O(log n)` when its search condition is valid.
- Always distinguish auxiliary space from memory used by the input.
- In interviews, explain *why* the complexity has that bound.
