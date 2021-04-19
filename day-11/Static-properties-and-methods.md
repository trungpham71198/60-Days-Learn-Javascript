# Static properties and methods

- Chúng ta cũng có thể gán một phương thức cho chính hàm `Class` chứ không phải cho `"prototype"` của nó. Các phương thức như vậy được gọi là `static`.

- Trong một lớp, chúng được thêm vào trước bởi từ khóa `static`, như thế này:

```
    class User {
        static staticMethod() {
            alert(this === User);
        }
    }

    User.staticMethod(); // true
```

- Điều đó thực sự hoạt động tương tự như việc chỉ định trực tiếp nó như một thuộc tính:

```
    class User { }

    User.staticMethod = function() {
        alert(this === User);
    };

    User.staticMethod(); // true
```

- Giá trị của `this` trong `User.staticMethod()` là chính hàm tạo `Class User` (quy tắc “đối tượng trước dấu chấm”).

- Thông thường, các phương thức tĩnh được sử dụng để triển khai các hàm thuộc về lớp, nhưng không được sử dụng cho bất kỳ đối tượng cụ thể nào của nó.

- Ví dụ: chúng ta có các đối tượng `Article` và cần một hàm để so sánh chúng. Một giải pháp tự nhiên sẽ là thêm phương thức `Article.compare`, như sau:

```
    class Article {
        constructor(title, date) {
            this.title = title;
            this.date = date;
        }

        static compare(articleA, articleB) {
            return articleA.date - articleB.date;
        }
    }

    // usage
    let articles = [
        new Article("HTML", new Date(2019, 1, 1)),
        new Article("CSS", new Date(2019, 0, 1)),
        new Article("JavaScript", new Date(2019, 11, 1))
    ];

    articles.sort(Article.compare);

    alert( articles[0].title ); // CSS
```

- Ở đây `Article.compare` là viết tắt của "phía trên" các bài báo, như một phương tiện để so sánh chúng. Nó không phải là một phương pháp của một bài báo, mà là của cả lớp.

- Một ví dụ khác là một phương pháp được gọi là `"factory"`. Hãy tưởng tượng, chúng ta cần một số cách để tạo một bài báo:
  1. Tạo theo các tham số nhất định (`title`, `date`, v.v.).
  2. Tạo một bài báo trống với ngày hôm nay.

- Cách đầu tiên có thể được thực hiện bởi phương thức khởi tạo. Và đối với cái thứ hai, chúng ta có thể tạo một phương thức tĩnh của lớp.

- Giống như `Article.createTodays()` tại đây:

```
    class Article {
        constructor(title, date) {
            this.title = title;
            this.date = date;
        }

        static createTodays() {
            // remember, this = Article
            return new this("Today's digest", new Date());
        }
    }

    let article = Article.createTodays();

    alert( article.title ); // Today's digest
```

- Các phương thức tĩnh cũng được sử dụng trong các lớp liên quan đến cơ sở dữ liệu để tìm kiếm / lưu / xóa các mục nhập khỏi cơ sở dữ liệu, như sau:

```
    // assuming Article is a special class for managing articles
    // static method to remove the article:
    Article.remove({id: 12345});
```

## Static properties

- Thuộc tính `static` cũng có thể, chúng trông giống như thuộc tính lớp thông thường, nhưng được thêm vào bởi `static`:

```
    class Article {
        static publisher = "Ilya Kantor";
    }

    alert( Article.publisher ); // Ilya Kantor
```

- Điều đó cũng giống như việc chuyển nhượng trực tiếp cho `Article`:

```
    Article.publisher = "Ilya Kantor";
```


## Inheritance of static properties and methods

- Ví dụ: `Animal.compare` và `Animal.planet` trong mã bên dưới được kế thừa và có thể truy cập được dưới dạng `Rabbit.compare` và `Rabbit.planet`:

```
    class Animal {
        static planet = "Earth";

        constructor(name, speed) {
            this.speed = speed;
            this.name = name;
        }

        run(speed = 0) {
            this.speed += speed;
            alert(`${this.name} runs with speed ${this.speed}.`);
        }

        static compare(animalA, animalB) {
            return animalA.speed - animalB.speed;
        }

    }

    // Inherit from Animal
    class Rabbit extends Animal {
        hide() {
            alert(`${this.name} hides!`);
        }
    }

    let rabbits = [
        new Rabbit("White Rabbit", 10),
        new Rabbit("Black Rabbit", 5)
    ];

    rabbits.sort(Rabbit.compare);

    rabbits[0].run(); // Black Rabbit runs with speed 5.

    alert(Rabbit.planet); // Earth
```

- Bây giờ khi chúng ta gọi `Rabbit.compare`, `Animal.compare` kế thừa sẽ được gọi.

- Làm thế nào nó hoạt động? Một lần nữa, sử dụng `Prototype`. Như bạn có thể đã đoán, `extends` cung cấp cho `Rabbit` tham chiếu `[[Prototype]]` đến `Animal`.

![image](https://javascript.info/article/static-properties-methods/animal-rabbit-static.svg)

- Vì vậy, `Rabbit extends Animal` tạo ra hai tham chiếu `[[Prototype]]`:

1. Hàm `Rabbit` kế thừa `Prototype` từ hàm `Animal`.
2. `Rabbit.prototype` kế thừa `Prototype1` từ `Animal.prototype`.

- Kết quả là, kế thừa hoạt động cho cả phương thức thông thường và phương thức tĩnh.

```
    class Animal {}
    class Rabbit extends Animal {}

    // for statics
    alert(Rabbit.__proto__ === Animal); // true

    // for regular methods
    alert(Rabbit.prototype.__proto__ === Animal.prototype); // true
```


## Tóm lược

- Các phương thức tĩnh được sử dụng cho chức năng thuộc về lớp “nói chung”. Nó không liên quan đến một phiên bản lớp cụ thể.

- Thuộc tính static được sử dụng khi chúng tôi muốn lưu trữ dữ liệu cấp lớp, cũng không bị ràng buộc với một phiên bản.

- cú pháp:

```
    class MyClass {
        static property = ...;

        static method() {
            ...
        }
    }
```

- Về mặt kỹ thuật, khai báo static cũng giống như việc gán cho chính lớp đó:

```
    MyClass.property = ...
    MyClass.method = ...
```

- Đối với lớp `B extends A`, nguyên mẫu của lớp B chính nó trỏ đến A: `B.[[Prototype]] = A`. Vì vậy, nếu một trường không được tìm thấy trong B, việc tìm kiếm sẽ tiếp tục trong A.

```
    class Rabbit extends Object {
        constructor(name) {
            super(); // need to call the parent constructor when inheriting
            this.name = name;
        }
    }

    let rabbit = new Rabbit("Rab");

    alert( rabbit.hasOwnProperty('name') ); // true
```