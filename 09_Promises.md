# 09. Promises

## Introduction

The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value.

The constructor syntax for a promise object is:

```javascript
let promise = new Promise(function(resolve, reject) {
  // executor (the producing code, "singer")
});
```

A Promise is in one of these states:
- `pending`: initial state, neither fulfilled nor rejected.
- `fulfilled`: meaning that the operation was completed successfully.
- `rejected`: meaning that the operation failed.

## 1. Khái niệm Promise

Promise trong JavaScript dùng để xử lý bất đồng bộ (asynchronous).
Nó đại diện cho một giá trị có thể sẽ có trong tương lai (sau khi tác vụ hoàn thành).
Một Promise có 3 trạng thái:
- `pending` (đang chờ)
- `fulfilled` (hoàn thành thành công → trả về value)
- `rejected` (thất bại → trả về error)

## 2. Cú pháp cơ bản

```javascript
let promise = new Promise((resolve, reject) => {
  // Giả sử có một tác vụ mất thời gian
  let success = true;

  if (success) {
    resolve("✅ Thành công!");
  } else {
    reject("❌ Thất bại!");
  }
});

// Cách sử dụng
promise
  .then(result => console.log(result))  // khi resolve()
  .catch(error => console.log(error))   // khi reject()
  .finally(() => console.log("Hoàn tất"));
```

## 3. Ví dụ minh họa

Giả sử em gọi API để lấy danh sách user:

```javascript
function getUser() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let success = Math.random() > 0.3; // 70% thành công
      if (success) {
        resolve({ name: "An", age: 20 });
      } else {
        reject("Không lấy được user!");
      }
    }, 1000);
  });
}

getUser()
  .then(user => console.log("Người dùng:", user))
  .catch(err => console.error(err));
```

## 4. Câu hỏi suy nghĩ 🤔

- Nếu em gọi nhiều API liên tiếp (API A → API B → API C), em sẽ xử lý Promise như thế nào để không bị callback hell?
- Sự khác nhau giữa `.then().catch()` và `async/await` là gì?
- Khi một Promise bị reject, nếu em không bắt lỗi, chuyện gì sẽ xảy ra?

## 5. Usecase thực tế

- **Frontend**: Gọi API lấy dữ liệu sản phẩm → hiển thị ra giao diện.
- **Backend**: Xử lý ghi log vào DB → trả về cho client sau khi lưu xong.
- **UX**: Hiển thị loading spinner khi Promise còn ở trạng thái pending.

## 6. Bug thường gặp 🐞

- Quên `.catch()` → gây ra `UnhandledPromiseRejectionWarning`.
- Dùng `resolve` và `reject` cả hai trong cùng một Promise (phải chỉ dùng một).
- Không `return Promise` trong `.then()` → chuỗi Promise không hoạt động đúng.

## 7. Bài tập nhỏ 💪

- Tạo một Promise giả lập tải hình ảnh (`setTimeout` 2s), nếu `random < 0.5` thì `resolve("Ảnh tải xong")`, ngược lại thì `reject("Lỗi tải ảnh")`.
- Viết một hàm `getWeather(city)` trả về Promise: nếu `city = "Hanoi"` → `resolve "Trời nắng"`, nếu `city` khác → `reject "Không có dữ liệu"`.
- Dùng `async/await` để gọi lại 2 hàm trên.

---

## Promise Chaining

### 1. Sai lầm thường gặp

Nhiều bạn viết tất cả API vào trong một Promise như thế này:

```javascript
let handle = new Promise((resolve, reject) => {
    // API A
    // API B
    // API C
})
```

Như vậy sẽ khó quản lý vì:
- Mất ý nghĩa "chuỗi Promise".
- Nếu B phụ thuộc A, thì code sẽ trở thành "callback hell" ngay trong Promise constructor.

### 2. Cách đúng – Promise chaining

Giả sử em có 3 hàm trả về Promise:

```javascript
function apiA() {
  return new Promise((resolve) => {
    setTimeout(() => resolve("Kết quả A"), 1000);
  });
}

function apiB(dataFromA) {
  return new Promise((resolve) => {
    setTimeout(() => resolve(dataFromA + " → B"), 1000);
  });
}

function apiC(dataFromB) {
  return new Promise((resolve) => {
    setTimeout(() => resolve(dataFromB + " → C"), 1000);
  });
}
```

Gọi nối tiếp bằng `.then()` chaining:

```javascript
apiA()
  .then(resultA => {
    console.log("API A:", resultA);
    return apiB(resultA);
  })
  .then(resultB => {
    console.log("API B:", resultB);
    return apiC(resultB);
  })
  .then(resultC => {
    console.log("API C:", resultC);
  })
  .catch(err => {
    console.error("Có lỗi:", err);
  });
```

### 3. Viết bằng async/await (cách hiện đại, dễ đọc hơn)

```javascript
async function handleAPIs() {
  try {
    let resultA = await apiA();
    console.log("API A:", resultA);

    let resultB = await apiB(resultA);
    console.log("API B:", resultB);

    let resultC = await apiC(resultB);
    console.log("API C:", resultC);
  } catch (err) {
    console.error("Có lỗi:", err);
  }
}

handleAPIs();
```

Ưu điểm: code đọc tuần tự giống đồng bộ, dễ debug, dễ maintain.

### 4. Câu hỏi suy nghĩ 🤔

- Nếu API A, B, C không phụ thuộc lẫn nhau, em có nên dùng `.then()` nối tiếp không? Hay có cách chạy song song nhanh hơn?
- Em sẽ xử lý thế nào nếu API B thất bại nhưng vẫn muốn chạy API C với giá trị mặc định?
- Em muốn thầy tiếp tục giảng về `Promise.all` / `Promise.race` (để xử lý nhiều API song song) không?

---

## Promise với chỉ `resolve`

### 1. Nguyên nhân

Khi thấy một đoạn code như thế này:

```javascript
return new Promise((resolve) => {
  setTimeout(() => resolve("Kết quả A"), 1000);
});
```

Tức là trong constructor của Promise chỉ nhận mỗi `resolve` mà không có `reject`.
Người viết code biết chắc là Promise này luôn thành công, không có tình huống lỗi cần bắt.
Trong JavaScript, `new Promise` luôn truyền vào 2 tham số (`resolve`, `reject`). Nhưng nếu em không dùng `reject`, thì có thể bỏ đi để code gọn hơn.

### 2. Ví dụ có cả `reject`

Nếu API có khả năng thất bại (network lỗi, server hỏng), thì ta phải dùng cả `reject`:

```javascript
function apiA() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let success = Math.random() > 0.3; // 70% thành công
      if (success) {
        resolve("✅ API A thành công");
      } else {
        reject("❌ API A thất bại");
      }
    }, 1000);
  });
}
```

### 3. Khi nào chỉ cần `resolve`?

- Khi hàm luôn trả về giá trị hợp lệ (vd: delay, tính toán đơn giản, mock data).
- Khi em chỉ muốn mô phỏng một Promise "đợi thời gian" (delay function).

Ví dụ: viết hàm `sleep`:

```javascript
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}
```

`sleep` không có tình huống nào "thất bại" → không cần `reject`.

### 4. Kết luận

- Nếu Promise có khả năng thất bại → dùng cả `resolve`, `reject`.
- Nếu Promise luôn chắc chắn thành công → chỉ cần `resolve`.

📌 Câu hỏi suy nghĩ cho em:
- Trong thực tế, có API nào em nghĩ luôn `resolve` và không bao giờ `reject` không?
- Nếu em quên viết `reject` trong một API có thể lỗi, chuyện gì sẽ xảy ra khi nó gặp lỗi?
- Em có muốn thầy demo luôn trường hợp "quên reject" để thấy hậu quả không?

---

## Unhandled Promise Rejection

### 1. Khi không gọi `reject`

Ví dụ:

```javascript
function apiA() {
  return new Promise((resolve) => {
    setTimeout(() => {
      // có lỗi nhưng lại không reject
      console.error("Có lỗi xảy ra!");
      // KHÔNG gọi reject()
      // cũng KHÔNG gọi resolve()
    }, 1000);
  });
}

apiA()
  .then(result => console.log("Thành công:", result))
  .catch(err => console.log("Lỗi:", err));
```

Ở đây, vì không có `resolve` cũng không có `reject`, nên Promise mãi mãi ở trạng thái `pending`.
- `.then()` sẽ không chạy.
- `.catch()` cũng không chạy.

Hệ quả: app bị treo, như kiểu đang chờ hoài không có kết quả.

### 2. Khi có lỗi nhưng chỉ log ra

Ví dụ:

```javascript
function apiB() {
  return new Promise((resolve) => {
    try {
      throw new Error("Có lỗi thật sự!");
    } catch (e) {
      console.error("Bị lỗi nhưng không reject:", e.message);
      // resolve hoặc reject đều không được gọi
    }
  });
}
```

Tương tự, Promise sẽ bị kẹt ở `pending` → cực kỳ nguy hiểm trong thực tế, vì hệ thống tưởng là “chưa xong”.

### 3. Bài học rút ra

- Nếu trong hàm có thể gặp lỗi, luôn luôn nhớ `reject(error)`.
- Nếu cố tình không dùng `reject`, thì phải chắc chắn rằng Promise không bao giờ thất bại (vd `sleep`, `delay`,…).

### 4. Bài tập nhỏ cho em 💪

Viết một hàm `getData()` trả về Promise, trong đó:
- Nếu `random > 0.5` → `resolve "Dữ liệu đã tải"`.
- Nếu `random <= 0.5` → **quên reject** và chỉ `console.error("Server lỗi")`.

Sau đó chạy thử `.then().catch()` và quan sát kết quả: tại sao không vào `.catch()`?

```javascript
function getData() {
    return new Promise(resolve => {
        if (Math.random > 0.5) resolve("Dữ liệu đã tải");
        else console.error("Server lỗi");
    })
}

getData()
    .then(console.log("success"))
    .catch("Error")
```

---

## Phân tích và sửa lỗi bài tập

### 1. Code của em

```javascript
function getData() {
    return new Promise(resolve => {
        if (Math.random > 0.5) resolve("Dữ liệu đã tải");
        else console.error("Server lỗi");
    })
}

getData()
    .then(console.log("success"))
    .catch("Error")
```

### 2. Những lỗi đang có

- `Math.random`: `Math.random` là một hàm, phải gọi `Math.random()` mới ra số ngẫu nhiên. Hiện tại em đang so sánh `Math.random > 0.5`, luôn `true` (vì hàm được chuyển thành function object, so sánh với số thì luôn ra `true`). Kết quả: Promise luôn `resolve` → không bao giờ chạy vào "lỗi".
- `.then(console.log("success"))`: Em đang gọi hàm `console.log("success")` ngay lập tức, chứ không truyền callback vào `.then`. Đúng cú pháp phải là:
  ```javascript
  .then(result => console.log("success", result))
  ```
  Hoặc ngắn gọn:
  ```javascript
  .then(console.log)
  ```
- `.catch("Error")`: `.catch` cần một hàm callback để xử lý lỗi. Viết `"Error"` chỉ là một string, không có tác dụng gì. Đúng cú pháp:
  ```javascript
  .catch(err => console.error("Error:", err))
  ```

### 3. Phiên bản đúng (theo yêu cầu bài tập – quên reject)

```javascript
function getData() {
    return new Promise(resolve => {
        if (Math.random() > 0.5) {
            resolve("Dữ liệu đã tải");
        } else {
            console.error("Server lỗi"); // ❌ quên reject
        }
    });
}

getData()
    .then(result => console.log("Success:", result))
    .catch(err => console.error("Error:", err));
```

Kết quả:
- Nếu `random > 0.5` → `"Success: Dữ liệu đã tải"`.
- Nếu `random <= 0.5` → chỉ log `"Server lỗi"` ra console, nhưng Promise không reject → nên `.catch` không bao giờ chạy. Promise bị pending mãi.

### 4. Phiên bản chuẩn (có reject ✅)

```javascript
function getData() {
    return new Promise((resolve, reject) => {
        if (Math.random() > 0.5) {
            resolve("Dữ liệu đã tải");
        } else {
            reject("Server lỗi");
        }
    });
}

getData()
    .then(result => console.log("Success:", result))
    .catch(err => console.error("Error:", err));
```

Bây giờ:
- Nếu `random > 0.5` → `resolve`.
- Nếu `random <= 0.5` → `reject` → `.catch` chạy như mong đợi.

📌 Tóm lại:
- Khi em "quên reject", Promise bị pending mãi → `.catch` không chạy.
- Phải `reject()` thì Promise mới sang trạng thái `rejected`.

Bài tập nâng cao 💪
Viết lại đoạn code trên bằng `async/await` + `try..catch`, với flow y hệt:
- Bắt đầu với giá trị 100.
- Trong logic xử lý, cố tình `throw new Error("Bug!")`.
- Bắt lỗi trong `catch` và in ra `"Đã bắt lỗi: Bug!"`.

---

## Promise Lifecycle (Vòng đời của Promise)

### 1. Các trạng thái của Promise

Một Promise chỉ có 3 trạng thái chính:
- `pending` (đang chờ):
  - Promise vừa được tạo.
  - Chưa gọi `resolve()` hay `reject()`.
  - Đang "lơ lửng" – chưa biết kết quả.
- `fulfilled` (hoàn thành thành công):
  - Khi gọi `resolve(value)`.
  - Chuyển sang trạng thái thành công.
  - Kích hoạt `.then()`.
- `rejected` (bị lỗi):
  - Khi gọi `reject(error)`.
  - Chuyển sang trạng thái thất bại.
  - Kích hoạt `.catch()`.

### 2. Sơ đồ trực quan

```
                  +---------------------+
                  |      PENDING        |
                  | (chưa resolve/reject)|
                  +----------+----------+
                             |
               +-------------+--------------+
               |                            |
       resolve(value)                 reject(error)
               |                            |
               v                            v
     +-------------------+        +-------------------+
     |    FULFILLED      |        |     REJECTED      |
     |  (thành công)     |        |   (thất bại)      |
     +-------------------+        +-------------------+
               |                            |
            .then()                      .catch()
```

### 3. Trường hợp "quên reject"

Ví dụ của em:

```javascript
new Promise((resolve) => {
  // Không gọi resolve() hoặc reject()
});
```

Lúc này Promise không bao giờ rời khỏi trạng thái `pending`:
- `.then()` không chạy.
- `.catch()` cũng không chạy.
Giống như "treo máy" chờ hoài.

### 4. Ví dụ minh họa lifecycle

```javascript
let promise = new Promise((resolve, reject) => {
  let success = Math.random() > 0.5;

  setTimeout(() => {
    if (success) {
      resolve("Thành công ✅"); // chuyển -> fulfilled
    } else {
      reject("Thất bại ❌");    // chuyển -> rejected
    }
  }, 1000);
});

promise
  .then(result => console.log("then:", result))
  .catch(error => console.error("catch:", error));
```

Chạy nhiều lần sẽ thấy:
- Có lúc `.then()` chạy (`fulfilled`).
- Có lúc `.catch()` chạy (`rejected`).

### 5. Câu hỏi suy nghĩ cho em 🤔

- Một Promise sau khi đã `fulfilled` hoặc `rejected`, có thể quay lại trạng thái `pending` không?
- Nếu em gọi cả `resolve()` và `reject()` trong cùng một Promise, thì cái nào được ưu tiên?
- Em có muốn thầy demo bằng code thực tế để thấy sự khác biệt giữa `resolve` và `reject` gọi cùng lúc không?

---

## Promise Immutability (Tính bất biến của Promise)

Một Promise sau khi đã `fulfilled` hoặc `rejected` thì **KHÔNG bao giờ quay lại trạng thái `pending` được nữa.**

### Vì sao?

Promise trong JavaScript là immutable về trạng thái sau khi đã `settled` (tức là `fulfilled` hoặc `rejected`).
Nó chỉ chạy một lần duy nhất để xác định kết quả.
Sau khi `settled`:
- Nếu là `fulfilled`, thì `.then()` luôn nhận được cùng một giá trị mỗi lần gọi.
- Nếu là `rejected`, thì `.catch()` luôn nhận cùng một lỗi.

### Ví dụ minh họa

```javascript
let promise = new Promise((resolve, reject) => {
  resolve("Đã thành công");
  reject("Đã thất bại"); // <- lệnh này bị bỏ qua
});

promise
  .then(result => console.log("then:", result))
  .catch(err => console.error("catch:", err));
```

Kết quả:
```
then: Đã thành công
```
Lệnh `reject("Đã thất bại")` không có tác dụng, vì Promise đã `fulfilled` trước đó.

### ✅ Kết luận:

Trạng thái của một Promise chỉ thay đổi đúng 1 lần từ `pending` → `fulfilled` hoặc `pending` → `rejected`.
Sau đó, nó đóng băng ở trạng thái đó, không quay lại `pending` và cũng không đổi sang trạng thái khác.

---

## Consumers: `.then()`, `.catch()`

### 1. `.then()` trong Promise

Cú pháp:
```javascript
promise.then(onFulfilled, onRejected)
```
- `onFulfilled`: callback khi `resolve` được gọi.
- `onRejected`: callback khi `reject` được gọi.

Ví dụ:

```javascript
let promise = new Promise((resolve, reject) => {
  let success = Math.random() > 0.5;
  if (success) resolve("✅ Thành công");
  else reject("❌ Thất bại");
});

promise.then(
  result => console.log("then success:", result),
  error => console.error("then error:", error)
);
```

### 2. Dùng `.catch()` thay cho `then(null, errorHandlingFunction)`

Trong thực tế, ta ít khi viết `then(null, errorCallback)` vì trông rối.
Thay vào đó, ta dùng `.catch(errorCallback)`:

```javascript
promise
  .then(result => console.log("Success:", result))
  .catch(error => console.error("Error:", error));
```

Hai cách dưới đây là tương đương:
```javascript
promise.then(null, error => console.error(error));
promise.catch(error => console.error(error));
```

### 3. Ưu điểm của `.catch()`

- Code rõ ràng, dễ đọc.
- Có thể đặt `.catch()` ở cuối chuỗi Promise để xử lý mọi lỗi trong cả chuỗi.

Ví dụ:

```javascript
apiA()
  .then(resA => apiB(resA))
  .then(resB => apiC(resB))
  .catch(err => console.error("Có lỗi xảy ra:", err));
```

Chỉ cần một `.catch()` cuối cùng là đủ, không cần viết nhiều lần.

### 4. Bug thường gặp 🐞

- **Quên `return Promise` trong `.then()`**
  ```javascript
  apiA()
    .then(resA => { apiB(resA); }) // ❌ quên return
    .then(resB => console.log(resB));
  ```
  `resB` sẽ là `undefined`. Cần `return apiB(res);`
- Dùng `.then(success, error)` quá nhiều tầng → khó đọc, dễ thành callback hell kiểu mới.
  → Giải pháp: dùng `.catch()` một lần cuối cùng.

### 5. Bài tập nhỏ 💪

Viết lại đoạn sau bằng cách dùng `.catch()` thay vì `then(null, errorHandler)`:

```javascript
let promise = new Promise((resolve, reject) => {
  reject("Server lỗi");
});

promise.then(null, error => console.error("Error:", error));
```

Yêu cầu: dùng `.catch()` để code gọn hơn.

---

## Promises Chaining

### 1. Promise chaining là gì?

Khi em dùng `.then()`, giá trị trả về từ callback sẽ trở thành input cho `.then()` tiếp theo.
Nếu handler trả về một Promise mới, thì `.then()` kế tiếp sẽ chờ Promise đó `settle` (`resolve` hoặc `reject`) rồi mới chạy.
Đây chính là cách để tránh callback hell.

### 2. Ví dụ đơn giản

```javascript
new Promise(resolve => {
  setTimeout(() => resolve(1), 1000);  // (*) resolve sau 1s
})
.then(result => {
  console.log("Kết quả 1:", result);   // (**)
  return result * 2;                   // trả về 2
})
.then(result => {
  console.log("Kết quả 2:", result);   // (***)
  return result * 2;                   // trả về 4
})
.then(result => {
  console.log("Kết quả 3:", result);   // 4
});
```

Flow:
- Promise đầu tiên `resolve` → `result = 1`.
- Handler đầu tiên chạy, `return 2`.
- Handler tiếp theo nhận 2, xử lý, `return 4`.
- Handler tiếp theo nhận 4, in ra.

### 3. Trường hợp trả về Promise trong `.then()`

```javascript
new Promise(resolve => {
  setTimeout(() => resolve(10), 1000);
})
.then(result => {
  console.log("Lần 1:", result);  // 10
  return new Promise(resolve => {
    setTimeout(() => resolve(result * 2), 1000);
  });
})
.then(result => {
  console.log("Lần 2:", result);  // 20 (chờ thêm 1s)
  return result * 2;
})
.then(result => {
  console.log("Lần 3:", result);  // 40
});
```

Ở đây:
- Lần 1: Promise ban đầu `resolve 10`.
- Lần 2: Handler `return Promise` mới → `.then()` tiếp theo phải chờ `settle` rồi mới nhận 20.
- Lần 3: Nhận 20, nhân đôi thành 40.

### 4. Bug thường gặp 🐞

- **Quên `return Promise` trong `.then()`**
  ```javascript
  apiA()
    .then(res => { apiB(res); }) // ❌ quên return
    .then(resB => console.log(resB)); // resB = undefined
  ```
  → Cần `return apiB(res);`
- Dùng quá nhiều `.then(success, error)`
  → khó đọc, dễ thành "callback hell kiểu mới".
  → Giải pháp: dùng `.catch()` một lần ở cuối.

### 5. Bài tập nhỏ 💪

Viết chuỗi Promise như sau:
- Promise đầu tiên `resolve` số 5 sau 1 giây.
- Handler đầu tiên nhận số này, cộng thêm 10, rồi `return`.
- Handler tiếp theo nhân đôi kết quả.
- Handler cuối cùng in ra kết quả.

Hãy viết bằng Promise chaining.

```javascript
new Promise(resolve => {
    setTimeout(() => resolve(5), 1000) ;
})
.then(result => {
    return result + 10;
})
.then(result => {
    return result*2;
})
.then(result => {
    console.log(result)
})
```

---

## Phân tích và cải tiến bài tập Promise Chaining

### 1. Promise đầu tiên

```javascript
new Promise(resolve => {
    setTimeout(() => resolve(5), 1000);
})
```
Sau 1 giây, `resolve(5)` → giá trị 5 đi vào `.then()` đầu tiên.

### 2. Handler đầu tiên

```javascript
.then(result => {
    return result + 10;
})
```
Nhận 5, cộng thêm 10 → trả về 15.

### 3. Handler tiếp theo

```javascript
.then(result => {
    return result * 2;
})
```
Nhận 15, nhân đôi → 30.

### 4. Handler cuối cùng

```javascript
.then(result => {
    console.log(result);
})
```
In ra 30. ✅

### Kết quả chạy sau 1 giây

```
30
```

### Một số cải tiến nhỏ (viết gọn hơn)

Vì mỗi `.then()` chỉ có một `return`, ta có thể viết ngắn gọn:

```javascript
new Promise(resolve => setTimeout(() => resolve(5), 1000))
  .then(result => result + 10)
  .then(result => result * 2)
  .then(console.log);
```
Kết quả vẫn là 30.

### Bài tập nâng cao 💪

Thử viết lại cùng logic trên nhưng dùng `async/await` thay vì chaining.
Gợi ý:

```javascript
async function run() {
  // tạo Promise resolve(5) sau 1s
  let result = await new Promise(resolve => setTimeout(() => resolve(5), 1000));

  // cộng thêm 10
  // nhân đôi
  // in kết quả
}

run();
```

---

## Error Handling with Promises: "Invisible try..catch"

### 1. "Invisible try..catch" là gì?

Trong Promise, cả executor (hàm trong `new Promise(...)`) và các handler (`.then()`) đều được bọc ngầm bởi một `try..catch`.
Nếu có lỗi xảy ra (`throw error` hoặc code lỗi), thì Promise sẽ tự động chuyển sang trạng thái `rejected`, giống như gọi `reject(error)`.

### 2. Ví dụ – Executor bị lỗi

```javascript
let promise = new Promise((resolve, reject) => {
  throw new Error("Có lỗi trong executor!");
});

promise
  .then(result => console.log("Success:", result))
  .catch(error => console.error("Error:", error.message));
```

Output:
```
Error: Có lỗi trong executor!
```

### 3. Ví dụ – Handler bị lỗi

```javascript
Promise.resolve(1)
  .then(result => {
    console.log("Result:", result);
    throw new Error("Lỗi trong then!");
  })
  .catch(error => {
    console.error("Error:", error.message);
  });
```

Dù ban đầu `resolve(1)`, nhưng vì handler `then` ném lỗi → Promise bị `rejected`.
Output:
```
Result: 1
Error: Lỗi trong then!
```

### 4. Ưu điểm

- Không cần phải tự `try..catch` thủ công trong mỗi handler.
- Lỗi luôn được “bắt” và chuyển thành `rejected`.

### 5. Bug thường gặp 🐞

- **Quên `.catch()`**: Nếu không có `.catch()`, lỗi sẽ bị báo là `UnhandledPromiseRejection`. Trong browser: xuất hiện warning. Trong Node.js: có thể crash app (ở phiên bản mới).
- **Nhầm lẫn giữa lỗi sync và async**:
  ```javascript
  try {
    Promise.reject("Lỗi!"); // ❌ try..catch này không bắt được
  } catch (e) {
    console.error("Không bao giờ chạy", e);
  }
  ```
  Vì Promise chạy async, nên phải `.catch()` hoặc `await` mới bắt được.

### 6. Bài tập nhỏ 💪

Viết code với flow sau:
- Tạo một Promise `resolve` với số 100.
- Trong `.then()` đầu tiên, cố tình ném lỗi `throw new Error("Bug!")`.
- Viết `.catch()` để in ra `"Đã bắt lỗi: Bug!"`.

```javascript
new Promise(resolve => resolve(100))
.then(()=>{throw new Error("Bug!");})
.catch(()=>console.log("Đã bắt lỗi: Bug!"))
```

---

## Phân tích và cải tiến bài tập Error Handling

### Code của em

```javascript
new Promise(resolve => resolve(100))
  .then(() => { throw new Error("Bug!"); })
  .catch(() => console.log("Đã bắt lỗi: Bug!"));
```

### Flow hoạt động

1. `new Promise(resolve => resolve(100))`
   → Promise `fulfilled` ngay lập tức với giá trị 100.
2. `.then(() => { throw new Error("Bug!"); })`
   → Callback trong `.then()` chạy, nhưng ném lỗi.
   → Promise lập tức chuyển sang trạng thái `rejected` với error `"Bug!"`.
3. `.catch(() => console.log("Đã bắt lỗi: Bug!"))`
   → Lỗi được `.catch()` bắt lại, in ra:
   ```
   Đã bắt lỗi: Bug!
   ```

### Nhận xét

✅ Em hiểu đúng cơ chế "invisible try..catch" của Promise: chỉ cần ném lỗi trong handler thì tự động thành reject.

⚠️ Tuy nhiên, hiện tại em không dùng error object trong `.catch()`. Nếu muốn debug chi tiết hơn, ta nên viết thế này:

```javascript
new Promise(resolve => resolve(100))
  .then(() => { throw new Error("Bug!"); })
  .catch(err => console.log("Đã bắt lỗi:", err.message));
```

Output:
```
Đã bắt lỗi: Bug!
```

### Bài tập nâng cao 💪

Hãy thử viết lại đoạn code trên bằng `async/await` + `try..catch`, với flow y hệt:
- Bắt đầu với giá trị 100.
- Trong logic xử lý, cố tình `throw new Error("Bug!")`.
- Bắt lỗi trong `catch` và in ra `"Đã bắt lỗi: Bug!"`.

---

## Error Handling with Promises: Lỗi di chuyển trong chuỗi

### 1. Nguyên tắc cơ bản

- Nếu `throw` bên trong `.catch()`, lỗi sẽ chuyển tiếp sang `.catch()` tiếp theo (nếu có).
- Nếu `.catch()` xử lý xong lỗi và `return` bình thường, thì Promise sẽ được coi như `fulfilled`, và control đi tiếp vào `.then()` kế tiếp.

Tức là `.catch()` vừa có thể nuốt lỗi (khi xử lý xong), vừa có thể ném lỗi tiếp cho handler sau.

### 2. Ví dụ – `throw` trong `.catch()`

```javascript
Promise.reject("Lỗi ban đầu")
  .catch(err => {
    console.error("Bắt lần 1:", err);
    throw new Error("Lỗi mới trong catch!");
  })
  .then(() => {
    console.log("Sẽ không chạy do bị lỗi tiếp");
  })
  .catch(err => {
    console.error("Bắt lần 2:", err.message);
  });
```

Kết quả:
```
Bắt lần 1: Lỗi ban đầu
Bắt lần 2: Lỗi mới trong catch!
```

### 3. Ví dụ – xử lý lỗi xong, `return` giá trị

```javascript
Promise.reject("Lỗi ban đầu")
  .catch(err => {
    console.error("Đã xử lý lỗi:", err);
    return "Giá trị thay thế sau khi xử lý";
  })
  .then(result => {
    console.log("Tiếp tục bình thường:", result);
  });
```

Kết quả:
```
Đã xử lý lỗi: Lỗi ban đầu
Tiếp tục bình thường: Giá trị thay thế sau khi xử lý
```

### 4. Tóm tắt flow

- `throw` trong `.catch()` → lỗi được truyền tiếp xuống `.catch()` sau.
- `return` bình thường trong `.catch()` → coi như lỗi đã xử lý xong → đi tiếp vào `.then()` sau.

### 5. Bug thường gặp 🐞

- Quên `return` trong `.catch()` khi muốn thay thế giá trị → `.then()` sau nhận `undefined`.
- Lẫn lộn giữa “tiếp tục flow thành công” và “ném lỗi mới” trong `.catch()` → dẫn đến flow không như mong muốn.

### 6. Bài tập nhỏ 💪

Viết chuỗi Promise như sau:
- Promise ban đầu bị `reject` với `"Server lỗi"`.
- Trong `.catch()` đầu tiên: in ra `"Bắt lỗi lần 1"`, xử lý lỗi xong và `return "Default data"`.
- `.then()` tiếp theo: in ra `"Nhận được: Default data"`.

---

## Error Handling with Promises: Unhandled Rejection Event

### 1. Lỗi bình thường (synchronous error)

Nếu em viết code bình thường mà có lỗi không được `try..catch`, script sẽ chết ngay:

```javascript
throw new Error("Bug!");
console.log("Dòng này không bao giờ chạy");
```

Kết quả: trình duyệt / Node.js log lỗi và dừng script.

### 2. Lỗi bất đồng bộ trong Promise

Với Promise, khi `reject` mà không có `.catch()` để xử lý → gọi là `unhandled rejection`.
Lúc này JavaScript engine sẽ theo dõi, và nếu sau một vòng event loop mà vẫn chưa có `.catch()` gắn vào, nó sẽ sinh ra global error.

Ví dụ:

```javascript
new Promise((_, reject) => reject("Lỗi server!"));
```

Nếu không `.catch()`, trình duyệt sẽ báo:
```
Uncaught (in promise) Lỗi server!
```

### 3. Bắt lỗi unhandled rejection trong browser

Trình duyệt cho phép bắt lỗi toàn cục bằng event `unhandledrejection`:

```javascript
window.addEventListener("unhandledrejection", event => {
  console.error("Có lỗi Promise chưa xử lý:", event.reason);
});
```

Lúc này, nếu có Promise nào bị `reject` mà không `.catch()`, event này sẽ được gọi.

### 4. Lợi ích trong thực tế

- Giúp debug những Promise mà dev quên `.catch()`.
- Dùng để log lỗi toàn cục gửi về server (ví dụ hệ thống logging / Sentry).

### 5. Bug thường gặp 🐞

- Quên `.catch()` ở cuối chuỗi Promise → dễ gây crash app.
- Xử lý lỗi không đầy đủ (ví dụ `.catch()` chỉ log mà không trả về gì) → khiến chuỗi sau đó nhận `undefined`.

### 6. Bài tập nhỏ 💪

Viết đoạn code:
- Tạo một Promise luôn `reject("Lỗi API")`.
- Không `.catch()` → xem console báo lỗi gì.
- Sau đó thêm `window.addEventListener("unhandledrejection", ...)` để log ra `"Bắt được unhandled rejection: ..."`.

---

## Frontend vs Backend: Unhandled Promise Rejection

### 1. Lý do chính

- **Frontend (browser)**:
  - Người dùng cuối chạy app trên trình duyệt.
  - Nếu có lỗi Promise bị `reject` mà không xử lý → nó không làm crash ngay JS engine (vì Promise là async).
  - Nhưng nếu im lặng bỏ qua → người dùng sẽ thấy app đứng im, không phản hồi gì → trải nghiệm rất tệ.
  - Vì vậy browser sinh ra event `unhandledrejection` để dev có cơ chế bắt toàn cục (global handler).
- **Backend (Node.js)**:
  - Backend server chạy liên tục, lỗi không xử lý có thể gây rò rỉ tài nguyên, hỏng kết nối DB, crash process...
  - Node.js không muốn "âm thầm bỏ qua" lỗi, nên mặc định:
    - Từ Node 15+, một `unhandledRejection` sẽ ném lỗi global và có thể kill process.
    - Dev có thể bắt bằng:
      ```javascript
      process.on("unhandledRejection", (reason, promise) => {
        console.error("Unhandled rejection:", reason);
      });
      ```

Tóm lại:
- **Frontend**: tránh việc app im lặng treo → có `window.addEventListener("unhandledrejection")`.
- **Backend**: đảm bảo server không chạy sai → Node.js mặc định coi `unhandledRejection` là lỗi nghiêm trọng.

### 2. So sánh trực quan

| | Frontend (Browser) | Backend (Node.js) |
|---|---|---|
| **Nếu Promise reject mà không catch** | Báo `Uncaught (in promise)` | Báo `UnhandledPromiseRejectionWarning` (và từ Node 15 trở đi có thể crash app) |
| **Global handler** | `window.addEventListener("unhandledrejection", ...)` | `process.on("unhandledRejection", ...)` |
| **Triết lý** | Bảo vệ UX, tránh im lặng treo | Bảo vệ server, tránh chạy sai logic |

### 3. Thực tế dev nên làm gì?

- **Frontend**: Luôn `.catch()` ở cuối chain, hoặc có 1 global handler để log lỗi.
- **Backend**: Luôn xử lý lỗi async. Nếu có `unhandledRejection`, coi đó là bug, log và crash → để hệ thống restart service (theo triết lý fail fast).

### 4. Bài tập nhỏ 💪

Em thử chạy code này ở browser và Node.js để so sánh:

```javascript
new Promise((_, reject) => reject("Lỗi test!"));

// Browser: thử thêm
// window.addEventListener("unhandledrejection", e => console.log("Browser bắt lỗi:", e.reason));

// Node.js: thử thêm
// process.on("unhandledRejection", (reason, p) => console.log("Node bắt lỗi:", reason));
```

Em sẽ thấy khác nhau khá rõ: browser log warning, còn Node.js log lỗi dạng warning và có thể crash app tùy version.

---

## Promise API: `Promise.all()`

### 1. Cú pháp

```javascript
Promise.all([promise1, promise2, promise3, ...])
  .then(results => {
    // results là array chứa kết quả của từng promise
  })
  .catch(error => {
    // Nếu bất kỳ promise nào reject, toàn bộ Promise.all sẽ reject ngay
  });
```

### 2. Nguyên tắc hoạt động

- Nhận vào một iterable (thường là array).
- Chỉ khi tất cả promise trong array đều `fulfilled` → `Promise.all` `resolve` với mảng kết quả.
- Nếu một promise bị `reject` → `Promise.all` `reject` ngay với error đó.

### 3. Ví dụ cơ bản

```javascript
let p1 = Promise.resolve(10);
let p2 = new Promise(resolve => setTimeout(() => resolve(20), 1000));
let p3 = Promise.resolve(30);

Promise.all([p1, p2, p3])
  .then(results => console.log("Kết quả:", results))
  .catch(err => console.error("Error:", err));
```

Output sau 1 giây:
```
Kết quả: [10, 20, 30]
```

### 4. Ví dụ bị reject

```javascript
let p1 = Promise.resolve(1);
let p2 = Promise.reject("Lỗi ở p2");
let p3 = Promise.resolve(3);

Promise.all([p1, p2, p3])
  .then(results => console.log("Kết quả:", results))
  .catch(err => console.error("Error:", err));
```

Output:
```
Error: Lỗi ở p2
```

### 5. Use case thực tế

Gọi nhiều API song song thay vì chờ tuần tự:
- Lấy thông tin user
- Lấy danh sách bài viết
- Lấy thông báo
Tất cả xong rồi mới render giao diện.

Ví dụ:

```javascript
Promise.all([
  fetch("/api/user"),
  fetch("/api/posts"),
  fetch("/api/notifications")
])
  .then(async ([userRes, postsRes, notiRes]) => {
    const user = await userRes.json();
    const posts = await postsRes.json();
    const noti = await notiRes.json();
    console.log({ user, posts, noti });
  })
  .catch(err => console.error("API lỗi:", err));
```

### 6. Bug thường gặp 🐞

- **Nhầm lẫn giữa tuần tự và song song**:
  - Nếu viết `await` từng cái → chạy tuần tự, chậm.
  - Nếu gói vào `Promise.all` → chạy song song, nhanh hơn.
- **Một lỗi làm fail tất cả**:
  - Chỉ cần 1 promise `reject` → `Promise.all` `reject`, bỏ qua kết quả các promise khác.
  - Nếu muốn lấy cả kết quả lẫn lỗi → phải dùng `Promise.allSettled`.

### 7. Bài tập nhỏ 💪

Viết code dùng `Promise.all` với 3 Promise:
- Promise 1 `resolve` số 5 sau 500ms.
- Promise 2 `resolve` số 10 sau 1000ms.
- Promise 3 `resolve` số 15 sau 1500ms.

Khi tất cả xong, in ra tổng của 3 số này (30).

---

## Promise API: `Promise.allSettled()`

### 1. Cú pháp

```javascript
Promise.allSettled([promise1, promise2, ...])
  .then(results => {
    // results là array các object có dạng:
    // { status: "fulfilled", value: ... } hoặc { status: "rejected", reason: ... }
  });
```

### 2. Nguyên tắc hoạt động

- `Promise.all` → fail ngay nếu có 1 thằng `reject`.
- `Promise.allSettled` → chờ tất cả `settle` (`fulfilled` hoặc `rejected`).
- Kết quả luôn là 1 array, mỗi phần tử có `status` để cho biết thành công hay thất bại.

### 3. Ví dụ

```javascript
let p1 = Promise.resolve(10);
let p2 = Promise.reject("Lỗi ở p2");
let p3 = new Promise(resolve => setTimeout(() => resolve(30), 500));

Promise.allSettled([p1, p2, p3])
  .then(results => console.log(results));
```

Output:
```json
[
  { "status": "fulfilled", "value": 10 },
  { "status": "rejected", "reason": "Lỗi ở p2" },
  { "status": "fulfilled", "value": 30 }
]
```

### 4. Use case thực tế

Khi gọi nhiều API song song nhưng:
- Cái nào thành công thì lấy kết quả,
- Cái nào thất bại thì ghi log hoặc bỏ qua,
- Không muốn “cả trang web chết” chỉ vì 1 API hỏng.

Ví dụ: tải 3 widget cùng lúc (user info, weather, news). Nếu weather API lỗi thì vẫn hiển thị user info và news.

### 5. Bug thường gặp 🐞

- **Nhầm với `Promise.all`**: nhiều bạn nghĩ `allSettled` trả về giá trị trực tiếp → nhưng thực tế phải check `status`.
- **Không handle lỗi**: quên check `status: "rejected"`, dẫn đến dùng nhầm `reason` như thể nó là `value`.

### 6. Bài tập nhỏ 💪

Em thử viết code:
- Promise A `resolve "OK A"` sau 1 giây.
- Promise B `reject "Fail B"` sau 0.5 giây.
- Promise C `resolve "OK C"` ngay lập tức.

Dùng `Promise.allSettled`, in ra kết quả của từng promise với format:
- `"Thành công: ..."`
- `"Thất bại: ..."`

```javascript
let p1 = new Promise(resolve => setTimeout(() => resolve("OK A"), 1000));
let p2 = new Promise(reject=>setTimeout(() => reject("Fail B"), 500));
let p3 = Promise.resolve("OK C");

Promise.allSettled([p1, p2, p3])
  .then(results =>{
      console.log("Thanh cong: ", results.filter(i => i.status == "fullfilled"));
      console.log("That bai: ", results.filter(i => i.status == "reject"));
  });
```

---

## Phân tích và sửa lỗi bài tập `Promise.allSettled`

### 1. Bug trong Promise p2

```javascript
let p2 = new Promise(reject=>setTimeout(() => reject("Fail B"), 500));
```
Ở đây constructor `new Promise` nhận 2 tham số (`resolve`, `reject`), chứ không phải chỉ có `reject`.
Phải viết thế này:

```javascript
let p2 = new Promise((_, reject) => setTimeout(() => reject("Fail B"), 500));
```

### 2. Sai chính tả status

Trong `Promise.allSettled`, object trả về có:
- `status: "fulfilled"` ✅ (không phải `"fullfilled"`)
- `status: "rejected"` ✅ (không phải `"reject"`)

### 3. Code đã fix

```javascript
let p1 = new Promise(resolve => setTimeout(() => resolve("OK A"), 1000));
let p2 = new Promise((_, reject) => setTimeout(() => reject("Fail B"), 500));
let p3 = Promise.resolve("OK C");

Promise.allSettled([p1, p2, p3])
  .then(results => {
    console.log("Thành công:", results.filter(i => i.status === "fulfilled"));
    console.log("Thất bại:", results.filter(i => i.status === "rejected"));
  });
```

### 4. Output mong đợi sau 1 giây

```
Thành công: [
  { status: "fulfilled", value: "OK A" },
  { status: "fulfilled", value: "OK C" }
]
Thất bại: [
  { status: "rejected", reason: "Fail B" }
]
```

Bài nâng cao: Em thử map `results` ra dạng text rõ ràng hơn, ví dụ:
```
Thành công: OK A
Thành công: OK C
Thất bại: Fail B
```

---

## Promise API: `Promise.race()`

### 1. Cú pháp

```javascript
Promise.race([promise1, promise2, ...])
  .then(result => {
    // nhận kết quả của promise nào settled (fulfilled hoặc rejected) sớm nhất
  })
  .catch(error => {
    // nếu promise đầu tiên bị reject, thì chạy vào đây
  });
```

### 2. Nguyên tắc hoạt động

- Giống `Promise.all`, nhận vào một iterable (thường là array).
- Chỉ chờ promise đầu tiên `settle` (hoặc `fulfilled` hoặc `rejected`).
- Promise trả về có giá trị (nếu `fulfilled`) hoặc lỗi (nếu `rejected`).
- Các promise khác vẫn chạy, chỉ là kết quả của chúng bị bỏ qua.

### 3. Ví dụ

```javascript
let p1 = new Promise(resolve => setTimeout(() => resolve("OK từ p1"), 1000));
let p2 = new Promise(resolve => setTimeout(() => resolve("OK từ p2"), 500));
let p3 = new Promise(resolve => setTimeout(() => resolve("OK từ p3"), 2000));

Promise.race([p1, p2, p3])
  .then(result => console.log("Kết quả race:", result))
  .catch(err => console.error("Lỗi race:", err));
```

Output sau 0.5 giây:
```
Kết quả race: OK từ p2
```

### 4. Ví dụ lỗi

```javascript
let p1 = new Promise((_, reject) => setTimeout(() => reject("Lỗi từ p1"), 300));
let p2 = new Promise(resolve => setTimeout(() => resolve("OK từ p2"), 500));

Promise.race([p1, p2])
  .then(result => console.log("Kết quả:", result))
  .catch(err => console.error("Lỗi:", err));
```

Output sau 0.3 giây:
```
Lỗi: Lỗi từ p1
```

### 5. Use case thực tế

Timeout API call:
- Gọi API, nhưng nếu quá 5s chưa xong thì coi như timeout.

Ví dụ:

```javascript
function fetchWithTimeout(url, ms) {
  let timeout = new Promise((_, reject) =>
    setTimeout(() => reject("Timeout!"), ms)
  );
  return Promise.race([fetch(url), timeout]);
}

fetchWithTimeout("/api/data", 5000)
  .then(res => console.log("Có dữ liệu:", res))
  .catch(err => console.error("Lỗi:", err));
```

### 6. Bug thường gặp 🐞

- **Nhầm với `Promise.any`** (cái này chỉ lấy promise `fulfilled` đầu tiên, bỏ qua `rejected`).
- Tưởng promise khác sẽ bị hủy → thật ra các promise còn lại vẫn chạy, chỉ là kết quả bị bỏ qua (cần chú ý khi chúng tốn tài nguyên, ví dụ call API).

### 7. Bài tập nhỏ 💪

Viết code tạo 3 promise:
- p1 `resolve "A"` sau 1 giây.
- p2 `resolve "B"` sau 2 giây.
- p3 `reject "Lỗi C"` sau 0.5 giây.

Dùng `Promise.race` để xem kết quả cuối cùng là gì.

---

## Promise API: `Promise.any()`

### 1. Cú pháp

```javascript
Promise.any([promise1, promise2, ...])
  .then(result => {
    // Nhận kết quả của promise fulfilled đầu tiên
  })
  .catch(error => {
    // Nếu tất cả đều reject → bị lỗi AggregateError
  });
```

### 2. Nguyên tắc hoạt động

- Chỉ quan tâm promise `fulfilled` đầu tiên.
- Bỏ qua các promise bị `reject`.
- Nếu tất cả đều `reject`, thì `reject` với `AggregateError` (chứa toàn bộ lỗi trong `.errors`).

### 3. Ví dụ fulfilled sớm nhất

```javascript
let p1 = new Promise((_, reject) => setTimeout(() => reject("Fail A"), 300));
let p2 = new Promise(resolve => setTimeout(() => resolve("OK B"), 500));
let p3 = new Promise(resolve => setTimeout(() => resolve("OK C"), 1000));

Promise.any([p1, p2, p3])
  .then(result => console.log("Kết quả:", result))
  .catch(err => console.error("Lỗi:", err));
```

Output sau 0.5 giây:
```
Kết quả: OK B
```
(p1 reject bị bỏ qua, p2 fulfilled trước p3 → lấy kết quả của p2).

### 4. Ví dụ tất cả đều reject

```javascript
let p1 = Promise.reject("Lỗi A");
let p2 = Promise.reject("Lỗi B");
let p3 = Promise.reject("Lỗi C");

Promise.any([p1, p2, p3])
  .then(result => console.log("Kết quả:", result))
  .catch(err => {
    console.error("Tất cả đều fail!");
    console.error(err instanceof AggregateError); // true
    console.error("Danh sách lỗi:", err.errors);
  });
```

Output:
```
Tất cả đều fail!
true
Danh sách lỗi: [ 'Lỗi A', 'Lỗi B', 'Lỗi C' ]
```

### 5. So sánh nhanh

| API | Fulfilled khi nào? | Rejected khi nào? |
|---|---|---|
| `Promise.all` | Khi tất cả đều fulfilled (array kết quả) | Ngay khi 1 promise reject |
| `Promise.allSettled` | Khi tất cả settled (fulfilled/rejected) | Không bao giờ reject |
| `Promise.race` | Khi promise đầu tiên settled (fulfilled/rejected) | Ngay nếu thằng đầu settle là reject |
| `Promise.any` | Khi promise fulfilled đầu tiên | Nếu tất cả reject → `AggregateError` |

### 6. Use case thực tế

Muốn lấy promise thành công đầu tiên trong một nhóm, bỏ qua các lỗi:
- Ví dụ: gọi API từ nhiều mirror server → cái nào trả dữ liệu hợp lệ trước thì dùng, bỏ qua các server lỗi.

### 7. Bug thường gặp 🐞

- **Nhầm với `race`** → `race` có thể trả về lỗi nếu promise reject nhanh nhất, còn `any` thì chỉ quan tâm đến `fulfilled`.
- Quên check `AggregateError` khi tất cả fail → dễ bị crash nếu code không xử lý `.errors`.

### 8. Bài tập nhỏ 💪

Em hãy viết 3 promise:
- p1 `reject "Lỗi A"` sau 0.2 giây.
- p2 `reject "Lỗi B"` sau 0.4 giây.
- p3 `resolve "Thành công C"` sau 0.6 giây.

Dùng `Promise.any` để in ra `"Thành công C"`.

---

## So sánh các Promise API (Giọng điệu đầu bếp 🍳)

### 🍳 1. `Promise.all` – “Đợi đủ nguyên liệu”

Giống như đầu bếp chờ tất cả nguyên liệu (rau, thịt, gia vị) được giao đủ mới bắt đầu nấu.
- Nếu thiếu 1 nguyên liệu → toàn bộ món ăn hỏng, không nấu được.
- Kết quả: có đủ tất cả trong 1 giỏ.

### 🥘 2. `Promise.allSettled` – “Kiểm kê hết nguyên liệu”

Đầu bếp ghi chú lại tình trạng của từng nguyên liệu sau khi chợ giao:
- Cái nào tươi → `fulfilled`.
- Cái nào hỏng → `rejected`.
Nhưng dù sao vẫn có bảng tổng kết đầy đủ: cái ngon, cái hỏng.
Đầu bếp sau đó tự quyết định dùng cái nào.

### 🍜 3. `Promise.race` – “Ai chạy nhanh hơn thì lấy”

Ba anh phụ bếp đi chợ, ai về trước thì đầu bếp lấy giỏ hàng của người đó để nấu ngay.
- Có thể là anh về sớm với đồ ngon (`fulfilled`).
- Hoặc anh về sớm mà tay không, gặp mưa (`rejected`) → đầu bếp đành báo lỗi.

### 🍰 4. `Promise.any` – “Chỉ cần một món ngon”

Ba anh phụ bếp thử nấu món tráng miệng khác nhau.
- Anh nào làm ra món ngon đầu tiên → đưa cho khách ăn.
- Anh nào thất bại (bánh cháy, súp mặn quá) → bỏ qua, không tính.
- Nếu tất cả đều hỏng → báo khách “Xin lỗi, hôm nay bếp fail toàn tập” (`AggregateError`).

### 🎯 Tóm lại (theo ngôn ngữ bếp)

- `all` → Đợi tất cả bếp phụ mang nguyên liệu, 1 hỏng là fail cả bàn.
- `allSettled` → Ghi nhận đủ: cái nào hỏng, cái nào tốt, rồi chọn tiếp.
- `race` → Ai về trước thì lấy của người đó, bất kể ngon hay hỏng.
- `any` → Chỉ cần ai đó làm được món ngon → thành công, bỏ qua mấy anh fail.

### Bài tập nhỏ cho em:

Nếu thầy (đầu bếp trưởng) muốn gọi 3 phụ bếp đi mua nguyên liệu, nhưng chỉ cần một người về thành công để bắt đầu nấu, thì em sẽ dùng API nào?

---

## Giải pháp bài tập: Chọn Promise API

Trong tình huống “3 phụ bếp đi chợ, chỉ cần một người mang được nguyên liệu ngon về là đủ để nấu”, thì:
- `Promise.any` sẽ bỏ qua các phụ bếp fail (mua nhầm, quên đồ…).
- Ngay khi có một phụ bếp thành công → bếp trưởng bắt đầu nấu.
- Nếu cả 3 đều fail → báo lỗi `AggregateError` (cả đội bếp đi tong 😅).

### Mở rộng thêm:

- Nếu thầy nói: “Anh nào về trước thì dùng đồ của anh đó, bất kể ngon hay hỏng” → dùng `Promise.race`.
- Nếu thầy nói: “Tôi phải kiểm kê đủ 3 anh: ai ngon, ai fail, để còn tính toán” → dùng `Promise.allSettled`.
- Nếu thầy nói: “Tất cả 3 anh đều phải mang đủ đồ, thiếu 1 anh coi như hỏng món” → dùng `Promise.all`.

---

## Promise API: `Promise.resolve()` và `Promise.reject()`

### 1. `Promise.resolve(value)`

Giống như bếp trưởng nói: “Món này coi như xong rồi, không cần phải chờ nấu nữa!”
Nó tạo ngay một promise đã `fulfilled` với giá trị mình đưa vào.
Thường dùng khi muốn đồng bộ hóa dữ liệu bình thường thành dạng Promise để có thể `.then()` cho thống nhất.

```javascript
Promise.resolve("Nguyên liệu sẵn sàng")
  .then(res => console.log(res));
// 👉 "Nguyên liệu sẵn sàng"
```

**Use case thực tế**:
Một hàm API có thể trả về ngay kết quả (cache có sẵn) hoặc phải fetch từ server.
Để code phía sau không phải phân biệt → luôn cho nó trả về Promise.

### 2. `Promise.reject(error)`

Giống như bếp trưởng hét lên: “Không nấu nữa, hỏng rồi! 🚫”
Nó tạo ngay một promise bị `rejected` với error mình đưa vào.
Dùng khi cần trả về lỗi ngay lập tức, nhưng vẫn tuân theo cơ chế promise (có thể `.catch`).

```javascript
Promise.reject(new Error("Thiếu nguyên liệu!"))
  .catch(err => console.error(err.message));
// 👉 "Thiếu nguyên liệu!"
```

### 3. Tình huống bếp ví dụ

- `resolve`: Phụ bếp mang sẵn 1 giỏ rau sạch về → không cần chờ, đưa ngay cho bếp trưởng.
- `reject`: Phụ bếp báo ngay “chợ đóng cửa, không có đồ” → báo lỗi liền, khỏi chờ.

### 4. Bug thường gặp 🐞

- **Nhầm lẫn khi `.then()`**
  ```javascript
  Promise.resolve("OK").then(console.log("xong"));
  // Sai ❌: in ngay "xong", không phải đợi promise
  // Đúng ✅:
  Promise.resolve("OK").then(() => console.log("xong"));
  ```
- **Không bắt lỗi khi `reject`** → dẫn đến `UnhandledPromiseRejection`.
  ```javascript
  Promise.reject("Lỗi")
  // Nếu không có .catch -> warning trong console
  ```

### 5. Bài tập nhỏ 💪

Em hãy viết 2 hàm:
- `getCacheData()` → luôn `Promise.resolve("Dữ liệu từ cache")`.
- `getError()` → luôn `Promise.reject("Server lỗi!")`.

Sau đó gọi `.then()` và `.catch()` để xử lý.

---

## Tổng hợp: Error Handling & Promise API

### 1. Error Handling with Promises

#### 🔎 Giải thích
- `.catch()` sẽ xử lý mọi lỗi trong promise:
  - Gọi `reject()`.
  - Hoặc `throw Error` trong `.then()`.
- `.then()` cũng có thể nhận error handler qua đối số thứ 2.
- `.catch()` nên đặt ở nơi ta biết cách xử lý lỗi.
- Nếu lỗi chưa xử lý, dùng `unhandledrejection` event để bắt lỗi toàn cục (browser).
- Nếu không recover được → có thể bỏ qua `.catch()` (nhưng vẫn log/report).

#### 🍜 Use case
- **API call**: Nếu server lỗi → báo lỗi UI + ghi log.
- **Validation**: Nếu data không hợp lệ → `throw error` trong `.then()`, `.catch()` sẽ bắt.
- **Global handler**: Để app không bị “chết lặng” khi có bug chưa bắt.

#### 🐞 Bug thường gặp
- Nhầm chỗ để `.catch()` → khiến lỗi không được bắt.
- Viết `catch("Error")` thay vì `catch(err => ...)` → sai cú pháp.
- Quên `return` trong `.then()` → chuỗi promise không truyền đúng dữ liệu.
- Không log hoặc `rethrow` lỗi → khó debug.

### 2. 6 Static Methods của Promise

#### 2.1 `Promise.all(promises)`
- Chờ tất cả promise `resolve`.
- Nếu 1 `reject` → fail toàn bộ.
- ✅ **Use case**: tải nhiều API mà tất cả đều bắt buộc.

#### 2.2 `Promise.allSettled(promises)`
- Chờ tất cả `settle` (`fulfilled` hoặc `rejected`).
- Trả về mảng object `{status, value/reason}`.
- ✅ **Use case**: tải nhiều API, ghi nhận cả thành công lẫn thất bại.

#### 2.3 `Promise.race(promises)`
- Lấy promise `settle` đầu tiên (`fulfilled` hoặc `rejected`).
- ✅ **Use case**: timeout request (API vs. timeout).

#### 2.4 `Promise.any(promises)`
- Lấy promise `fulfilled` đầu tiên.
- Nếu tất cả `reject` → trả về `AggregateError`.
- ✅ **Use case**: gọi nhiều mirror server, lấy cái nào trả nhanh nhất.

#### 2.5 `Promise.resolve(value)`
- Tạo promise đã `fulfilled` ngay với `value`.
- ✅ **Use case**: chuẩn hóa giá trị sync thành promise.

#### 2.6 `Promise.reject(error)`
- Tạo promise `reject` ngay với `error`.
- ✅ **Use case**: simulate API lỗi, trả lỗi ngay lập tức.

### 3. 🐛 Các bug phổ biến với Promise API
- Dùng nhầm `race` thay vì `any` (`race` nhận cả lỗi, `any` chỉ lấy thành công).
- Quên `.catch()` khi xài `all` → chỉ cần 1 promise fail là crash.
- Nhầm `"fulfilled"` thành `"fullfilled"` khi filter kết quả từ `allSettled`.
- Hiểu nhầm rằng `race` sẽ “hủy” promise còn lại → thật ra chúng vẫn chạy.

### 4. 📊 Bảng so sánh Promise API

| API | Chờ bao nhiêu? | Kết quả trả về | Khi có lỗi |
|---|---|---|---|
| `Promise.all` | Tất cả phải `resolve` | Mảng kết quả | 1 `reject` → fail toàn bộ |
| `Promise.allSettled` | Tất cả `settle` | Mảng `{status, value/reason}` | Không fail, chỉ ghi nhận |
| `Promise.race` | 1 promise `settle` đầu tiên | Kết quả hoặc lỗi đầu tiên | Lỗi đầu tiên thắng |
| `Promise.any` | 1 promise `fulfilled` đầu tiên | Giá trị `fulfilled` đầu tiên | Nếu tất cả fail → `AggregateError` |
| `Promise.resolve` | Không chờ | Promise đã `fulfilled` với `value` | — |
| `Promise.reject` | Không chờ | Promise đã `rejected` với `error` | — |

### 5. 🗺️ Sơ đồ minh họa (trừu tượng hóa trong bếp 🍳)

- **`Promise.all`**: Đợi đủ 3 phụ bếp → 1 fail = cả bếp fail
- **`Promise.allSettled`**: Ghi lại kết quả từng phụ bếp: thành công / thất bại
- **`Promise.race`**: Ai về trước (thắng/thua) → chọn kết quả của người đó
- **`Promise.any`**: Chỉ lấy phụ bếp thành công đầu tiên
- **`Promise.resolve`**: Đưa sẵn 1 giỏ rau ngon → coi như done
- **`Promise.reject`**: Báo luôn “chợ đóng cửa”, fail ngay
