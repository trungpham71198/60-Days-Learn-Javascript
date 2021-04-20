# 7 Questions on JavaScript Closures.

- Mọi nhà phát triển JavaScript phải biết `Closure` là gì. Trong một cuộc phỏng vấn viết mã JavaScript, rất có thể bạn sẽ được hỏi về khái niệm `Closure`.


## Questions 1: Closures raise your hand

- Hãy chọn câu trả lời đúng:

```markdown
    let countClicks = 0;
    button.addEventListener('click', function clickHandler() {
        countClicks++;
    });
```
```markdown
    const result = (function immediate(number) {
        const message = `number is: ${number}`;
        return message;
    })(100);
```
```markdown
    setTimeout(function delayedReload() {
        location.reload();
    }, 1000);
```

- Hàm nào trong số 3 hàm này truy cập các biến phạm vi bên ngoài?

1. `clickHandler` truy cập biến `countClicks` từ phạm vi bên ngoài.
2. `immediate` không truy cập bất kỳ biến nào từ phạm vi bên ngoài.
3. `delayReload` truy cập vị trí biến toàn cục từ phạm vi toàn cầu (còn gọi là phạm vi ngoài cùng).


## Questions 2: Lost in parameters

- Điều gì sẽ ghi vào bảng điều khiển đoạn mã sau:

```markdown
    (function immediateA(a) {
        return (function immediateB(b) {
            console.log(a); // What is logged?
        })(1);
    })(0);
```

- 0 is logged to the console.

- `immediateA` được gọi với đối số `0`, do đó một tham số là `0`.

- Hàm `immediateB`, được lồng vào hàm `immediateA`, là một bao đóng để nắm bắt một biến từ phạm vi bên ngoài `immediateA`, trong đó `a` là `0`. Vì vậy, `console.log(a)` ghi lại `0`.




## Questions 3: Who’s who

```markdown
    let count = 0;
    (function immediate() {
        if (count === 0) {
            let count = 1;
            console.log(count); // What is logged?
        }
        console.log(count); // What is logged?
    })();
```

- 1 and 0 is logged to the console.




## Questions 4: Tricky closure

```markdown
    for (var i = 0; i < 3; i++) {
        setTimeout(function log() {
            console.log(i); // What is logged?
        }, 1000);
    }
```

- 3, 3, 3 is logged to console.

- Đoạn mã thực thi trong 2 giai đoạn.

  **Phase 1**

  1. `for()` lặp 3 lần. Trong mỗi lần lặp lại, một hàm `log()` mới được tạo, ghi lại biến `i`. `setTimout()` lên lịch `log()` để thực thi sau `1000ms`.


  2. Khi vòng lặp `for()` hoàn thành, biến `i` có giá trị `3`.


  **Phase 2**

  Giai đoạn thứ hai xảy ra sau `1000ms`:

  - `setTimeout()` thực thi các hàm `log()` đã lên lịch. `log()` đọc giá trị hiện tại của biến `i`, là `3` và ghi vào bảng điều khiển `3`.

  => Đó là lý do tại sao giá trị là 3, 3, 3.

  vậy nếu muốn hàm `log()` chạy đúng thì chúng ta sẽ viết như thế nào???

  ```markdown
        for (let i = 0; i < 3; i++) {
            setTimeout(function log() {
                console.log(i); // 0, 1, 2
            }, 1000);
        }
  ```




## Questions 5: Right or wrong message

```markdown
    function createIncrement() {
        let count = 0;
        function increment() { 
            count++;
        }

        let message = `Count is ${count}`;
        function log() {
            console.log(message);
        }
        
        return [increment, log];
    }

    const [increment, log] = createIncrement();
    increment(); 
    increment(); 
    increment(); 
    log(); // What is logged?
```

- 'Count is 0' is logged to console.

- Hàm `increment()` đã được gọi 3 lần, hiệu quả tăng số đếm đến giá trị `3`.

- biến `message` tồn tại trong phạm vi của hàm `createIncrement()`. Giá trị ban đầu của nó là `'Count là 0'`. Tuy nhiên, ngay cả khi biến đếm đã được tăng lên một vài lần, thì biến thông báo luôn giữ `'Count là 0'`.

- Hàm `log()` là một `closure` để nắm bắt biến `message` từ phạm vi `createIncrement()`. `console.log(message)` ghi nhật ký `'Count là 0'` vào bảng điều khiển.

Vậy làm sao chúng ta có thể lấy số đếm `count` đã được thay đổi????

- chúng ta sẽ làm như sau:

```markdown
    function createIncrement() {
        let count = 0;
        function increment() { 
            count++;
        }

        function log() {
            let message = `Count is ${count}`;
            console.log(message);
        }
        
        return [increment, log];
    }

    const [increment, log] = createIncrement();
    increment(); 
    increment(); 
    increment(); 
    log(); // Count is 3
```




## Questions 6: Restore encapsulation

- Hàm `createStack()` sau đây tạo các thể hiện của cấu trúc dữ liệu ngăn xếp:

```markdown
    function createStack() {
        return {
            items: [],
            push(item) {
            this.items.push(item);
            },
            pop() {
            return this.items.pop();
            }
        };
    }

    const stack = createStack();
    stack.push(10);
    stack.push(5);
    stack.pop(); // => 5

    stack.items; // => [10]
    stack.items = [10, 100, 1000]; // Encapsulation broken!
```

- `Stack` hoạt động như mong đợi, nhưng có một vấn đề nhỏ. Bất kỳ ai cũng có thể sửa đổi mảng trực tiếp vì thuộc tính `stack.items` được hiển thị.

- Đó là một vấn đề vì nó phá vỡ tính đóng gói của `stack`: chỉ các phương thức `push()` và `pop()` mới được công khai, còn `stack.items` hoặc bất kỳ chi tiết nào khác sẽ không thể truy cập được.

**Đây là cách cấu trúc lại có thể có của createStack():**

```markdown
    function createStack() {
        const items = [];
        return {
            push(item) {
            items.push(item);
            },
            pop() {
            return items.pop();
            }
        };
    }

    const stack = createStack();
    stack.push(10);
    stack.push(5);
    stack.pop(); // => 5

    stack.items; // => undefined
```

- các `items` đã được chuyển đến một biến bên trong phạm vi `createStack()`.

- Nhờ sự thay đổi này, từ bên ngoài phạm vi `createStack()`, không có cách nào để truy cập hoặc sửa đổi mảng `items`. các m`items` bây giờ là một biến riêng và ngăn xếp được đóng gói: chỉ có phương thức `push()` và `pop()` là công khai.

- phương thức `push()` và `pop()`, là các `closure`, nắm bắt biến các `items` từ phạm vi hàm `createStack()`.




## Questions 7: Smart multiplication

- Viết một hàm `multiply()` bội 2 số:

```markdown
    function multiply(num1, num2) {
        // Write your code here...
    }
```

- Nếu  `multiply(num1, num2)` được gọi với 2 đối số, nó sẽ trả về phép nhân của 2 đối số.

- Nhưng nếu được gọi với 1 đối số `const anotherFunc = multiply(num1)`, hàm sẽ trả về một hàm khác. Hàm trả về khi được gọi là `anotherFunc(num2)` thực hiện phép nhân `num1 * num2`.

```markdown
    kết quả mong muốn: 

    multiply(4, 5); // => 20
    multiply(3, 3); // => 9

    const double = multiply(2);
    double(5);  // => 10
    double(11); // => 22
```

- Dưới đây là cách triển khai có thể có của hàm `multiply()`:

```markdown
    function multiply(number1, number2) {
        return function doMultiply(number2) {
            return number1 * number2;
        };
    }
```