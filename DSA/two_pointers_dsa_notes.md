# Two Pointers — DSA Study Notes

Two pointers is a problem-solving technique in which we use two index variables to traverse a data structure, often an array or string. The pointers may move toward each other, in the same direction, or at different speeds.

It can reduce a brute-force solution—often using nested loops—to a more efficient solution.

## 1. When should you consider two pointers?

Look for clues such as:

- The input is a sorted array.
- You need to find a pair that meets a condition.
- You need to reverse an array or string.
- You need to compare characters from both ends.
- You need to remove duplicates in-place.
- You need to process two arrays together.
- You need to detect a cycle in a linked list using slow and fast pointers.

Two pointers is a technique, not a guarantee of `O(n)` time. The complexity depends on how the pointers move and whether the algorithm uses nested loops.

## 2. Common two-pointer patterns

### Pattern A: Opposite ends

Start one pointer at the beginning and another at the end. Move them toward each other.

Useful for:
- Reversing an array
- Checking a palindrome
- Finding a pair in a sorted array

```java
int left = 0;
int right = arr.length - 1;

while (left < right) {
    // Process arr[left] and arr[right]

    left++;
    right--;
}
```

If each pointer moves inward and each iteration does constant work, time is typically `O(n)` and auxiliary space is `O(1)`.

### Pattern B: Same direction (read/write pointers)

Use one pointer to read elements and another to track where the next valid element should go.

Useful for:
- Removing duplicates from a sorted array
- Moving zeroes
- Removing elements in-place

```java
int write = 0;

for (int read = 0; read < arr.length; read++) {
    if (/* arr[read] should be kept */) {
        arr[write] = arr[read];
        write++;
    }
}
```

This often runs in `O(n)` time and `O(1)` auxiliary space, modifying the original array.

### Pattern C: Two arrays

Use a pointer for each of two arrays, often to merge or compare sorted sequences.

Useful for:
- Merging sorted arrays
- Finding intersections
- Comparing values across sequences

### Pattern D: Fast and slow pointers

Use two pointers that move at different speeds, most commonly in linked lists.

Useful for:
- Detecting a cycle
- Finding the middle of a linked list
- Finding the start of a cycle (with a further step)

## 3. Example 1: Reverse an array

**Problem:** Reverse an integer array in-place.

```java
void reverse(int[] arr) {
    int left = 0;
    int right = arr.length - 1;

    while (left < right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;

        left++;
        right--;
    }
}
```

**How it works:**
1. `left` starts at the first element.
2. `right` starts at the last element.
3. Swap the two elements.
4. Move `left` forward and `right` backward.
5. Stop when the pointers meet or cross.

Example: `[1, 2, 3, 4, 5]` becomes `[5, 4, 3, 2, 1]`.

- **Time complexity:** `O(n)` — about half as many swaps as elements, which is still linear.
- **Auxiliary space:** `O(1)` — only a temporary variable is used.

## 4. Example 2: Valid Palindrome

**Problem:** Determine whether a string is a palindrome, ignoring non-alphanumeric characters and letter case.

```java
boolean isPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1;

    while (left < right) {
        while (left < right &&
               !Character.isLetterOrDigit(s.charAt(left))) {
            left++;
        }

        while (left < right &&
               !Character.isLetterOrDigit(s.charAt(right))) {
            right--;
        }

        if (Character.toLowerCase(s.charAt(left)) !=
            Character.toLowerCase(s.charAt(right))) {
            return false;
        }

        left++;
        right--;
    }

    return true;
}
```

Example: `"A man, a plan, a canal: Panama"` returns `true`.

- **Time complexity:** `O(n)` — each character is visited at most a constant number of times.
- **Auxiliary space:** `O(1)` — the method does not create another string.

## 5. Example 3: Two Sum II — sorted input

**Problem:** Given a sorted array of integers and a target, return the 1-based indices of two numbers that add up to the target. Assume exactly one solution exists.

```java
int[] twoSum(int[] numbers, int target) {
    int left = 0;
    int right = numbers.length - 1;

    while (left < right) {
        int sum = numbers[left] + numbers[right];

        if (sum == target) {
            return new int[]{left + 1, right + 1};
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }

    return new int[]{-1, -1};
}
```

**Why do the pointers move this way?**

- If the sum is too small, move `left` right to increase the sum.
- If the sum is too large, move `right` left to decrease the sum.
- If the sum matches the target, return the indices.

This reasoning depends on the array being sorted.

- **Time complexity:** `O(n)`.
- **Auxiliary space:** `O(1)`; the returned array is output storage and has constant size.

## 6. Example 4: Remove duplicates from a sorted array

**Problem:** Remove duplicates in-place from a sorted integer array and return the number of unique elements. The first `k` positions must contain the unique values in sorted order.

```java
int removeDuplicates(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }

    int write = 1;

    for (int read = 1; read < nums.length; read++) {
        if (nums[read] != nums[write - 1]) {
            nums[write] = nums[read];
            write++;
        }
    }

    return write;
}
```

Example: Input `[1, 1, 2, 2, 3]` gives `k = 3`; the first three elements become `[1, 2, 3]`. Values after index `k - 1` do not matter.

- **Time complexity:** `O(n)`.
- **Auxiliary space:** `O(1)`.

## 7. Example 5: Move zeroes to the end

**Problem:** Move all zeroes to the end while preserving the relative order of non-zero elements. Modify the array in-place.

```java
void moveZeroes(int[] nums) {
    int write = 0;

    for (int read = 0; read < nums.length; read++) {
        if (nums[read] != 0) {
            int temp = nums[write];
            nums[write] = nums[read];
            nums[read] = temp;
            write++;
        }
    }
}
```

Example: `[0, 1, 0, 3, 12]` becomes `[1, 3, 12, 0, 0]`.

- **Time complexity:** `O(n)`.
- **Auxiliary space:** `O(1)`.

## 8. Example 6: Merge two sorted arrays

**Problem:** Merge two sorted arrays into a new sorted array.

```java
int[] mergeSorted(int[] a, int[] b) {
    int[] result = new int[a.length + b.length];
    int i = 0;
    int j = 0;
    int k = 0;

    while (i < a.length && j < b.length) {
        if (a[i] <= b[j]) {
            result[k++] = a[i++];
        } else {
            result[k++] = b[j++];
        }
    }

    while (i < a.length) {
        result[k++] = a[i++];
    }

    while (j < b.length) {
        result[k++] = b[j++];
    }

    return result;
}
```

Let `n` and `m` be the lengths of the two arrays.

- **Time complexity:** `O(n + m)`.
- **Auxiliary/output space:** `O(n + m)` for the result array.

## 9. Fast and slow pointers: linked-list cycle

**Problem:** Determine whether a linked list contains a cycle.

```java
boolean hasCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow == fast) {
            return true;
        }
    }

    return false;
}
```

Here, `slow` moves one node at a time and `fast` moves two. If there is a cycle, the pointers eventually meet.

- **Time complexity:** `O(n)`.
- **Auxiliary space:** `O(1)`.

Assume `ListNode` has `int val` and `ListNode next` fields.

## 10. How to recognize the right pointer movement

For opposite-end pointers on a sorted array, use the condition to eliminate impossible candidates. In Two Sum II:

- Sum too small → increase the left pointer.
- Sum too large → decrease the right pointer.

For read/write pointers:

- `read` examines each element.
- `write` marks the next location for a kept element.

For fast/slow pointers:

- Different speeds help reveal cycles or locate a midpoint.

**Interview warning:** Do not move pointers arbitrarily. Explain why every move safely eliminates possibilities or maintains the required invariant.

## 11. Common mistakes

- Using two pointers on unsorted data when the solution depends on sorted order.
- Moving a pointer in the wrong direction.
- Using `left <= right` when the problem requires distinct elements and the pointers must not meet.
- Forgetting empty or one-element inputs.
- Accidentally changing the order of elements when order must be preserved.
- Claiming `O(1)` space when the algorithm creates an additional array or collection of size `n`.
- Failing to explain why the pointer movement is correct.

## 12. Practice problems

Try these in order. First attempt each problem without reading a solution.

### Beginner
1. [Two Sum II — Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
2. [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
3. [Reverse String](https://leetcode.com/problems/reverse-string/)
4. [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
5. [Move Zeroes](https://leetcode.com/problems/move-zeroes/)

### Intermediate
6. [3Sum](https://leetcode.com/problems/3sum/)
7. [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
8. [Sort Colors](https://leetcode.com/problems/sort-colors/)
9. [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
10. [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)

## 13. Interview explanation template

When asked to explain a two-pointer solution, use this structure:

> “I can solve this using two pointers. I initialize them based on the problem—for example, at opposite ends of a sorted array. At each step, I evaluate the current elements and move the pointer that allows me to eliminate candidates safely. Each pointer moves only a bounded number of times, so the time complexity is `O(n)`. If I modify the input in-place and use only a few variables, the auxiliary space complexity is `O(1)`.”

Adapt the reasoning to the actual problem rather than repeating the template blindly.

## Key takeaways

- Two pointers is a technique for coordinating two positions in a data structure.
- Opposite-end pointers are common for sorted pair problems and palindrome checks.
- Read/write pointers are useful for in-place filtering and rearrangement.
- Fast/slow pointers are useful for linked-list cycles and midpoints.
- Always justify pointer movement.
- Analyze time and auxiliary space separately.
- Practice a small set of representative problems and re-solve them without help.
