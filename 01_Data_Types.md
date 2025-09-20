# JavaScript Data Types

## var

### Usage

The `var` keyword is almost the same as `let`.

### Differences

*   `var` has no block scope.
*   `var` tolerates redeclarations.
*   `var` variables can be declared below their use (hoisting).

### Example

#### Block scope

```javascript
{
  var x = 1;
  let y = 2;
}

console.log(x); // ✅ 1 (var không bị giới hạn trong block)
// console.log(y); // ❌ ReferenceError (let chỉ tồn tại trong block)
```

#### Redeclaration

```javascript
var a = 10;
var a = 20;  // ✅ Hợp lệ, var cho phép khai báo lại
console.log(a); // 20
```

```javascript
let b = 10;
// let b = 20; // ❌ SyntaxError: Identifier 'b' has already been declared
```

#### Hoisting

```javascript
console.log(m); // ✅ undefined (var được hoisted nhưng gán sau)
var m = 5;

// console.log(n); // ❌ ReferenceError (let cũng hoisted nhưng ở TDZ)
let n = 5;
```

### Summary

`var` is more flexible but less safe and can easily cause logic errors.

`let` (and `const`) should be used in ES6+ because they are stricter and make the code easier to read.

## const

### Usage

To declare a constant variable, use the `const` keyword. Variables declared using `const` are called “constants”. They cannot be reassigned. Constants are named using capital letters and underscores.

### Example

#### Declaring a constant

```javascript
const PI = 3.14159;
console.log(PI); // 3.14159
```

#### Cannot reassign

```javascript
const GREETING = "Hello";
// GREETING = "Hi"; // ❌ TypeError: Assignment to constant variable
```

#### Naming convention

```javascript
const MAX_USERS = 100;
const API_KEY = "12345-ABCDE";
```

#### const with object and array

`const` only prevents changing the reference, but the contents of the object/array can still be modified.

##### Array

```javascript
const NUMBERS = [1, 2, 3];
NUMBERS.push(4); // ✅ cho phép
console.log(NUMBERS); // [1, 2, 3, 4]

// NUMBERS = [10, 20]; // ❌ Không được gán lại mảng khác
```

##### Object

```javascript
const USER = { name: "Alice", age: 25 };
USER.age = 26; // ✅ thay đổi property
console.log(USER); // { name: "Alice", age: 26 }

// USER = { name: "Bob" }; // ❌ Không được gán object khác
```

### Summary

*   Must be assigned a value when declared.
*   Cannot be reassigned.
*   Usually used for constants or immutable object/array references.

## Comparison of var, let, and const

| Property            | var                                  | let                       | const                         |
| ------------------- | ------------------------------------ | ------------------------- | ----------------------------- |
| Scope               | Function scope (no block scope)      | Block scope             | Block scope                   |
| Hoisting            | Yes, initialized with undefined      | Yes, but in TDZ         | Yes, but in TDZ               |
| Redeclaration       | ✅ Allowed                           | ❌ Not allowed            | ❌ Not allowed                |
| Reassignment        | ✅ Allowed                           | ✅ Allowed            | ❌ Not allowed                |
| Initialization      | ❌ Not required                       | ❌ Not required            | ✅ Required                    |
| Use Cases           | Old code (ES5 and earlier)           | Mutable variables         | Constants, immutable references |
| Example             | `var x = 1;`                         | `let y = 2;`             | `const PI = 3.14;`           |

### Best Practices

*   Avoid using `var` (prone to logic errors).
*   Use `let` for mutable variables.
*   Use `const` by default for variables that do not need to be changed.

## Number

### Usage

The number type represents both integer and floating point numbers. Besides regular numbers, there are so-called “special numeric values” which also belong to this data type: `Infinity`, `-Infinity` and `NaN`.

*   `Infinity` represents the mathematical Infinity ∞. It is a special value that’s greater than any number.
*   `NaN` represents a computational error. It is a result of an incorrect or an undefined mathematical operation.

### Example

```javascript
let intNum = 42;        // số nguyên
let floatNum = 3.14;    // số thực
```

### Special values

#### Infinity and -Infinity

Represents infinity (∞).

Greater than any number (`Infinity`), less than any number (`-Infinity`).

```javascript
console.log(1 / 0);    // Infinity
console.log(-1 / 0);   // -Infinity
console.log(Infinity > 1000000); // true
```

#### NaN (Not-a-Number)

Occurs when an operation is invalid or undefined.

```javascript
console.log("hello" / 2);   // NaN (không thể chia chuỗi cho số)
console.log(0 / 0);         // NaN
```

#### Notes

*   `NaN` is of type `Number`.
*   `NaN` is not equal to itself.

```javascript
console.log(NaN === NaN); // false
console.log(isNaN(NaN));  // true
```

### Summary

`Number` includes both regular numbers and special values (`Infinity`, `-Infinity`, `NaN`).

When working with mathematics, use `Number.isNaN()` and `Number.isFinite()` to check safely.

## BigInt

### Usage

BigInt type is used to represent extremely large integers (larger than the safe range of Number).

Normally, Number in JS is only safe in the range:

`-(2^53 - 1)` to `(2^53 - 1)`

that is, about -9,007,199,254,740,991 to 9,007,199,254,740,991.

With BigInt, we can represent arbitrarily long integers.

### Creating BigInt

Add the suffix `n` after the integer:

```javascript
const big = 1234567890123456789012345678901234567890n;
console.log(big);
```

Or use the function constructor:

```javascript
const big2 = BigInt("1234567890123456789012345678901234567890");
console.log(big2);
```

### Operations with BigInt

```javascript
const a = 1000000000000000000000n;
const b = 2000000000000000000000n;

console.log(a + b); // 3000000000000000000000n
console.log(b - a); // 1000000000000000000000n
console.log(a * b); // 2000000000000000000000000000000000000000n
console.log(b / a); // 2n (chia lấy phần nguyên)
```

### Notes

Do not mix Number and BigInt directly in operations:

```javascript
const big = 10n;
const num = 5;
// console.log(big + num); // ❌ TypeError
console.log(big + BigInt(num)); // ✅ 15n
```

`Math` does not support BigInt:

```javascript
// Math.sqrt(16n); // ❌ Error
```

### Summary

*   Use BigInt when you need very large integers (e.g., encryption, super-precise timestamps).
*   Not suitable for floating point numbers.

## String

### Usage

A string in JavaScript must be surrounded by quotes. In JavaScript, there are 3 types of quotes:

*   Double quotes: `"Hello"`.
*   Single quotes: `'Hello'`.
*   Backticks: `` `Hello` ``.

Backticks are “extended functionality” quotes. They allow us to embed variables and expressions into a string by wrapping them in `${...}`. The expression inside `${...}` is evaluated and the result becomes a part of the string. We can put anything in there: a variable like `name` or an arithmetical expression like `1 + 2` or something more complex.

### Example

```javascript
let name = "Alice";
let age = 25;

console.log(`Tên: ${name}, Tuổi: ${age}`);
// Tên: Alice, Tuổi: 25
```

### Backticks (Template literals)

Backticks allow:

*   Easy line breaks:

```javascript
let text = `Dòng 1
Dòng 2
Dòng 3`;
console.log(text);
```

*   Insert variables and expressions with `${...}`:

```javascript
let name = "Alice";
let age = 25;

console.log(`Tên: ${name}, Tuổi: ${age}`);
// Tên: Alice, Tuổi: 25
```

*   Insert complex expressions:

```javascript
console.log(`1 + 2 = ${1 + 2}`); // 1 + 2 = 3

function greet(user) {
  return `Xin chào, ${user}!`;
}
console.log(greet("Bob")); // Xin chào, Bob!
```

### Notes

`"..."` and `'...'` are only for creating simple strings.

`` `...` `` (backticks) are more powerful, use when you need:

*   Variable interpolation (`${...}`)
*   Multi-line string

### Summary

*   `"..."` and `'...'` = basic string.
*   `` `...` `` = advanced string (used a lot in modern ES6+ code).

## Null

### Usage

The special `null` value does not belong to any of the types described above. In JavaScript, `null` is not a “reference to a non-existing object” or a “null pointer” like in some other languages. It’s just a special value which represents “nothing”, “empty” or “value unknown”.

### Example

```javascript
let age = null;
console.log(age); // null (chưa có giá trị tuổi)
```

### Notes

#### Type of null

```javascript
console.log(typeof null); // "object" ❗ (đây là bug lịch sử của JS, ai cũng biết)
```

Many new developers mistakenly think that `null` is an object, but it is not.

#### Comparing null

```javascript
console.log(null == undefined);  // true (so sánh lỏng lẻo)
console.log(null === undefined); // false (so sánh nghiêm ngặt)
```

#### Different from undefined

*   `undefined`: variable has not been assigned a value.
*   `null`: variable has been assigned, but has an empty/nothing value.

```javascript
let a;
let b = null;

console.log(a); // undefined
console.log(b); // null
```

### Summary

*   `null` = “no value”, “empty”, or “unknown”.
*   Use `null` when intentionally wanting to reset the value to “nothing”.
*   Don't confuse with `undefined`.

### Use cases in frontend

`null` is often used to represent the state of "no data" or "empty" intentionally.

#### 1. State in React (or other framework)

Example: when waiting for data from the API.

```javascript
function UserProfile() {
  const [user, setUser] = React.useState(null); // initially no data

  React.useEffect(() => {
    fetch("/api/user/1")
      .then(res => res.json())
      .then(data => setUser(data));
  }, []);

  if (user === null) {
    return <p>Loading...</p>; // display when no data
  }

  return <div>Tên: {user.name}</div>;
}
```

Here `null` is used to represent the state of no data.

#### 2. Initially empty form input

Example: a form that allows you to enter a date of birth.

```javascript
let birthDate = null; // default no value

function handleInput(e) {
  birthDate = e.target.value || null;
}
```

Use `null` instead of `""` to distinguish between "not entered" and "empty string".

#### 3. Conditional rendering

```javascript
const [selectedProduct, setSelectedProduct] = useState(null);

return (
  <div>
    {selectedProduct === null
      ? <p>Chưa chọn sản phẩm</p>
      : <ProductDetail product={selectedProduct} />}
  </div>
);
```

`null` here indicates nothing has been selected.

#### 4. Reset the value to "nothing"

Example: when a user deletes an avatar.

```javascript
let avatarUrl = "https://example.com/avatar.png";

// User deletes avatar
avatarUrl = null;

// Send to server: { avatarUrl: null }
```

`null` here means there is an avatar, but it has been deleted.

#### 5. API data returned

APIs often use `null` to report that a certain field has no value:

```json
{
  "id": 1,
