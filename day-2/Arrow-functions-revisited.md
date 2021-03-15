# **Arrow functions revisited** 

- Các hàm mũi tên không chỉ là một `shorthand` để viết những thứ nhỏ. Chúng có một số tính năng rất cụ thể và hữu ích.
- JavaScript chứa đầy các tình huống mà chúng ta cần viết một hàm nhỏ được thực thi ở một nơi khác.

  - ví dụ:
    `arr.forEach (func) - func được forEach thực thi cho mọi mục mảng.`

- `Theo tinh thần của JavaScript, việc tạo một hàm và chuyển nó đến một nơi nào đó.`
- `Và trong các chức năng như vậy, chúng tôi thường không muốn rời khỏi ngữ cảnh hiện tại. Đó là lúc các chức năng mũi tên trở nên hữu ích.`

### **Arrow functions have no “this”** (Các hàm mũi tên không có `**this**`)

- Như chúng ta nhớ từ  `Object methods, "this"`, các hàm mũi tên không có `this`. Nếu `this` được truy cập, `this` được lấy từ bên ngoài.

- Ví dụ, chúng ta có thể sử dụng nó để lặp lại bên trong một phương thức đối tượng:

```
    let group = {
        title: "Our Group",
        students: ["John", "Pete", "Alice"],

        showList() {
            this.students.forEach(
                student => alert(this.title + ': ' + student)
            );
        }
    };

    group.showList();
```

- Ở đây trong `forEach`, hàm mũi tên được sử dụng, vì vậy hàm `this.title` trong nó hoàn toàn giống như trong phương thức `showList` bên ngoài. Đó là: `group.title`.

- Nếu chúng tôi sử dụng hàm `"thông thường"`, sẽ xảy ra lỗi:

```
    let group = {
        title: "Our Group",
        students: ["John", "Pete", "Alice"],

        showList() {
            this.students.forEach(function(student) {
                // Error: Cannot read property 'title' of undefined
                alert(this.title + ': ' + student);
            });
        }
    };

    group.showList();
```

- Lỗi xảy ra do `forEach` chạy các chức năng với `this = undefined` theo mặc định, vì vậy việc truy cập `undefined.title` được thực hiện.

- Điều đó không ảnh hưởng đến các chức năng của mũi tên, vì chúng không có chức năng `this`.


### **Arrow functions can’t run with `new`** (Các hàm mũi tên không thể chạy với `**new**`)

- Không có `this` đương nhiên có nghĩa là một hạn chế khác: không thể sử dụng các hàm mũi tên làm hàm tạo. Chúng không thể được gọi với `new`.


### **Arrow functions VS bind** (Hàm mũi tên VS ràng buộc)

- Có một sự khác biệt nhỏ giữa hàm mũi tên `=>` và hàm thông thường được gọi với `.bind (this)`:

  - `.bind(this)` tạo ra một "phiên bản ràng buộc" của hàm.
  - Mũi tên `=>` không tạo ra bất kỳ ràng buộc nào. Chức năng này chỉ đơn giản là không có `this`. Việc tra cứu `this` được thực hiện giống hệt như cách tìm kiếm theo biến thông thường: trong môi trường `lexical` bên ngoài.


### **Arrows have no “arguments”** (Mũi tên không có "đối số")

- Hàm mũi tên cũng không có biến `arguments` (đối số).
- Điều đó thật tuyệt vời `decorators`, khi chúng ta cần chuyển tiếp cuộc gọi với `this` và `args`.

- Ví dụ: `defer (f, ms)` nhận một hàm và trả về một trình bao bọc xung quanh nó làm chậm cuộc gọi theo `mili` giây:

```
    function defer(f, ms) {
        return function() {
            setTimeout(() => f.apply(this, arguments), ms);
        };
    }

    function sayHi(who) {
        alert('Hello, ' + who);
    }

    let sayHiDeferred = defer(sayHi, 2000);
    sayHiDeferred("John"); // Hello, John after 2 seconds
```

- Tương tự nếu không có hàm mũi tên sẽ giống như sau:

```
    function defer(f, ms) {
        return function(...args) {
            let ctx = this;
            setTimeout(function() {
                return f.apply(ctx, args);
            }, ms);
        };
    }
```

- Ở đây chúng tôi phải tạo thêm các biến `args` và `ctx` để hàm bên trong `setTimeout` có thể nhận chúng.


## **Summary** (Tóm lược)

- Arrow functions:
  - Không có `this`
  - Không có `arguments`
  - Không thể được gọi với `new`

`Đó là bởi vì chúng dành cho các đoạn mã ngắn không có “ngữ cảnh” của riêng chúng, mà là hoạt động trong bối cảnh hiện tại. Và chúng thực sự tỏa sáng trong trường hợp sử dụng đó.`