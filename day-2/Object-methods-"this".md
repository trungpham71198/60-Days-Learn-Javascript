# **“this” in methods**

- Thông thường, một phương thức đối tượng cần truy cập thông tin được lưu trữ trong đối tượng để thực hiện công việc của nó.

- Ví dụ: mã bên trong `user.sayHi()` có thể cần tên của người dùng.

  **Để truy cập đối tượng, một phương thức có thể sử dụng từ khóa this.**

- Giá trị `this` là đối tượng “Trước dấu chấm”, đối tượng được sử dụng để gọi phương thức.

```
    let user = {
        name: "John",
        age: 30,

        sayHi() {
            // "this" is the "current object"
            alert(this.name);
        }
    };

    user.sayHi(); // John
```

- Ở đây trong quá trình thực thi `user.sayHi()`, giá trị `this` sẽ là `user`.
- Về mặt kỹ thuật, cũng có thể truy cập đối tượng mà không có `this`, bằng cách tham chiếu nó qua biến bên ngoài:

```
    let user = {
        name: "John",
        age: 30,

        sayHi() {
            alert(user.name); // "user" instead of "this"
        }

    };
```

- Nhưng mã như vậy là không đáng tin cậy. Nếu chúng tôi quyết định sao chép `user` sang một biến khác
- ví dụ: `admin = user` và ghi đè `user` bằng thứ khác, sau đó nó sẽ truy cập sai đối tượng.

```
    let user = {
        name: "John",
        age: 30,

        sayHi() {
            alert( user.name ); // leads to an error
        }

    };


    let admin = user;
    user = null; // overwrite to make things obvious

    admin.sayHi(); // TypeError: Cannot read property 'name' of null
```

- Nếu chúng ta sử dụng this.name thay vì user.name bên trong cảnh báo, thì mã sẽ hoạt động.

- “this” is not bound (không bị ràng buộc)

  - Trong JavaScript, từ khóa `this` hoạt động không giống như hầu hết các ngôn ngữ lập trình khác. Nó có thể được sử dụng trong bất kỳ hàm nào, ngay cả khi nó không phải là một phương thức của một đối tượng.

    ```
        function sayHi() {
            alert( this.name );
        }
    ```

  - Giá trị của `this` được đánh giá trong thời gian chạy, tùy thuộc vào ngữ cảnh.

  - Ví dụ: ở đây, cùng một hàm được gán cho hai đối tượng khác nhau và có “this” khác nhau trong các lệnh gọi:

  ```
    let user = { name: "John" };
    let admin = { name: "Admin" };

    function sayHi() {
        alert( this.name );
    }

    // use the same function in two objects
    user.f = sayHi;
    admin.f = sayHi;

    // these calls have different this
    // "this" inside the function is the object "before the dot"
    user.f(); // John  (this == user)
    admin.f(); // Admin  (this == admin)

    admin['f'](); // Admin (dot or square brackets access the method – doesn't matter) (dấu chấm hoặc dấu ngoặc vuông truy cập phương thức - không quan trọng)
  ```

  - Quy tắc rất đơn giản: nếu `obj.f()` được gọi, thì `this` là `obj` trong quá trình gọi của `f`. Vì vậy, đó là `user` hoặc `admin` trong ví dụ trên.

### **Calling without an object: this == undefined** (Gọi mà không có đối tượng: this == undefined)

- Chúng ta thậm chí có thể gọi hàm mà không có đối tượng nào:

  ```
    function sayHi() {
        alert(this);
    }

    sayHi(); // undefined
  ```

- Trong trường hợp `this`, điều này `undefined` trong chế độ nghiêm ngặt. Nếu chúng tôi cố gắng truy cập `this.name`, sẽ xảy ra lỗi.
- Ở chế độ không nghiêm ngặt, giá trị của `this` trong trường hợp này sẽ là `window` toàn cục (cửa sổ trong trình duyệt, chúng ta sẽ tìm hiểu nó sau trong chương Đối tượng toàn cục). Đây là một hành vi lịch sử `use strict`.
- Thông thường cuộc gọi như vậy là một lỗi lập trình. Nếu có `this` bên trong một hàm, nó sẽ được gọi trong ngữ cảnh đối tượng.

### **The consequences of unbound this** (Hậu quả của việc không ràng buộc **this**)

- Nếu bạn đến từ một ngôn ngữ lập trình khác, thì có lẽ bạn đã quen với ý tưởng về một "ràng buộc cái này", trong đó các phương thức được định nghĩa trong một đối tượng luôn tham chiếu đến đối tượng đó.
- Trong JavaScript, `this` là "Free", giá trị của nó được đánh giá tại thời điểm gọi và không phụ thuộc vào nơi phương thức được khai báo, mà phụ thuộc vào đối tượng nào là "trước dấu chấm".
- Khái niệm thời gian chạy đã đánh giá điều này có cả ưu điểm và nhược điểm. Một mặt, một chức năng có thể được sử dụng lại cho các đối tượng khác nhau. Mặt khác, tính linh hoạt cao hơn tạo ra nhiều khả năng mắc sai lầm hơn.
  `Ở đây quan điểm của tôi không phải là đánh giá liệu quyết định thiết kế ngôn ngữ này là tốt hay xấu. Chúng tôi sẽ hiểu cách làm việc với nó, cách nhận được lợi ích và tránh các vấn đề.`

### **Arrow functions have no “this”** (Các hàm mũi tên không có **“This”**)

- Các hàm mũi tên rất đặc biệt: chúng không có `this` "riêng". Nếu chúng ta tham chiếu `this` từ một hàm như vậy, nó được lấy từ hàm "bình thường" bên ngoài.
- Ví dụ, ở đây `arrow()` sử dụng `this` từ phương thức `user.sayHi()` bên ngoài:

  ```
    let user = {
        firstName: "Ilya",
        sayHi() {
            let arrow = () => alert(this.firstName);
            arrow();
        }
    };

    user.sayHi(); // Ilya
  ```

- Đó là một tính năng đặc biệt của các hàm mũi tên, điều này rất hữu ích khi chúng tôi thực sự không muốn có một cái riêng biệt mà muốn đưa nó ra khỏi bối cảnh bên ngoài. Ở phần sau của chương Các hàm mũi tên được xem lại, chúng ta sẽ đi sâu hơn vào các hàm mũi tên. [**Arrow functions revisited**](https://javascript.info/arrow-functions)

## **Summary** (Tóm lược)

- Các hàm được lưu trữ trong thuộc tính đối tượng được gọi là **Method** (phương thức).
- Các phương thức cho phép các đối tượng “hoạt động” như object.doSomething ().
- Các phương thức có thể tham chiếu đối tượng `this`.

  **Giá trị của `this` được xác định tại thời điểm chạy.**

- Khi một hàm được khai báo, nó có thể sử dụng `this`, nhưng `this` không có giá trị cho đến khi hàm được gọi.
- Một chức năng có thể được sao chép giữa các đối tượng.
- Khi một hàm được gọi theo cú pháp `“method”`: `object.method()`, giá trị của `this` gọi là object.

  `Xin lưu ý rằng các chức năng mũi tên là đặc biệt: chúng không có chức năng này. Khi điều này được truy cập bên trong một hàm mũi tên, nó sẽ được lấy từ bên ngoài.`
