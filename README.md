# Tổng hợp kiến thức JavaScript cơ bản

Tài liệu này tổng hợp các kiến thức nền tảng về JavaScript từ các file markdown trong thư mục này, bao gồm các khái niệm cơ bản, use cases thực tế, các lỗi thường gặp và bài tập thực hành.

## Mục lục

1.  [Kiến thức nền tảng](#1-kiến-thức-nền-tảng)
    *   [Kiểu dữ liệu](#kiểu-dữ-liệu)
    *   [Chuyển đổi kiểu dữ liệu](#chuyển-đổi-kiểu-dữ-liệu)
    *   [Hàm](#hàm)
    *   [Vòng lặp và Iterator](#vòng-lặp-và-iterator)
    *   [Đối tượng (Objects)](#đối-tượng-objects)
    *   [Mảng (Arrays)](#mảng-arrays)
    *   [Promises](#promises)
    *   [Async/Await](#asyncawait)
    *   [Modules](#modules)
2.  [Các Use Case thực tế](#2-các-use-case-thực-tế)
3.  [Các Bug thường gặp](#3-các-bug-thường-gặp)
4.  [Bài tập tổng hợp](#4-bài-tập-tổng-hợp)

---

## 1. Kiến thức nền tảng

### Kiểu dữ liệu

JavaScript có các kiểu dữ liệu cơ bản như `Number`, `BigInt`, `String`, `Boolean`, `null`, `undefined`, `Symbol`.

*   **`var`, `let`, `const`**:
    *   `var`: không có block scope, cho phép khai báo lại, hỗ trợ hoisting (khởi tạo với `undefined`). Ít an toàn, dễ gây lỗi logic.
    *   `let`: có block scope, không cho phép khai báo lại, hoisting nhưng ở Temporal Dead Zone (TDZ). Dùng cho biến có thể thay đổi.
    *   `const`: có block scope, không cho phép khai báo lại, hoisting nhưng ở TDZ. Phải gán giá trị khi khai báo, không thể gán lại. Dùng cho hằng số hoặc tham chiếu bất biến.

    **Bảng so sánh `var`, `let`, `const`**

    | Property            | `var`                                | `let`                       | `const`                         |
    | :------------------ | :----------------------------------- | :-------------------------- | :------------------------------ |
    | Scope               | Function scope (no block scope)      | Block scope                 | Block scope                     |
    | Hoisting            | Yes, initialized with `undefined`    | Yes, but in TDZ             | Yes, but in TDZ                 |
    | Redeclaration       | ✅ Allowed                           | ❌ Not allowed              | ❌ Not allowed                  |
    | Reassignment        | ✅ Allowed                           | ✅ Allowed                  | ❌ Not allowed                  |
    | Initialization      | ❌ Not required                      | ❌ Not required             | ✅ Required                     |
    | Use Cases           | Old code (ES5 and earlier)           | Mutable variables           | Constants, immutable references |

*   **`Number`**: Biểu diễn số nguyên và số thực. Bao gồm các giá trị đặc biệt như `Infinity`, `-Infinity`, `NaN`.
    *   `NaN` không bằng chính nó (`NaN === NaN` là `false`). Dùng `isNaN()` để kiểm tra.
*   **`BigInt`**: Dùng cho số nguyên cực lớn, vượt quá giới hạn an toàn của `Number` (`-(2^53 - 1)` đến `(2^53 - 1)`). Được tạo bằng cách thêm `n` vào cuối số hoặc dùng `BigInt()`. Không thể trộn trực tiếp với `Number` trong phép toán.
*   **`String`**: Chuỗi ký tự, có thể dùng dấu nháy đơn (`'...'`), nháy kép (`"..."`) hoặc backticks (`` `...` ``).
    *   Backticks (template literals) hỗ trợ nội suy biến/biểu thức (`${...}`) và chuỗi đa dòng.
*   **`Null`**: Giá trị đặc biệt biểu thị "không có giá trị", "rỗng" hoặc "giá trị không xác định". `typeof null` trả về `"object"` (một bug lịch sử).
*   **`Undefined`**: Biến đã khai báo nhưng chưa gán giá trị.

### Chuyển đổi kiểu dữ liệu

JavaScript tự động hoặc tường minh chuyển đổi giữa các kiểu dữ liệu.

*   **String Conversion**: Chuyển sang chuỗi.
    *   `String(value)`: an toàn, hoạt động với mọi giá trị (kể cả `null`, `undefined`).
    *   `value.toString()`: phương thức của đối tượng, không dùng được với `null`, `undefined`.
    *   Xảy ra ngầm định khi nối chuỗi (`"" + value`).
    **Bảng so sánh `String(value)` vs `value.toString()`**

    | Đặc điểm            | `String(value)` | `value.toString()` |
    | :------------------ | :-------------- | :----------------- |
    | Dùng được với `null` | ✅ Có           | ❌ Lỗi             |
    | Dùng được với `undefined` | ✅ Có           | ❌ Lỗi             |
    | Là hàm toàn cục     | ✅              | ❌ (chỉ là method) |
    | An toàn hơn         | ✅              | ❌                 |

*   **Numeric Conversion**: Chuyển sang số.
    *   `Number(value)`: chuyển đổi tường minh.
    *   Xảy ra ngầm định trong các phép toán số học (trừ phép cộng `+` nếu có chuỗi).
    *   Quy tắc: `""` → `0`, `true` → `1`, `false` → `0`, `null` → `0`, `undefined` → `NaN`, chuỗi không phải số → `NaN`.
    **Bảng quy tắc chuyển đổi số (`Number(value)`)**
    | Giá trị             | Kết quả sau `Number(value)` |
    | :------------------ | :-------------------------- |
    | `"123"`             | `123`                       |
    | `" 456 "`           | `456`                       |
    | `""` (chuỗi rỗng)   | `0`                         |
    | `true`              | `1`                         |
    | `false`             | `0`                         |
    | `null`              | `0`                         |
    | `undefined`         | `NaN`                       |
    | `"abc"`             | `NaN`                       |
    | `[]` (mảng rỗng)    | `0`                         |
    | `[1,2]`             | `NaN`                       |
    | `{}` (object rỗng)  | `NaN`                       |

*   **Boolean Conversion**: Chuyển sang boolean.
    *   `Boolean(value)`: chuyển đổi tường minh.
    *   Xảy ra ngầm định trong câu điều kiện (`if`, `while`) hoặc toán tử logic (`!`, `||`, `&&`).
    *   **Falsy values**: `0`, `""`, `null`, `undefined`, `NaN`.
    *   **Truthy values**: Tất cả các giá trị khác (bao gồm `"0"`, `" "`, `[]`, `{}`).

### Hàm

Hàm trong JavaScript là "first-class citizens", tức là chúng có thể được coi như giá trị.

*   **Function Expression**: Gán hàm cho một biến. Chỉ khả dụng sau khi định nghĩa.
    ```javascript
    let sayHi = function() { return "Hello!"; };
    ```
*   **Callback Functions**: Hàm được truyền làm tham số cho hàm khác và được gọi lại sau.
    *   Giúp tách biệt logic, xử lý bất đồng bộ (ví dụ: `setTimeout`, event handlers).
*   **Function Declaration**: Khai báo hàm thông thường. Được hoisting, có thể gọi trước khi định nghĩa.
    ```javascript
    function sayHello() { return "Hello!"; }
    ```
    **Bảng so sánh Function Declaration vs Function Expression**

    | Đặc điểm                  | Function Declaration      | Function Expression         |
    | :------------------------ | :------------------------ | :-------------------------- |
    | Cú pháp                   | `function f() {}`         | `let f = function() {};`    |
    | Hoisting                  | ✅ Có                     | ❌ Không                    |
    | Gọi trước khi định nghĩa  | ✅ Được                   | ❌ Lỗi                      |
    | Dùng như giá trị          | Khó linh hoạt             | Rất linh hoạt (truyền vào biến, callback, v.v.) |

### Vòng lặp và Iterator

Dùng để lặp qua các tập hợp dữ liệu.

*   **`while` loop**: Lặp khi điều kiện còn đúng.
    ```javascript
    while (condition) { /* code */ }
    ```
*   **`do..while` loop**: Thực thi khối lệnh ít nhất một lần, sau đó kiểm tra điều kiện.
    ```javascript
    do { /* code */ } while (condition);
    ```
*   **`for` loop**: Cú pháp đầy đủ với khởi tạo, điều kiện và bước nhảy.
    ```javascript
    for (initialization; condition; step) { /* code */ }
    ```
*   **`break`**: Dừng toàn bộ vòng lặp ngay lập tức.
*   **`continue`**: Bỏ qua lần lặp hiện tại và chuyển sang lần lặp tiếp theo.
*   **`for..of`**: Lặp qua các giá trị của các đối tượng iterable (như mảng, chuỗi).
    ```javascript
    for (let item of array) { /* code */ }
    ```
*   **`for..in`**: Lặp qua các key (tên thuộc tính) của đối tượng. **Không nên dùng cho mảng.**
    ```javascript
    for (let key in object) { /* code */ }
    ```
    **Bảng so sánh các loại vòng lặp**

    | Vòng lặp  | Mục đích chính           | Duyệt qua gì?             | Hoạt động với mảng? | Hoạt động với object? |
    | :-------- | :----------------------- | :------------------------ | :------------------ | :-------------------- |
    | `for`     | Lặp theo chỉ số/số lần   | Chỉ số (index)            | ✅ Có               | ❌ Không trực tiếp    |
    | `for..of` | Lặp qua giá trị          | Giá trị của iterable      | ✅ Có               | ❌ Không              |
    | `for..in` | Lặp qua key/thuộc tính   | Key của object (và prototype) | ❌ Không khuyến nghị | ✅ Có                 |
    | `while`   | Lặp khi điều kiện đúng   | Không có iterator cụ thể  | ✅ Có               | ✅ Có                 |
    | `do..while` | Lặp ít nhất 1 lần        | Không có iterator cụ thể  | ✅ Có               | ✅ Có                 |

### Đối tượng (Objects)

Objects là tập hợp các cặp `key: value`.

*   **Literals và Properties**: Tạo object bằng `{ key: value }`. Key là chuỗi hoặc Symbol, value có thể là bất kỳ kiểu dữ liệu nào.
    *   Truy cập thuộc tính bằng dot notation (`obj.key`) hoặc bracket notation (`obj["key"]`).
    *   Có thể thêm/xóa/sửa thuộc tính sau khi khai báo.
    *   `delete obj.key` để xóa thuộc tính.
*   **Property Value Shorthand**: Khi tên biến trùng với tên key, có thể viết tắt (`{ name, age }` thay vì `{ name: name, age: age }`).
*   **Tham chiếu đối tượng (Reference Object)**: Biến object lưu trữ tham chiếu đến vùng nhớ của object, không phải giá trị trực tiếp.
    *   Khi copy object, chỉ tham chiếu được copy. Thay đổi trên một biến sẽ ảnh hưởng đến biến kia nếu chúng cùng trỏ đến một object.
    *   `const` với object: biến không thể gán lại, nhưng thuộc tính bên trong object vẫn có thể thay đổi.
*   **So sánh đối tượng**: Hai object khác nhau trong bộ nhớ không bao giờ bằng nhau (`obj1 === obj2` là `false` dù nội dung giống). So sánh theo tham chiếu.
*   **Cloning và Merging**:
    *   **Shallow Copy**: Chỉ copy cấp độ đầu tiên, các object lồng nhau vẫn share tham chiếu. Dùng `Object.assign({}, obj)` hoặc spread syntax `{ ...obj }`.
    *   **Deep Copy**: Tạo bản sao độc lập hoàn toàn, bao gồm cả các object lồng nhau. Dùng `structuredClone(obj)` (hỗ trợ circular references) hoặc thư viện như Lodash (`_.cloneDeep`).
    **Bảng so sánh Shallow Copy vs Deep Copy**

    | Đặc điểm          | Shallow Copy (`{...obj}`, `Object.assign`) | Deep Copy (`structuredClone`, `_.cloneDeep`) |
    | :---------------- | :----------------------------------------- | :--------------------------------------------- |
    | Object gốc        | Bị ảnh hưởng nếu mutate nested properties  | Không bị ảnh hưởng                             |
    | Object lồng nhau  | Share tham chiếu                          | Tạo bản sao độc lập                           |
    | Hiệu năng         | Nhanh hơn                                  | Chậm hơn (phải duyệt sâu)                      |
    | Hỗ trợ Circular Ref | ❌ Không                                   | ✅ Có (`structuredClone`)                      |
    | Clone Function/Symbol | ❌ Không                                   | ❌ Không (`structuredClone`)                   |

### Mảng (Arrays)

Mảng là một kiểu đối tượng đặc biệt để lưu trữ dữ liệu có thứ tự, đánh số từ 0.

*   **Khai báo**: `let arr = []` (phổ biến) hoặc `new Array()`.
*   **`pop()` / `push()`**: Thêm/xóa phần tử ở cuối mảng (nhanh, O(1)).
*   **`shift()` / `unshift()`**: Thêm/xóa phần tử ở đầu mảng (chậm, O(n) vì phải dịch chuyển các phần tử).
    **Bảng so sánh `pop`/`push` vs `shift`/`unshift`**

    | Phương thức       | Thao tác           | Vị trí | Thay đổi mảng gốc? | Độ phức tạp |
    | :---------------- | :----------------- | :----- | :----------------- | :---------- |
    | `push(...items)`  | Thêm phần tử       | Cuối   | ✅ Có              | O(1)        |
    | `pop()`           | Xóa phần tử        | Cuối   | ✅ Có              | O(1)        |
    | `unshift(...items)` | Thêm phần tử       | Đầu    | ✅ Có              | O(n)        |
    | `shift()`         | Xóa phần tử        | Đầu    | ✅ Có              | O(n)        |

*   **`splice(start, deleteCount, ...items)`**: Thay đổi nội dung mảng bằng cách xóa, thay thế hoặc thêm phần tử tại vị trí bất kỳ. Thay đổi mảng gốc.
*   **`slice(start, end)`**: Trả về một bản sao nông (shallow copy) của một phần mảng mới. Không thay đổi mảng gốc.
*   **`concat(...arraysOrValues)`**: Gộp hai hoặc nhiều mảng/giá trị thành một mảng mới. Không thay đổi mảng gốc.
    **Bảng so sánh `splice`, `slice`, `concat`**

    | Phương thức | Mục đích chính           | Thay đổi mảng gốc? | Trả về gì?                 |
    | :---------- | :----------------------- | :----------------- | :------------------------- |
    | `splice()`  | Xóa/thêm/thay thế phần tử | ✅ Có              | Mảng các phần tử đã xóa    |
    | `slice()`   | Tạo bản sao nông của một phần | ❌ Không           | Mảng mới chứa các phần tử được chọn |
    | `concat()`  | Gộp mảng/giá trị         | ❌ Không           | Mảng mới đã gộp            |

*   **`forEach(callback)`**: Thực thi một hàm cho mỗi phần tử. Không trả về mảng mới.
*   **`indexOf(item, from)` / `lastIndexOf(item, from)` / `includes(item, from)`**: Tìm kiếm phần tử trong mảng. `indexOf` trả về index đầu tiên, `lastIndexOf` trả về index cuối cùng, `includes` trả về `true`/`false`.
*   **`find(callback)` / `findIndex(callback)` / `findLastIndex(callback)`**: Tìm phần tử/index đầu tiên/cuối cùng thỏa mãn điều kiện. `find` trả về phần tử, `findIndex`/`findLastIndex` trả về index.
*   **`filter(callback)`**: Tạo mảng mới chứa các phần tử thỏa mãn điều kiện. Không thay đổi mảng gốc.
*   **`map(callback)`**: Tạo mảng mới bằng cách áp dụng một hàm cho mỗi phần tử. Không thay đổi mảng gốc.
*   **`reduce(callback, initial)`**: Tính toán một giá trị duy nhất từ mảng bằng cách áp dụng một hàm tích lũy.
    **Bảng so sánh các phương thức lặp/tìm kiếm/chuyển đổi mảng**

    | Phương thức       | Mục đích chính           | Thay đổi mảng gốc? | Trả về gì?                 |
    | :---------------- | :----------------------- | :----------------- | :------------------------- |
    | `forEach()`       | Thực thi hàm cho mỗi phần tử | ✅ Có (nếu callback thay đổi) | `undefined`                |
    | `map()`           | Chuyển đổi mỗi phần tử   | ❌ Không           | Mảng mới chứa kết quả chuyển đổi |
    | `filter()`        | Lọc các phần tử          | ❌ Không           | Mảng mới chứa các phần tử thỏa mãn |
    | `reduce()`        | Tính toán một giá trị duy nhất | ❌ Không           | Giá trị tích lũy cuối cùng |
    | `find()`          | Tìm phần tử đầu tiên thỏa mãn | ❌ Không           | Phần tử tìm được hoặc `undefined` |
    | `findIndex()`     | Tìm index phần tử đầu tiên thỏa mãn | ❌ Không           | Index tìm được hoặc `-1`   |
    | `includes()`      | Kiểm tra sự tồn tại của phần tử | ❌ Không           | `true` hoặc `false`        |
    | `indexOf()`       | Tìm index đầu tiên của phần tử | ❌ Không           | Index tìm được hoặc `-1`   |

### Promises

Promise là đối tượng đại diện cho kết quả cuối cùng của một thao tác bất đồng bộ.

*   **Trạng thái**: `pending` (đang chờ), `fulfilled` (thành công), `rejected` (thất bại).
*   **Cú pháp**: `new Promise((resolve, reject) => { ... })`.
*   **Xử lý**:
    *   `.then(onFulfilled, onRejected)`: Xử lý khi Promise thành công hoặc thất bại.
    *   `.catch(onRejected)`: Cách viết gọn hơn để xử lý lỗi.
    *   `.finally(callback)`: Luôn chạy khi Promise `settle` (dù thành công hay thất bại).
*   **Promise Chaining**: Nối tiếp các thao tác bất đồng bộ bằng cách `return` một Promise từ `.then()`.
*   **Error Handling**: Promise có "invisible try..catch". Lỗi trong executor hoặc handler sẽ tự động `reject` Promise.
    *   `UnhandledPromiseRejection`: Xảy ra khi Promise bị `reject` mà không có `.catch()`. Browser dùng `window.addEventListener("unhandledrejection")`, Node.js dùng `process.on("unhandledRejection")`.

    **Sơ đồ Vòng đời của Promise**
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

*   **Promise API**:
    *   `Promise.all([p1, p2, ...])`: Chờ tất cả `resolve`. Nếu 1 `reject` → fail toàn bộ.
    *   `Promise.allSettled([p1, p2, ...])`: Chờ tất cả `settle` (fulfilled/rejected). Trả về mảng object `{status, value/reason}`.
    *   `Promise.race([p1, p2, ...])`: Lấy promise `settle` đầu tiên (fulfilled hoặc rejected).
    *   `Promise.any([p1, p2, ...])`: Lấy promise `fulfilled` đầu tiên. Nếu tất cả `reject` → `AggregateError`.
    *   `Promise.resolve(value)`: Tạo Promise đã `fulfilled` ngay lập tức.
    *   `Promise.reject(error)`: Tạo Promise đã `rejected` ngay lập tức.

    **Bảng so sánh các Promise API**

    | API                 | Chờ bao nhiêu?                     | Kết quả trả về                 | Khi có lỗi                               |
    | :------------------ | :--------------------------------- | :----------------------------- | :--------------------------------------- |
    | `Promise.all`       | Tất cả phải `resolve`              | Mảng kết quả                   | 1 `reject` → fail toàn bộ                |
    | `Promise.allSettled` | Tất cả `settle`                    | Mảng `{status, value/reason}`  | Không fail, chỉ ghi nhận                 |
    | `Promise.race`      | 1 promise `settle` đầu tiên        | Kết quả hoặc lỗi đầu tiên      | Lỗi đầu tiên thắng                       |
    | `Promise.any`       | 1 promise `fulfilled` đầu tiên     | Giá trị `fulfilled` đầu tiên   | Nếu tất cả fail → `AggregateError`       |
    | `Promise.resolve`   | Không chờ                          | Promise đã `fulfilled` với `value` | —                                        |
    | `Promise.reject`    | Không chờ                          | Promise đã `rejected` với `error` | —                                        |

    **Sơ đồ so sánh các Promise API (Analogy đầu bếp 🍳)**
    *   **`Promise.all` – “Đợi đủ nguyên liệu”**: Đợi tất cả bếp phụ mang nguyên liệu, 1 hỏng là fail cả bàn.
    *   **`Promise.allSettled` – “Kiểm kê hết nguyên liệu”**: Ghi nhận đủ: cái nào hỏng, cái nào tốt, rồi chọn tiếp.
    *   **`Promise.race` – “Ai chạy nhanh hơn thì lấy”**: Ai về trước (thắng/thua) → chọn kết quả của người đó.
    *   **`Promise.any` – “Chỉ cần một món ngon”**: Chỉ lấy phụ bếp thành công đầu tiên.
    *   **`Promise.resolve`**: Đưa sẵn 1 giỏ rau ngon → coi như done.
    *   **`Promise.reject`**: Báo luôn “chợ đóng cửa”, fail ngay.

### Async/Await

Cú pháp đặc biệt giúp làm việc với Promise dễ dàng hơn, giống như code đồng bộ.

*   **`async` function**:
    *   Luôn trả về một Promise.
    *   Cho phép sử dụng `await` bên trong.
*   **`await` expression**:
    *   Chỉ dùng trong hàm `async`.
    *   Tạm dừng việc thực thi hàm `async` cho đến khi Promise hoàn thành.
    *   Nếu Promise `resolve` → trả về giá trị.
    *   Nếu Promise `reject` → ném ra lỗi (`throw`).
*   **Xử lý lỗi**: Dùng `try...catch` trong hàm `async` hoặc `.catch()` khi gọi hàm `async`.
*   **Song song vs Tuần tự**:
    *   `Promise.all` với `await`: Chạy song song các Promise độc lập để tối ưu tốc độ.
    *   `await` tuần tự: Chạy lần lượt khi có sự phụ thuộc dữ liệu hoặc cần thứ tự.
*   **Lỗi thường gặp**: Dùng `await` trong vòng lặp `for` mà không dùng `Promise.all` sẽ gây chạy tuần tự và chậm.

    **Sơ đồ Flow của `async/await`**
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

    **Bảng so sánh `.then/.catch` vs `async/await`**

    | Đặc điểm          | `Promise .then/.catch`                               | `Async/Await`                                            |
    | :---------------- | :--------------------------------------------------- | :------------------------------------------------------- |
    | Cú pháp           | Dùng callback (`.then`, `.catch`)                    | Dùng cú pháp đồng bộ (`try...catch`)                     |
    | Độ dài code       | Dễ bị dài dòng (chuỗi `.then`)                      | Ngắn gọn, dễ đọc như code đồng bộ                        |
    | Quản lý Promise   | Quản lý nhiều Promise dễ hơn với `Promise.all`      | Quản lý nhiều Promise dễ hơn với `await + Promise.all`  |
    | Trường hợp phù hợp | Hợp với xử lý đồng thời nhiều tác vụ                 | Hợp với flow tuần tự + xử lý lỗi                         |

    **Bảng so sánh `for + await` vs `Promise.all`**

    | Đặc điểm          | `for + await` (Tuần tự)                               | `Promise.all` (Song song)                                |
    | :---------------- | :---------------------------------------------------- | :------------------------------------------------------- |
    | Cách thực thi     | Chạy từng Promise một (chậm)                          | Chạy tất cả cùng lúc (nhanh hơn)                         |
    | Trường hợp phù hợp | Khi có phụ thuộc dữ liệu hoặc cần thứ tự             | Khi các tác vụ độc lập, không phụ thuộc                  |
    | Độ phức tạp       | Dễ hiểu, rõ ràng                                      | Tối ưu hiệu năng                                         |

    **Mindmap: `async / await` trong JavaScript**
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
    ```

### Modules

Giúp chia ứng dụng thành nhiều file nhỏ, dễ quản lý và tái sử dụng.

*   **`export`**: Đánh dấu biến, hàm, lớp có thể truy cập từ bên ngoài module.
*   **`import`**: Nhập chức năng từ các module khác.
*   **Named Export**: Export nhiều giá trị bằng tên (`export function add() { ... }`). Khi import phải dùng đúng tên (`import { add } from './file.js'`). Có thể đổi tên khi import (`import { add as plus } from './file.js'`).
*   **Default Export**: Mỗi module chỉ có một `export default`. Khi import có thể đặt tên tùy ý (`import MyFunction from './file.js'`).
*   **Kết hợp**: Có thể vừa có named export vừa có default export.
*   **`import * as name from './file.js'`**: Import tất cả các named export vào một đối tượng.
*   **Re-export**: `export ... from ...` để gom các export từ nhiều file vào một file trung gian (entry point).
*   **Module Scope**: Biến, hàm trong module không rơi vào global scope.
*   **Browser Modules**: Cần `type="module"` trong thẻ `<script>`. Browser tự động tải các module phụ thuộc.

    **Bảng so sánh Script thường và Module**
    
    | Thuộc tính            | Script thường (`<script>`)      | Module (`<script type="module">`) |
    | :-------------------- | :------------------------------ | :-------------------------------- |
    | **Scope mặc định**    | Global (biến rơi vào `window`) | Module scope (riêng biệt)         |
    | **Hoisting**          | Có (`var`, `function`)          | Có, nhưng module được xử lý trước |
    | **Hỗ trợ `import`/`export`** | ❌ Không                        | ✅ Có                             |
    | **Tải script**        | Từng file độc lập               | Browser tự load dependency graph  |
    | **Caching**           | Không rõ ràng                   | ✅ Module chỉ load 1 lần          |
    | **Strict mode**       | Không bắt buộc                  | ✅ Luôn ở strict mode             |

---

## 2. Các Use Case thực tế

*   **Frontend Development**:
    *   Quản lý state trong React/Vue/Angular (dùng `const` với object, shallow/deep copy, Promise, async/await).
    *   Gọi API lấy dữ liệu (Promise, async/await, `fetch`, `Promise.all`, `Promise.allSettled`).
    *   Xử lý sự kiện (Function Expression, Callback Functions).
    *   Hiển thị danh sách, lọc, chuyển đổi dữ liệu (Array methods: `map`, `filter`, `forEach`).
    *   Tạo các component/module tái sử dụng (Modules).
    *   Hiển thị loading spinner khi Promise đang `pending`.
    *   Timeout API calls (`Promise.race`).
*   **Backend Development (Node.js)**:
    *   Xử lý ghi log vào DB (Promise, async/await).
    *   Xử lý các tác vụ bất đồng bộ (đọc/ghi file, gọi DB).
*   **General Programming**:
    *   Undo/Redo chức năng (Array methods, object cloning).
    *   Xử lý hàng đợi/ngăn xếp (Array methods: `push`, `pop`, `shift`, `unshift`).
    *   Tính toán tổng, trung bình, đếm số lần xuất hiện (Array `reduce`).
    *   Quản lý cấu hình ứng dụng (Objects, Modules).

---

## 3. Các Bug thường gặp

*   **Kiểu dữ liệu**:
    *   Nhầm lẫn `var` với `let`/`const` (scope, redeclaration, hoisting).
    *   `NaN === NaN` là `false`.
    *   `typeof null` là `"object"`.
*   **Chuyển đổi kiểu**:
    *   Phép cộng `+` ưu tiên nối chuỗi nếu có chuỗi.
    *   `Number(undefined)` → `NaN`, không phải `0`.
    *   `Boolean("0")` và `Boolean(" ")` là `true`.
*   **Hàm**:
    *   Nhầm lẫn giữa gọi hàm (`func()`) và truyền tham chiếu hàm (`func`).
    *   Quên `return` trong hàm khi cần giá trị.
*   **Vòng lặp**:
    *   Dùng `for..in` cho mảng.
    *   Truy cập index ngoài giới hạn mảng (`undefined`).
*   **Đối tượng**:
    *   Quên dấu ngoặc kép cho multiword key.
    *   Dùng dot notation với biến key (`obj.key` thay vì `obj[key]`).
    *   Shallow copy với object lồng nhau → thay đổi bản sao ảnh hưởng gốc.
    *   `const` object vẫn có thể mutate thuộc tính bên trong.
    *   Dùng `JSON.parse(JSON.stringify(obj))` để deep clone object có circular reference, function, `undefined`, `Symbol` → sẽ bị lỗi hoặc mất dữ liệu.
*   **Mảng**:
    *   `shift()`/`unshift()` chậm trên mảng lớn.
    *   `pop()`/`shift()` trên mảng rỗng trả về `undefined`.
    *   Nhầm lẫn `splice()` (thay đổi gốc) với `slice()` (tạo bản sao).
    *   `forEach()` không trả về mảng mới (nhầm với `map()`).
    *   `find()` trả về `undefined` nếu không tìm thấy.
    *   `reduce()` quên giá trị `initial` hoặc không `return` trong callback.
*   **Promises**:
    *   Quên `.catch()` → `UnhandledPromiseRejectionWarning`.
    *   Không `return Promise` trong `.then()` → chuỗi Promise không hoạt động đúng.
    *   Dùng `resolve` và `reject` cả hai trong cùng một Promise (chỉ cái đầu tiên có tác dụng).
    *   `Promise.all` bị fail toàn bộ nếu 1 Promise `reject`.
    *   Nhầm lẫn `Promise.race` với `Promise.any`.
*   **Async/Await**:
    *   Dùng `await` ngoài hàm `async`.
    *   Quên `try...catch` trong hàm `async` hoặc `.catch()` khi gọi hàm `async`.
    *   Dùng `await` trong vòng lặp `for` cho các tác vụ độc lập → gây chạy tuần tự và chậm.
*   **Modules**:
    *   Quên `type="module"` trong HTML.
    *   Sai đường dẫn import (`import { add } from 'math.js'` thay vì `'./math.js'`).
    *   Import nhầm `default` và `named export`.
    *   Circular dependency.
    *   Chạy trực tiếp file HTML bằng `file:///` gây CORS error.
    *   Xung đột tên khi re-export.

---

## 4. Bài tập tổng hợp

Để củng cố kiến thức, hãy thực hành các bài tập sau:

1.  **Xây dựng một ứng dụng Todo List đơn giản**:
    *   Sử dụng mảng để lưu trữ các task.
    *   Áp dụng các phương thức mảng (`push`, `pop`, `splice`, `filter`, `map`) để thêm, xóa, sửa, lọc task.
    *   Sử dụng `localStorage` để lưu trữ dữ liệu (yêu cầu kiến thức ngoài các file hiện có, nhưng là một use case thực tế).
2.  **Viết một hàm `fetchData(url)` sử dụng `async/await`**:
    *   Hàm này sẽ gọi một API (ví dụ: `https://jsonplaceholder.typicode.com/posts/1`).
    *   Sử dụng `try...catch` để xử lý lỗi mạng hoặc lỗi từ API.
    *   Trả về dữ liệu nếu thành công, hoặc `null` nếu thất bại.
3.  **Tạo một hệ thống quản lý người dùng đơn giản**:
    *   Sử dụng object để biểu diễn thông tin người dùng (`id`, `name`, `email`).
    *   Sử dụng mảng các object để lưu danh sách người dùng.
    *   Viết các hàm để:
        *   Thêm người dùng mới.
        *   Tìm người dùng theo `id` (`find`).
        *   Cập nhật thông tin người dùng (`findIndex` + thay đổi object).
        *   Xóa người dùng (`filter`).
    *   Đảm bảo các hàm này không mutate object gốc khi cập nhật (sử dụng shallow/deep copy).
4.  **Xử lý nhiều API song song**:
    *   Viết một hàm `loadDashboardData()` sử dụng `Promise.all` để gọi 3 API độc lập cùng lúc (ví dụ: `users`, `posts`, `comments` từ `jsonplaceholder.typicode.com`).
    *   Xử lý kết quả khi tất cả thành công.
    *   Sử dụng `Promise.allSettled` để xử lý trường hợp một hoặc nhiều API bị lỗi, nhưng vẫn muốn hiển thị dữ liệu từ các API thành công.
5.  **Tạo một module tiện ích `utils.js`**:
    *   Export named function `capitalize(str)` và `reverseString(str)`.
    *   Export default một object `{ version: "1.0.0", author: "Your Name" }`.
    *   Trong `main.js`, import và sử dụng các chức năng này.
