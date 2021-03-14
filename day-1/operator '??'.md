# **Nullish coalescing operator '??'**

* **Khái niệm:**

  * ?? trả về đối số đầu tiên nếu nó không phải là null / undefined.

    ```result = a ?? b``` **===** ```result = (a !== null && a !== undefined) ? a : b;```

    ví dụ:

    ```
        let user;

        alert(user ?? "Anonymous"); // Anonymous (user not defined)
    ```

* **Comparison with ||.( so sánh với toán tử || )**

    ```
        let firstName = null;
        let lastName = null;
        let nickName = "Supercoder";

        // shows the first truthy value:
        alert(firstName || lastName || nickName || "Anonymous"); // Supercoder

        alert(firstName ?? lastName ?? nickName ?? "Anonymous"); // Supercoder
    ```

    **Sự khác biệt quan trọng giữa chúng là:**

    `||` returns the first **truthy** value.

    *( truthy là giá trị được hiểu là true trong ngữ cảnh Boolean. Tất cả mọi giá trị đều là truthy, trừ phi chúng được định nghĩa là falsy (tức là, ngoại trừ false, 0, "", null, undefined, và NaN)).*

    `??` returns the first **defined** value.

    ví dụ:

    ```
        let height = 0;

        alert(height || 100); // 100

        alert(height ?? 100); // 0
    ```

    => height || 100 kiểm tra height xem có phải là falsy hay không, và nó thực sự là 0. Do đó kết quả của || là đối số thứ hai, 100.

    => height ?? 100 kiểm tra height xem có phải là null / undefined. Vì vậy kết quả là Height "như hiện tại", đó là 0.

    => Trong thực tế, chiều cao bằng 0 thường là một giá trị hợp lệ, không nên được thay thế bằng giá trị mặc định. Vì thế ?? làm đúng.

* **Precedence. (Quyền ưu tiên)**
  * Mức độ ưu tiên của dấu ?? toán tử gần giống với ||, chỉ thấp hơn một chút. Nó bằng 5 trong bảng [**MDN**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#table), trong khi || là 6.

    ví dụ 1:

    ```
        let height = null;
        let width = null;

        // important: use parentheses
        let area = (height ?? 100) * (width ?? 50);

        alert(area); // 5000
    ```

    ví dụ 2:

    ```
        // without parentheses (không có dấu ngoặc) => toán tử * sẽ có độ ưu tiên cao hơn, nó sẽ thực thi trước. => dẫn đến kết quả không chính xác.
        let area = height ?? 100 * width ?? 50;

        // ...works the same as this (probably not what we want):
        let area = height ?? (100 * width) ?? 50;
    ```

* **Using ?? with && or ||.**
  * Vì lý do an toàn, JavaScript cấm sử dụng ?? cùng với && và || toán tử, trừ khi mức độ ưu tiên được chỉ định rõ ràng bằng dấu ngoặc đơn. Đoạn mã dưới đây gây ra lỗi cú pháp:

    ví dụ:

    ``` let x = 1 && 2 ?? 3; // Syntax error ```

     => ``` let x = (1 && 2) ?? 3; // Works ```
