Chuyển đến nội dung
Lịch sử đoạn chat

String conversion (chuyển đổi sang chuỗi) xảy ra khi ta cần biểu diễn một giá trị dưới dạng chuỗi. Có thể dùng String(value) hoặc value.toString() (nếu có hỗ trợ).

Ví dụ:

// Sử dụng String(value)
```javascript
let value1 = 123;
console.log(String(value1)); // "123" (kiểu string)
```

// Chuyển kiểu boolean
```javascript
let value2 = true;
console.log(String(value2)); // "true"
```

// Chuyển kiểu null và undefined
```javascript
console.log(String(null));      // "null"
console.log(String(undefined)); // "undefined"
```

// Chuyển kiểu object
```javascript
let obj = { name: "IT 日本語先生" };
console.log(String(obj)); // "[object Object]"
```

// Chuyển kiểu array
```javascript
let arr = [1, 2, 3];
console.log(String(arr)); // "1,2,3"
```
Ngoài ra, string conversion ngầm định (implicit conversion) cũng xảy ra khi nối chuỗi:

let num = 42;
let str = "Số là: " + num;
console.log(str); // "Số là: 42"
👉 Như vậy có thể dùng:

String(value): rõ ràng, dễ đọc.

"" + value: cách viết ngắn, chuyển sang chuỗi tự động.






1. String(value)
Đây là hàm toàn cục (global function).

Nó có thể nhận bất kỳ giá trị nào và trả về chuỗi tương ứng.

An toàn hơn vì nó hoạt động cả với null và undefined.

Ví dụ:

console.log(String(123));        // "123"
console.log(String(true));       // "true"
console.log(String(null));       // "null"
console.log(String(undefined));  // "undefined"
2. value.toString()
Đây là phương thức (method) của đối tượng (Number, Boolean, Array, Object...).

Không dùng được với null và undefined (sẽ báo lỗi).

Trả về chuỗi biểu diễn của giá trị.

Ví dụ:

```javascript
console.log((123).toString());   // "123"
console.log((true).toString());  // "true"
console.log([1,2,3].toString()); // "1,2,3"
```

// ❌ Lỗi:
```javascript
console.log(null.toString());      // TypeError
console.log(undefined.toString()); // TypeError
```
🔑 So sánh nhanh
Đặc điểm	String(value)	value.toString()
Dùng được với null	✅ Có	❌ Lỗi
Dùng được với undefined	✅ Có	❌ Lỗi
Là hàm toàn cục	✅	❌ (chỉ là method)
An toàn hơn	✅	❌
👉 Kinh nghiệm thực tế:

Nếu muốn chuyển đổi chắc chắn (kể cả null/undefined) → dùng String(value).

Nếu đã biết chắc giá trị là object/number/array/... (không phải null/undefined) → có thể dùng value.toString().

🔹 1. Numeric conversion tự động (implicit)
Khi giá trị được dùng trong biểu thức toán học, JavaScript sẽ tự động chuyển sang số (trừ phép cộng "+", nếu có chuỗi thì sẽ thành nối chuỗi).

Ví dụ:

```javascript
console.log("6" / "2");    // 3   (chuỗi "6" và "2" được chuyển sang số)
console.log("10" - 3);     // 7
console.log("2" * "3");    // 6
console.log("6" + 2);      // "62"  (không phải 8, vì "+" ưu tiên nối chuỗi)
```
🔹 2. Numeric conversion tường minh (explicit)
Dùng Number(value) để chuyển sang số một cách rõ ràng.

Ví dụ:

```javascript
console.log(Number("123"));     // 123
console.log(Number("  456 "));  // 456 (bỏ khoảng trắng đầu/cuối)
console.log(Number(""));        // 0
console.log(Number(true));      // 1
console.log(Number(false));     // 0
console.log(Number(null));      // 0
console.log(Number(undefined)); // NaN
```
🔹 3. Quy tắc chuyển đổi (Numeric Conversion Rules)
Giá trị	Kết quả sau Number(value)
"123"	123 (chuỗi số → số)
" 456 "	456 (trim khoảng trắng)
"" (chuỗi rỗng)	0
true	1
false	0
null	0
undefined	NaN
"abc"	NaN (không phải số)
[] (mảng rỗng)	0
[1,2]	NaN
{} (object rỗng)	NaN
👉 Tóm lại:

Dùng Number(value) khi muốn ép kiểu rõ ràng.

Cẩn thận với "+" vì dễ nhầm giữa cộng số và nối chuỗi.

🔹 1. Boolean conversion tự động (implicit)
Xảy ra khi:

Dùng trong câu điều kiện (if, while, for)

Khi áp dụng toán tử logic (!, ||, &&)

Ví dụ:

```javascript
if ("hello") {
  console.log("Chuỗi không rỗng là true");
}

if (0) {
  console.log("Sẽ không in ra");
} else {
  console.log("0 là false");
}
```
🔹 2. Boolean conversion tường minh (explicit)
Dùng Boolean(value) để ép kiểu.

Ví dụ:

```javascript
console.log(Boolean(1));        // true
console.log(Boolean(0));        // false
console.log(Boolean("hello"));  // true
console.log(Boolean(""));       // false
console.log(Boolean(null));     // false
console.log(Boolean(undefined));// false
console.log(Boolean(NaN));      // false
```
🔹 3. Quy tắc chuyển đổi
Falsy values (khi chuyển sang boolean sẽ thành false):

0

"" (chuỗi rỗng)

null

undefined

NaN

👉 Tất cả giá trị khác đều là truthy (chuyển sang true).

Ví dụ đặc biệt:

```javascript
console.log(Boolean("0"));    // true (chuỗi có ký tự)
console.log(Boolean(" "));    // true (chuỗi có khoảng trắng)
console.log(Boolean([]));     // true (mảng rỗng vẫn là object)
console.log(Boolean({}));     // true (object rỗng)
```
✅ Tóm tắt:

Falsy (→ false): 0, "", null, undefined, NaN

Truthy (→ true): tất cả còn lại (kể cả "0", " ", [], {})



🔹 Summary – Type Conversions in JavaScript
1. String Conversion
Xảy ra khi cần in ra hoặc ép kiểu bằng String(value).

Ví dụ:

```javascript
String(123)      // "123"
String(true)     // "true"
String(null)     // "null"
String(undefined)// "undefined"
String([1,2,3])  // "1,2,3"
```
2. Numeric Conversion
Xảy ra trong biểu thức toán học hoặc dùng Number(value).

Ví dụ:

```javascript
Number("123")     // 123
Number(" 456 ")   // 456
Number("")        // 0
Number(true)      // 1
Number(false)     // 0
Number(null)      // 0
Number(undefined) // NaN
"6" / "2"         // 3 (tự động chuyển sang số)
```
3. Boolean Conversion
Xảy ra trong câu điều kiện / toán tử logic hoặc Boolean(value).

Falsy values → false: 0, "", null, undefined, NaN

Tất cả còn lại → true (truthy)

Ví dụ:

```javascript
Boolean(0)       // false
Boolean("")      // false
Boolean(null)    // false
Boolean("0")     // true  (chuỗi có ký tự)
Boolean(" ")     // true  (chuỗi có khoảng trắng)
Boolean([])      // true  (object/mảng rỗng vẫn truthy)
```
🔎 Những điểm dễ nhầm lẫn
undefined khi chuyển sang số → NaN, không phải 0.

```javascript
Number(undefined); // NaN
```
Chuỗi "0" và " " (khoảng trắng) khi chuyển sang boolean → true.

```javascript
Boolean("0"); // true
Boolean(" "); // true
```
✅ Tóm gọn:

String Conversion → khi in ra (String(value)).

Numeric Conversion → khi tính toán (Number(value)).

Boolean Conversion → khi logic test (Boolean(value)).

Nhớ 2 ngoại lệ:

undefined → NaN khi thành số.

"0" và " " vẫn là true khi thành boolean.
