# 1. Async/Await trong JavaScript

Async/await là một cú pháp đặc biệt giúp làm việc với Promise một cách thoải mái hơn, dễ hiểu và dễ sử dụng. Nó giúp viết code bất đồng bộ (asynchronous) dễ đọc hơn, thay vì dùng callback hell hoặc chuỗi `.then()`.

## 1.1. Từ khóa `async`

Từ khóa `async` được đặt trước một hàm. Nó có hai tác dụng chính:

1.  **Luôn trả về một Promise:** Một hàm `async` luôn trả về một Promise. Nếu hàm `async` trả về một giá trị bình thường, JavaScript sẽ tự động gói giá trị đó vào một `Promise.resolve(value)`.

    **Ví dụ:**

    ```javascript
    async function hello() {
      return "Xin chào!";
    }

    hello().then(msg => console.log(msg)); // Output: "Xin chào!"
    ```

    Mặc dù hàm `hello()` chỉ trả về một chuỗi `"Xin chào!"`, nhưng bản chất nó trả về một Promise đã được giải quyết với giá trị đó.

2.  **Cho phép sử dụng `await` bên trong:** Từ khóa `await` chỉ có thể được sử dụng bên trong một hàm được khai báo với `async`.

## 1.2. Từ khóa `await`

Từ khóa `await` chỉ có thể được sử dụng bên trong một hàm `async`. Nó làm cho JavaScript đợi cho đến khi một Promise hoàn thành (settle - resolve hoặc reject) và trả về kết quả của nó.

*   `await` tạm dừng việc thực thi của hàm `async` tại điểm đó cho đến khi Promise settle.
*   Điều quan trọng là `await` không làm tốn tài nguyên CPU, vì JavaScript engine có thể thực hiện các công việc khác trong thời gian chờ đợi (ví dụ: thực thi các script khác, xử lý sự kiện, cập nhật UI).

**Ví dụ về cách `await` hoạt động:**

```javascript
async function demo() {
  console.log("Bắt đầu");
  await new Promise(resolve => setTimeout(resolve, 2000)); // Dừng 2 giây
  console.log("Kết thúc sau 2 giây");
}

demo();
console.log("Code khác vẫn chạy ngay!");
```

**Kết quả:**

```
Bắt đầu
Code khác vẫn chạy ngay!
Kết thúc sau 2 giây
```

Hàm `demo()` bị "tạm dừng", nhưng JavaScript engine vẫn tiếp tục chạy dòng `console.log("Code khác vẫn chạy ngay!")` ngay lập tức.

### 1.2.1. Chờ nhiều Promise cùng lúc với `Promise.all`

Khi cần đợi nhiều Promise hoàn thành đồng thời, cách tốt nhất là sử dụng `Promise.all()`.

**Ví dụ:**

```javascript
async function getData() {
  const [user, posts] = await Promise.all([
    fetch("https://jsonplaceholder.typicode.com/users/1").then(r => r.json()),
    fetch("https://jsonplaceholder.typicode.com/posts?userId=1").then(r => r.json())
  ]);

  console.log("User:", user.name);
  console.log("Posts:", posts.length);
}

getData();
```

**Ưu điểm:** Tất cả các request chạy song song, và kết quả được trả về khi tất cả Promise đều hoàn thành.

### 1.2.2. Câu hỏi suy nghĩ

*   Nếu ta bỏ `Promise.all` và thay bằng `await` từng cái, điều gì sẽ xảy ra với tốc độ?
*   Tại sao `await` không tốn CPU trong khi nó "tạm dừng" hàm?
*   Nếu một trong các Promise trong `Promise.all` bị reject, chuyện gì xảy ra?

### 1.2.3. Bài tập nhỏ

1.  **Viết hàm `getWeatherAndNews()`:**
    *   Lấy dữ liệu từ 2 API giả định:
        *   `https://jsonplaceholder.typicode.com/todos/1`
        *   `https://jsonplaceholder.typicode.com/posts/1`
    *   Dùng `Promise.all` để fetch song song.
    *   In ra kết quả của cả 2.
2.  Viết lại cùng hàm trên nhưng fetch tuần tự (`await` từng cái). So sánh tốc độ chạy.

# 2. Xử lý lỗi (Error Handling) với `async/await`

Trong các tình huống thực tế, Promise có thể mất một thời gian trước khi bị reject. Trong trường hợp đó, sẽ có một độ trễ trước khi `await` ném ra lỗi.

## 2.1. Cơ chế bắt lỗi với `try...catch`

Nếu một Promise bị reject, `await` sẽ ném ra một lỗi (tương tự như `throw`). Do đó, chúng ta có thể sử dụng `try...catch` để bắt lỗi, giống như cách xử lý lỗi trong code đồng bộ.

**Ví dụ:**

```javascript
async function getUser() {
  try {
    const res = await fetch("https://wrong-url.typicode.com/users/1");
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error("❌ Lỗi khi gọi API:", err.message);
  }
}

getUser();
```

Nếu URL sai, `await fetch(...)` sẽ bị reject và chương trình sẽ nhảy vào khối `catch`.

## 2.2. Xử lý lỗi khi không dùng `try...catch`

Khi không có `try...catch` bên trong hàm `async`, hàm `async` đó sẽ trả về một Promise bị reject. Chúng ta có thể xử lý lỗi này bằng cách gắn `.catch()` vào lời gọi hàm `async`.

**Ví dụ:**

```javascript
async function broken() {
  const res = await fetch("https://wrong-url.typicode.com");
  return res.json();
}

broken()
  .then(data => console.log(data))
  .catch(err => console.error("❌ Bắt lỗi ở ngoài:", err.message));
```

## 2.3. Thời gian lỗi (Delay trước khi Reject)

Đôi khi Promise không reject ngay lập tức mà phải chờ một khoảng thời gian (ví dụ: timeout hoặc server trả về lỗi) mới reject.

**Ví dụ:**

```javascript
function delayedError(ms) {
  return new Promise((_, reject) =>
    setTimeout(() => reject(new Error("Lỗi sau " + ms + "ms")), ms)
  );
}

async function run() {
  try {
    await delayedError(2000);
  } catch (err) {
    console.error("❌ Bắt lỗi:", err.message);
  }
}

run();
```

Kết quả: Sau 2 giây mới in ra `"❌ Bắt lỗi: Lỗi sau 2000ms"`.

## 2.4. Những lỗi (Bug) thường gặp

*   Không dùng `try...catch` làm ứng dụng bị crash khi API thất bại.
*   Quên `.catch()` khi gọi hàm `async` dẫn đến `UnhandledPromiseRejectionWarning`.
*   Viết `catch` sai vị trí, ví dụ:
    ```javascript
    try {
      await fetch("url").catch(...); // ❌ không có tác dụng
    } catch (e) {}
    ```
*   Không log đầy đủ lỗi (chỉ log `message` mà bỏ qua `stack trace`).

## 2.5. Câu hỏi suy nghĩ

*   Nếu trong `Promise.all` có 5 request, chỉ 1 request fail thì điều gì xảy ra?
*   Khác biệt giữa `try...catch` bên trong hàm `async` và `.catch()` bên ngoài là gì?
*   Khi nào nên xử lý lỗi ngay trong hàm `async`, khi nào nên để lỗi propagate ra ngoài?

## 2.6. Bài tập nhỏ

1.  **Viết hàm `getPost(id)`:**
    *   Gọi API `https://jsonplaceholder.typicode.com/posts/{id}`.
    *   Nếu lỗi (ví dụ `id = 99999` không tồn tại), hãy bắt bằng `try...catch` và in ra `"Không tìm thấy bài viết"`.
2.  **Viết hàm `getAllPosts(ids)`:**
    *   Dùng `Promise.all` để lấy nhiều post cùng lúc.
    *   Nếu một request fail, thử xử lý sao cho các request khác vẫn trả kết quả (gợi ý: dùng `Promise.allSettled`).

# 3. `async` function không có `await`

Một hàm `async` luôn trả về một Promise, ngay cả khi bên trong nó không sử dụng từ khóa `await`.

## 3.1. Ví dụ

```javascript
async function foo() {
  return 42;
}

console.log(foo()); // Output: Promise { 42 }
```

Hàm `foo()` trả về một Promise đã được giải quyết với giá trị `42`, chứ không phải trực tiếp số `42`. Điều này tương đương với việc viết:

```javascript
function foo() {
  return Promise.resolve(42);
}
```

## 3.2. Khác biệt với hàm bình thường

| Hàm bình thường (`normal`) | Hàm `async` (`asyncFn`)                               |
| :------------------------- | :---------------------------------------------------- |
| Trả về giá trị ngay lập tức | Trả về một Promise đã resolve với giá trị đó          |
| Kết quả là giá trị đồng bộ | Kết quả là Promise (giá trị bất đồng bộ)             |

Khi gọi `asyncFn()`, ta phải sử dụng `.then()` hoặc `await` để lấy giá trị thực tế.

## 3.3. Tại sao gọi là "bất đồng bộ"?

Khi gọi một hàm `async`, JavaScript không chặn luồng thực thi chính mà ngay lập tức trả về một Promise. Promise này sẽ được giải quyết trong hàng đợi microtask (microtask queue) ở vòng lặp sự kiện (event loop) tiếp theo. Điều này đảm bảo rằng các đoạn code phía sau lời gọi hàm `async` vẫn tiếp tục chạy mà không bị chờ đợi.

**Ví dụ:**

```javascript
async function foo() {
  return 42;
}

console.log("Bắt đầu");
foo().then(v => console.log("Giá trị:", v));
console.log("Kết thúc");
```

**Kết quả:**

```
Bắt đầu
Kết thúc
Giá trị: 42
```

Dù `foo()` chỉ trả về giá trị `42`, nhưng do là hàm `async`, nó làm cho việc in `"Giá trị: 42"` xảy ra sau cùng, chứng tỏ tính bất đồng bộ.

## 3.4. Câu hỏi suy nghĩ

*   Nếu một hàm `async` không có `await`, nó có thực sự cần thiết không? (khác gì viết hàm thường + `Promise.resolve`?)
*   Nếu muốn viết hàm đồng bộ mà vẫn trả về Promise, ta có cần `async` không?

## 3.5. Bài tập nhỏ

1.  Viết 2 hàm `syncAdd(a, b)` và `asyncAdd(a, b)` (hàm `async` nhưng không dùng `await`).
    *   So sánh kết quả khi gọi `console.log(syncAdd(1,2))` và `console.log(asyncAdd(1,2))`.
    *   Sau đó thử dùng `await asyncAdd(1,2)` để lấy kết quả.
2.  Thử viết `async function` mà bên trong chỉ `return` giá trị tĩnh. Rồi chạy 3 lệnh:
    ```javascript
    console.log(fn());
    fn().then(console.log);
    console.log(await fn());
    ```
    Quan sát sự khác biệt về output.

# 4. Khi nào dùng `await` tuần tự và `Promise.all` song song?

Việc lựa chọn giữa `await` tuần tự và `Promise.all` song song là rất quan trọng khi thiết kế code bất đồng bộ để tối ưu hiệu năng và đảm bảo logic chính xác.

## 4.1. Khi nên dùng `Promise.all` (chạy song song)

Sử dụng `Promise.all` khi các Promise không phụ thuộc vào nhau. Mục tiêu chính là tối ưu tốc độ thực thi vì tất cả các tác vụ sẽ chạy cùng lúc.

**Ví dụ: Tải dữ liệu người dùng và danh sách bài viết song song**

```javascript
async function getUserAndPosts() {
  const [user, posts] = await Promise.all([
    fetch("https://jsonplaceholder.typicode.com/users/1").then(r => r.json()),
    fetch("https://jsonplaceholder.typicode.com/posts?userId=1").then(r => r.json())
  ]);
  console.log(user, posts);
}
```

Trong trường hợp này, không cần phải chờ API lấy thông tin người dùng hoàn thành mới gọi API lấy danh sách bài viết.

## 4.2. Khi nên dùng `await` tuần tự (chạy lần lượt)

Sử dụng `await` tuần tự khi:

*   Kết quả của một request sau phụ thuộc vào kết quả của request trước.
*   Thứ tự thực hiện các tác vụ là quan trọng (ví dụ: ghi log, chạy các tác vụ tuần tự).
*   Server có giới hạn tốc độ (rate limit) và không cho phép gửi nhiều request cùng lúc.

**Ví dụ: Lấy `userId` từ API 1, sau đó dùng nó để lấy chi tiết từ API 2**

```javascript
async function getUserDetails() {
  const user = await fetch("/api/user").then(r => r.json());
  const posts = await fetch(`/api/posts?userId=${user.id}`).then(r => r.json());
  console.log(user, posts);
}
```

Ở đây, việc thực thi tuần tự là bắt buộc vì API thứ hai cần dữ liệu (`user.id`) từ kết quả của API thứ nhất.

## 4.3. Quy tắc chọn lựa

*   **Song song (`Promise.all`):** Nhanh, khi các tác vụ độc lập.
*   **Tuần tự (`await`):** An toàn, khi có sự phụ thuộc dữ liệu hoặc có hạn chế về số lượng request.

## 4.4. Câu hỏi suy nghĩ

*   Nếu có 10 API độc lập, nhưng server giới hạn chỉ cho gọi tối đa 3 request cùng lúc, em sẽ xử lý thế nào?
*   Nếu 1 trong các Promise trong `Promise.all` fail, em muốn toàn bộ fail, hay chỉ bỏ qua cái lỗi?

## 4.5. Bài tập nhỏ

1.  **Viết hàm `loadUserAndPosts()`:**
    *   Gọi API `https://jsonplaceholder.typicode.com/users/1`.
    *   Dùng kết quả `user.id` để fetch posts của user.
    *   Bắt buộc dùng `await` tuần tự.
2.  **Viết hàm `load3Todos()`:**
    *   Lấy todo `id=1,2,3` từ `https://jsonplaceholder.typicode.com/todos/{id}`.
    *   Viết 2 phiên bản:
        *   Dùng tuần tự.
        *   Dùng `Promise.all`.
    *   So sánh tốc độ chạy.

# 5. Lỗi thường gặp: Dùng `await` trong vòng lặp `for`

Việc sử dụng `await` trực tiếp trong vòng lặp `for` là một sai lầm phổ biến có thể dẫn đến hiệu năng kém nghiêm trọng, đặc biệt khi xử lý nhiều request.

## 5.1. Sai lầm thường gặp: `await` trong vòng lặp

Khi viết code như sau:

```javascript
async function getData() {
  const ids = [1, 2, 3];

  for (let id of ids) {
    const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
    const data = await res.json();
    console.log(data.title);
  }
}
getData();
```

**Vấn đề:**

*   Mỗi lần `fetch` cho một `id` phải hoàn thành trước khi `fetch` cho `id` tiếp theo bắt đầu.
*   Việc này chạy tuần tự, rất chậm nếu có nhiều request (ví dụ: 100+ request sẽ cực kỳ tệ).

## 5.2. Giải pháp: Chạy song song với `Promise.all`

Thay vì chờ từng request một, chúng ta tạo tất cả các Promise trước rồi đợi kết quả của chúng cùng lúc.

```javascript
async function getData() {
  const ids = [1, 2, 3];

  // Tạo mảng promises
  const promises = ids.map(id =>
    fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)
      .then(res => res.json())
  );

  // Chờ tất cả xong
  const results = await Promise.all(promises);

  results.forEach(post => console.log(post.title));
}
getData();
```

**Ưu điểm:**

*   Tất cả các request chạy cùng lúc.
*   Tốc độ nhanh hơn nhiều (gần bằng thời gian của request lâu nhất).

**Ứng dụng thực tế:**

*   Gọi API để lấy danh sách sản phẩm trong giỏ hàng.
*   Tải nhiều ảnh hoặc file JSON cùng lúc.
*   Đồng bộ nhiều request không phụ thuộc nhau.

## 5.3. Khi nào vẫn cần `await` trong vòng lặp?

Khi mỗi request phụ thuộc vào kết quả của request trước đó.

**Ví dụ:** Lấy `userId` từ API 1, sau đó dùng `userId` đó để fetch chi tiết từ API 2. Trong trường hợp này, `Promise.all` sẽ không hợp lý.

## 5.4. Thí nghiệm so sánh tốc độ

Bạn có thể chạy đoạn code sau trong console trình duyệt (hoặc Node.js) để thấy sự khác biệt về hiệu năng:

```javascript
console.time("for-await");

async function testForAwait() {
  const ids = [1, 2, 3];
  for (let id of ids) {
    await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
  }
}
await testForAwait();
console.timeEnd("for-await");

console.time("promise-all");

async function testPromiseAll() {
  const ids = [1, 2, 3];
  await Promise.all(ids.map(id => fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)));
}
await testPromiseAll();
console.timeEnd("promise-all");
```

**Kết quả quan sát:**

*   `for-await` sẽ mất khoảng 3 lần thời gian của một request đơn lẻ.
*   `promise-all` sẽ mất thời gian gần như bằng một request duy nhất.

## 5.5. Bài tập

1.  **Viết hàm `downloadImages(urls)`** nhận một mảng link ảnh:
    *   Cách 1: Dùng `for-await`.
    *   Cách 2: Dùng `Promise.all`.
    *   In ra thời gian thực thi và so sánh.
2.  Giả sử có 5 API phụ thuộc nhau (API 2 cần dữ liệu từ API 1, v.v.).
    *   Viết code dùng `for-await`.
    *   Giải thích tại sao `Promise.all` không dùng được trong trường hợp này.

# 6. Tổng kết và Bảng so sánh

## 6.1. Tóm tắt lý thuyết

*   **`async` trước hàm:**
    *   Luôn trả về Promise (dù return giá trị bình thường).
    *   Cho phép dùng `await` bên trong.
*   **`await` trước Promise:**
    *   Dừng hàm `async` cho đến khi Promise settle.
    *   Nếu resolve → trả về giá trị.
    *   Nếu reject → ném ra exception (`throw`).
*   **Xử lý lỗi:**
    *   Dùng `try...catch` trong hàm `async`.
    *   Hoặc `.catch()` khi gọi hàm `async`.
*   **Song song vs Tuần tự:**
    *   Dùng `Promise.all([...])` khi các Promise độc lập (chạy song song, nhanh).
    *   Dùng `await` tuần tự khi các Promise phụ thuộc nhau hoặc cần thứ tự (chạy lần lượt, an toàn).

## 6.2. Bảng so sánh

### 6.2.1. `Async function` vs `Normal function`

| Đặc điểm          | `Normal function`         | `Async function` (`async`)                               |
| :---------------- | :------------------------ | :------------------------------------------------------- |
| Giá trị trả về    | Trả về ngay lập tức       | Giá trị được bọc trong `Promise.resolve(value)`          |
| Sử dụng `await`   | Không thể dùng `await`    | Có thể dùng `await`                                     |
| Kết quả           | Là giá trị đồng bộ        | Là Promise                                               |

### 6.2.2. `.then/.catch` vs `async/await`

| Đặc điểm          | `Promise .then/.catch`                               | `Async/Await`                                            |
| :---------------- | :--------------------------------------------------- | :------------------------------------------------------- |
| Cú pháp           | Dùng callback (`.then`, `.catch`)                    | Dùng cú pháp đồng bộ (`try...catch`)                     |
| Độ dài code       | Dễ bị dài dòng (chuỗi `.then`)                      | Ngắn gọn, dễ đọc như code đồng bộ                        |
| Quản lý Promise   | Quản lý nhiều Promise dễ hơn với `Promise.all`      | Quản lý nhiều Promise dễ hơn với `await + Promise.all`  |
| Trường hợp phù hợp | Hợp với xử lý đồng thời nhiều tác vụ                 | Hợp với flow tuần tự + xử lý lỗi                         |

### 6.2.3. `for + await` vs `Promise.all`

| Đặc điểm          | `for + await` (Tuần tự)                               | `Promise.all` (Song song)                                |
| :---------------- | :---------------------------------------------------- | :------------------------------------------------------- |
| Cách thực thi     | Chạy từng Promise một (chậm)                          | Chạy tất cả cùng lúc (nhanh hơn)                         |
| Trường hợp phù hợp | Khi có phụ thuộc dữ liệu hoặc cần thứ tự             | Khi các tác vụ độc lập, không phụ thuộc                  |
| Độ phức tạp       | Dễ hiểu, rõ ràng                                      | Tối ưu hiệu năng                                         |

## 6.3. Sơ đồ trực quan (Flow Chart)

Cách `async/await` xử lý Promise:

```
          ┌───────────────┐
          │ async function │
          └───────┬───────┘
                  │
                  ▼
          return Promise
                  │
         ┌────────┴────────┐
         ▼                 ▼
    Promise resolve     Promise reject
         │                 │
         ▼                 ▼
  await → giá trị     await → throw error
```

## 6.4. Mindmap: `async / await` trong JavaScript

```
🧠 Mindmap: async / await trong JavaScript
│
├── async function
│   ├─ Luôn trả về Promise
│   ├─ return value → Promise.resolve(value)
│   └─ Cho phép dùng await bên trong
│
├── await
│   ├─ Dừng async function đến khi Promise settle
│   ├─ Nếu resolve → trả về giá trị
│   └─ Nếu reject → throw error
│
├── Error Handling
│   ├─ try...catch trong async function
│   └─ .catch() khi gọi async function
│
├── Song song vs Tuần tự
│   ├─ Promise.all([...]) → song song, nhanh
│   └─ for + await → tuần tự, khi có phụ thuộc
│
├── Ưu điểm
│   ├─ Code ngắn gọn, dễ đọc
│   ├─ Flow như đồng bộ
│   └─ Kết hợp tốt với Promise API
│
└── Lỗi thường gặp
    ├─ Dùng await ngoài async function → SyntaxError
    ├─ Quên try...catch → crash
    ├─ Hiểu nhầm await block toàn bộ JS
    └─ Dùng await trong vòng lặp → chậm
