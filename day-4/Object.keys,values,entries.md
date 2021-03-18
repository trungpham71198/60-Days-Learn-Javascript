# **Object.keys, values, entries**

- Hãy bước ra khỏi các cấu trúc dữ liệu riêng lẻ và nói về các lần lặp lại chúng.
  - `map.keys(), map.values(), map.entries()`
- Các phương pháp này là chung, có một thỏa thuận chung để sử dụng chúng cho các cấu trúc dữ liệu. Nếu chúng ta đã từng tạo một cấu trúc dữ liệu của riêng mình, chúng ta cũng nên triển khai chúng.
- Chúng được hỗ trợ cho:
  - Map
  - Set
  - Array
- Các đối tượng thuần túy cũng hỗ trợ các phương thức tương tự, nhưng cú pháp có một chút khác biệt.


### **Object.keys, values, entries**

- `Object.keys(obj)` – trả về 1 mảng keys.
- `Object.values(obj)` – trả về 1 mảng values.
- `Object.entries(obj)` – trả về 1 mảng [key, value].

- Điểm khác biệt đầu tiên là chúng ta phải gọi `Object.keys(obj)` chứ không phải `obj.keys()`.
- Tại sao như vậy? Lý do chính là tính linh hoạt. Hãy nhớ rằng, các đối tượng(Object) là cơ sở của tất cả các cấu trúc phức tạp trong JavaScript. Vì vậy, chúng ta có thể có một đối tượng của riêng chúng ta như dữ liệu`(data)` triển khai phương thức `data.values​​()` của chính nó. Và chúng ta vẫn có thể gọi `Object.values(data)` trên đó.
- Điểm khác biệt thứ hai là các phương thức `Object. *` Trả về các đối tượng mảng “thực”, không chỉ là một đối tượng có thể lặp lại. Đó chủ yếu là vì lý do lịch sử.

- Ví dụ:
  ```
    let user = {
        name: "John",
        age: 30
    };
  ```
- `Object.keys(user) = ["name", "age"]`
- `Object.values(user) = ["John", 30]`
- `Object.entries(user) = [ ["name","John"], ["age",30] ]`

- Dưới đây là một ví dụ về việc sử dụng `Object.values` ​​để lặp qua các giá trị thuộc tính:
  ```
    let user = {
        name: "John",
        age: 30
    };

    // loop over values
    for (let value of Object.values(user)) {
        alert(value); // John, then 30
    }
  ```


### **Transforming objects** (Chuyển đổi đối tượng)
- Các đối tượng thiếu nhiều phương thức tồn tại cho mảng, ví dụ: `map`, `filter` và những thứ khác.
- Nếu chúng ta muốn áp dụng chúng, thì chúng ta có thể sử dụng `Object.entries` theo sau là `Object.fromEntries:`
1. Sử dụng `Object.entries(obj)` để lấy một mảng các cặp khóa / giá trị từ `obj`.
2. Sử dụng các phương thức mảng trên mảng đó, ví dụ: `map`.
3. Sử dụng `Object.fromEntries(array)` trên mảng kết quả để biến nó trở lại thành một đối tượng

- Ví dụ: chúng tôi có một đối tượng có giá và muốn nhân đôi chúng:
  ``` 
    let prices = {
        banana: 1,
        orange: 2,
        meat: 4,
    };

    let doublePrices = Object.fromEntries(
        // convert to array, map, and then fromEntries gives back the object
        Object.entries(prices).map(([key, value]) => [key, value * 2])
    );

    alert(doublePrices.meat); // 8
  ```