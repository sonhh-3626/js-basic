# Array methods in JavaScript

## Splice

The `splice()` method changes the contents of an array by removing or replacing existing elements and/or adding new elements in place.

*   It modifies the array starting from the index `start`: removes `deleteCount` elements and then inserts `elem1, ..., elemN` at their place.
*   Returns the array of removed elements

**Syntax:**

```javascript
array.splice(start, deleteCount, item1, item2, ..., itemN)
```

`start`: index to start from.

`deleteCount`: Number of elements to delete (0 if only adding).

`item1, item2...`: Elements to add at the start position.

**Example:**

```javascript
let fruits = ["🍎", "🍌", "🍇", "🍉"];

// Xóa 1 phần tử từ index 1
let removed = fruits.splice(1, 1);
console.log(fruits);   // ["🍎", "🍇", "🍉"]
console.log(removed);  // ["🍌"]

// Thêm 2 phần tử tại index 2
fruits.splice(2, 0, "🍒", "🥭");
console.log(fruits);   // ["🍎", "🍇", "🍒", "🥭", "🍉"]

// Thay thế 1 phần tử tại index 0
fruits.splice(0, 1, "🍍");
console.log(fruits);   // ["🍍", "🍇", "🍒", "🥭", "🍉"]
```

**Questions:**

*   What if `deleteCount` is greater than the number of elements remaining in the array?
*   Does `splice()` create a new array or modify the original array?
*   If you only want to add an element without deleting anything, what is the correct syntax?

**Use case:**

Todo list: Delete or change the status of a task.

Student list: Add a new student to a specific position.

Carousel: Replace or insert a new image at a specific position.

```javascript
let tasks = ["Wash dishes", "Do homework", "Clean room"];
// Hoàn thành task thứ 2 và thay thế nó bằng 'Homework done'
tasks.splice(1, 1, "Homework done");
console.log(tasks); // ["Wash dishes", "Homework done", "Clean room"]
```

**Common bugs:**

*   Forgetting that `splice()` modifies the original array.
*   Using the wrong index.
*   Deleting too many elements.

**Practice:**

1.  Delete element

```javascript
let colors = ["red", "green", "blue", "yellow"];
// Xóa "green" khỏi mảng
```

2.  Add element

```javascript
let numbers = [1, 2, 4, 5];
// Thêm số 3 vào đúng vị trí để mảng có thứ tự tăng dần
```

3.  Replace element

```javascript
let animals = ["cat", "dog", "bird"];
// Thay "dog" bằng "hamster"
```

## Slice

The `slice()` method returns a shallow copy of a portion of an array into a new array object selected from start to end (end not included) where start and end represent the index of items in that array.

*   Both start and end can be negative, in that case position from array end is assumed.
*   The original array will not be modified.

**Syntax:**

```javascript
array.slice(start, end)
```

`start`: index to start from (inclusive). If negative, counts from the end of the array.

`end`: index to end at (exclusive). If negative, counts from the end of the array.

**Example:**

```javascript
let fruits = ["🍎", "🍌", "🍇", "🍉", "🥭"];

// Lấy phần tử từ index 1 đến 3 (không bao gồm index 3)
let subFruits = fruits.slice(1, 3);
console.log(subFruits);  // ["🍌", "🍇"]
console.log(fruits);     // ["🍎", "🍌", "🍇", "🍉", "🥭"]  => không thay đổi

// Dùng chỉ số âm
let lastTwo = fruits.slice(-2);
console.log(lastTwo);    // ["🍉", "🥭"]

// Lấy toàn bộ mảng
let copyFruits = fruits.slice();
console.log(copyFruits); // ["🍎", "🍌", "🍇", "🍉", "🥭"]
```

**Questions:**

*   If end is not passed, where does slice() take it to?
*   Does slice() change the original array?
*   When should I use slice() instead of splice()?

**Use case:**

Copy array: to copy an array without affecting the original array.

Pagination: get a number of elements from the array to display the page.

Undo/Redo: save the previous state of the list without changing the original.

```javascript
let messages = ["Hi", "Hello", "How are you?", "Bye"];
// Lấy 2 tin nhắn đầu tiên để hiển thị preview
let preview = messages.slice(0, 2);
console.log(preview); // ["Hi", "Hello"]
```

**Common bugs:**

*   Confusing with splice() → thinking slice() deletes elements but it doesn't actually change the original array.
*   Negative index is not understood correctly.
*   end does not include the element at index.

**Practice:**

1.  Get element

```javascript
let colors = ["red", "green", "blue", "yellow", "purple"];
// Lấy 2 phần tử cuối cùng
```

2.  Copy array

```javascript
let numbers = [1, 2, 3, 4, 5];
// Tạo bản sao của mảng numbers
```

3.  Pagination

```javascript
let items = ["item1", "item2", "item3", "item4", "item5"];
// Lấy 3 phần tử đầu tiên để hiển thị trang 1
```

## Concat

The `concat()` method is used to merge two or more arrays.

*   This method does not change the existing arrays, but instead returns a new array.
*   It accepts any number of arguments – either arrays or values.

**Syntax:**

```javascript
newArray = array1.concat(array2, array3, ..., value1, value2, ...)
```

Accepts any number of parameters: array or value.

Returns a new array.

The original array is not changed.

**Example:**

```javascript
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];

// Gộp 2 mảng
let merged = arr1.concat(arr2);
console.log(merged);  // [1, 2, 3, 4, 5, 6]
console.log(arr1);    // [1, 2, 3]  => mảng gốc không thay đổi

// Thêm giá trị trực tiếp
let extended = arr1.concat(7, 8);
console.log(extended); // [1, 2, 3, 7, 8]

// Nối nhiều mảng và giá trị
let combined = arr1.concat(arr2, [7, 8], 9);
console.log(combined); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**Questions:**

*   Does concat() change the original array?
*   If you pass a non-array value, how does concat() handle it?
*   Compare concat() with push() when adding multiple elements.

**Use case:**

Concatenating lists: for example, concatenating a list of products from multiple sources.

Clone and extend array: create a copy and add new data.

Merge data: in the API, get multiple result arrays and merge them into one array.

```javascript
let morningTasks = ["brush teeth", "have breakfast"];
let eveningTasks = ["exercise", "read book"];

let allTasks = morningTasks.concat(eveningTasks);
console.log(allTasks); // ["brush teeth", "have breakfast", "exercise", "read book"]
```

**Common bugs:**

*   Confusing with push() → push() changes the original array, concat() does not.
*   Passing nested arrays → concat() does not automatically flatten, only merges level 1.
*   Not recognizing single values vs arrays.

**Practice:**

1.  Merge array

```javascript
let arr1 = ["apple", "banana"];
let arr2 = ["cherry", "date"];
// Gộp arr1 và arr2 thành một mảng mới
```

2.  Add value

```javascript
let nums = [1,2,3];
// Thêm các số 4,5,6 vào nums mà không thay đổi nums
```

3.  Concatenate multiple arrays and values

```javascript
let a = [10, 20];
let b = [30, 40];
let c = [50];
// Nối a, b, c và thêm giá trị 60
```

## forEach

The `forEach()` method allows you to run a function for every element of the array.

**Syntax:**

```javascript
array.forEach(callback(currentValue, index, array))
```

`callback`: function to be called for each element.

`currentValue`: current element value.

`index` (optional): current element index.

`array` (optional): original array.

Does not return a new array.

Changes the original array if the callback changes the value.

**Example:**

```javascript
let fruits = ["🍎", "🍌", "🍇"];

// In ra từng phần tử
fruits.forEach(function(fruit, index) {
  console.log(`${index}: ${fruit}`);
});
// 0: 🍎
// 1: 🍌
// 2: 🍇
```

Using arrow function:

```javascript
fruits.forEach((fruit) => console.log(fruit.toUpperCase()));
// 🍎
// 🍌
// 🍇
```

Changing the original array:

```javascript
fruits.forEach((fruit, i, arr) => {
  arr[i] = fruit + "🍒";
});
console.log(fruits); // ["🍎🍒", "🍌🍒", "🍇🍒"]
```

**Questions:**

*   Does forEach() return a new array? If you want to create a new array, what do you use?
*   Can forEach() be used with break or return to stop the loop?
*   Compare forEach() with for...of and map() in terms of flexibility.

**Use case:**

Display list: print a list of products or articles to the interface.

Update data: change the status of each item in the array.

Calculation: perform calculations with each element (no new array needed).

```javascript
let tasks = ["Wash dishes", "Do homework", "Clean room"];
tasks.forEach((task, i) => console.log(`Task ${i+1}: ${task}`));
```

**Common bugs:**

*   Confusing with map() → forEach() does not return a new array.
*   Cannot stop the loop → cannot break in the middle, need some() or for...of.
*   Unintentionally changing the original array.

**Practice:**

1.  Print element

```javascript
let colors = ["red", "green", "blue"];
// Dùng forEach in ra "Color: red", ...
```

2.  Change array

```javascript
let nums = [1,2,3];
// Nhân mỗi phần tử với 2 bằng forEach
```

3.  Use index

```javascript
let names = ["Alice", "Bob", "Charlie"];
// In ra "1: Alice", "2: Bob", ...
```

## indexOf, includes

The methods `arr.indexOf` and `arr.includes` have the similar syntax and do essentially the same as their string counterparts, but operate on items instead of characters:

*   `arr.indexOf(item, from)` – looks for item starting from index from, and returns the index where it was found, otherwise -1.
*   `arr.includes(item, from)` – looks for item starting from index from, returns true if found.
*   The method `arr.lastIndexOf` is the same as indexOf, but looks for from right to left.

**Syntax:**

```javascript
arr.indexOf(item, from)
arr.includes(item, from)
arr.lastIndexOf(item, from)
```

**Example:**

```javascript
let fruits = ["🍎", "🍌", "🍇", "🍌"];

// indexOf
console.log(fruits.indexOf("🍌"));     // 1
console.log(fruits.indexOf("🍌", 2));  // 3
console.log(fruits.indexOf("🍉"));     // -1

// lastIndexOf
console.log(fruits.lastIndexOf("🍌")); // 3

// includes
console.log(fruits.includes("🍌"));    // true
console.log(fruits.includes("🍉"));    // false
```

**Questions:**

*   When should I use includes() instead of indexOf()?
*   Does indexOf() return correctly if the element is an object?
*   If the array has multiple identical elements, what do indexOf() and lastIndexOf() return?

**Use case:**

Check existence: check if the product is already in the cart.

Find position: get the index to update the value or delete the element.

Avoid duplicates: add a new element if it is not already in the array.

```javascript
let cart = ["apple", "banana"];
if (!cart.includes("apple")) {
  cart.push("apple");
} else {
  console.log("Item already in cart!");
}
```

**Common bugs:**

*   Confusing object and primitive
*   Index from is not clear.
*   Using includes to get the index.

**Practice:**

1.  indexOf

```javascript
let colors = ["red", "green", "blue", "green"];
// Tìm index đầu tiên và cuối cùng của "green"
```

2.  includes

```javascript
let nums = [1, 2, 3, 4];
// Kiểm tra xem số 3 có trong mảng không
```

3.  lastIndexOf

```javascript
let letters = ["a","b","c","b","d"];
// Tìm index cuối cùng của "b"
```

## Find

The `find()` method returns the first element in the provided array that satisfies the provided testing function. If no values satisfy the testing function, undefined is returned.

**Syntax:**

```javascript
arr.find(callback(element, index, array))
```

`callback`: function to test the condition.

`element`: current element.

`index` (optional): element index.

`array` (optional): original array.

Returns: the first element that satisfies the condition, or undefined if not found.

Does not change the original array.

**Example:**

```javascript
let numbers = [5, 12, 8, 130, 44];

// Tìm phần tử đầu tiên > 10
let found = numbers.find((num) => num > 10);
console.log(found); // 12

// Không tìm thấy
let notFound = numbers.find((num) => num > 200);
console.log(notFound); // undefined

// Sử dụng index
let foundIndex = numbers.find((num, idx) => idx % 2 === 0 && num > 10);
console.log(foundIndex); // 12 (index 1 là số 12, index 0 là 5)
```

**Questions:**

*   What does find() return if no element satisfies the condition?
*   Does find() create a new array?
*   Compare find() and filter(): when to use which?

**Use case:**

Find object in array: for example, find user by ID.

Filter by special condition: find the first order that exceeds a certain amount.

Process data from API: find the first element that meets the criteria.

```javascript
let users = [
  {id:1, name:"Alice"},
  {id:2, name:"Bob"},
  {id:3, name:"Charlie"}
];

let user = users.find(u => u.id === 2);
console.log(user); // {id:2, name:"Bob"}
```

**Common bugs:**

*   Confusing with filter() → find() only returns 1 element, filter() returns an array of all elements that satisfy the condition.
*   Returns undefined if not found.
*   Comparing objects.

**Practice:**

1.  Find number

```javascript
let nums = [3, 7, 10, 15];
// Tìm số đầu tiên > 8
```

2.  Find object

```javascript
let products = [
  {id: 1, name: "Pen"},
  {id: 2, name: "Notebook"},
  {id: 3, name: "Eraser"}
];
// Tìm product có id = 3
```

3.  Not found

```javascript
let ages = [10, 20, 30];
// Tìm số > 100 và kiểm tra kết quả
```

## FindIndex, findLastIndex

The `arr.findIndex` method has the same syntax, but returns the index where the element was found instead of the element itself. The value of -1 is returned if nothing is found.

The `arr.findLastIndex` method is like findIndex, but searches from right to left, similar to lastIndexOf.

**Syntax:**

```javascript
arr.findIndex(callback(element, index, array))
arr.findLastIndex(callback(element, index, array))
```

**Example:**

```javascript
let numbers = [5, 12, 8, 130, 44];

// findIndex: tìm phần tử > 10
let idx = numbers.findIndex(num => num > 10);
console.log(idx); // 1 (vị trí số 12)

// findLastIndex: tìm phần tử > 10 từ phải sang trái
let lastIdx = numbers.findLastIndex(num => num > 10);
console.log(lastIdx); // 4 (vị trí số 44)

// Không tìm thấy
let notFound = numbers.findIndex(num => num > 200);
console.log(notFound); // -1
```

**Questions:**

*   What does findIndex() return if no element satisfies the condition?
*   When should I use findIndex() instead of indexOf()?
*   Compare findIndex() and filter() + indexOf() in terms of performance.

**Use case:**

Update object array: find user position by ID and then edit information.

Delete element by condition: find index and then use splice().

Check existence and position: just know if the element exists, just know the position to handle.

```javascript
let users = [
  {id:1, name:"Alice"},
  {id:2, name:"Bob"},
  {id:3, name:"Charlie"}
];

let idx = users.findIndex(u => u.id === 2);
if(idx !== -1) {
  users[idx].name = "Bobby";
}
console.log(users);
// [{id:1,name:"Alice"},{id:2,name:"Bobby"},{id:3,name:"Charlie"}]
```

**Common bugs:**

*   Confusing with find() → findIndex() returns index, not element.
*   Comparing objects.
*   Not checking -1.

**Practice:**

1.  findIndex

```javascript
let nums = [3, 7, 10, 15];
// Tìm index đầu tiên > 8
```

2.  findLastIndex

```javascript
let letters = ["a","b","c","b","d"];
// Tìm index cuối cùng của "b"
```

3.  Combine splice

```javascript
let fruits = ["🍎","🍌","🍇"];
// Tìm index của "🍌" và xóa nó
```

## Filter

The `filter()` method creates a shallow copy of a portion of a given array, filtered down to just the elements from the given array that pass the test implemented by the provided function.

**Syntax:**

```javascript
let newArray = arr.filter(callback(element, index, array))
```

`callback`: function to test the condition.

`element`: current element.

`index` (optional): element index.

`array` (optional): original array.

Returns: a new array containing the elements that satisfy the condition.

The original array is not changed.

**Example:**

```javascript
let numbers = [5, 12, 8, 130, 44];

// Lọc các số > 10
let filtered = numbers.filter(num => num > 10);
console.log(filtered); // [12, 130, 44]

// Không tìm thấy phần tử
let empty = numbers.filter(num => num > 200);
console.log(empty); // []

// Sử dụng index
let evenIndex = numbers.filter((num, idx) => idx % 2 === 0);
console.log(evenIndex); // [5, 8, 44]
```

**Questions:**

*   What does filter() return if no element satisfies the condition?
*   Does filter() change the original array?
*   Compare filter() with find() and map() in terms of results and purpose.

**Use case:**

Filter products by price: display products > 100k.

Filter user list by status: only get active users.

Process API data: filter records that meet the conditions.

```javascript
let users = [
  {id:1, name:"Alice", active:true},
  {id:2, name:"Bob", active:false},
  {id:3, name:"Charlie", active:true}
];

let activeUsers = users.filter(u => u.active);
console.log(activeUsers);
// [{id:1,name:"Alice",active:true},{id:3,name:"Charlie",active:true}]
```

**Common bugs:**

*   Confusing with find() → find() returns 1 element, filter() returns an array of all elements that satisfy the condition.
*   Forgetting to check for empty results.
*   Changing object in filter callback.

**Practice:**

1.  Filter even numbers

```javascript
let nums = [1,2,3,4,5,6];
// Lọc các số chẵn
```

2.  Filter object

```javascript
let products = [
  {id:1, price:50},
  {id:2, price:120},
  {id:3, price:80}
];
// Lọc các sản phẩm giá > 70
```

3.  Not found

```javascript
let ages = [10,20,30];
// Lọc các số > 100 và kiểm tra kết quả
```

## Map

The `map()` method calls the function for each element of the array and returns the array of results.

**Syntax:**

```javascript
let newArray = arr.map(callback(element, index, array))
```

`callback`: function to process each element.

`element`: current element.

`index` (optional): element index.

`array` (optional): original array.

Returns a new array, does not change the original array.

**Example:**

```javascript
let numbers = [1, 2, 3, 4];

// Nhân mỗi phần tử với 2
let doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6, 8]
console.log(numbers); // [1, 2, 3, 4]  => mảng gốc không thay đổi

// Thêm chỉ số vào phần tử
let withIndex = numbers.map((num, idx) => `${idx}-${num}`);
console.log(withIndex); // ["0-1", "1-2", "2-3", "3-4"]
```

**Questions:**

*   What does map() return if the callback does not return a value (undefined)?
*   When should I use map() instead of forEach()?
*   Does map() change the original array?

**Use case:**

Data conversion: for example, convert an array of numbers to a string to display on the UI.

Add new properties to object: create a copy with a new field.

Create a new array from API data: filter and calculate before rendering.

```javascript
let users = [
  {id:1, name:"Alice"},
  {id:2, name:"Bob"}
];

// Tạo mảng tên user viết hoa
let upperNames = users.map(u => u.name.toUpperCase());
console.log(upperNames); // ["ALICE", "BOB"]
```

**Common bugs:**

*   Confusing with forEach() → forEach() does not return a new array, map() returns a new array.
*   Callback does not return.
*   Only using map for side-effect.

**Practice:**

1.  Multiply number

```javascript
let nums = [1,2,3,4];
// Tạo mảng mới với mỗi số * 3
```

2.  Add index

```javascript
let colors = ["red","green","blue"];
// Tạo mảng mới ["0-red","1-green","2-blue"]
```

3.  Object

```javascript
let products = [
  {id:1, price:50},
  {id:2, price:100}
];
// Tạo mảng giá +10
```

## Reduce

The methods `arr.reduce` is used to calculate a single value based on the array.

*   The function is applied to all array elements one after another and “carries on” its result to the next call.
*   As function is applied, the result of the previous function call is passed to the next one as the first argument.
*   So, the first argument is essentially the accumulator that stores the combined result of all previous executions. And at the end it becomes the result of reduce.

**Syntax:**

```javascript
let result = arr.reduce(callback(accumulator, item, index, array), initial)
```

`callback` is the processing function:

`accumulator`: value accumulated from previous calls (first time is initial if any).

`item`: current element.

`index` (optional): element index.

`array` (optional): original array.

`initial` (optional): initial value of accumulator.

Returns: a single value after "merging" all elements.

**Example:**

```javascript
let numbers = [1, 2, 3, 4, 5];

// Tính tổng các số
let sum = numbers.reduce((acc, item) => acc + item, 0);
console.log(sum); // 15

// Tính tích các số
let product = numbers.reduce((acc, item) => acc * item, 1);
console.log(product); // 120

// Gom object
let people = [
  {name:"Alice", age:25},
  {name:"Bob", age:30},
  {name:"Charlie", age:35}
];

let totalAge = people.reduce((acc, person) => acc + person.age, 0);
console.log(totalAge); // 90
```

**Questions:**

*   What is an accumulator and why use an initial value?
*   Does reduce() change the original array?
*   Compare reduce() with forEach() + variable outside the loop.

**Use case:**

Calculate sum, average, product of numbers in array.

Collect data: for example, count occurrences, group objects by key.

Convert data: flatten multi-dimensional array into 1-dimensional array.

```javascript
// Đếm số lần xuất hiện của các từ
let words = ["apple", "banana", "apple", "orange", "banana"];
let count = words.reduce((acc, word) => {
  acc[word] = (acc[word] || 0) + 1;
  return acc;
}, {});
console.log(count); // {apple:2, banana:2, orange:1}
```

**Common bugs:**

*   Forgetting initial value.
*   Not returning value in callback.
*   Using reduce with object without cloning.

**Practice:**

1.  Calculate sum

```javascript
let nums = [10, 20, 30];
// Tính tổng các số dùng reduce
```

2.  Calculate product

```javascript
let nums = [1, 2, 3, 4];
// Tính tích của các số dùng reduce
```

3.  Count occurrences

```javascript
let fruits = ["🍎","🍌","🍎","🍇","🍌"];
// Đếm số lần xuất hiện của mỗi loại
// Kết quả: { "🍎":2, "🍌":2, "🍇":1 }
