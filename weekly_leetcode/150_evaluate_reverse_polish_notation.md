# 150. Evaluate Reverse Polish Notation

## Problem Breakdown

Given an array of strings representing an arithmetic expression in Reverse Polish Notation (RPN), we need to evaluate this expression and return the result. Valid operators are +, -, *, and /. Each operand may be an integer or another expression.

### Example:
```
Input: tokens = ["2","1","+","3","*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9

Input: tokens = ["4","13","5","/","+"]
Output: 6
Explanation: (4 + (13 / 5)) = 6
```

### Constraints:
- 1 <= tokens.length <= 10⁴
- tokens[i] is either an operator or an integer in the range [-200, 200]
- Division between two integers always truncates toward zero
- No division by zero
- The answer is guaranteed to fit in a 32-bit integer

## Bloom's Taxonomy Level 1 (Remember/Knowledge)

### Stack Basics
1. What is a stack data structure?
   - Answer: A Last-In-First-Out (LIFO) data structure where elements are:
     * Added at the top (push)
     * Removed from the top (pop)

2. Why is a stack particularly useful for RPN?
   - Answer: It naturally handles the order of operations by storing operands until an operator is encountered.

### RPN Concepts
3. What is Reverse Polish Notation?
   - Answer: A mathematical notation where operators follow their operands
     * Regular: 3 + 4
     * RPN: 3 4 +

4. What are the advantages of RPN?
   - Answer:
     * No need for parentheses
     * No need for operator precedence rules
     * Easy to evaluate using a stack
     * Unambiguous expression evaluation

### Basic Operations
5. What are the key operations needed?
   - Answer:
     * Stack operations (push, pop)
     * String to integer conversion
     * Basic arithmetic operations
     * Integer division handling

### Edge Cases
6. What are the important edge cases?
   - Answer:
     * Single number
     * Negative numbers
     * Division with negative numbers
     * Division results that need truncation
     * Large numbers that might cause overflow

## Key Insight 

The core concept here is using a stack to process RPN expressions. The key insights are:
1. Numbers are pushed onto the stack
2. When an operator is encountered, pop two numbers and apply the operator
3. Push the result back onto the stack
4. The final answer will be the only number left on the stack

What makes this problem interesting:
- Need to handle division truncation toward zero
- Need to process operators in the correct order
- Need to manage the stack efficiently
- Need to handle negative numbers correctly

## Optimized Solution

### Approach: Stack-based Evaluation
We'll use a stack to keep track of numbers and process operators:

1. Initialize empty stack
2. For each token:
   - If token is number, push to stack
   - If token is operator:
     * Pop two numbers (b then a)
     * Apply operator (a operator b)
     * Push result back to stack
3. Return final number in stack

### Python Implementation:
```python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        stack = []
        
        # Dictionary of operations
        operations = {
            '+': lambda a, b: a + b,
            '-': lambda a, b: a - b,
            '*': lambda a, b: a * b,
            '/': lambda a, b: int(a / b)  # Truncates toward zero
        }
        ''' in javascript 
        lambda is anonymous function, ( lambda a , b : a + b ) == ( (a,b ) => a + b )
        const operations = {
          '+': (a, b) => a + b,
          '-': (a, b) => a - b,
          '*': (a, b) => a * b,
          '/': (a, b) => Math.trunc(a / b)  // Truncates toward zero
        };
        '''
        
        for token in tokens:
            if token in operations:
                # Pop operands in reverse order
                b = stack.pop()
                a = stack.pop()
                # Apply operation and push result
                result = operations[token](a, b)
                stack.append(result)
            else:
                # Push number to stack
                stack.append(int(token))
        
        return stack[0]
```

### Alternative Implementation (More Explicit):
```python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        stack = []
        
        for token in tokens:
            if token not in {'+', '-', '*', '/'}:
                stack.append(int(token))
                continue
                
            b = stack.pop()
            a = stack.pop()
            
            if token == '+':
                stack.append(a + b)
            elif token == '-':
                stack.append(a - b)
            elif token == '*':
                stack.append(a * b)
            else:  # division
                # Handle division truncation toward zero
                stack.append(int(a / b))
        
        return stack[0]
```

### Complexity Analysis:
- Time Complexity: O(n) where n is the length of tokens
  - We process each token exactly once
- Space Complexity: O(n)
  - Stack can grow up to n/2 size in worst case
  - This happens when we have all numbers before operators

## How This Problem is Reused

### 1. Common Patterns
- Stack-based evaluation pattern
- Expression parsing pattern
- Operator processing pattern
- LIFO data structure usage

### 2. Related LeetCode Problems
- 224: Basic Calculator
  - Similar but with infix notation and parentheses
- 227: Basic Calculator II
  - Handles operator precedence
- 772: Basic Calculator III
  - Combines concepts from I and II
- 394: Decode String
  - Uses stack for nested structures

### 3. Real-world Applications
- Calculator implementations
- Expression evaluators in programming languages
- Compiler design (parsing expressions)
- Spreadsheet formula evaluation
- Mathematical software
- Command-line calculators

### 4. Key Takeaways
1. Stack is perfect for expression evaluation
2. Order of operands matters in non-commutative operations
3. Integer division requires special handling
4. RPN eliminates need for operator precedence
5. Breaking down complex expressions into simple operations

### 5. Common Mistakes to Avoid
1. Forgetting to convert strings to integers
2. Incorrect order of operands in subtraction/division
3. Not handling division truncation properly
4. Forgetting to handle negative numbers
5. Not considering stack underflow (invalid expressions)