# Difference between `stopPropagation` and `preventDefault`?

- Để biết sự khác biệt giữa `stopPropagation` và `PreventDefault`, chúng ta cần biết về `Event`.

- `Event` là gì?

 ```
    Event đại diện cho một sự kiện diễn ra trong cây DOM để đến mục tiêu của nó.
 ```

- `Event` lan truyền có hai giai đoạn:

  - **`Capturing:`** Trình duyệt kiểm tra xem liệu phần tử ngoài cùng của `<html>` có trình xử lý sự kiện `onclick` được đăng ký trên nó trong giai đoạn `capturing` hay không và chạy nó nếu có.
  - Sau đó, nó chuyển sang phần tử tiếp theo bên trong `<html>` (phần tử con) và làm điều tương tự, rồi đến phần tiếp theo, v.v. cho đến khi đến phần tử đã được nhấp vào.

  ```
    `Event` bắt đầu từ đối tượng `window` trở xuống cho đến khi nó đến `event.target`.
  ```

  - **`Bubling:`** Nó ngược lại với `capturing`. Trình duyệt sẽ kiểm tra xem liệu phần tử đã được nhấp vào có trình xử lý sự kiện `onclick` được đăng ký trên nó trong giai đoạn `bubbling` hay không và chạy nó nếu có.
  - Sau đó, nó chuyển sang phần tử cha và làm điều tương tự, rồi đến phần tiếp theo, và cứ tiếp tục như vậy cho đến khi đến phần tử ngoài cùng.

  ```
    Sự kiện `Bubling` thì ngược lại với `capturing` nó đi từ phần tử `event.target` (phần tử mình `click` vào) cho đến khi nó đến đối tượng `window`.
  ```

    ![image](https://res.cloudinary.com/practicaldev/image/fetch/s--CNAdH2a---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/shafikshaon/learning-stack/master/images/event-propagation-illustration.png)


## event.preventDefault()

- Chúng ta có thể hiểu đơn giản là hàm `preventDefault()` nó ngăn chặn `event` mặc định của trình duyệt thực hiện đối với sự kiện đó.
- ví dụ: `Blocking default click handling`
  ```
    document.querySelector("#id-checkbox").addEventListener("click", function(event) {
        document.getElementById("output-box").innerHTML += "Sorry! <code>preventDefault()</code> won't let you check this!<br>";
        event.preventDefault();
    }, false);
  ```

  ```
    <p>Please click on the checkbox control.</p>
    <form>
        <label for="id-checkbox">Checkbox:</label>
        <input type="checkbox" id="id-checkbox"/>
    </form>
    <div id="output-box"></div>
  ```

  - Nếu bạn nhấp vào hộp kiểm, sau đó bạn sẽ hiển thị một cái gì đó như thế này.

  ```
    Sorry!  preventDefault()  won't let you check this!
  ```


## event.stopPropagation()

- Hiểu nôn na là nó ngăn sự lan truyền thêm của sự kiện hiện tại trong các giai đoạn `capturing` và `bubbling`.(có nghĩa là sự kiện nó không ảnh hưởng đến thằng cha or con của nó)

- Theo mặc định, tất cả các trình xử lý sự kiện được đăng ký trong giai đoạn `bubbling`. Vì vậy, nếu bạn nhấp vào một phần tử `HTML`, sự kiện nhấp chuột sẽ `bubbling` từ phần tử được nhấp ra ngoài đến phần tử ngoài cùng của `<html>`.

- Vì vậy chúng ta cần phải sử dụng event.stopPropagation() để ngăn chặn giai đoạn `bubbling`.

- ví dụ:
  ```
    $("#but").click(function (event) {
        event.stopPropagation()
    })

    // since propagation was stopped by the #but id element's click, this alert will never be seen!
    $("#foo").click(function () {
        alert("parent click event fired!")
    })
  ```

  ```
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
    <div id="foo">
        <button id="but">button</button>
    </div>
  ```

## Tham khảo.
   - https://dev.to/shafikshaon/difference-between-stoppropagation-and-preventdefault-3gga.
