# Class Basic System

```
    Trong lập trình hướng đối tượng, một lớp là một mẫu mã trường trình có thể mở rộng để tạo các đối tượng, cung cấp các giá trị ban đầu cho trạng thái ( các hiến thành viên), vầ triển khai hành vi (các hàm hoặc phương thức thành viên).
```

- Trong thực tế, chúng ta thường cần tạo ra nhiều đối tượng cùng loại như người dùng, hàng hoá, hoặc bất cứ thứ gì.
- Như chúng ta đã biết thì từ khoá `new` giúp chúng ta làm điều đó.
- Nhưng trong `Javascript` hiện đại, có 1 cấu trúc `Class` nâng cao hơn, giới thiệu các tính năng mới tuyệt vời hữu ích cho lập trình viên hướng đối tượng.

## Systax `Class`

```
    class MyClass {
        // class methods
        constructor() { ... }
        method1() { ... }
        method2() { ... }
        method3() { ... }
        ...
    }
```

- Sau đó, sử dụng `new MyClass` để tạo 1 đối tượng mới với tất cả các phương thức đã được liệt kê.
- Phương thức `Constructor()` được gọi tự động bởi `new`, vì vậy chúng ta có thể khởi tạo đối tượng ở đó.

```
    class User {

        constructor(name) {
            this.name = name;
        }

        sayHi() {
            alert(this.name);
        }

    }

    // Usage:
    let user = new User("John");
    user.sayHi();
```

- khi `new User("Join")` được gọi:
  1. Một đối tượng mới được tạo ra.
  2. Hàm `constructor` chạy với đối số đã cho và gán nó cho `this.name`.

- … Sau đó, chúng ta có thể gọi các phương thức đối tượng, chẳng hạn như `user.sayHi()`.


## What is a Class?

- Vì vậy, chính xác một lớp là gì? Đó không phải là một thực thể cấp ngôn ngữ hoàn toàn mới, như người ta vẫn nghĩ.
- Trong JavaScript, một lớp là một loại hàm.

```
    class User {
        constructor(name) { this.name = name; }
        sayHi() { alert(this.name); }
    }

    // proof: User is a function
    alert(typeof User); // function
```

- Cấu trúc `Class User {...}` thực sự làm là gì:
  1. Tạo một hàm có tên `User`, hàm này trở thành kết quả của khai báo lớp. Mã hàm được lấy từ phương thức khởi tạo (giả sử trống nếu chúng ta không viết phương thức như vậy).
  2. Lưu trữ các phương thức của lớp, chẳng hạn như `sayHi`, trong `User.prototype`.

- Sau khi đối tượng `new User` được tạo, khi chúng tôi gọi phương thức của nó, nó được lấy từ `prototype`, giống như được mô tả trong chương F.prototype. Vì vậy, đối tượng có quyền truy cập vào các phương thức của lớp.

- Chúng ta có thể minh họa kết quả của khai báo `class User` như:

![class](https://javascript.info/article/class/class-user.svg)

- Đây là mã để tìm hiểu nội dung về nó:

```
    class User {
        constructor(name) { this.name = name; }
        sayHi() { alert(this.name); }
    }

    // class is a function
    alert(typeof User); // function

    // ...or, more precisely, the constructor method
    alert(User === User.prototype.constructor); // true

    // The methods are in User.prototype, e.g:
    alert(User.prototype.sayHi); // the code of the sayHi method

    // there are exactly two methods in the prototype
    alert(Object.getOwnPropertyNames(User.prototype)); // constructor, sayHi
```


## Not just a syntactic sugar

- Đôi khi mọi người nói rằng `Class` là một "cú pháp" (cú pháp được thiết kế để làm cho mọi thứ dễ đọc hơn, nhưng không giới thiệu bất kỳ điều gì mới), bởi vì chúng tôi thực sự có thể khai báo giống nhau mà không có từ khóa `Class`:

```
    // rewriting class User in pure functions

    // 1. Create constructor function
    function User(name) {
        this.name = name;
    }
    // a function prototype has "constructor" property by default,
    // so we don't need to create it

    // 2. Add the method to prototype
    User.prototype.sayHi = function() {
        alert(this.name);
    };

    // Usage:
    let user = new User("John");
    user.sayHi();
```

- Kết quả của định nghĩa này là giống nhau. Vì vậy, thực sự có những lý do tại sao `Class` có thể được coi là một đường cú pháp để xác định một phương thức khởi tạo cùng với các phương thức nguyên mẫu của nó.

- Tuy nhiên, vẫn có những khác biệt quan trọng.

  1. Đầu tiên, một hàm được tạo bởi `Class` được gắn nhãn bởi một thuộc tính bên trong đặc biệt `[[FunctionKind]]: "classConstructor"`. Vì vậy, nó không hoàn toàn giống với việc tạo nó theo cách thủ công.

  - Ngôn ngữ kiểm tra thuộc tính đó ở nhiều nơi. Ví dụ: không giống như một hàm thông thường, nó phải được gọi với new:

  ```
    class User {
        constructor() {}
    }

    alert(typeof User); // function
    User(); // Error: Class constructor User cannot be invoked without 'new'
  ```

  - Ngoài ra, một biểu diễn chuỗi của một phương thức khởi tạo lớp trong hầu hết các công cụ JavaScript bắt đầu bằng “lớp…”

  ```
    class User {
        constructor() {}
    }

    alert(User); // class User { ... }
  ```

  2. Các phương thức lớp là không thể liệt kê. Một định nghĩa lớp đặt cờ `enumerable` thành `false` cho tất cả các phương thức trong `"Prototype"`.

  - Điều đó là tốt, bởi vì nếu chúng ta dùng `for..in` trong một đối tượng, chúng ta thường không muốn các phương thức lớp của nó.

  3. Các lớp luôn sử dụng nghiêm ngặt. Tất cả mã bên trong cấu trúc lớp được tự động ở chế độ nghiêm ngặt.


- Bên cạnh đó, cú pháp `Class` mang lại nhiều tính năng khác mà chúng ta sẽ khám phá ở phần sau.



## Class Expression

- Cũng giống như các hàm, các lớp có thể được định nghĩa bên trong một biểu thức khác, được truyền xung quanh, trả về, được gán, v.v.

```
    let User = class {
        sayHi() {
            alert("Hello");
        }
    };
```

- Tương tự như Biểu thức hàm được đặt tên, biểu thức lớp có thể có tên.

- Nếu một biểu thức lớp có tên, thì tên đó chỉ hiển thị bên trong lớp:

```
    // "Named Class Expression"
    // (no such term in the spec, but that's similar to Named Function Expression)
    let User = class MyClass {
        sayHi() {
            alert(MyClass); // MyClass name is visible only inside the class
        }
    };

    new User().sayHi(); // works, shows MyClass definition

    alert(MyClass); // error, MyClass name isn't visible outside of the class
```

- Chúng tôi thậm chí có thể tạo các lớp động "theo yêu cầu", như thế này:

```
    function makeClass(phrase) {
        // declare a class and return it
        return class {
            sayHi() {
            alert(phrase);
            }
        };
    }

    // Create a new class
    let User = makeClass("Hello");

    new User().sayHi(); // Hello
```


## Getters/setters

- Cũng giống như các đối tượng theo nghĩa đen, các lớp có thể bao gồm getters / setters, các thuộc tính được tính toán, v.v.

- Dưới đây là một ví dụ cho `user.name` được triển khai bằng `get / set`:

```
    class User {

        constructor(name) {
            // invokes the setter
            this.name = name;
        }

        get name() {
            return this._name;
        }

        set name(value) {
        if (value.length < 4) {
            alert("Name is too short.");
            return;
            }
            this._name = value;
        }

    }

    let user = new User("John");
    alert(user.name); // John

    user = new User(""); // Name is too short.
```

- Về mặt kỹ thuật, khai báo lớp như vậy hoạt động bằng cách tạo getters và setters trong User.prototype.


## Computed names […]

- Dưới đây là một ví dụ với tên phương thức được tính bằng cách sử dụng dấu ngoặc [...]:

```
    class User {

        ['say' + 'Hi']() {
            alert("Hello");
        }

    }

    new User().sayHi();
```

- Các tính năng như vậy rất dễ nhớ, vì chúng giống với các đối tượng theo nghĩa đen.


## Class fields

- Trước đây, các lớp của chúng ta chỉ có các phương thức.
- “Trường lớp” là một cú pháp cho phép thêm bất kỳ thuộc tính nào.
- Ví dụ: hãy thêm thuộc tính `name` vào `Class User`:

```
    class User {
        name = "John";

        sayHi() {
            alert(`Hello, ${this.name}!`);
        }
    }

    new User().sayHi(); // Hello, John!
```

- Vì vậy, chúng tôi chỉ cần viết "=" trong khai báo, và thế là xong.
- Sự khác biệt quan trọng của các trường lớp là chúng được đặt trên các đối tượng riêng lẻ, không phải User.prototype:

```
    class User {
        name = "John";
    }

    let user = new User();
    alert(user.name); // John
    alert(User.prototype.name); // undefined
```

- Chúng tôi cũng có thể chỉ định các giá trị bằng cách sử dụng các biểu thức phức tạp hơn và các lệnh gọi hàm:

```
    class User {
        name = prompt("Name, please?", "John");
    }

    let user = new User();
    alert(user.name); // John
```

- Như đã trình bày trong chương Các hàm ràng buộc hàm trong JavaScript có một động lực này. Nó phụ thuộc vào ngữ cảnh của cuộc gọi.

- Vì vậy, nếu một phương thức đối tượng được truyền xung quanh và được gọi trong một ngữ cảnh khác, thì `this` sẽ không còn là một tham chiếu đến đối tượng của nó nữa.

- Ví dụ: mã này sẽ hiển thị `undefined`:
  
```
    class Button {
        constructor(value) {
            this.value = value;
        }

        click() {
            alert(this.value);
        }
    }

    let button = new Button("hello");

    setTimeout(button.click, 1000); // undefined
```

- Vấn đề được gọi là `losing this`.
- Có hai cách tiếp cận để khắc phục nó, như đã thảo luận trong chương Ràng buộc hàm:

  1. Truyền một hàm wrapper, chẳng hạn như `setTimeout (() => button.click (), 1000)`.
  2. Ràng buộc phương thức với đối tượng, ví dụ: trong hàm tạo.

- Các trường lớp cung cấp một cú pháp khác, khá thanh lịch:

```
    class Button {
        constructor(value) {
            this.value = value;
        }
        click = () => {
            alert(this.value);
        }
    }

    let button = new Button("hello");

    setTimeout(button.click, 1000); // hello
```

- Trường lớp `click = () => {...}` được tạo trên cơ sở từng đối tượng, có một chức năng riêng biệt cho từng đối tượng `Button`, bên trong nó tham chiếu đến đối tượng đó. Chúng ta có thể vượt qua `button.click` ở bất kỳ đâu và giá trị của `this` sẽ luôn đúng.

- Điều đó đặc biệt hữu ích trong môi trường trình duyệt, dành cho người nghe sự kiện.


## Summary

- Cú pháp lớp cơ bản trông giống như sau:

```
    class MyClass {
        prop = value; // property

        constructor(...) { // constructor
            // ...
        }

        method(...) {} // method

        get something(...) {} // getter method
        set something(...) {} // setter method

        [Symbol.iterator]() {} // method with computed name (symbol here)
        // ...
    }
```

- Về mặt kỹ thuật, `MyClass` là một hàm (hàm mà chúng tôi cung cấp dưới dạng hàm `constructor`), trong khi các phương thức, bộ nhận và bộ định tuyến được ghi vào `MyClass.prototype`.
