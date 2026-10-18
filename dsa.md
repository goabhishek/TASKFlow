from pathlib import Path
import zipfile, textwrap, shutil

root = Path("/mnt/data/NEETCODE_JAVA_STARTER")
if root.exists():
    shutil.rmtree(root)
(root / "00-Getting-Started").mkdir(parents=True)
(root / "01-Arrays-and-Hashing").mkdir()
(root / "02-Two-Pointers").mkdir()
(root / "03-Sliding-Window").mkdir()
(root / "04-Stack").mkdir()
(root / "05-Binary-Search").mkdir()
(root / "06-Linked-List").mkdir()
(root / "07-Trees").mkdir()

files = {}

files["README.md"] = r"""# NeetCode First 50 — Java Study Pack

This folder contains a beginner-first preparation guide and Java solutions for the first 50 problems in the common NeetCode 150 topic order, through the first five Tree problems.

## Folder structure

```text
NEETCODE_JAVA_STARTER/
├── README.md
├── 00-Getting-Started/
│   ├── 01-Java-for-DSA.md
│   ├── 02-Big-O-and-Collections.md
│   └── 03-How-to-Solve-Problems.md
├── 01-Arrays-and-Hashing/
│   └── problems-01-09.md
├── 02-Two-Pointers/
│   └── problems-10-14.md
├── 03-Sliding-Window/
│   └── problems-15-20.md
├── 04-Stack/
│   └── problems-21-27.md
├── 05-Binary-Search/
│   └── problems-28-34.md
├── 06-Linked-List/
│   └── problems-35-45.md
└── 07-Trees/
    └── problems-46-50.md
```

## Recommended order

1. Study Java arrays, strings, loops, methods, classes, and collections.
2. Learn Big O and common operations for `HashMap`, `HashSet`, `ArrayList`, `ArrayDeque`, and `PriorityQueue`.
3. Attempt each problem for 20–35 minutes before reading its solution.
4. Write the solution from memory, test edge cases, and state complexity.
5. Revisit problems after 1, 3, and 7 days.

## First 50 problems

1. Contains Duplicate
2. Valid Anagram
3. Two Sum
4. Group Anagrams
5. Top K Frequent Elements
6. Encode and Decode Strings
7. Product of Array Except Self
8. Valid Sudoku
9. Longest Consecutive Sequence
10. Valid Palindrome
11. Two Sum II — Input Array Is Sorted
12. 3Sum
13. Container With Most Water
14. Trapping Rain Water
15. Best Time to Buy and Sell Stock
16. Longest Substring Without Repeating Characters
17. Longest Repeating Character Replacement
18. Permutation in String
19. Minimum Window Substring
20. Sliding Window Maximum
21. Valid Parentheses
22. Min Stack
23. Evaluate Reverse Polish Notation
24. Generate Parentheses
25. Daily Temperatures
26. Car Fleet
27. Largest Rectangle in Histogram
28. Binary Search
29. Search a 2D Matrix
30. Koko Eating Bananas
31. Find Minimum in Rotated Sorted Array
32. Search in Rotated Sorted Array
33. Time Based Key-Value Store
34. Median of Two Sorted Arrays
35. Reverse Linked List
36. Merge Two Sorted Lists
37. Reorder List
38. Remove Nth Node From End of List
39. Copy List with Random Pointer
40. Add Two Numbers
41. Linked List Cycle
42. Find the Duplicate Number
43. LRU Cache
44. Merge K Sorted Lists
45. Reverse Nodes in K-Group
46. Invert Binary Tree
47. Maximum Depth of Binary Tree
48. Diameter of Binary Tree
49. Balanced Binary Tree
50. Same Tree

## Java solution conventions

- Each problem's method is designed for the usual LeetCode `class Solution` format.
- For linked-list/tree problems, LeetCode provides `ListNode`, `Node`, or `TreeNode`; the snippets note any expected node shape.
- Add `import java.util.*;` when using collections.
- Problem descriptions and platform constraints may change; always read the current statement.
"""

files["00-Getting-Started/01-Java-for-DSA.md"] = r"""# Java Essentials for DSA

## Learn these first

- Primitive types: `int`, `long`, `double`, `boolean`, `char`
- Conditions: `if`, `else`, `switch`
- Loops: `for`, enhanced `for`, `while`
- Methods, parameters, return values, and scope
- Arrays: `int[] nums`, `nums.length`
- Strings: `length()`, `charAt(i)`, `substring()`, `toCharArray()`
- `StringBuilder` for repeated string construction
- Classes, objects, constructors, `static`, and access modifiers
- `null`, references, and object equality (`equals` vs `==`)
- Sorting with `Arrays.sort()` and custom comparators
- Basic recursion before trees and backtracking

## Core syntax

```java
import java.util.*;

class Solution {
    public int sum(int[] nums) {
        int total = 0;
        for (int n : nums) {
            total += n;
        }
        return total;
    }

    public String reverse(String s) {
        StringBuilder result = new StringBuilder(s);
        return result.reverse().toString();
    }
}
```

## Collections to know

```java
Map<String, Integer> counts = new HashMap<>();
counts.put("apple", counts.getOrDefault("apple", 0) + 1);
boolean hasApple = counts.containsKey("apple");

Set<Integer> seen = new HashSet<>();
seen.add(7);
boolean exists = seen.contains(7);

List<Integer> list = new ArrayList<>();
list.add(3);
Collections.sort(list);

Deque<Integer> stack = new ArrayDeque<>();
stack.push(10);
int top = stack.pop();

Deque<Integer> queue = new ArrayDeque<>();
queue.offer(10);
int first = queue.poll();

PriorityQueue<Integer> minHeap = new PriorityQueue<>();
minHeap.offer(8);
minHeap.offer(2);
int smallest = minHeap.poll();
```

## Common pitfalls

- Arrays use `.length`; strings use `.length()`.
- Compare strings with `.equals()`, not `==`.
- Use `long` if intermediate arithmetic may exceed `int`.
- Java integer division truncates: `5 / 2 == 2`.
- Avoid `Stack`; `ArrayDeque` is usually preferred for stack operations.
- `HashMap` and `HashSet` have average O(1) lookup, not a strict worst-case guarantee.
- Use `StringBuilder` rather than repeated `result += character` in loops.
"""

files["00-Getting-Started/02-Big-O-and-Collections.md"] = r"""# Big O and Java Collections

## Complexity quick reference

| Complexity | Meaning | Example |
|---|---|---|
| O(1) | Constant | Array access by index |
| O(log n) | Logarithmic | Binary search on sorted data |
| O(n) | Linear | Scan an array once |
| O(n log n) | Efficient comparison sort | Typical comparison sorting |
| O(n²) | Quadratic | Two nested loops over n items |
| O(2ⁿ) | Exponential | Some brute-force subset searches |
| O(n!) | Factorial | Enumerating all permutations |

Space complexity should distinguish input storage from extra/auxiliary memory when appropriate.

## Collections

| Structure | Common operation | Typical time |
|---|---|---:|
| `int[]` | Index read/write | O(1) |
| `ArrayList` | Get by index | O(1) |
| `ArrayList` | Append amortized | O(1) |
| `HashMap` | Get/put average | O(1) |
| `HashSet` | Contains/add average | O(1) |
| `ArrayDeque` | Push/pop at ends | O(1) |
| `PriorityQueue` | Add/remove top | O(log n) |
| `PriorityQueue` | Peek top | O(1) |
| Sorting | Typical comparison sort | O(n log n) |

## How to analyze

1. Count how many times the main operation executes.
2. Drop constant factors: O(2n) becomes O(n).
3. Keep the dominant term: O(n² + n) becomes O(n²).
4. State extra space separately.
5. Mention assumptions, such as average-case hash lookup.
"""

files["00-Getting-Started/03-How-to-Solve-Problems.md"] = r"""# A Repeatable Problem-Solving Method

For each problem:

1. **Restate:** Explain input and output in your own words.
2. **Constraints:** Check size, value range, duplicates, and sortedness.
3. **Brute force:** Describe a simple correct approach first.
4. **Pattern:** Look for a hash map/set, two pointers, sliding window, stack, binary search, heap, linked-list pointer, or tree DFS/BFS.
5. **Invariant:** State what remains true as the algorithm runs.
6. **Code:** Use clear names and avoid clever shortcuts.
7. **Test:** Normal case, empty/small case, duplicates, negative values, boundaries, and overflow.
8. **Complexity:** Give time and auxiliary-space complexity.
9. **Review:** Re-code from memory after 1, 3, and 7 days.

Do not memorize code without understanding why it works. If stuck for 25–35 minutes, read a hint, then attempt again before viewing a full solution.
"""

# Each entry: title, problem summary, solution code, time, space, explanation
groups = {
"01-Arrays-and-Hashing/problems-01-09.md": [
("1. Contains Duplicate", "Return true if any integer occurs more than once.",
"""class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int n : nums) {
            if (!seen.add(n)) return true;
        }
        return false;
    }
}""", "O(n) average", "O(n)", "A set remembers values already seen. `add` returns false when the value is already present."),
("2. Valid Anagram", "Return true when two strings contain the same characters with the same frequencies.", 
"""class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        int[] freq = new int[26];
        for (int i = 0; i < s.length(); i++) {
            freq[s.charAt(i) - 'a']++;
            freq[t.charAt(i) - 'a']--;
        }
        for (int count : freq) {
            if (count != 0) return false;
        }
        return true;
    }
}""", "O(n)", "O(1)", "For lowercase English letters, a fixed frequency array is enough. If input is not restricted to lowercase letters, use a map or sorting."),
("3. Two Sum", "Return indices of two values whose sum equals target; assume one valid answer if the platform says so.",
"""class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> indexByValue = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int need = target - nums[i];
            if (indexByValue.containsKey(need)) {
                return new int[] { indexByValue.get(need), i };
            }
            indexByValue.put(nums[i], i);
        }
        return new int[0];
    }
}""", "O(n) average", "O(n)", "Store prior value-to-index mappings. Check the complement before inserting the current item so an element is not used twice."),
("4. Group Anagrams", "Group strings that are anagrams of each other.",
"""class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> groups = new HashMap<>();
        for (String s : strs) {
            char[] chars = s.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);
            groups.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
        }
        return new ArrayList<>(groups.values());
    }
}""", "O(m · k log k)", "O(m · k)", "Sorted characters form a canonical key. m is the number of strings and k is their typical length."),
("5. Top K Frequent Elements", "Return the k most frequent integers.",
"""class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int n : nums) freq.put(n, freq.getOrDefault(n, 0) + 1);

        PriorityQueue<int[]> minHeap =
            new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
        for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
            minHeap.offer(new int[] {e.getKey(), e.getValue()});
            if (minHeap.size() > k) minHeap.poll();
        }

        int[] result = new int[k];
        for (int i = 0; i < k; i++) result[i] = minHeap.poll()[0];
        return result;
    }
}""", "O(n log k)", "O(n)", "Count frequencies, then keep a min-heap of only k entries. Output order is not guaranteed."),
("6. Encode and Decode Strings", "Implement encoding/decoding for a list of strings, including strings containing delimiter characters.",
"""public class Codec {
    public String encode(List<String> strs) {
        StringBuilder out = new StringBuilder();
        for (String s : strs) {
            out.append(s.length()).append('#').append(s);
        }
        return out.toString();
    }

    public List<String> decode(String s) {
        List<String> result = new ArrayList<>();
        int i = 0;
        while (i < s.length()) {
            int hash = s.indexOf('#', i);
            int len = Integer.parseInt(s.substring(i, hash));
            int start = hash + 1;
            result.add(s.substring(start, start + len));
            i = start + len;
        }
        return result;
    }
}""", "O(total characters)", "O(total characters)", "Prefix every string with its length and a marker. Decode by reading the number before `#`, then consuming exactly that many characters."),
("7. Product of Array Except Self", "For every index, return the product of all other elements without division.",
"""class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        int prefix = 1;
        for (int i = 0; i < n; i++) {
            result[i] = prefix;
            prefix *= nums[i];
        }
        int suffix = 1;
        for (int i = n - 1; i >= 0; i--) {
            result[i] *= suffix;
            suffix *= nums[i];
        }
        return result;
    }
}""", "O(n)", "O(1) extra, excluding output", "First store each index's prefix product, then multiply by its suffix product. Follow the platform's integer-overflow constraints."),
("8. Valid Sudoku", "Check whether the filled cells obey row, column, and 3×3 box uniqueness rules.",
"""class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                char v = board[r][c];
                if (v == '.') continue;
                String row = v + " in row " + r;
                String col = v + " in col " + c;
                String box = v + " in box " + (r / 3) + "-" + (c / 3);
                if (!seen.add(row) || !seen.add(col) || !seen.add(box)) return false;
            }
        }
        return true;
    }
}""", "O(1)", "O(1)", "The board has fixed 9×9 size. Each digit is tracked separately for its row, column, and box."),
("9. Longest Consecutive Sequence", "Find the longest run of consecutive integers, regardless of input order.",
"""class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> values = new HashSet<>();
        for (int n : nums) values.add(n);
        int best = 0;
        for (int n : values) {
            if (n != Integer.MIN_VALUE && values.contains(n - 1)) continue;
            int length = 1;
            int current = n;
            while (current != Integer.MAX_VALUE && values.contains(current + 1)) {
                current++;
                length++;
            }
            best = Math.max(best, length);
        }
        return best;
    }
}""", "O(n) average", "O(n)", "Only start counting from values that have no predecessor. The overflow guards protect integer boundaries.")
],
"02-Two-Pointers/problems-10-14.md": [
("10. Valid Palindrome", "Ignore non-alphanumeric characters and case; check whether the remaining string is a palindrome.",
"""class Solution {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) left++;
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;
            if (Character.toLowerCase(s.charAt(left)) !=
                Character.toLowerCase(s.charAt(right))) return false;
            left++;
            right--;
        }
        return true;
    }
}""", "O(n)", "O(1)", "Move pointers inward while skipping ignored characters."),
("11. Two Sum II — Input Array Is Sorted", "Return 1-based indices of two sorted values summing to target.",
"""class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int l = 0, r = numbers.length - 1;
        while (l < r) {
            int sum = numbers[l] + numbers[r];
            if (sum == target) return new int[] {l + 1, r + 1};
            if (sum < target) l++;
            else r--;
        }
        return new int[0];
    }
}""", "O(n)", "O(1)", "Sorted order lets you increase the sum by moving left, or decrease it by moving right."),
("12. 3Sum", "Return unique triplets whose sum is zero.",
"""class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            int l = i + 1, r = nums.length - 1;
            while (l < r) {
                long sum = (long) nums[i] + nums[l] + nums[r];
                if (sum == 0) {
                    result.add(Arrays.asList(nums[i], nums[l], nums[r]));
                    int lv = nums[l], rv = nums[r];
                    while (l < r && nums[l] == lv) l++;
                    while (l < r && nums[r] == rv) r--;
                } else if (sum < 0) l++;
                else r--;
            }
        }
        return result;
    }
}""", "O(n²)", "O(1) extra, excluding output", "Sort, fix one value, then use two pointers. Skip duplicates at all three positions."),
("13. Container With Most Water", "Find the maximum area formed by two heights and the x-axis.",
"""class Solution {
    public int maxArea(int[] height) {
        int l = 0, r = height.length - 1, best = 0;
        while (l < r) {
            int area = (r - l) * Math.min(height[l], height[r]);
            best = Math.max(best, area);
            if (height[l] < height[r]) l++;
            else r--;
        }
        return best;
    }
}""", "O(n)", "O(1)", "Area is limited by the shorter wall, so move that wall inward in search of a taller one."),
("14. Trapping Rain Water", "Compute how much water can be trapped between elevation bars.",
"""class Solution {
    public int trap(int[] height) {
        int l = 0, r = height.length - 1;
        int leftMax = 0, rightMax = 0, water = 0;
        while (l < r) {
            if (height[l] <= height[r]) {
                leftMax = Math.max(leftMax, height[l]);
                water += leftMax - height[l];
                l++;
            } else {
                rightMax = Math.max(rightMax, height[r]);
                water += rightMax - height[r];
                r--;
            }
        }
        return water;
    }
}""", "O(n)", "O(1)", "Process the side with the lower current height; its trapped water is bounded by the maximum seen from that side.")
],
"03-Sliding-Window/problems-15-20.md": [
("15. Best Time to Buy and Sell Stock", "Maximize profit from one buy followed by one sell.",
"""class Solution {
    public int maxProfit(int[] prices) {
        int minPrice = Integer.MAX_VALUE, best = 0;
        for (int price : prices) {
            minPrice = Math.min(minPrice, price);
            best = Math.max(best, price - minPrice);
        }
        return best;
    }
}""", "O(n)", "O(1)", "Maintain the lowest price seen so far and the best profit from selling at each current price."),
("16. Longest Substring Without Repeating Characters", "Find the longest substring with all distinct characters.",
"""class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> last = new HashMap<>();
        int left = 0, best = 0;
        for (int right = 0; right < s.length(); right++) {
            char ch = s.charAt(right);
            if (last.containsKey(ch)) left = Math.max(left, last.get(ch) + 1);
            last.put(ch, right);
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}""", "O(n) average", "O(min(n, character set))", "The left boundary only moves forward, skipping past a repeated character's last index."),
("17. Longest Repeating Character Replacement", "Find the longest substring that can be made one repeated character using at most k replacements.",
"""class Solution {
    public int characterReplacement(String s, int k) {
        int[] freq = new int[26];
        int left = 0, maxFreq = 0, best = 0;
        for (int right = 0; right < s.length(); right++) {
            maxFreq = Math.max(maxFreq, ++freq[s.charAt(right) - 'A']);
            while ((right - left + 1) - maxFreq > k) {
                freq[s.charAt(left) - 'A']--;
                left++;
            }
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}""", "O(n)", "O(1)", "Window length minus its most frequent character count is the number of replacements needed. A historical max frequency is sufficient for this standard algorithm."),
("18. Permutation in String", "Return true if s2 contains a permutation of s1.",
"""class Solution {
    public boolean checkInclusion(String s1, String s2) {
        if (s1.length() > s2.length()) return false;
        int[] need = new int[26], window = new int[26];
        for (char c : s1.toCharArray()) need[c - 'a']++;
        int k = s1.length();
        for (int r = 0; r < s2.length(); r++) {
            window[s2.charAt(r) - 'a']++;
            if (r >= k) window[s2.charAt(r - k) - 'a']--;
            if (Arrays.equals(need, window)) return true;
        }
        return false;
    }
}""", "O(n × alphabet) with array comparison", "O(1)", "Maintain a fixed-size window and compare character counts. The alphabet size is constant (26)."),
("19. Minimum Window Substring", "Find the shortest substring of s containing every character in t with multiplicity.",
"""class Solution {
    public String minWindow(String s, String t) {
        if (t.length() == 0 || s.length() < t.length()) return "";
        Map<Character, Integer> need = new HashMap<>();
        for (char c : t.toCharArray()) need.put(c, need.getOrDefault(c, 0) + 1);

        int required = need.size(), formed = 0, left = 0;
        Map<Character, Integer> window = new HashMap<>();
        int bestLen = Integer.MAX_VALUE, bestStart = 0;

        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);
            window.put(c, window.getOrDefault(c, 0) + 1);
            if (need.containsKey(c) &&
                window.get(c).intValue() == need.get(c).intValue()) formed++;

            while (formed == required) {
                int len = right - left + 1;
                if (len < bestLen) {
                    bestLen = len;
                    bestStart = left;
                }
                char drop = s.charAt(left++);
                window.put(drop, window.get(drop) - 1);
                if (need.containsKey(drop) && window.get(drop) < need.get(drop)) formed--;
            }
        }
        return bestLen == Integer.MAX_VALUE ? "" : s.substring(bestStart, bestStart + bestLen);
    }
}""", "O(|s| + |t|) average", "O(|s| + |t|) map space", "Expand until all required character counts are satisfied, then shrink while the window remains valid."),
("20. Sliding Window Maximum", "Return the maximum value in each window of size k.",
"""class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums.length == 0 || k <= 0) return new int[0];
        int[] result = new int[nums.length - k + 1];
        Deque<Integer> dq = new ArrayDeque<>(); // indices, values decreasing
        for (int r = 0; r < nums.length; r++) {
            while (!dq.isEmpty() && dq.peekFirst() <= r - k) dq.pollFirst();
            while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[r]) dq.pollLast();
            dq.offerLast(r);
            if (r >= k - 1) result[r - k + 1] = nums[dq.peekFirst()];
        }
        return result;
    }
}""", "O(n)", "O(k)", "Keep indices in decreasing value order. The front is the current maximum; remove expired indices and dominated values.")
],
"04-Stack/problems-21-27.md": [
("21. Valid Parentheses", "Determine whether brackets are correctly opened and closed in order.",
"""class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '[' || c == '{') stack.push(c);
            else {
                if (stack.isEmpty()) return false;
                char open = stack.pop();
                if ((c == ')' && open != '(') ||
                    (c == ']' && open != '[') ||
                    (c == '}' && open != '{')) return false;
            }
        }
        return stack.isEmpty();
    }
}""", "O(n)", "O(n)", "Each closing bracket must match the most recent unmatched opening bracket."),
("22. Min Stack", "Support push, pop, top, and retrieving the minimum in constant time.",
"""class MinStack {
    private final Deque<Integer> values = new ArrayDeque<>();
    private final Deque<Integer> mins = new ArrayDeque<>();

    public void push(int val) {
        values.push(val);
        mins.push(mins.isEmpty() ? val : Math.min(val, mins.peek()));
    }
    public void pop() { values.pop(); mins.pop(); }
    public int top() { return values.peek(); }
    public int getMin() { return mins.peek(); }
}""", "O(1) per operation", "O(n)", "Store the minimum up to each stack position in a parallel stack. Assumes operations follow valid constraints."),
("23. Evaluate Reverse Polish Notation", "Evaluate an expression in postfix notation.",
"""class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (String token : tokens) {
            if (token.equals("+") || token.equals("-") ||
                token.equals("*") || token.equals("/")) {
                int b = stack.pop(), a = stack.pop();
                switch (token) {
                    case "+": stack.push(a + b); break;
                    case "-": stack.push(a - b); break;
                    case "*": stack.push(a * b); break;
                    default: stack.push(a / b); // Java truncates toward zero
                }
            } else stack.push(Integer.parseInt(token));
        }
        return stack.pop();
    }
}""", "O(n)", "O(n)", "Pop the right operand first, then the left operand. Division uses Java integer truncation toward zero."),
("24. Generate Parentheses", "Generate all valid strings containing n pairs of parentheses.",
"""class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        backtrack(result, new StringBuilder(), 0, 0, n);
        return result;
    }
    private void backtrack(List<String> out, StringBuilder path,
                           int open, int close, int n) {
        if (path.length() == 2 * n) {
            out.add(path.toString());
            return;
        }
        if (open < n) {
            path.append('(');
            backtrack(out, path, open + 1, close, n);
            path.deleteCharAt(path.length() - 1);
        }
        if (close < open) {
            path.append(')');
            backtrack(out, path, open, close + 1, n);
            path.deleteCharAt(path.length() - 1);
        }
    }
}""", "O(Catalan(n) × n)", "O(n) recursion, excluding output", "Add an opening bracket while fewer than n are used; add a closing bracket only when it will not exceed openings."),
("25. Daily Temperatures", "For each day, find how many days until a warmer temperature.",
"""class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] answer = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() &&
                   temperatures[i] > temperatures[stack.peek()]) {
                int prev = stack.pop();
                answer[prev] = i - prev;
            }
            stack.push(i);
        }
        return answer;
    }
}""", "O(n)", "O(n)", "The stack stores unresolved indices in non-increasing temperature order."),
("26. Car Fleet", "Count fleets reaching a target, given each car's position and speed.",
"""class Solution {
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;
        int[][] cars = new int[n][2];
        for (int i = 0; i < n; i++) {
            cars[i][0] = position[i];
            cars[i][1] = speed[i];
        }
        Arrays.sort(cars, (a, b) -> Integer.compare(b[0], a[0]));
        int fleets = 0;
        double slowestArrival = -1.0;
        for (int[] car : cars) {
            double arrival = (double) (target - car[0]) / car[1];
            if (arrival > slowestArrival) {
                fleets++;
                slowestArrival = arrival;
            }
        }
        return fleets;
    }
}""", "O(n log n)", "O(n)", "Process cars from nearest to target to farthest. A car that arrives no later than the fleet ahead joins that fleet."),
("27. Largest Rectangle in Histogram", "Find the largest rectangle under histogram bars.",
"""class Solution {
    public int largestRectangleArea(int[] heights) {
        Deque<Integer> stack = new ArrayDeque<>();
        int best = 0;
        for (int i = 0; i <= heights.length; i++) {
            int current = i == heights.length ? 0 : heights[i];
            while (!stack.isEmpty() && heights[stack.peek()] > current) {
                int h = heights[stack.pop()];
                int leftSmaller = stack.isEmpty() ? -1 : stack.peek();
                int width = i - leftSmaller - 1;
                best = Math.max(best, h * width);
            }
            stack.push(i);
        }
        return best;
    }
}""", "O(n)", "O(n)", "An increasing stack of indices identifies the first smaller bar on each side. A sentinel height flushes the stack.")
],
"05-Binary-Search/problems-28-34.md": [
("28. Binary Search", "Find target in a sorted array; return its index or -1.",
"""class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return -1;
    }
}""", "O(log n)", "O(1)", "Each step discards half of the remaining sorted search interval."),
("29. Search a 2D Matrix", "Search a row-wise and
