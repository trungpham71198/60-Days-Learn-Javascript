# The "new Function" syntax

Còn một cách nữa để tạo một hàm. Nó hiếm khi được sử dụng, nhưng đôi khi không có giải pháp thay thế.

## Cú pháp.

``` let func = new Function ([arg1, arg2, ...argN], functionBody);```

- Hàm được tạo với các đối số `arg1 ...argN` và `functionBody` đã cho.

- Sẽ dễ hiểu hơn bằng cách xem một ví dụ. Đây là một hàm có hai đối số:

```
    let sum = new Function('a', 'b', 'return a + b');

    alert( sum(1, 2) ); // 3
```

- Và đây là một hàm không có đối số, chỉ có thân hàm:
```
    let sayHi = new Function('alert("Hello")');

    sayHi(); // Hello
```

- Sự khác biệt chính so với các cách khác mà chúng tôi đã thấy là hàm được tạo theo nghĩa đen từ một chuỗi, được chuyển vào thời gian chạy.

- Tất cả các khai báo trước đây đều yêu cầu chúng ta, những người lập trình, viết mã hàm trong script.

- Nhưng `new Function` cho phép biến bất kỳ chuỗi nào thành một hàm. Ví dụ: chúng ta có thể nhận một hàm mới từ một máy chủ và sau đó thực thi nó:

```
    let str = ... receive the code from a server dynamically ...

    let func = new Function(str);
    func();
```

- Nó được sử dụng trong các trường hợp rất cụ thể, như khi chúng tôi nhận mã từ máy chủ hoặc để biên dịch động một hàm từ mẫu, trong các ứng dụng web phức tạp.


## Closure
- Thông thường, một hàm ghi nhớ nơi nó được sinh ra trong thuộc tính đặc biệt `[[Environment]]`. Nó tham chiếu đến Môi trường Lexical từ nơi nó được tạo ra.

- Nhưng khi một hàm được tạo bằng `new Function`, `[[Environment]]` của nó được đặt để tham chiếu không phải là Môi trường Lexical hiện tại mà là toàn cục.

- Vì vậy, hàm như vậy không có quyền truy cập vào các biến bên ngoài, chỉ truy cập vào các biến toàn cục.

```
    function getFunc() {
    let value = "test";

    let func = new Function('alert(value)');

    return func;
    }

    getFunc()(); // error: value is not defined
```
- So sánh nó với hành vi thông thường:

```
    function getFunc() {
    let value = "test";

    let func = function() { alert(value); };

    return func;
    }

    getFunc()(); // "test", from the Lexical Environment of getFunc
```

- Tính năng đặc biệt này của `new Function` trông lạ, nhưng lại rất hữu ích trong thực tế.

- Hãy tưởng tượng rằng chúng ta phải tạo một hàm từ một chuỗi. `code` của hàm đó không được biết tại thời điểm viết tập lệnh (đó là lý do tại sao chúng tôi không sử dụng các hàm thông thường), nhưng sẽ được biết trong quá trình thực thi. Chúng tôi có thể nhận nó từ máy chủ hoặc từ một nguồn khác.

- `new Function` cần phải tương tác với tập lệnh chính.