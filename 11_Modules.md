# 11. Modules trong JavaScript

## 1. Giới thiệu khái niệm

Khi ứng dụng phát triển lớn hơn, chúng ta muốn chia nó thành nhiều file, được gọi là "modules".
Một module chỉ đơn giản là một file. Một script là một module.
Các module có thể tải lẫn nhau và sử dụng các chỉ thị đặc biệt `export` và `import` để trao đổi chức năng, gọi các hàm của module này từ module khác:
- `export`: từ khóa này đánh dấu các biến và hàm có thể truy cập từ bên ngoài module hiện tại.
- `import`: cho phép nhập chức năng từ các module khác.

Trong JavaScript hiện đại (ES6+), Module giúp chia nhỏ ứng dụng ra thành nhiều file, dễ bảo trì, dễ tái sử dụng.
- `export`: dùng để "công khai" biến/hàm/lớp ra ngoài.
- `import`: dùng để "lấy" những thứ đã export từ file khác vào.
Một file JS = một module.

## 2. Ví dụ cơ bản

**File: `math.js`**
```javascript
// math.js
export function add(a, b) {
  return a + b;
}

export const PI = 3.14;
```

**File: `app.js`**
```javascript
// app.js
import { add, PI } from './math.js';

console.log(add(2, 3)); // 👉 5
console.log(PI);        // 👉 3.14
```

## 3. Câu hỏi suy nghĩ

- Nếu trong `math.js` có rất nhiều hàm, mà chỉ muốn import một hàm thôi, sẽ viết như thế nào?
- Nếu muốn import tất cả dưới một cái tên, cú pháp sẽ ra sao?

## 4. Use case thực tế

- **Project lớn**: tách API calls ra file `api.js`, tách validate form ra `validation.js`.
- **Teamwork**: mỗi thành viên viết một module riêng, sau đó gắn kết với nhau bằng `import`/`export`.

**Ví dụ:**
```javascript
// api.js
export function getUser(id) { /* ... */ }
export function getPosts() { /* ... */ }

// validation.js
export function validateEmail(email) { /* ... */ }
```
Ở `main.js` ta chỉ việc `import { getUser } from './api.js';`

## 5. Bug thường gặp

- **Quên thêm `type="module"` khi import trong HTML:**
  ```html
  <script src="app.js"></script> <!-- ❌ sai -->
  <script type="module" src="app.js"></script> <!-- ✅ đúng -->
  ```
- **Sai đường dẫn:**
  ```javascript
  import { add } from 'math.js' // ❌ (thiếu ./)
  import { add } from './math.js' // ✅
  ```
- **Import nhầm `default` và `named export`:**
  ```javascript
  // greeting.js
  export function sayHello() { /* ... */ }

  // main.js
  import sayHello from './greeting.js'; // ❌ lỗi: vì greeting.js không có default export
  import { sayHello } from './greeting.js'; // ✅ đúng
  ```
- **Circular dependency (vòng lặp import):** Khi A import B, B lại import A → dễ gây bug "undefined". Cần thiết kế module rõ ràng để tránh vòng tròn.
- **Chạy trực tiếp file HTML bằng `file:///`**: gây CORS error vì module yêu cầu server.
  Giải pháp: chạy bằng local server (npx serve, python -m http.server, VSCode Live Server...).

## 6. Export thường (Named Export)

Mỗi module có thể export nhiều giá trị bằng cú pháp `export`.
Khi import, phải gọi đúng tên.
Có thể import nhiều cái trong 1 dòng.

**Ví dụ:**
```javascript
// math.js
export const PI = 3.14;
export function add(a, b) { return a + b; }
export function sub(a, b) { return a - b; }

// main.js
import { PI, add, sub } from './math.js';
console.log(add(2, 3)); // 5
console.log(PI);        // 3.14
```

Có thể đổi tên khi import:
```javascript
import { add as plus } from './math.js';
console.log(plus(2, 3)); // 5
```

## 7. Export mặc định (Default Export)

Một module chỉ có duy nhất 1 `export default`.
Khi import, tự đặt tên.
Không cần `{ }`.

**Ví dụ:**
```javascript
// greeting.js
export default function sayHello(name) {
  return `Hello, ${name}`;
}

// main.js
import sayHi from './greeting.js'; // có thể đặt tên khác
console.log(sayHi("IT 日本語先生"));
```

## 8. Kết hợp cả hai (Default và Named Export)

Có thể vừa `export default` vừa có `export` thường trong cùng một module:

```javascript
// utils.js
export default function log(msg) {
  console.log("LOG:", msg);
}

export function warn(msg) {
  console.warn("WARNING:", msg);
}
// app.js
import log, { warn } from './utils.js';

log("Xin chào");
warn("Cẩn thận");
```

## 9. Import tất cả (`* as`)

Nếu module có nhiều export, có thể gom lại:

```javascript
import * as math from './math.js';

console.log(math.add(2, 3));
console.log(math.PI);
```
Khi đó, tất cả các hàm/biến nằm trong object `math`.

## 10. Re-export

### 10.1. Khái niệm

Thay vì `import` một hàm/biến từ file A vào file B, rồi `export` lại từ B → ta có thể dùng cú pháp `export ... from ...` để import và export ngay trong một bước.
Re-export giúp tạo "cổng trung gian" (entry point), để người dùng chỉ cần import từ 1 file thay vì nhiều file.

### 10.2. Ví dụ cơ bản

**`math.js`**
```javascript
export function add(a, b) { return a + b; }
export function sub(a, b) { return a - b; }
```

**`string.js`**
```javascript
export function upper(str) { return str.toUpperCase(); }
export function lower(str) { return str.toLowerCase(); }
```

**`index.js` (dùng Re-export)**
```javascript
// Gom tất cả export từ math.js
export * from './math.js';

// Gom tất cả export từ string.js
export * from './string.js';
```

**`app.js`**
```javascript
import { add, upper } from './index.js';

console.log(add(2, 3));     // 5
console.log(upper("hello")) // HELLO
```
Người dùng chỉ cần import từ `index.js` thay vì nhớ nhiều file.

### 10.3. Re-export có đổi tên

Có thể đổi tên khi re-export:
```javascript
// re-export từ math.js nhưng đổi tên
export { add as plus } from './math.js';
```

**`app.js`**
```javascript
import { plus } from './index.js';
console.log(plus(4, 6)); // 10
```

### 10.4. Re-export default

Nếu file có default export, ta có thể re-export như sau:

**`config.js`**
```javascript
export default {
  apiUrl: "https://api.example.com",
  timeout: 5000
};
```

**`index.js`**
```javascript
export { default as config } from './config.js';
```

**`app.js`**
```javascript
import { config } from './index.js';
console.log(config.apiUrl);
```

### 10.5. Lợi ích thực tế

Tạo một file entry-point (`index.js`) để gom exports → người dùng chỉ cần nhớ 1 đường dẫn.
Hữu ích trong thư viện hoặc project lớn.

**Ví dụ:**
```javascript
// index.js
export * from './api.js';
export * from './validation.js';
export { default as config } from './config.js';
```
Trong `main.js`:
```javascript
import { getUser, validateEmail, config } from './index.js';
```

### 10.6. Bug thường gặp với Re-export

- **Xung đột tên**: nếu 2 file export cùng tên → lỗi.
  ```javascript
  // math.js
  export function calc() {}
  // string.js
  export function calc() {}
  // index.js
  export * from './math.js';
  export * from './string.js'; // ❌ Error: Duplicate export 'calc'
  ```
  Giải pháp: đổi tên khi re-export:
  ```javascript
  export { calc as calcMath } from './math.js';
  export { calc as calcString } from './string.js';
  ```
- **Re-export default không được với `export *`**:
  ```javascript
  export * from './config.js'; // ❌ default không re-export
  ```
  Cần làm:
  ```javascript
  export { default as config } from './config.js';
  ```

## 11. Cách hoạt động của modules trong browser

- Mỗi module file (ví dụ: `math.js`, `text.js`) là độc lập.
- Browser khi thấy `import ...` thì:
  - Gửi request để tải file module (giống như tải script).
  - Thực hiện parse + evaluate module đó một lần duy nhất.
  - Cache lại kết quả → nếu module được import nhiều nơi thì browser không chạy lại, mà dùng lại từ cache.
- Điều này giúp tránh việc chạy lại code trùng lặp.

## 12. Vị trí `import`/`export`

`import` và `export` có thể viết ở bất kỳ chỗ nào trong file (đầu hoặc cuối file), vì:
- Trình duyệt sẽ phân tích toàn bộ file trước khi chạy (hoisting cho module).
- Nên có thể import ở trên cùng hoặc cuối file, kết quả vẫn giống nhau.

**Ví dụ:**
```javascript
// main.js
console.log(add(2,3));  // Vẫn chạy OK
import { add } from './math.js';
```
Vì import được xử lý trước khi chạy code.

**Best practice**: viết import ở đầu file để:
- Dễ đọc, dễ quản lý dependencies.
- Các IDE, linter (ESLint, Prettier) đều khuyến nghị vậy.

## 13. Module scope

Mỗi module có scope riêng → biến, hàm định nghĩa trong file không rơi vào global scope.
Khác với `<script>` bình thường (non-module), nơi biến có thể leak ra `window`.

**Ví dụ:**
```javascript
// math.js
const secret = 123;
export function add(a, b) { return a + b; }
// main.js
import { add } from './math.js';

console.log(add(1,2)); // 3
console.log(secret);   // ❌ Lỗi: secret is not defined
```

## 14. Browser tự động load

Khi viết trong HTML:
```html
<script type="module" src="main.js"></script>
```
Browser sẽ tự động tải `main.js`.
Nếu `main.js` có import khác, browser lại tiếp tục tải file phụ (`math.js`, `text.js`...) mà không cần phải thêm thủ công trong HTML.
Đây là cơ chế dependency graph: từ 1 file chính, tất cả file liên quan được tải theo nhu cầu.

## 15. Bảng so sánh Script thường và Module

| Thuộc tính            | Script thường (`<script>`)      | Module (`<script type="module">`) |
| :-------------------- | :------------------------------ | :-------------------------------- |
| **Scope mặc định**    | Global (biến rơi vào `window`) | Module scope (riêng biệt)         |
| **Hoisting**          | Có (`var`, `function`)          | Có, nhưng module được xử lý trước |
| **Hỗ trợ `import`/`export`** | ❌ Không                        | ✅ Có                             |
| **Tải script**        | Từng file độc lập               | Browser tự load dependency graph  |
| **Caching**           | Không rõ ràng                   | ✅ Module chỉ load 1 lần          |
| **Strict mode**       | Không bắt buộc                  | ✅ Luôn ở strict mode             |

## 16. Tổng hợp bài tập

### 16.1. Cơ bản

1. Tạo `math.js` export `add`, `sub`. Import trong `main.js` và chạy thử.
2. Tạo `greeting.js` export default `sayHello`. Import trong `main.js` và in ra "Hello, IT 日本語先生!".

### 16.2. Trung bình

1. Tạo `mathUtils.js`:
   - default export: `multiply`
   - named export: `divide`
   Import vào `main.js` và thử gọi.
2. Thử đổi tên khi import (`import { divide as div }`).

### 16.3. Nâng cao

1. Tạo 3 file: `math.js`, `text.js`, `config.js`.
   - `math.js`: `add`, `sub`
   - `text.js`: `capitalize`, `trim`
   - `config.js`: default export `{ version: "1.0.0" }`
2. Tạo `index.js` để re-export tất cả.
3. Trong `main.js` import `add`, `capitalize`, `config`.

### 16.4. Thực tế

1. Tạo mini project:
   - `api.js`: export hàm `getUser(id)` (fake trả về object `{id, name}`).
   - `validation.js`: export `validateEmail(email)`.
   - `config.js`: default export `{ baseUrl: "https://api.test.com" }`.
   - `index.js`: re-export tất cả.
2. Trong `main.js`: import từ `index.js` và gọi thử.
