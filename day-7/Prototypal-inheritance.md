# Prototypal inheritance (Kế thừa nguyên mẫu)

- Trong lập trình, chúng ta thường muốn lấy một thứ gì đó và mở rộng nó.

- Ví dụ: chúng tôi có một đối tượng `user` với các thuộc tính và phương thức của nó, và muốn đặt `admin` và `guest` như các biến thể được sửa đổi một chút của nó. Chúng tôi muốn sử dụng lại những gì chúng tôi có trong `user`, không sao chép hoặc thực hiện lại các phương pháp của nó, chỉ cần xây dựng một đối tượng mới trên đó.

`Prototypal inheritance` là một tính năng ngôn ngữ giúp làm điều đó.

## [[Prototype]]

- Trong JavaScript, các đối tượng có một thuộc tính ẩn đặc biệt `[[Prototype]]` (như được đặt tên trong đặc tả), là giá trị `null` hoặc tham chiếu đến đối tượng khác. Đối tượng đó được gọi là `"a prototype"`:

    ![Prototype](https://javascript.info/article/prototype-inheritance/object-prototype-empty.svg)

- Khi chúng tôi đọc một thuộc tính từ `object` và nó bị thiếu, JavaScript sẽ tự động lấy nó từ `prototype`. Trong lập trình, thứ như vậy được gọi là `Prototypal inheritance`.

- Thuộc tính `[[Prototype]]` là thuộc tính nội bộ và ẩn, nhưng có nhiều cách để thiết lập.

- Một trong số đó là sử dụng tên đặc biệt` __proto__`, như sau:

```
    let animal = {
        eats: true
    };

    let rabbit = {
        jumps: true
    };

    rabbit.__proto__ = animal; // sets rabbit.[[Prototype]] = animal
```

- Bây giờ, nếu chúng ta đọc một thuộc tính từ `rabbit` và nó bị thiếu, JavaScript sẽ tự động lấy thuộc tính đó từ `animal`.

```
    let animal = {
        eats: true
    };
    let rabbit = {
        jumps: true
    };

    rabbit.__proto__ = animal; // (*)

    // we can find both properties in rabbit now:
    alert( rabbit.eats ); // true (**)
    alert( rabbit.jumps ); // true
```

- Ở đây dòng (*) đặt `animal` là một nguyên mẫu của `rabbit`.

- Sau đó, khi `alert` cố gắng đọc thuộc tính `Rabbit.eats(**)`, thuộc tính này không có trong `rabbit`, vì vậy JavaScript đi theo tham chiếu `[[Prototype]]` và tìm thấy nó trong `animal` (nhìn từ dưới lên):

    ![animals](https://javascript.info/article/prototype-inheritance/proto-animal-rabbit.svg)

- Ở đây chúng ta có thể nói rằng "`animal` là nguyên mẫu của `rabbit`" hoặc "`rabbit` kế thừa nguyên mẫu từ `animal`".

- Vì vậy, nếu `annimal` có nhiều thuộc tính và phương pháp hữu ích, thì chúng sẽ tự động có sẵn trong `rabbit`. Các thuộc tính như vậy được gọi là `"kế thừa"`.

- Nếu chúng ta có một phương thức trên `animal`, nó có thể được gọi trên `rabbit`:

```
    let animal = {
        eats: true,
        walk() {
            alert("Animal walk");
        }
    };

    let rabbit = {
        jumps: true,
        __proto__: animal
    };

    // walk is taken from the prototype
    rabbit.walk(); // Animal walk
```

![animal](https://javascript.info/article/prototype-inheritance/proto-animal-rabbit-walk.svg)

- Chuỗi nguyên mẫu có thể dài hơn:

```
    let animal = {
        eats: true,
        walk() {
            alert("Animal walk");
        }
    };

    let rabbit = {
        jumps: true,
        __proto__: animal
    };

    let longEar = {
        earLength: 10,
        __proto__: rabbit
    };

    // walk is taken from the prototype chain
    longEar.walk(); // Animal walk
    alert(longEar.jumps); // true (from rabbit)
```

![animals](https://javascript.info/article/prototype-inheritance/proto-animal-rabbit-chain.svg)

- Bây giờ, nếu chúng ta đọc một cái gì đó từ `longEar` và nó bị thiếu, JavaScript sẽ tìm kiếm nó trong `rabbit` và sau đó là `animal`.

- Chỉ có hai hạn chế:
  - Các tham chiếu không thể đi trong vòng kết nối. JavaScript sẽ gặp lỗi nếu chúng ta cố gắng gán `__proto__` trong một vòng kết nối.
  - Giá trị của `__proto__` có thể là một đối tượng hoặc `null`. Các loại khác bị bỏ qua.

- Ngoài ra, nó có thể rõ ràng, nhưng vẫn có thể có chỉ một `[[Nguyên mẫu]]`. Một đối tượng không được kế thừa từ hai đối tượng khác.

## Writing doesn’t use prototype

- `prototype` chỉ được sử dụng để đọc các thuộc tính.
- Các thao tác ghi / xóa làm việc trực tiếp với đối tượng.

```
    let animal = {
        eats: true,
        walk() {
            /* this method won't be used by rabbit */
        }
    };

    let rabbit = {
        __proto__: animal
    };

    rabbit.walk = function() {
        alert("Rabbit! Bounce-bounce!");
    };

    rabbit.walk(); // Rabbit! Bounce-bounce!
```

- Từ bây giờ, lệnh gọi `Rabbit.walk()` tìm thấy phương thức ngay lập tức trong đối tượng và thực thi nó mà không cần sử dụng `prototype`:
![prototype](https://javascript.info/article/prototype-inheritance/proto-animal-rabbit-walk-2.svg)

- Thuộc tính trình truy cập là một ngoại lệ, vì việc gán được xử lý bởi một hàm `setter`. Vì vậy, việc ghi vào một thuộc tính như vậy thực sự cũng giống như việc gọi một hàm.

- ví dụ:

```
    let user = {
        name: "John",
        surname: "Smith",

        set fullName(value) {
            [this.name, this.surname] = value.split(" ");
        },

        get fullName() {
            return `${this.name} ${this.surname}`;
        }
    };

    let admin = {
        __proto__: user,
        isAdmin: true
    };

    alert(admin.fullName); // John Smith (*)

    // setter triggers!
    admin.fullName = "Alice Cooper"; // (**)

    alert(admin.fullName); // Alice Cooper, state of admin modified
    alert(user.fullName); // John Smith, state of user protected
```

- Ở đây trong dòng `(*)` thuộc tính `admin.fullName` có một `getter` trong `user` nguyên mẫu, vì vậy nó được gọi. Và trong dòng `(**)`, thuộc tính có một `setter` trong nguyên mẫu, vì vậy nó được gọi.


## The value of “this”

- Một câu hỏi thú vị có thể nảy sinh trong ví dụ trên: giá trị của `this` bên trong `set fullName(value)` là bao nhiêu? Các thuộc tính `this.name` và `this.surname` được viết ở đâu: `user` hoặc `admin`?

- Câu trả lời rất đơn giản: `this` hoàn toàn không bị ảnh hưởng bởi `prototype`.

- **Bất kể phương thức được tìm thấy ở đâu: trong một đối tượng hay `prototype` của nó. Trong một cuộc gọi phương thức, `this` luôn là đối tượng trước dấu chấm.**

- Vì vậy, `setter` gọi `admin.fullName` = sử dụng `admin` như `this`, không phải `user`.

- Đó thực sự là một điều cực kỳ quan trọng, bởi vì chúng ta có thể có một đối tượng lớn với nhiều phương thức và có các đối tượng kế thừa từ nó. Và khi các đối tượng kế thừa chạy các phương thức kế thừa, chúng sẽ chỉ sửa đổi trạng thái của chính chúng, không phải trạng thái của đối tượng lớn.

- Ví dụ, ở đây `animal` đại diện cho “phương thức lưu trữ” và `rabbit` sử dụng nó.
- Gọi `Rabbit.sleep()` đặt `this.isSleeping` trên đối tượng `rabbit`:
  
```
    // animal has methods
    let animal = {
        walk() {
            if (!this.isSleeping) {
            alert(`I walk`);
            }
        },
        sleep() {
            this.isSleeping = true;
        }
    };

    let rabbit = {
        name: "White Rabbit",
        __proto__: animal
    };

    // modifies rabbit.isSleeping
    rabbit.sleep();

    alert(rabbit.isSleeping); // true
    alert(animal.isSleeping); // undefined (no such property in the prototype)
```

![prototype](https://javascript.info/article/prototype-inheritance/proto-animal-rabbit-walk-3.svg)

- Nếu chúng ta có các đối tượng khác, như `bird`, `snake`, v.v., kế thừa từ `animal`, chúng cũng sẽ có quyền truy cập vào các phương thức của `animal`. Nhưng `this` trong mỗi lần gọi phương thức sẽ là đối tượng tương ứng, được đánh giá tại thời điểm gọi (trước dấu chấm), không phải `animal`. Vì vậy, khi chúng ta ghi dữ liệu vào `this`, nó sẽ được lưu trữ vào các đối tượng này.

- Kết quả là, các phương thức được chia sẻ, nhưng trạng thái đối tượng thì không.


## for…in loop

- Vòng lặp `for..in` cũng lặp lại các thuộc tính được kế thừa.
- ví dụ:
    ```
        let animal = {
            eats: true
        };

        let rabbit = {
            jumps: true,
            __proto__: animal
        };

        // Object.keys only returns own keys
        alert(Object.keys(rabbit)); // jumps

        // for..in loops over both own and inherited keys
        for(let prop in rabbit) alert(prop); // jumps, then eats
    ```

- Nếu đó không phải là những gì chúng tôi muốn và chúng tôi muốn loại trừ các thuộc tính được kế thừa, thì có một phương thức được tích hợp sẵn `obj.hasOwnProperty(key)`: nó trả về `true` nếu obj có thuộc tính riêng (không được kế thừa) có tên là `key`.

- Vì vậy, chúng tôi có thể lọc ra các thuộc tính kế thừa (hoặc làm điều gì đó khác với chúng):

```
    let animal = {
        eats: true
    };

    let rabbit = {
        jumps: true,
        __proto__: animal
    };

    for(let prop in rabbit) {
        let isOwn = rabbit.hasOwnProperty(prop);

        if (isOwn) {
            alert(`Our: ${prop}`); // Our: jumps
        } else {
            alert(`Inherited: ${prop}`); // Inherited: eats
        }
    }
```

- Ở đây chúng ta có chuỗi kế thừa sau: `rabbit` kế thừa từ `animal`, chuỗi kế thừa từ `Object.prototype` (vì `animal` là một đối tượng theo nghĩa đen `{...}`, vì vậy theo mặc định), và sau đó `null` ở trên nó:

![prototype](https://javascript.info/article/prototype-inheritance/rabbit-animal-object.svg)

- Lưu ý, có một điều thú vị. Phương thức `Rabbit.hasOwnProperty` đến từ đâu? Chúng tôi đã không xác định nó. Nhìn vào chuỗi, chúng ta có thể thấy rằng phương thức được cung cấp bởi `Object.prototype.hasOwnProperty`. Nói cách khác, nó được kế thừa.

- … Nhưng tại sao `hasOwnProperty` không xuất hiện trong vòng lặp `for..in` giống như `eats` và `jumps`, nếu `for..in` liệt kê các thuộc tính được kế thừa?

- Câu trả lời rất đơn giản: nó không thể liệt kê được. Cũng giống như tất cả các thuộc tính khác của `Object.prototype`, nó có cờ `enumerable:false`. Và `for..in` chỉ liệt kê các thuộc tính có thể liệt kê được. Đó là lý do tại sao nó và phần còn lại của các thuộc tính `Object.prototype` không được liệt kê.

**Hầu hết tất cả các phương thức nhận `key/value` khác đều bỏ qua các thuộc tính kế thừa**

- Hầu như tất cả các phương thức nhận `key/value`, chẳng hạn như` Object.keys`, `Object.values`, v.v. đều bỏ qua các thuộc tính kế thừa.
- Chúng chỉ hoạt động trên chính đối tượng. Các thuộc tính từ nguyên mẫu không được tính đến.



## Summary

- Trong JavaScript, tất cả các đối tượng đều có thuộc tính `[[Prototype]]` ẩn, là một đối tượng khác hoặc là `null`.
- Chúng tôi có thể sử dụng `obj.__ proto__` để truy cập nó.
- Đối tượng được tham chiếu bởi `[[Prototype]]` được gọi là “nguyên mẫu”.
- Nếu chúng ta muốn đọc một thuộc tính của `obj` hoặc gọi một phương thức và nó không tồn tại, thì JavaScript sẽ cố gắng tìm nó trong nguyên mẫu.
- Các hoạt động `Write/delete` hoạt động trực tiếp trên đối tượng, chúng không sử dụng nguyên mẫu (giả sử đó là một thuộc tính dữ liệu, không phải một bộ thiết lập).
- Nếu chúng ta gọi `obj.method()` và `method` được lấy từ nguyên mẫu, thì điều này vẫn tham chiếu tới `obj`. Vì vậy, các phương thức luôn hoạt động với đối tượng hiện tại ngay cả khi chúng được kế thừa.
- Vòng lặp `for..in` lặp lại cả các thuộc tính của chính nó và các thuộc tính kế thừa của nó. Tất cả các phương thức lấy `key/value` khác chỉ hoạt động trên chính đối tượng.