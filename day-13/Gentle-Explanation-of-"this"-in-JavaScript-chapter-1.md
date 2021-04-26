# Gentle Explanation of "this" in JavaScript (chapter 1).

- Từ khóa `this` đã là một bí ẩn đối với tôi trong một thời gian dài.

- Đây là ngữ cảnh của một lệnh gọi hàm (còn gọi là thực thi). Có 4 kiểu gọi hàm:
  1. function invocation: alert('Hello World!')
  2. method invocation: console.log('Hello World!')
  3. constructor invocation: new RegExp('\\d')
  4. indirect invocation: alert.call(undefined, 'Hello World!')

- Mỗi loại lệnh gọi xác định ngữ cảnh theo cách của nó, vì vậy `this` hoạt động khác với mong đợi của nhà phát triển.
- Hơn nữa chế độ nghiêm ngặt cũng ảnh hưởng đến bối cảnh thực thi.

```
  Chìa khóa để hiểu từ khóa "This" là có một cái nhìn rõ ràng về lệnh gọi hàm và cách nó tác động đến ngữ cảnh.
```

- Bài viết này tập trung vào giải thích về lời gọi, cách mà lời gọi hàm ảnh hưởng đến `this` và giải thích những cạm bẫy phổ biến khi xác định giá trị của `this`.

- Trước khi bắt đầu, hãy làm quen với một số thuật ngữ:
  - **Invocation** của một hàm hiểu đơn giản là gọi hàm. vd: parseInt('15'),...
  - **Context** của một `Invocation` là giá trị của `this` trong thân hàm.
  - **Scope** của một hàm là tập hợp các biến và hàm có thể truy cập được trong hàm đó.



## 1. Function invocation (gọi hàm).

- **Function invocation** được thực hiện khi một biểu thức đánh giá đối tượng hàm được theo sau bởi dấu ngoặc đơn mở `(` và dấu ngoặc đơn đóng `)`.
- ví dụ:
```
  function hello(name) {
    return 'Hello ' + name + '!';
  }

  // Function invocation
  const message = hello('World');
```

=> `hello('World')` là một `function invocation`.

- Biểu thức gọi hàm không được là một trình truy cập thuộc tính `obj.myFunc()`, mà tạo ra một lệnh gọi phương thức.(method invocation)
- Ví dụ `[1,5].join(',')` không phải là một lời `function invocation`, mà là một `method invocation`. Hãy nhớ sự phân biệt giữa chúng.

- Một ví dụ nâng cao hơn là IIFE (biểu thức hàm được gọi ngay lập tức):

```
  // IIFE
  const message = (function(name) {
    return 'Hello ' + name + '!';
  })('World');
```


## 1.1 This in a function invocation

  ``` "This" là đối tượng toàn cục trong một lệnh gọi hàm. ```

- Đối tượng toàn cục được xác định bởi môi trường thực thi. Trong trình duyệt, đối tượng toàn cục là đối tượng cửa sổ.

![image](https://dmitripavlutin.com/static/7035b35b8d8dce31376d9839065e58f7/d3e6f/2-1.webp)

- Trong một `function invocation`, ngữ cảnh thực thi là đối tượng toàn cục.

- Hãy kiểm tra ngữ cảnh trong hàm sau:
  
```
  function sum(a, b) {
    console.log(this === window); // => true
    this.myNumber = 20; // add 'myNumber' property to global object
    return a + b;
  }
  // sum() is invoked as a function
  // this in sum() is a global object (window)
  sum(15, 16);     // => 31
  window.myNumber; // => 20
```

- Tại thời điểm `sum(15, 16)` được gọi, JavaScript tự động đặt đối tượng `this` làm đối tượng toàn cục (cửa sổ trong trình duyệt).

- Khi `this` được sử dụng bên ngoài bất kỳ phạm vi chức năng nào (phạm vi trên cùng: ngữ cảnh thực thi toàn cục), nó cũng tương đương với đối tượng toàn cục:

```
  console.log(this === window); // => true

  this.myString = 'Hello World!';
  console.log(window.myString); // => 'Hello World!'
```


## 1.2 This in a function invocation, strict mode.

  ```"This" là undefined trong một lệnh gọi hàm ở chế độ nghiêm ngặt```

![image](https://dmitripavlutin.com/static/2a757bd246d0f4e60395273d0aeeb767/d43fa/3-1.webp)

```
  function multiply(a, b) {
    'use strict'; // enable the strict mode
    console.log(this === undefined); // => true
    return a * b;
  }
  // multiply() function invocation with strict mode enabled
  // this in multiply() is undefined
  multiply(2, 5); // => 10
```


## 1.3 Pitfall: this in an inner function.

- Một cái bẫy phổ biến với việc gọi hàm là nghĩ rằng `this` giống với hàm bên trong cũng như hàm bên ngoài.

- Ngữ cảnh của hàm bên trong (ngoại trừ hàm mũi tên) chỉ phụ thuộc vào loại lệnh gọi của riêng nó, chứ không phụ thuộc vào ngữ cảnh của hàm bên ngoài.

- Để làm cho `this` có giá trị mong muốn, hãy sửa đổi ngữ cảnh của hàm bên trong bằng cách gọi gián tiếp(indirect invocation) (sử dụng `.call()` hoặc `.apply()`) hoặc tạo một hàm liên kết (sử dụng `.bind()`).

```
  const numbers = {
    numberA: 5,
    numberB: 10,
    sum: function() {
      console.log(this === numbers); // => true

      function calculate() {
        console.log(this === numbers); // => true
        return this.numberA + this.numberB;
      }

      // use .call() method to modify the context
      return calculate.call(this);
    }
  };
  numbers.sum(); // => 15
```



## 2. Method invocation

- Phương thức là một hàm được lưu trữ trong một thuộc tính của một đối tượng. Ví dụ:

  ```
    const myObject = {
      // helloMethod is a method
      helloMethod: function() {
        return 'Hello World!';
      }
    };
    const message = myObject.helloMethod();
  ```

- `helloMethod` là một phương thức của `myObject`. Sử dụng công cụ truy cập thuộc tính `myObject.helloMethod` để truy cập phương thức.

- **Method invocation** được thực hiện khi một biểu thức ở dạng trình truy cập thuộc tính đánh giá đối tượng hàm được theo sau bởi một dấu ngoặc đơn mở `(` và một dấu ngoặc đơn đóng `)`.

- `myObject.helloMethod()` là một lệnh gọi phương thức của `helloMethod` trên đối tượng `myObject`.

- Các ví dụ khác về lời gọi phương thức là: `[1, 2].join(',')` hoặc `/\s/.test('beautiful world ')`.

- Hiểu được sự khác biệt giữa lệnh gọi hàm và lệnh gọi phương thức là rất quan trọng!

- Phương thức gọi hàm yêu cầu một biểu mẫu truy cập thuộc tính để gọi hàm (`obj.myFunc()`hoặc `obj['myFunc']()`), trong khi lệnh gọi hàm thì không (`myFunc()`).

```
  const words = ['Hello', 'World'];
  words.join(', ');   // method invocation

  const obj = {
    myMethod() {
      return new Date().toString();
    }
  };
  obj.myMethod();     // method invocation

  const func = obj.myMethod;
  func();             // function invocation
  parseFloat('16.6'); // function invocation
  isNaN(0);           // function invocation
```


## 2.1 This in a method invocation

  ```"This" là đối tượng sở hữu phương thức trong một lệnh gọi phương thức```

- Khi gọi một phương thức trên một đối tượng, `this` là đối tượng đó.

![image](https://dmitripavlutin.com/static/baf7c3a9ba2fa5c631ea32b940d95f95/248d9/4-1.webp)

- ví dụ:
  
  ```
    const calc = {
      num: 0,
      increment() {
        console.log(this === calc); // => true
        this.num += 1;
        return this.num;
      }
    };

    // method invocation. this is calc
    calc.increment(); // => 1
    calc.increment(); // => 2
  ```

- Gọi `calc.increment()` làm cho ngữ cảnh của hàm tăng trở thành đối tượng `calc`. Vì vậy, sử dụng `this.num` để tăng thuộc tính `number` hoạt động tốt.

- Hãy theo dõi một trường hợp khác. Một đối tượng JavaScript kế thừa một phương thức từ nguyên mẫu của nó. Khi phương thức kế thừa được gọi trên đối tượng, ngữ cảnh của lời gọi vẫn là chính đối tượng:

```
  const myDog = Object.create({
    sayName() {
      console.log(this === myDog); // => true
      return this.name;
    }
  });

  myDog.name = 'Milo';
  // method invocation. this is myDog
  myDog.sayName(); // => 'Milo'
```

- `Object.create()` tạo một đối tượng mới `myDog` và đặt nguyên mẫu của nó từ đối số đầu tiên. Đối tượng `myDog` kế thừa phương thức `sayName`.

- Khi `myDog.sayName()` được thực thi, `myDog` là ngữ cảnh của lệnh gọi.

- Đối với `Class` cũng vậy:

```
  class Planet {
    constructor(name) {
      this.name = name;
    }

    getName() {
      console.log(this === earth); // => true
      return this.name;
    }
  }

  const earth = new Planet('Earth');
  // method invocation. the context is earth
  earth.getName(); // => 'Earth'
```


## 2.2 Pitfall: separating method from its object

- Một phương thức có thể được trích xuất từ ​​một đối tượng thành một biến riêng biệt `const alone = myObj.myMethod`. Khi phương thức được gọi một mình `alone()`, tách rời khỏi đối tượng ban đầu, bạn có thể nghĩ rằng đây là đối tượng `myObject` mà phương thức được định nghĩa.

- Chính xác nếu phương thức được gọi mà không có đối tượng, thì một lệnh gọi hàm sẽ xảy ra, trong đó đây là cửa sổ đối tượng toàn cục hoặc không xác định trong chế độ nghiêm ngặt.

- Một hàm liên kết `const alone = myObj.myMethod.bind(myObj)` (sử dụng `.bind()`) sửa ngữ cảnh bằng cách ràng buộc đối tượng sở hữu phương thức `this`.

- ví dụ: định nghĩa hàm tạo `Pet` và tạo một thể hiện của nó: `myCat`. Sau đó `setTimout()` sau 1 giây ghi lại thông tin đối tượng `myCat`:

```
  function Pet(type, legs) {
    this.type = type;
    this.legs = legs;

    this.logInfo = function() {
      console.log(this === myCat); // => false
      console.log(`The ${this.type} has ${this.legs} legs`);
    }
  }

  const myCat = new Pet('Cat', 4);
  // logs "The undefined has undefined legs"
  // or throws a TypeError in strict mode
  setTimeout(myCat.logInfo, 1000);
```

- Bạn có thể nghĩ rằng `setTimeout(myCat.logInfo, 1000)` sẽ gọi `myCat.logInfo()`, sẽ ghi lại thông tin về đối tượng `myCat`.

- Thật không may, phương thức bị tách khỏi đối tượng của nó khi được truyền dưới dạng tham số: `setTimout(myCat.logInfo)`. Các trường hợp sau là tương đương:

```
  setTimout(myCat.logInfo);

  // is equivalent to:

  const extractedLogInfo = myCat.logInfo;
  setTimout(extractedLogInfo);
```

- Khi `logInfo` được phân tách được gọi dưới dạng một hàm, đây là đối tượng toàn cục hoặc `undefined` trong chế độ nghiêm ngặt (nhưng không phải đối tượng `myCat`). Vì vậy thông tin đối tượng đăng nhập không chính xác.

- Một hàm giới hạn với một đối tượng sử dụng phương thức `.bind()`. Nếu phương thức được phân tách được liên kết với đối tượng `myCat`, vấn đề ngữ cảnh sẽ được giải quyết:

```
  function Pet(type, legs) {
    this.type = type;
    this.legs = legs;

    this.logInfo = function() {
      console.log(this === myCat); // => true
      console.log(`The ${this.type} has ${this.legs} legs`);
    };
  }

  const myCat = new Pet('Cat', 4);

  // Create a bound function
  const boundLogInfo = myCat.logInfo.bind(myCat);
  // logs "The Cat has 4 legs"
  setTimeout(boundLogInfo, 1000);
```

- `myCat.logInfo.bind(myCat)` trả về một hàm mới thực thi chính xác như `logInfo`, nhưng có hàm này là `myCat`, ngay cả trong một lệnh gọi hàm.

- Một giải pháp thay thế là xác định phương thức `logInfo()` như một hàm mũi tên, liên kết từ vựng này:

```
  function Pet(type, legs) {
    this.type = type;
    this.legs = legs;

    this.logInfo = () => {
      console.log(this === myCat); // => true
      console.log(`The ${this.type} has ${this.legs} legs`);
    };
  }

  const myCat = new Pet('Cat', 4);
  // logs "The Cat has 4 legs"
  setTimeout(myCat.logInfo, 1000);
```

- Nếu bạn muốn sử dụng các lớp và liên kết nó với cá thể lớp trong phương thức của mình, hãy sử dụng hàm mũi tên làm thuộc tính lớp:

```
  class Pet {
    constructor(type, legs) {
      this.type = type;
      this.legs = legs;
    }

    logInfo = () => {
      console.log(this === myCat); // => true
      console.log(`The ${this.type} has ${this.legs} legs`);
    }
  }

  const myCat = new Pet('Cat', 4);
  // logs "The Cat has 4 legs"
  setTimeout(myCat.logInfo, 1000);
```