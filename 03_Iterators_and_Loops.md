# Iterators and Loops in JavaScript

## The "while" loop

*   The `while` loop has the following syntax:
*   While the condition is truthy, the code from the loop body is executed.
*   A single execution of the loop body is called an iteration.

```javascript
// Example: Count from 1 to 5
let count = 1;

while (count <= 5) {
  console.log("Iteration number:", count);
  count++; // Increment the counter to avoid an infinite loop
}
```

Explanation:

*   `let count = 1;` → Initializes the counter variable.
*   `while (count <= 5)` → Condition: Continue looping as long as it is less than or equal to 5.
*   `console.log(...)` → Prints the current iteration number.
*   `count++` → Increments the counter by 1 in each iteration to eventually exit the loop.

## The "do..while" loop

*   The condition check can be moved below the loop body using the `do..while` syntax:
*   The loop will first execute the body, then check the condition, and, while it's truthy, execute it again and again.
*   This form of syntax should only be used when you want the body of the loop to execute at least once regardless of the condition being truthy.

```javascript
// Example: Prompt for a number greater than 0
let number;

do {
  number = parseInt(prompt("Enter a number greater than 0:"), 10);
  console.log("You entered:", number);
} while (number <= 0);
```

Explanation:

With `do..while`, the code block inside `do { ... }` will run at least once, and then the condition in `while(...)` is checked.

In the example above:

*   The user will be prompted to enter a number at least once.
*   If the entered number is <= 0, the loop will continue.
*   If the entered number is > 0, the loop will stop.

## The "for" loop

*   The `for` loop is more complex, but it's also the most commonly used loop.

Basic syntax:

```javascript
for (initialization; condition; step) {
  // Code block
}
```

*   `initialization`: Initializes the counter variable (runs once at the beginning).
*   `condition`: Condition to continue the loop (if true, continue; if false, stop).
*   `step`: Command to run after each iteration (usually incrementing or decrementing the counter).

```javascript
// Example: Count from 1 to 5
for (let i = 1; i <= 5; i++) {
  console.log("Iteration number:", i);
}
```

Advantages:

*   More concise than `while` or `do..while` because it can write the initialization, condition, and step in one line.
*   Easy to use for loops with a predetermined number of iterations.

## Skipping parts

*   Any part of `for` can be skipped.
*   For example, we can omit `begin` if we don’t need to do anything at the loop start.

```javascript
// Example 1: Skip begin
let i = 1; // Initialize outside

for (; i <= 3; i++) {
  console.log("Iteration:", i);
}
```

```javascript
// Example 2: Skip step
for (let j = 1; j <= 3;) {
  console.log("Iteration:", j);
  j++; // Manually increment in the loop body
}
```

```javascript
// Example 3: Skip both begin and step
let k = 1;

for (; k <= 3;) {
  console.log("Iteration:", k);
  k++;
}
```

```javascript
// Example 4: Skip all (for(;;)) → Infinite loop
for (;;) {
  console.log("Running forever...");
  break; // Need break to exit, otherwise it will run indefinitely
}
```

In summary:

*   `begin` can be moved outside the loop.
*   `step` can be written in the loop body.
*   If `condition` is omitted, it defaults to true → easily becomes an infinite loop.

## Breaking the loop

*   Normally, a loop exits when its condition becomes falsy.
*   But we can force the exit at any time using the special `break` directive.

```javascript
// Example 1: Stop when a value is found
let numbers = [3, 7, 12, 9, 5];

for (let i = 0; i < numbers.length; i++) {
  console.log("Checking:", numbers[i]);

  if (numbers[i] === 12) {
    console.log("Found 12, exiting the loop!");
    break; // Stop immediately
  }
}
```

After `break`, the loop ends immediately, without checking the remaining elements.

```javascript
// Example 2: Use with while
let count = 1;

while (true) { // Infinite loop
  console.log("Iteration:", count);
  if (count === 3) {
    break; // Stop after printing 3 times
  }
  count++;
}
```

Key points:

*   `break` → stops the loop completely.
*   If you only want to skip the current iteration but continue the loop, use `continue`.

## Continue to the next iteration

*   The `continue` directive is a “lighter version” of `break`. It doesn’t stop the whole loop. Instead, it stops the current iteration and forces the loop to start a new one (if the condition allows).
*   We can use it if we’re done with the current iteration and would like to move onto the next one.

`break` → always exits the entire loop.

`continue` → only skips the remainder of the current iteration, then returns to check the condition for the next iteration.

```javascript
// Example 1: Skip even numbers, only print odd numbers
for (let i = 1; i <= 5; i++) {
  if (i % 2 === 0) {
    continue; // Skip even numbers
  }
  console.log("Odd number:", i);
}
```

```javascript
// Example 2: Use in while
let n = 0;

while (n < 5) {
  n++;

  if (n === 3) {
    continue; // Skip when n = 3
  }

  console.log("Value:", n);
}
```

In summary:

*   `break` → stops the entire loop.
*   `continue` → skips the current iteration, jumps to the next iteration.

## Summary

*   We covered 3 types of loops:
    *   `while` – The condition is checked before each iteration.
    *   `do..while` – The condition is checked after each iteration.
    *   `for (;;) `– The condition is checked before each iteration, additional settings available.
*   To make an “infinite” loop, usually the `while(true)` construct is used. Such a loop, just like any other, can be stopped with the `break` directive.
*   If we don’t want to do anything in the current iteration and would like to forward to the next one, we can use the `continue` directive.



Không có tệp nào được chọnKhông có tệp nào được chọn
ChatGPT có thể mắc lỗi. Hãy kiểm tra các thông tin quan trọng.
