# **Constructor, operator `"new"`**

- Cú pháp `{...}` thông thường cho phép tạo một đối tượng. Nhưng thường thì chúng ta cần tạo nhiều đối tượng giống nhau, như nhiều người dùng hoặc các mục menu, v.v.

- Điều đó có thể được thực hiện bằng cách sử dụng các hàm khởi tạo và toán tử `"new"`.

### **Constructor function**
- Về mặt kỹ thuật, hàm tạo là các hàm thông thường. Tuy nhiên, có hai quy ước:
  1. Chúng được đặt tên bằng chữ cái in hoa đầu tiên.
  2. Chúng chỉ nên được thực thi với toán tử `new`.

  - ví dụ:

  ```
    function User(name) {
        this.name = name;
        this.isAdmin = false;
    }

    let user = new User("Jack");

    alert(user.name); // Jack
    alert(user.isAdmin); // false
  ```

  - Khi một hàm được thực thi với `new`, nó sẽ thực hiện các bước sau:
    1. Một đối tượng trống mới được tạo và gán cho đối tượng `this`.
    2. Phần thân hàm thực thi. Thông thường nó sửa đổi `this`, thêm các thuộc tính mới vào nó.
    3. Giá trị của `this` được trả lại.

  - Nói cách khác, `new User(...)` thực hiện một số việc như:

  ```
    function User(name) {
        // this = {};  (implicitly)

        // add properties to this
        this.name = name;
        this.isAdmin = false;

        // return this;  (implicitly)
    }
  ```

  - Vì vậy, hãy để `user = new User ("Jack")` cho kết quả tương tự như:
  ```
    let user = {
        name: "Jack",
        isAdmin: false
    };
  ```

  - Bây giờ nếu chúng ta muốn tạo người dùng khác, chúng ta có thể gọi `new User("Ann")`, `new User("Alice")`, v.v. Ngắn hơn nhiều so với việc sử dụng các ký tự mọi lúc, và cũng dễ đọc.
  - Đó là mục đích chính của trình tạo - để triển khai mã tạo đối tượng có thể tái sử dụng.
  - Hãy lưu ý một lần nữa - về mặt kỹ thuật, bất kỳ hàm nào cũng có thể được sử dụng làm hàm tạo. Đó là: bất kỳ chức năng nào có thể được chạy với `new`, và nó sẽ thực thi thuật toán trên. “Chữ cái viết hoa trước” là một thỏa thuận chung, để làm rõ ràng rằng một chức năng phải được chạy với `new`.


### **new function() { … }**

  - Nếu chúng ta có nhiều dòng mã liên quan đến việc tạo một đối tượng phức hợp, chúng ta có thể bọc chúng trong hàm khởi tạo, như sau:
  ```
    let user = new function() {
        this.name = "John";
        this.isAdmin = false;

        // ...other code for user creation (... mã khác để tạo người dùng)
        // maybe complex logic and statements (có thể là logic và câu lệnh phức tạp)
        // local variables etc (biến cục bộ)
    };
  ```
  - Không thể gọi lại hàm tạo vì nó không được lưu ở bất kỳ đâu, chỉ được tạo và gọi. Vì vậy, thủ thuật này nhằm mục đích đóng gói mã xây dựng một đối tượng duy nhất, mà không cần sử dụng lại trong tương lai.


### **Constructor mode test: new.target**
  - Bên trong một hàm, chúng ta có thể kiểm tra xem nó có được gọi với `new` hay không, bằng cách sử dụng thuộc tính `new.targe`t đặc biệt.
  - Nó không được xác định cho các cuộc gọi thông thường và bằng hàm nếu được gọi với `new`:

  ```
    function User() {
        alert(new.target);
    }

    // without "new":
    User(); // undefined

    // with "new":
    new User(); // function User { ... }
  ```

  - Điều đó có thể được sử dụng bên trong hàm để biết liệu nó được gọi với `new`, “ở chế độ khởi tạo” hay không có nó, “ở chế độ thông thường”.
  - Chúng tôi cũng có thể thực hiện cả cuộc gọi mới và cuộc gọi thông thường để thực hiện tương tự, như sau:

  ```
    function User(name) {
        if (!new.target) { // if you run me without new
            return new User(name); // ...I will add new for you
        }

        this.name = name;
    }

    let john = User("John"); // redirects call to new User
    alert(john.name); // John
  ```
  - Cách tiếp cận này đôi khi được sử dụng trong các thư viện để làm cho cú pháp linh hoạt hơn. Vì vậy, mọi người có thể gọi hàm có hoặc không có hàm `new`, và nó vẫn hoạt động.
  - Mặc dù vậy, có lẽ không phải là một điều tốt để sử dụng ở mọi nơi, bởi vì việc bỏ qua tính năng `new` sẽ làm cho nó trở nên ít rõ ràng hơn một chút về những gì đang xảy ra. Với `new`, tất cả chúng ta đều biết rằng đối tượng mới đang được tạo.


### **Return from constructors**
  - Thông thường, các hàm tạo không có câu lệnh `return`. Nhiệm vụ của họ là viết tất cả những thứ cần thiết vào `this`, và nó sẽ tự động trở thành kết quả.
  - Nhưng nếu có một câu lệnh `return`, thì quy tắc rất đơn giản:
    - Nếu `return` được gọi với một đối tượng, thì đối tượng được trả về thay vì đối tượng `this`.
    - Nếu `return` được gọi với giá trị nguyên thủy, nó sẽ bị bỏ qua.

  - Nói cách khác, `return` với một đối tượng trả về đối tượng đó, trong tất cả các trường hợp khác, `this` được trả về.
  - Ví dụ: ở đây `return` ghi đè `this` bằng cách trả về một đối tượng:

  ```
    function BigUser() {

        this.name = "John";

        return { name: "Godzilla" };  // <-- returns this object
    }

    alert( new BigUser().name );  // Godzilla, got that object
  ```

  - Và đây là một ví dụ với giá trị `return` trống (hoặc chúng ta có thể đặt một giá trị nguyên thủy sau nó, không thành vấn đề):

  ```
    function SmallUser() {

        this.name = "John";

        return; // <-- returns this
    }

    alert( new SmallUser().name );  // John
  ```
  - Thông thường các hàm tạo không có câu lệnh `return`. Ở đây chúng tôi đề cập đến hành vi đặc biệt với các đối tượng trả về chủ yếu là vì mục đích hoàn chỉnh.



  - Nhân tiện, chúng ta có thể bỏ qua dấu ngoặc đơn sau dấu ngoặc đơn, nếu nó không có đối số:
  ```
    let user = new User; // <-- no parentheses
    // same as
    let user = new User();
  ```
  - Việc bỏ qua dấu ngoặc đơn ở đây không được coi là một "phong cách tốt", nhưng cú pháp được cho phép bởi đặc tả.


### **Methods in constructor** (Các phương thức trong hàm tạo)
  - Sử dụng các hàm khởi tạo để tạo các đối tượng mang lại rất nhiều tính linh hoạt. Hàm khởi tạo có thể có các tham số xác định cách xây dựng đối tượng và những gì cần đặt trong đó.
  - Tất nhiên, chúng ta có thể thêm vào `this` không chỉ các thuộc tính, mà còn cả các phương thức.
  - Ví dụ: `new User(tên)` bên dưới tạo một đối tượng với `name` đã cho và phương thức `sayHi`:

  ```
    function User(name) {
        this.name = name;

        this.sayHi = function() {
            alert( "My name is: " + this.name );
        };
    }

    let john = new User("John");

    john.sayHi(); // My name is: John

    /*
    john = {
        name: "John",
        sayHi: function() { ... }
    }
    */
  ```



## **Summary**
  - Hàm tạo hay ngắn gọn là hàm tạo, là các hàm thông thường, nhưng có một thỏa thuận chung là đặt tên chúng bằng chữ in hoa trước tiên.
  - Hàm tạo chỉ nên được gọi bằng cách sử dụng `new`. Một cuộc gọi như vậy ngụ ý tạo ra một trống rỗng `this` ở đầu và trả về một phổ biến ở cuối.