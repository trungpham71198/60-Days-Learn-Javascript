# Gentle Explanation of "this" in JavaScript (chapter 2).

## 4. Constructor invocation.

- **Constructor invocation** được thực hiện với từ khóa `new` được theo sau một hàm.

- ví dụ: `new Pet('cat', 4)`

```
    function Country(name, traveled) {
        this.name = name ? name : 'United Kingdom';
        this.traveled = Boolean(traveled); // transform to a boolean
    }

    Country.prototype.travel = function() {
        this.traveled = true;
    };

    // Constructor invocation
    const france = new Country('France', false);
    // Constructor invocation
    const unitedKingdom = new Country;

    france.travel(); // Travel to France
```

- `new Country('France', false)` là một lệnh gọi hàm tạo của hàm `Country`. Lệnh gọi này tạo ra một đối tượng mới, thuộc tính tên là `'France'`.

- Nếu hàm tạo được gọi mà không có đối số thì có thể bỏ qua cặp ngoặc đơn: `new Country`.

- Bắt đầu từ ECMAScript 2015, JavaScript cho phép xác định các hàm tạo bằng cú pháp `Class`:

```
    class City {
        constructor(name, traveled) {
            this.name = name;
            this.traveled = false;
        }

        travel() {
            this.traveled = true;
        }
    }

    // Constructor invocation
    const paris = new City('Paris', false);
    paris.travel();
```

- `new City('Paris')` là một lệnh gọi tạo hàm. Quá trình khởi tạo của đối tượng được xử lý bởi một phương thức đặc biệt trong `class`: `constructor`, có phương thức `this` là đối tượng mới được tạo.

- Vai trò của hàm khởi tạo là khởi tạo phiên bản. Một lệnh gọi tạo hàm sẽ tạo ra một đối tượng trống mới, đối tượng này kế thừa các thuộc tính từ nguyên mẫu của hàm tạo.

- Khi một trình truy cập thuộc tính `myObject.myFunction` được đặt trước bởi từ khóa `new`, JavaScript thực hiện một `constructor invocation`, nhưng không phải là một `method invocation`.


## 4.1. This in a constructor invocation

- `This` là đối tượng mới được tạo trong một `constructor invocation`

- Bối cảnh của một `constructor invocation` là đối tượng mới được tạo. Hàm khởi tạo, khởi tạo đối tượng với dữ liệu đến từ các đối số của hàm tạo, thiết lập giá trị ban đầu cho thuộc tính, đính kèm trình xử lý sự kiện, v.v.

![image](https://dmitripavlutin.com/static/a0f6062c2d9848de7c0b569aff17195b/589ff/5-1.webp)

- Hãy kiểm tra ngữ cảnh trong ví dụ sau:

```
    function Foo () {
        // this is fooInstance
        this.property = 'Default Value';
    }

    // Constructor invocation
    const fooInstance = new Foo();
    fooInstance.property; // => 'Default Value'
```

- `new Foo()` đang thực hiện một cuộc gọi phương thức khởi tạo trong đó ngữ cảnh là `fooInstance`. Bên trong Foo, đối tượng được khởi tạo: `this.property` được gán với một giá trị mặc định.

- Trường hợp tương tự cũng xảy ra khi sử dụng cú pháp lớp (có sẵn trong ES2015), chỉ việc khởi tạo xảy ra trong phương thức khởi tạo:

```
    class Bar {
        constructor() {
            // this is barInstance
            this.property = 'Default Value';
        }
    }

    // Constructor invocation
    const barInstance = new Bar();
    barInstance.property; // => 'Default Value'
```


## 4.2. Pitfall: forgetting about new

- Một số hàm JavaScript tạo ra các phiên bản không chỉ khi được gọi dưới dạng hàm tạo mà còn khi được gọi dưới dạng hàm. Ví dụ `RegExp`:

```
    const reg1 = new RegExp('\\w+');
    const reg2 = RegExp('\\w+');

    reg1 instanceof RegExp; // => true
    reg2 instanceof RegExp; // => true
    reg1.source === reg2.source; // => true
```

- Khi thực thi `new RegExp('\\ w +') `và `RegExp('\\ w +')`, JavaScript tạo các đối tượng biểu thức chính quy tương đương.

- Việc sử dụng một lệnh gọi hàm để tạo đối tượng là một vấn đề tiềm ẩn (ngoại trừ `factory pattern`), vì một số hàm tạo có thể bỏ qua logic để khởi tạo đối tượng khi thiếu từ khóa `new`.

- Ví dụ sau minh họa vấn đề:

```
    function Vehicle(type, wheelsCount) {
        this.type = type;
        this.wheelsCount = wheelsCount;
        return this;
    }

    // Function invocation
    const car = Vehicle('Car', 4);
    car.type; // => 'Car'
    car.wheelsCount // => 4
    car === window // => true
```

- `Car` là một hàm thiết lập các thuộc tính `type` và `wheelsCount` trên đối tượng ngữ cảnh. Khi thực thi `Vehicle('Car', 4)` một đối tượng `car` được trả về, có các thuộc tính chính xác: `car.type` là `'Car'` và `car.wheelsCount` là `4`.

- Bạn có thể nghĩ rằng nó hoạt động tốt để tạo và khởi tạo các đối tượng mới.

- Tuy nhiên, đây là đối tượng cửa sổ trong một lệnh gọi hàm, Do đó `Car('Xe', 4)` đặt thuộc tính trên đối tượng cửa sổ. Đây là một sai lầm. Một đối tượng mới không được tạo.

- Đảm bảo sử dụng toán tử mới trong các trường hợp khi cuộc gọi hàm tạo được mong đợi:

```
    function Vehicle(type, wheelsCount) {
        if (!(this instanceof Vehicle)) {
            throw Error('Error: Incorrect invocation');
        }

        this.type = type;
        this.wheelsCount = wheelsCount;
        return this;
    }

    // Constructor invocation
    const car = new Vehicle('Car', 4);
    car.type               // => 'Car'
    car.wheelsCount        // => 4
    car instanceof Vehicle // => true

    // Function invocation. Throws an error.
    const brokenCar = Vehicle('Broken Car', 3);
```


## 5. Indirect invocation

- Lệnh gọi gián tiếp được thực hiện khi một hàm được gọi bằng phương thức `myFun.call()` hoặc `myFun.apply()`.

```
    function sum(number1, number2) {
        return number1 + number2;
    }

    sum.call(undefined, 10, 2);    // => 12
    sum.apply(undefined, [10, 2]); // => 12
```


## 5.1. this in an indirect invocation

- `this` là đối số đầu tiên của `.call()` hoặc `.apply()` trong `indirect invocation`

![image](https://dmitripavlutin.com/static/d2feff578dba0d5e2fff9942cd069030/589ff/6-1.webp)

- Ví dụ sau đây cho thấy ngữ cảnh gọi gián tiếp:

```
    const rabbit = { name: 'White Rabbit' };

    function concatName(string) {
        console.log(this === rabbit); // => true
        return string + this.name;
    }

    // Indirect invocations
    concatName.call(rabbit, 'Hello ');  // => 'Hello White Rabbit'
    concatName.apply(rabbit, ['Bye ']); // => 'Bye White Rabbit'
```

- Một ví dụ thực tế khác là tạo cấu trúc phân cấp của các lớp trong ES5 để gọi hàm tạo cha:

```
    function Runner(name) {
        console.log(this instanceof Rabbit); // => true
        this.name = name;
    }

    function Rabbit(name, countLegs) {
        console.log(this instanceof Rabbit); // => true
        // Indirect invocation. Call parent constructor.
        Runner.call(this, name);
        this.countLegs = countLegs;
    }

    const myRabbit = new Rabbit('White Rabbit', 4);
    myRabbit; // { name: 'White Rabbit', countLegs: 4 }
```

- `Runner.call(this, name)` bên trong `Rabbit` thực hiện một cuộc gọi gián tiếp của hàm cha để khởi tạo đối tượng.



## 6. Arrow function

- Hàm mũi tên được thiết kế để khai báo hàm ở dạng ngắn hơn và liên kết `lexically` với ngữ cảnh.

```
    const hello = (name) => {
        return 'Hello ' + name;
    };

    hello('World'); // => 'Hello World'
    // Keep only even numbers
    [1, 2, 5, 6].filter(item => item % 2 === 0); // => [2, 6]
```

- Các hàm mũi tên có cú pháp nhẹ nhàng, không có hàm từ khóa dài dòng. Khi hàm arrow chỉ có 1 câu lệnh, bạn thậm chí có thể bỏ qua từ khóa return.

- Một hàm mũi tên là ẩn danh, nhưng tên của nó có thể được suy ra. Nó không có tên hàm từ vựng (sẽ hữu ích cho các trình xử lý sự kiện tách rời, đệ quy).

- Ngoài ra, nó không cung cấp đối tượng đối số, trái ngược với một hàm thông thường. Các đối số bị thiếu được sửa bằng cách sử dụng các tham số còn lại của ES2015:

```
    const sumArguments = (...args) => {
        console.log(typeof arguments); // => 'undefined'
        return args.reduce((result, item) => result + item);
    };

    sumArguments.name      // => ''
    sumArguments(5, 5, 6); // => 16
```


## 7.1. this in arrow function

- `this` là bối cảnh bao quanh nơi hàm mũi tên được xác định

- Hàm mũi tên không tạo ngữ cảnh thực thi của riêng nó mà lấy nó từ hàm bên ngoài nơi nó được xác định. Nói cách khác, hàm mũi tên giải quyết vấn đề `this` một cách từ vựng.

![image](https://dmitripavlutin.com/static/898df438becd3f7c1ad1193806d8dbc6/5722e/8-1.webp)

- Ví dụ sau cho thấy thuộc tính minh bạch ngữ cảnh:

```
    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }

        log() {
            console.log(this === myPoint); // => true
            setTimeout(() => {
                console.log(this === myPoint);      // => true
                console.log(this.x + ':' + this.y); // => '95:165'
            }, 1000);
        }
    }
    const myPoint = new Point(95, 165);
    myPoint.log();
```

- `setTimeout()` gọi hàm mũi tên có cùng ngữ cảnh (đối tượng `myPoint`) với phương thức `log()`. Như đã thấy, hàm mũi tên “kế thừa” ngữ cảnh từ hàm nơi nó được định nghĩa.

- Một hàm thông thường trong ví dụ này sẽ tạo ngữ cảnh riêng của nó (cửa sổ hoặc không xác định ở chế độ nghiêm ngặt). Vì vậy, để làm cho cùng một mã hoạt động chính xác với một biểu thức hàm, cần phải liên kết ngữ cảnh theo cách thủ công: `setTimeout(function () {...}.bind(this))`. Điều này là dài dòng và sử dụng hàm mũi tên là một giải pháp gọn gàng và ngắn gọn hơn.

- Nếu hàm mũi tên được xác định trong phạm vi trên cùng (bên ngoài bất kỳ hàm nào), ngữ cảnh luôn là đối tượng toàn cục (cửa sổ trong trình duyệt):

```
    const getContext = () => {
        console.log(this === window); // => true
        return this;
    };

    console.log(getContext() === window); // => true
```

- Một hàm mũi tên được ràng buộc với từ vựng này một lần và mãi mãi. điều này không thể được sửa đổi ngay cả khi sử dụng các phương pháp sửa đổi ngữ cảnh:

```
    const numbers = [1, 2];

    (function() { 
        const get = () => {
            console.log(this === numbers); // => true
            return this;
        };
        
        console.log(this === numbers); // => true
        get(); // => [1, 2]
        
        // Try to change arrow function context manually
        get.call([0]);  // => [1, 2]
        get.apply([0]); // => [1, 2]
        
        get.bind([0])(); // => [1, 2]
    }).call(numbers);
```

- Bất kể hàm mũi tên `get()` được gọi như thế nào, nó luôn giữ các `number` ngữ cảnh `lexical`. Cuộc gọi gián tiếp với ngữ cảnh khác `get.call([0])` hoặc. `get.apply([0])`, sự ràng buộc `get.bind([0])()` không có hiệu lực.

- Một hàm mũi tên không thể được sử dụng như một hàm tạo. Gọi nó như một phương thức khởi tạo mới `get()` ném ra một lỗi: TypeError: `get` không phải là một phương thức khởi tạo.


## 6.2. Pitfall: defining method with an arrow function

- Bạn có thể muốn sử dụng các hàm mũi tên để khai báo các phương thức trên một đối tượng. Khá hợp lý: khai báo của chúng khá ngắn so với biểu thức hàm: `(param) => {...}` thay vì hàm `(param) {..}`.

- Ví dụ này xác định một phương thức `format()` trên một lớp `Period` bằng cách sử dụng một hàm mũi tên:

```
    function Period (hours, minutes) { 
        this.hours = hours;
        this.minutes = minutes;
    }

    Period.prototype.format = () => {
        console.log(this === window); // => true
        return this.hours + ' hours and ' + this.minutes + ' minutes';
    };

    const walkPeriod = new Period(2, 30);
    walkPeriod.format(); // => 'undefined hours and undefined minutes'
```

- Vì `format` là một hàm mũi tên và được xác định trong ngữ cảnh chung (phạm vi trên cùng), nó có đây là đối tượng cửa sổ.

- Ngay cả khi định dạng được thực thi dưới dạng một phương thức trên đối tượng `walkPeriod.format()`, cửa sổ vẫn được giữ làm bối cảnh của lệnh gọi. Điều này xảy ra vì hàm mũi tên có ngữ cảnh tĩnh không thay đổi trên các loại lệnh gọi khác nhau.

- Biểu thức hàm giải quyết vấn đề vì một hàm thông thường thay đổi ngữ cảnh của nó tùy thuộc vào lệnh gọi:

```
    function Period (hours, minutes) {
        this.hours = hours;
        this.minutes = minutes;
    }

    Period.prototype.format = function() {
        console.log(this === walkPeriod); // => true
        return this.hours + ' hours and ' + this.minutes + ' minutes';
    };

    const walkPeriod = new Period(2, 30);
    walkPeriod.format(); // => '2 hours and 30 minutes'
```


## 7. Conclusion

- Bởi vì lời gọi hàm có tác động lớn nhất đến `this`, từ bây giờ đừng tự hỏi bản thân:
- Cái này được lấy từ đâu?
- nhưng hãy tự hỏi:
- Hàm `được gọi như thế nào` ?
- Đối với một hàm mũi tên, hãy tự hỏi:
- Đây là gì bên trong hàm bên ngoài, nơi hàm mũi tên được xác định?
