# Arrays in JavaScript

## Khái niệm cơ bản về Array

Trong JavaScript, Array là một kiểu dữ liệu dùng để lưu trữ nhiều giá trị trong cùng một biến. Có hai cách khai báo mảng rỗng:

```javascript
// Cách 1: dùng constructor (ít dùng)
let arr1 = new Array();

// Cách 2: dùng cặp dấu ngoặc vuông [] (phổ biến hơn)
let arr2 = [];
```

Ghi nhớ: Hầu hết lập trình viên JS dùng cách thứ 2, vì gọn gàng và dễ đọc.

Bạn cũng có thể khởi tạo mảng với giá trị ban đầu:

```javascript
let fruits = ['Apple', 'Banana', 'Orange'];
console.log(fruits); // ['Apple', 'Banana', 'Orange']
```

Lưu ý: Phần tử của mảng được đánh số từ 0, nghĩa là fruits[0] = 'Apple', fruits[1] = 'Banana'.

## Ví dụ thực tế (Usecase)

Giả sử bạn làm một dashboard công ty cần lưu danh sách tên nhân viên đang online:

```javascript
let onlineUsers = ['Alice', 'Bob', 'Charlie'];
console.log(`Số người online: ${onlineUsers.length}`);
```

`onlineUsers.length` → trả về số phần tử trong mảng.

Bạn có thể truy cập từng người bằng index: `onlineUsers[1]` → 'Bob'.

## Câu hỏi suy nghĩ

Nếu bạn viết `console.log(fruits[3])`, kết quả sẽ là gì? Tại sao?

Khi nào bạn sẽ chọn dùng `new Array()` thay vì `[]`?

## Bug hay gặp

Truy cập phần tử không tồn tại:

```javascript
let arr = ['a', 'b'];
console.log(arr[5]); // undefined, không báo lỗi
```

Đây là một lỗi logic thường gặp: tưởng phần tử có tồn tại nhưng thực ra không.

Khởi tạo mảng với số nguyên trong constructor:

```javascript
let arr = new Array(3);
console.log(arr); // [ <3 empty items> ]
```

Trông giống mảng 3 phần tử nhưng thực chất chưa có phần tử nào. Đây là lý do hầu hết dev tránh dùng `new Array(number)`.

## Bài tập nhỏ thực hành

Tạo một mảng `colors` chứa các màu 'red', 'green', 'blue'.

Thêm màu 'yellow' vào cuối mảng.

In ra `colors[2]` và số lượng phần tử trong mảng.

Thử truy cập `colors[10]` và quan sát kết quả.

## Methods pop/push, shift/unshift

Arrays in JavaScript can work both as a queue and as a stack. They allow you to add/remove elements, both to/from the beginning or the end.

In computer science, the data structure that allows this, is called deque

### pop

Extracts the last element of the array and returns it:

Both `fruits.pop()` and `fruits.at(-1)` return the last element of the array, but `fruits.pop()` also modifies the array by removing it

### push

Append the element to the end of the array:

The call `fruits.push(...)` is equal to `fruits[fruits.length] = ...`

### shift

Extracts the first element of the array and returns it:

### unshift

Add the element to the beginning of the array:

## pop – Xóa phần tử cuối mảng

```javascript
let fruits = ['Apple', 'Banana', 'Orange'];

let last = fruits.pop();
console.log(last);   // 'Orange'
console.log(fruits); // ['Apple', 'Banana']
```

Chú ý: `pop()` thay đổi mảng gốc, còn `fruits.at(-1)` chỉ trả về phần tử cuối mà không xóa.

Stack (ngăn xếp) dùng LIFO (Last In First Out), pop chính là hành động "lấy ra phần tử trên cùng".

## push – Thêm phần tử vào cuối mảng

```javascript
fruits.push('Mango');
console.log(fruits); // ['Apple', 'Banana', 'Mango']
```

Tương đương: `fruits[fruits.length] = 'Mango';`

Stack dùng push để thêm phần tử lên đầu stack.

## shift – Xóa phần tử đầu mảng

```javascript
let first = fruits.shift();
console.log(first);  // 'Apple'
console.log(fruits); // ['Banana', 'Mango']
```

Shift giống queue (hàng đợi) FIFO (First In First Out) – lấy phần tử đầu tiên ra.

Chú ý: `shift()` chậm hơn `pop()` vì mảng phải dịch chuyển toàn bộ phần tử.

## unshift – Thêm phần tử vào đầu mảng

```javascript
fruits.unshift('Strawberry');
console.log(fruits); // ['Strawberry', 'Banana', 'Mango']
```

Giống queue: thêm phần tử mới vào đầu mảng.

Cũng giống shift, thao tác này tốn tài nguyên khi mảng lớn.

## Ví dụ thực tế (Usecase)

Stack (undo action): Mỗi lần người dùng làm thay đổi trên UI, push hành động vào stack. Khi undo → pop ra.

Queue (hàng chờ xử lý): Danh sách task gửi lên server → shift để lấy task tiếp theo.

```javascript
let undoStack = [];
undoStack.push('Change color');
undoStack.push('Add text');

console.log(undoStack.pop()); // 'Add text'
```

## Bug hay gặp

Gọi pop/shift trên mảng rỗng → trả về `undefined` nhưng không báo lỗi.

Nhầm lẫn giữa push/shift và pop/unshift → array thay đổi không đúng ý muốn.

shift/unshift trên mảng lớn → giảm performance, vì JS phải dịch chuyển phần tử.

## Bài tập nhỏ

Tạo mảng `tasks = ['Task1', 'Task2', 'Task3']`.

Thêm `'Task0'` vào đầu mảng, `'Task4'` vào cuối mảng.

Lấy phần tử đầu tiên và cuối cùng ra, in mảng còn lại.

Quan sát thứ tự phần tử sau mỗi thao tác và giải thích.

## Tại sao shift/unshift “chậm” hơn pop/push

`pop()` / `push()`: thêm hoặc xóa cuối mảng → chỉ thao tác với 1 phần tử cuối cùng → O(1) (thời gian hằng số).

`shift()` / `unshift()`: thêm hoặc xóa phần tử đầu tiên → phải dịch chuyển toàn bộ phần tử còn lại để giữ index liên tục → O(n) (thời gian tuyến tính với số phần tử).

Ví dụ:

```javascript
let arr = [1, 2, 3, 4, 5];
arr.shift();
console.log(arr); // [2, 3, 4, 5]
```

Khi `shift()` chạy, JS phải dịch 2 → index 0, 3 → index 1, …

Mảng càng lớn → số phần tử phải dịch chuyển càng nhiều → hiệu năng giảm.

## Minh họa trực quan

Original: `[A, B, C, D, E]`

`shift()`: remove A

Move: B→0, C→1, D→2, E→3

Result: `[B, C, D, E]`

Nếu mảng có 1 triệu phần tử, shift/unshift sẽ dịch chuyển hết 999,999 phần tử → rất chậm.

Pop/push thì chỉ thao tác 1 phần tử cuối → nhanh.

## Cách tránh vấn đề hiệu năng

Dùng mảng đảo chiều hoặc stack: thêm/xóa ở cuối thay vì đầu.

Dùng cấu trúc dữ liệu khác:

*   Linked list → thêm/xóa đầu mảng O(1)
*   Deque (double-ended queue) → tối ưu shift/unshift.

Nếu mảng không quá lớn (< vài nghìn phần tử) → shift/unshift vẫn ổn.

## Ví dụ thay thế shift/unshift

Thay vì xóa phần tử đầu tiên (shift):

```javascript
let arr = [1,2,3,4,5];
let first = arr[0];
arr = arr.slice(1); // tạo mảng mới không chứa phần tử đầu
```

Hơi tốn bộ nhớ, nhưng không dịch chuyển phần tử trong mảng cũ.

## Loops

One of the oldest ways to cycle array items is the for loop over indexes:

But for arrays there is another form of loop, for..of:

## Tổng quan

Array là một kiểu đối tượng đặc biệt, dùng để lưu trữ dữ liệu có thứ tự.

Có thể dùng Array như deque (double-ended queue):

| Operation          | Description                           |
| ------------------ | ------------------------------------- |
| `push(...items)`   | Thêm phần tử vào cuối mảng             |
| `pop()`            | Xóa phần tử cuối mảng và trả về nó    |
| `shift()`          | Xóa phần tử đầu mảng và trả về nó    |
| `unshift(...items)` | Thêm phần tử vào đầu mảng             |

## Lặp qua mảng

a) For loop truyền thống (the fastest, compatible)

```javascript
let fruits = ['Apple', 'Banana', 'Orange'];
for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}
```

Ưu điểm: tốc độ nhanh, hoạt động trong tất cả trình duyệt, kể cả cũ.

Nhược điểm: hơi dài dòng khi chỉ cần giá trị, không cần index.

b) For…of (hiện đại, gọn nhẹ)

```javascript
for (let fruit of fruits) {
    console.log(fruit);
}
```

Chỉ cần giá trị phần tử.

Không thay đổi mảng.

Ngắn gọn, dễ đọc.

c) For…in → KHÔNG dùng cho mảng

```javascript
for (let i in fruits) {
    console.log(fruits[i]);
}
```

`For…in` duyệt key của đối tượng, không tối ưu cho mảng.

Có thể gây ra bug nếu prototype của mảng có thêm thuộc tính.

Nhớ: never use for…in for arrays!

## Ví dụ thực tế

Giả sử bạn muốn hiển thị danh sách người dùng online:

```javascript
let onlineUsers = ['Alice', 'Bob', 'Charlie'];
for (let user of onlineUsers) {
    console.log(`User online: ${user}`);
}
```

Nếu muốn hiển thị cả số thứ tự, dùng for truyền thống:

```javascript
for (let i = 0; i < onlineUsers.length; i++) {
    console.log(`${i+1}. ${onlineUsers[i]}`);
}
```

## Bug hay gặp

Dùng for…in với mảng → có thể in ra cả các thuộc tính thêm vào mảng, không chỉ phần tử.

Thay đổi mảng trong khi lặp `for…of` → dễ gây lỗi logic.

Dùng index ngoài bounds → `arr[arr.length]` là `undefined`.

## Bài tập nhỏ

Tạo mảng `colors = ['red', 'green', 'blue']`.

Thêm `'yellow'` vào đầu, `'purple'` vào cuối.

Xóa phần tử đầu và cuối, in ra phần tử còn lại.

Duyệt mảng bằng for truyền thống và for…of, in thứ tự và giá trị.
