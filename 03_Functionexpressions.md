# Bài học: “Function is a value”

## 1. Giải thích lý thuyết
Trong JavaScript, hàm (function) cũng là một loại giá trị (value), giống như số (42), chuỗi ("Hello") hay object ({}).
Điều đó có nghĩa là:

Chúng ta có thể gán function cho biến.
Truyền function như một tham số cho function khác.
Trả về function từ một function khác.

## 2. Ví dụ cơ bản
```javascript
// Gán function vào biến
let sayHi = function() {
  return "Hello!";
};

// In ra giá trị (hàm cũng có thể được in ra)
alert(sayHi);   // hiển thị toàn bộ code của function

// Gọi hàm qua biến
alert(sayHi()); // hiển thị "Hello!"
```
Ở đây:

sayHi chính là một biến chứa function.
Nếu chỉ alert(sayHi) → in ra nội dung function.
Nếu alert(sayHi()) → gọi hàm và in ra kết quả trả về "Hello!".

## 3. Câu hỏi tư duy 🤔
Nếu mình viết alert(sayHi) và alert(sayHi()), kết quả khác nhau thế nào?

Em nghĩ tại sao JavaScript lại cho phép coi function như một giá trị thay vì chỉ là một “khối lệnh”?

## 4. Use case thực tế 💡
Event handler: khi click chuột, ta gán một function cho sự kiện:
```javascript
document.getElementById("btn").onclick = function() {
  alert("Button clicked!");
};
```
Callback trong setTimeout:
```javascript
setTimeout(function() {
  alert("This runs after 2 seconds!");
}, 2000);
```
Ở đây ta truyền function như một giá trị cho setTimeout.

## 5. Bug thường gặp 🐞
Nhầm lẫn giữa gọi hàm và tham chiếu hàm
```javascript
button.onclick = sayHi(); // ❌ sẽ chạy ngay lập tức, không chờ click
button.onclick = sayHi;   // ✅ truyền tham chiếu function
```
Quên return khi cần giá trị
```javascript
let f = function() {
  "Hello!"; // ❌ không trả về gì cả
};
alert(f()); // undefined
```
## 6. Bài tập nhỏ 📝
Tạo một biến greet lưu trữ function trả về "Xin chào, JavaScript!". Sau đó dùng alert để hiển thị:

Nội dung function
Kết quả trả về khi gọi function

Viết một function square nhận vào một số n, trả về n * n. Gán function đó vào biến và in thử với alert(square(5)).

(Nâng cao hơn một chút) Viết một function repeatTwice nhận vào một function f, và gọi f() 2 lần. Thử truyền vào function sayHi ở trên.

Bài tập:

### 1.
```javascript
let greet = function() {
    return "Xin chào Javascript!"
}

alert(greet)
```

### 2.
```javascript
function square(n) {
    return n*n;
}
alert(square(5))
```

### 3.
```javascript
function sayhi() {
    return "Hi ";
}

function repeatTwice(f) {
    f()
    f()
}

alert(repeatTwice(sayhi()))
```

🔥 Bài tập thực hành thêm
Viết một function shout trả về "HEY!". Sau đó viết một function repeatN(f, n) gọi function f n lần và nối kết quả lại thành một chuỗi. Ví dụ:

`repeatN(shout, 3); // "HEY!HEY!HEY!"`
Sửa repeatTwice để nó nhận thêm một tham số separator (chuỗi để chèn giữa các lần lặp). Ví dụ:

`repeatTwice(sayhi, "-"); // "Hi -Hi "`

# Bài học: Callback Functions

## 1. Giải thích lý thuyết
Callback function là một hàm được truyền như tham số vào một hàm khác.
Hàm bên ngoài sẽ gọi lại (call back) hàm đó ở một thời điểm nhất định.
Callback giúp chúng ta tách biệt logic và tái sử dụng code.

## 2. Ví dụ cơ bản
```javascript
function greeting(name) {
  alert("Hello " + name);
}

function processUserInput(callback) {
  let name = prompt("Nhập tên của bạn:");
  callback(name); // gọi lại hàm được truyền vào
}

processUserInput(greeting);
```
greeting là callback function.
processUserInput gọi callback(name) để hoàn tất công việc.

## 3. Câu hỏi tư duy 🤔
Tại sao không gọi trực tiếp greeting(name) mà phải truyền vào processUserInput?
Em có thể nghĩ ra tình huống thực tế nào cần callback thay vì gọi trực tiếp không?

## 4. Use case thực tế 💡
Sự kiện (event handling):
```javascript
document.getElementById("btn").addEventListener("click", function() {
  alert("Button clicked!");
});
```
Xử lý bất đồng bộ (asynchronous):
```javascript
setTimeout(function() {
  alert("Run after 2s!");
}, 2000);
```

## 5. Bug thường gặp 🐞
Gọi hàm quá sớm
```javascript
setTimeout(doSomething(), 1000); // ❌ chạy ngay lập tức
setTimeout(doSomething, 1000);   // ✅ truyền tham chiếu
```
Callback hell: lồng callback quá nhiều gây khó đọc.
```javascript
step1(function() {
  step2(function() {
    step3(function() {
      // ...
    });
  });
});
```

## 6. Bài tập nhỏ 📝
Viết một function calculator(a, b, callback) để tính toán. Callback nhận 2 số và trả về kết quả. Ví dụ:
```javascript
calculator(5, 3, add)   // 8
calculator(5, 3, mul)   // 15
```
với add và mul là hai hàm callback.

Viết một function delayMessage(message, time, callback) → sau time mili giây thì gọi callback với message.
```javascript
delayMessage("Xin chào!", 2000, alert);
```

# Bài học: Function Expression vs Function Declaration

## 1. Function Declaration
Viết với cú pháp “bình thường”:
```javascript
function sayHello() {
  return "Hello!";
}
```
Đặc điểm:

Được hoisted (tức là trình thông dịch JS “đưa khai báo hàm lên đầu” khi chạy).
Vì vậy, ta có thể gọi hàm trước khi định nghĩa.

Ví dụ:
```javascript
sayHi(); // OK, chạy được

function sayHi() {
  alert("Hi!");
}
```

## 2. Function Expression
Được tạo ra như một giá trị trong một biểu thức.
Ví dụ: gán hàm vào một biến:
```javascript
let sayBye = function() {
  return "Bye!";
};
```
Đặc điểm:

Chỉ khả dụng sau khi dòng code được thực thi.
Không được hoisted như Function Declaration.

Ví dụ:
```javascript
sayBye(); // ❌ Lỗi: Cannot access 'sayBye' before initialization

let sayBye = function() {
  alert("Bye!");
};
```

## 3. So sánh nhanh 🔎
Đặc điểm | Function Declaration | Function Expression
------- | -------- | --------
Cú pháp | `function f() {}` | `let f = function() {};`
Hoisting | ✅ Có | ❌ Không
Gọi trước khi định nghĩa | ✅ Được | ❌ Lỗi
Dùng như giá trị | Khó linh hoạt | Rất linh hoạt (truyền vào biến, callback, v.v.)

## 4. Use case thực tế 💡
Callback thường viết bằng Function Expression:
```javascript
setTimeout(function() {
  alert("Chạy sau 1 giây");
}, 1000);
```
Hoặc gán vào biến khi muốn linh hoạt:
```javascript
let sum = function(a, b) { return a + b; };
console.log(sum(2, 3)); // 5
```

## 5. Bug thường gặp 🐞
Gọi function expression trước khi định nghĩa → lỗi ReferenceError.
Nhầm lẫn giữa khai báo và gán:
```javascript
function test() {} // OK
let test = function() {}; // cũng OK, nhưng hai cái này hoạt động khác nhau
```

## 6. Bài tập nhỏ 📝
Viết một function declaration greet() trả về "Hello World". Gọi hàm này trước khi khai báo để test hoisting.

Viết một function expression greetExp làm việc tương tự. Gọi hàm trước khi khai báo và xem kết quả khác nhau thế nào.

Viết một function doTwice(callback) nhận vào một function và gọi nó 2 lần. Thử truyền vào cả function declaration và function expression.
