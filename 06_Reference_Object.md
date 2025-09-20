# JS Object References & Cloning

A variable assigned to an object stores not the object itself, but its “address in memory” – in other words “a reference” to it.

When an object variable is copied, the reference is copied, but the object itself is not duplicated

## 1️⃣ Lý thuyết cơ bản

Trong JavaScript:

Khi bạn tạo một object, JS lưu trữ object đó ở một vùng nhớ riêng.

Khi bạn gán object đó cho một biến, biến không chứa object thật, mà chứa một reference (tham chiếu) đến object trong bộ nhớ.

Khi copy một biến object sang biến khác, chỉ reference được copy, object vẫn chỉ tồn tại 1 lần trong bộ nhớ.

Điều này khác với primitive types (number, string, boolean, null, undefined, symbol), vì primitives được lưu trực tiếp giá trị.

## 2️⃣ Ví dụ minh họa

```javascript
let obj1 = { name: "Alice", age: 25 };
let obj2 = obj1; // chỉ copy reference, không copy object

obj2.age = 30;

console.log(obj1.age); // 30
console.log(obj2.age); // 30
```

Giải thích:

obj2 không phải là bản sao độc lập.

Thay đổi trên obj2 ảnh hưởng luôn obj1 vì chúng cùng trỏ đến một object trong bộ nhớ.

## 3️⃣ Câu hỏi suy nghĩ

Nếu muốn obj2 là bản sao độc lập của obj1, bạn sẽ làm thế nào?

Tại sao primitives không gặp vấn đề “tham chiếu” giống object?

Nếu có object lồng nhau, shallow copy có đủ để tạo một bản sao độc lập không?

## 4️⃣ Use case thực tế

State management trong React / Vue / Angular:

Nếu vô tình mutate object state trực tiếp thay vì clone nó, UI sẽ không re-render đúng.

API response caching:

Nếu giữ reference thay vì clone object, dữ liệu cache có thể bị thay đổi ngoài ý muốn.

## 5️⃣ Các bug phổ biến

Thay đổi object gốc khi copy reference:

```javascript
const user = { name: "Bob" };
const admin = user;
admin.name = "Admin";
console.log(user.name); // "Admin" – người dùng bất ngờ!
```

Shallow copy không đủ khi object lồng nhau:

```javascript
const obj1 = { info: { age: 20 } };
const obj2 = { ...obj1 }; // shallow copy
obj2.info.age = 25;
console.log(obj1.info.age); // 25 – object con vẫn share reference
```

Dùng JSON để clone mà có function / undefined / Symbol:

```javascript
const obj = { a: undefined, b: () => {} };
const clone = JSON.parse(JSON.stringify(obj));
console.log(clone); // { } – mất function và undefined
```

## 6️⃣ Bài tập thực hành

Bài tập 1: Copy object mà không thay đổi object gốc

```javascript
const person = { name: "Alice", age: 25 };

// TODO: tạo bản sao độc lập của person, lưu vào personCopy
// thay đổi personCopy.age = 30 mà không ảnh hưởng person
```

Bài tập 2: Shallow vs Deep Copy

```javascript
const obj = { nested: { value: 10 } };

// TODO: tạo một deep copy của obj
// thay đổi nested.value trong bản sao mà obj gốc không thay đổi
```

## Comparison by reference

### 1️⃣ So sánh object theo reference

Trong JavaScript:

Hai object khác nhau trong bộ nhớ sẽ không bao giờ bằng nhau, dù chúng có cùng nội dung.

So sánh object sử dụng == hoặc === so sánh reference, không so sánh giá trị bên trong.

Ví dụ:

```javascript
const obj1 = { name: "Alice" };
const obj2 = { name: "Alice" };
const obj3 = obj1;

console.log(obj1 === obj2); // false – khác reference
console.log(obj1 === obj3); // true – cùng reference
```

Giải thích:

obj1 và obj2 có cùng nội dung nhưng khác bộ nhớ → không bằng nhau.

obj3 được gán từ obj1 → cùng trỏ tới object → bằng nhau.

### 2️⃣ Const objects có thể thay đổi

Trong JavaScript, const không bảo vệ object khỏi mutate, chỉ bảo vệ biến không được gán lại:

```javascript
const person = { name: "Bob" };
person.name = "Alice"; // ✅ hợp lệ
// person = { name: "Charlie" }; // ❌ lỗi: Assignment to constant variable
```

Tóm lại:

const → biến không thể gán lại.

Object const → vẫn có thể mutate thuộc tính bên trong.

### 3️⃣ Câu hỏi suy nghĩ

Tại sao const không ngăn object bị mutate?

Làm thế nào để tạo object immutable trong JS?

So sánh == và === với object có ý nghĩa gì?

### 4️⃣ Use case thực tế

State management trong React / Redux:

Nếu bạn không tạo copy mới, const state vẫn có thể mutate state gốc → bug khó phát hiện.

Caching / configuration objects:

Config const có thể mutate nhưng không thể gán lại, giúp tránh nhầm lẫn nhưng vẫn cần chú ý khi mutate.

### 5️⃣ Bug phổ biến

So sánh object tưởng bằng nhưng không bằng:

```javascript
const a = { x: 1 };
const b = { x: 1 };
if (a === b) {
  console.log("Equal"); // không in ra, mặc dù nội dung giống
}
```

Mutate const object gây side-effect ngoài ý muốn:

```javascript
const config = { theme: "light" };
function switchTheme(cfg) {
  cfg.theme = "dark"; // mutate trực tiếp
}
switchTheme(config);
console.log(config.theme); // "dark" – có thể không mong muốn
```

### 6️⃣ Bài tập nhỏ

Bài tập 1: So sánh object

```javascript
const objA = { score: 100 };
const objB = { score: 100 };
const objC = objA;

// TODO: in ra true nếu object bằng nhau (reference), false nếu khác
console.log(objA === objB);
console.log(objA === objC);
```

Bài tập 2: Mutate const object

```javascript
const user = { name: "Alice", age: 25 };

// TODO: cập nhật tuổi user thành 30 mà không gán lại biến
```

Bài tập 3: Tạo object bất biến (immutable)

```javascript
const settings = { darkMode: false };

// TODO: làm settings bất biến để không thể mutate
```

## Cloning and merging, Object.assign

### 1️⃣ Lý thuyết cơ bản

Copy bằng gán (assignment) chỉ tạo một reference mới, không clone object.

Nếu muốn bản sao độc lập, bạn cần clone object:

Copy từng thuộc tính primitive.

Hoặc dùng phương pháp JS built-in như Object.assign().

### 2️⃣ Object.assign()

Object.assign(target, ...sources):

Target: object sẽ được thay đổi / merge properties.

Sources: object(s) chứa thuộc tính sẽ copy sang target.

Trả về: target object sau khi merge.

Lưu ý: chỉ shallow copy, object lồng nhau vẫn share reference.

### 3️⃣ Ví dụ

```javascript
const person = { name: "Alice", age: 25 };
const extra = { job: "Developer", age: 30 };

// Merge extra vào person
const merged = Object.assign({}, person, extra);

console.log(merged); // { name: 'Alice', age: 30, job: 'Developer' }
console.log(person); // { name: 'Alice', age: 25 } – person không thay đổi
```

Giải thích:

{} là target mới → tạo bản sao (clone) của person và merge extra.

age bị overwrite vì extra có thuộc tính trùng.

Nếu target là person trực tiếp, object gốc sẽ mutate.

### 4️⃣ Câu hỏi suy nghĩ

Tại sao Object.assign chỉ tạo shallow copy?

Làm sao để deep clone khi object lồng nhau?

Khi merge nhiều object có thuộc tính trùng, thứ tự source ảnh hưởng gì?

### 5️⃣ Use case thực tế

State management (React / Vue): tạo state mới khi update, tránh mutate gốc.

Merging config / options: combine default + user options.

Copy object để test / undo: cần clone object trước khi mutate.

### 6️⃣ Bug phổ biến

Shallow copy với object lồng nhau:

```javascript
const obj1 = { nested: { a: 1 } };
const obj2 = Object.assign({}, obj1);

obj2.nested.a = 2;
console.log(obj1.nested.a); // 2 – shared reference
```

Overwrite properties khi merge nhiều sources:

```javascript
const defaultSettings = { theme: "light", font: "Arial" };
const userSettings = { theme: "dark" };

const finalSettings = Object.assign({}, defaultSettings, userSettings);
console.log(finalSettings.theme); // "dark" – userSettings overwrite
```

### 7️⃣ Bài tập nhỏ

Bài tập 1: Clone object

```javascript
const person = { name: "Bob", age: 25 };

// TODO: tạo bản sao person mà không làm thay đổi object gốc
const clone = Object.assign({}, person);
```

Bài tập 2: Merge multiple objects

```javascript
const defaults = { theme: "light", font: "Arial" };
const userPrefs = { font: "Helvetica" };
const sessionPrefs = { theme: "dark" };

// TODO: merge tất cả vào một object mới
const merged = Object.assign({}, defaults, userPrefs, sessionPrefs);
console.log(merged); // ?
```

Bài tập 3: Shallow copy vs deep copy

```javascript
const original = { nested: { value: 10 } };
const copy = Object.assign({}, original);

// TODO: thay đổi copy.nested.value mà original không bị thay đổi
```

## Nested cloning

### 1️⃣ Lý thuyết cơ bản

structuredClone(object) tạo một bản sao độc lập hoàn toàn của object, bao gồm tất cả nested properties.

Nó hỗ trợ:

Objects, arrays, primitives

Circular references (object tham chiếu chính nó)

Hầu hết các kiểu dữ liệu khác ngoài function, Symbol, và một số loại đặc biệt.

Trái ngược với Object.assign() hoặc spread ({ ...obj }), structuredClone thực hiện deep copy.

### 2️⃣ Ví dụ minh họa

```javascript
const original = {
  name: "Alice",
  info: {
    age: 25,
    address: { city: "Hanoi", country: "Vietnam" }
  }
};

// Deep clone
const clone = structuredClone(original);

// Thay đổi nested property của clone
clone.info.age = 30;
clone.info.address.city = "Ho Chi Minh";

console.log(original.info.age); // 25 – object gốc không bị thay đổi
console.log(original.info.address.city); // "Hanoi"
```

✅ Kết quả: object gốc hoàn toàn bất biến, clone là độc lập.

### 3️⃣ Circular references

```javascript
const obj = {};
obj.self = obj; // circular reference

const clonedObj = structuredClone(obj);
console.log(clonedObj.self === clonedObj); // true
console.log(clonedObj.self === obj); // false
```

structuredClone xử lý vòng lặp mà không lỗi stack overflow.

Đây là điểm khác biệt lớn so với JSON.parse(JSON.stringify(obj)), vì cách này không hỗ trợ circular reference.

### 4️⃣ Câu hỏi tư duy

Khi nào bạn nên dùng structuredClone thay vì Object.assign()?

structuredClone có clone function hoặc Symbol không?

Tại sao JSON stringify/parse không phải là cách an toàn để deep clone?

### 5️⃣ Use case thực tế

State management trong React / Vue: clone state lồng nhau để update mà không mutate gốc.

Copy / undo / redo object phức tạp: có nested hoặc circular reference.

Data transfer: clone object phức tạp trước khi gửi vào worker thread hoặc lưu cache.

### 6️⃣ Bug phổ biến

Dùng spread hoặc Object.assign để clone object lồng nhau → mutation vẫn ảnh hưởng gốc.

Dùng JSON stringify/parse với object circular → lỗi: TypeError: Converting circular structure to JSON.

Mong muốn clone function → structuredClone không clone function.

### 7️⃣ Bài tập nhỏ

Bài tập 1: Nested clone

```javascript
const person = { name: "Bob", info: { age: 25, city: "Hanoi" } };

// TODO: deep clone person
const clone = structuredClone(person);

// Thay đổi clone.info.age = 30 mà person không bị thay đổi
```

Bài tập 2: Circular reference

```javascript
const obj = { name: "Circular" };
obj.self = obj;

// TODO: clone obj bằng structuredClone
const cloneObj = structuredClone(obj);
console.log(cloneObj.self === cloneObj); // true
console.log(cloneObj.self === obj); // false
```

Bài tập 3: Deep clone array lồng nhau

```javascript
const arr = [1, [2, [3, 4]]];

// TODO: deep clone arr và thay đổi giá trị inner array mà không ảnh hưởng arr gốc
```

## Summary

### 1️⃣ Objects được lưu trữ như thế nào

Object trong JS không lưu trực tiếp giá trị trong biến, mà lưu reference (địa chỉ trong bộ nhớ).

Khi copy hoặc truyền object vào function, reference được copy, object gốc không bị duplicate.

```javascript
let obj1 = { name: "Alice" };
let obj2 = obj1; // copy reference
obj2.name = "Bob";
console.log(obj1.name); // "Bob"
```

✅ Cả obj1 và obj2 trỏ tới cùng một object.

### 2️⃣ Các thao tác qua reference

Thêm / xóa / sửa property thông qua bất kỳ reference nào đều ảnh hưởng object gốc.

```javascript
const user = { age: 25 };
function updateAge(u) { u.age = 30; }
updateAge(user);
console.log(user.age); // 30
```

### 3️⃣ Clone object

#### 3.1 Shallow copy

Chỉ clone một level đầu, nested objects vẫn share reference.

Các phương pháp:

Object.assign({}, obj)

Spread syntax: { ...obj }

```javascript
const obj = { nested: { a: 1 } };
const shallow = { ...obj };
shallow.nested.a = 2;
console.log(obj.nested.a); // 2 – shared reference
```

#### 3.2 Deep copy

Clone toàn bộ nested object và tạo object độc lập hoàn toàn.

Các phương pháp:

structuredClone(obj) – built-in, hỗ trợ circular reference.

Thư viện: \_.cloneDeep(obj) (Lodash).

```javascript
const original = { nested: { a: 1 } };
const deep = structuredClone(original);
deep.nested.a = 10;
console.log(original.nested.a); // 1 – gốc không thay đổi
```

### 4️⃣ Tóm tắt mẹo quan trọng

Concept | Reference vs Value | Notes
------- | -------- | --------
Primitive | Value copy | Safe, không share memory
Object assign / copy | Reference copy | Cẩn thận mutate
Shallow copy | Copy top-level | Nested vẫn share reference
Deep copy | Copy toàn bộ | structuredClone hoặc \_.cloneDeep
Const object | Biến không đổi, object có thể mutate | ❌ Không gán lại, ✅ mutate property được
Comparison | So sánh reference | === true nếu cùng object

### 5️⃣ Bug phổ biến

Mutate object lồng nhau khi chỉ shallow copy → gốc bị thay đổi.

So sánh object tưởng giống nội dung → không bằng nhau ({a:1} === {a:1} → false).

JSON parse/stringify với circular reference → lỗi.

### 6️⃣ Bài tập tổng hợp

Clone object user sao cho nested properties không mutate gốc.

Merge 3 object configs mà không làm mutate object gốc.

So sánh 2 object cùng nội dung nhưng khác reference → kết quả gì?

Deep clone array lồng nhau và circular reference object bằng structuredClone.

There are two syntaxes for creating an empty array:

Almost all the time, the second syntax is used. We can supply initial elements in the brackets:

Array elements are numbered, starting with zero
