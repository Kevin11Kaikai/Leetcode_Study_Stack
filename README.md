# Leetcode_Study_Stack  
# Leetcode 20: Valid Parentheses

## Problem Summary

Given a string `s` containing only the characters:

```
( ) [ ] { }
```

Determine whether the input string is **valid**.

A string is considered valid if:

1. Every **opening bracket** has a corresponding **closing bracket**.
2. The **types** of brackets match:  
   `(` with `)`, `[` with `]`, `{` with `}`.
3. Brackets are closed in the **correct order**.

---

## Examples

```text
"()"        -> True
"()[]{}"    -> True
"(]"        -> False
"([)]"      -> False
"((("       -> False
```

---

## Core Insight (Problem Essence)

This problem is **not about counting brackets**.  
It is about **order and nesting**.

> The key rule is:  
> **At any moment, you may only close the most recently opened but not yet closed bracket.**

This makes **“recency”** the critical piece of information.

---

## Why This Is a Stack Problem

A **stack** naturally models this requirement:

- **Last In, First Out (LIFO)**
- The most recently opened bracket must be closed first

### Stack Semantics in This Problem

| Bracket Action         | Stack Operation   |
| ---------------------- | ----------------- |
| See an opening bracket | `push`            |
| See a closing bracket  | `pop` and compare |
| No bracket to pop      | Invalid           |
| Stack empty at end     | Valid             |

---

## Stack Representation in Python

Python does not have a built-in `Stack` class.  
We use a `list` with disciplined usage:

| Stack Concept | Python List |
| ------------- | ----------- |
| push          | `append(x)` |
| pop           | `pop()`     |
| peek (top)    | `stack[-1]` |
| empty         | `not stack` |

---

## Algorithm (High-Level)

1. Initialize an empty stack.
2. Create a mapping from **closing → opening** brackets.
3. Iterate through the string:

   - If the character is a **closing bracket**:
     - If the stack is empty → invalid.
     - Pop the top element and check if it matches.
   - Otherwise, it is an **opening bracket** → push onto stack.

4. After processing all characters:
   - If the stack is empty → valid.
   - Otherwise → invalid (unclosed brackets remain).

---

## Critical Correctness Principle

> **Algorithm correctness = process correctness + final state correctness**

- Catch **illegal actions during traversal**
- Also verify that **all opened brackets were eventually closed**

This is why a final check on the stack is mandatory.

---

## Implementation

```python
class Solution(object):
    def isValid(self, s):
        stack = []
        match = {')': '(', ']': '[', '}': '{'}

        for ch in s:
            if ch in match:                 # closing bracket
                if not stack:               # nothing to close
                    return False
                top = stack.pop()           # close most recent
                if top != match[ch]:        # type mismatch
                    return False
            else:                            # opening bracket
                stack.append(ch)

        return not stack                    # no unclosed brackets
```

---

## Step-by-Step Example

### Input: `"([)]"`

| Character | Stack Before | Action             | Stack After  |
| --------- | ------------ | ------------------ | ------------ |
| `(`       | `[]`         | push               | `['(']`      |
| `[`       | `['(']`      | push               | `['(', '[']` |
| `)`       | `['(', '[']` | pop `[` → mismatch | ❌ False      |

---

### Input: `"((("`

| Character | Stack             |
| --------- | ----------------- |
| `(`       | `['(']`           |
| `(`       | `['(', '(']`      |
| `(`       | `['(', '(', '(']` |

End of string → stack not empty → ❌ False

---

## Common Pitfalls (Learned from Debugging)

### 1. Calling non-existent APIs

```python
stack.top()     # ❌ invalid in Python
```

### 2. Treating dictionaries like functions

```python
match(ch)       # ❌ dict is not callable
match[ch]       # ✅ correct
```

### 3. Forgetting the final stack check

```python
return True       # ❌ incorrect
return not stack # ✅ correct
```

---

## Time Complexity

- Each character is pushed and popped **at most once**
- **O(n)** time, where `n` is the length of `s`

---

## Space Complexity

- Stack stores at most `n` opening brackets
- **O(n)** space in the worst case

---

## Key Takeaways

- This is a **nesting + order** problem, not a counting problem
- Stack is the only natural structure that preserves **“most recent unfinished state”**
- Always:
  - Validate **before mutating state**
  - Validate the **final state**
- `stack` represents **unfinished work**, not just data

---

## Transferable Pattern

Use a **stack** when a problem requires:

> “Always resolve the most recent unfinished action first.”

Examples:

- Function call stacks
- Expression evaluation
- HTML/XML tag validation
- Undo/Redo systems
- Nested structure parsing

---

## Follow-up Ideas

- Expression evaluation (`2 * (3 + 4)`)
- Infix → Postfix conversion
- Monotonic stack problems (Leetcode 739, 84, 402)
- Understanding recursion via the call stack
