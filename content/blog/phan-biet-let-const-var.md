---
title: "Phân biệt let, const, var"
date: "2020-03-28"
categories: 
  - "javascript"
tags: 
  - "es6"
---

Trước đây chúng ta thường sử dụng var để khai báo biến. Nhưng từ khi ES6 ra đời, ta có thêm 2 lựa chọn là const và let. Vậy const và let có gì hay ho so với var, cùng tìm hiểu trong bài viết dưới đây nhé.

## var

### var là global/function scope

Từ khóa var tạo ra một biến có phạm vi truy cập xuyên suốt function chứa nó.

var có [scope](/scope-trong-javascript) toàn cục (global) khi được định nghĩa bên ngoài hàm.

var là function scope nếu được định nghĩa bên trong hàm

```js
var name = "Javascript";
function myFunction() {
    var hello = "Hello";
}
console.log(name) // Javascript
console.log(hello); // Uncaught ReferenceError: hello is not defined
```

### var bị hoisting

Hoisting là cơ chế trong đó các khai báo biến và hàm sẽ được hoisted lên đầu phạm vi (scope) của chúng trước khi được thực thi.

Cùng xem một ví dụ đơn giản sau:

```js
console.log(hello); // undefined
var hello = 'Hello';

// Thứ tự hoisting được giải thích như sau
var hello;
console.log(hello); //undefined do biến hello được hoisted lên đầu scope và được thực thi trước khi gán giá trị
hello = 'Hello';
```

### var có thể định nghĩa trùng lặp

Trong cùng một scope, ta có thể khai báo các biến trùng tên mà không bị lỗi.

```js
var hello = 'Hello';
var hello = 'say hi !';
// Hoặc
var hello = 'Hello';
hello = 'say hi !';
```

### Vấn đề của var

Cho đoạn code dưới đây

```js
function foo(condition) {
    var name = 'Javascript';
    if (condition > 10) {
        var name = 'Java'; // name ở đây cũng là name bên ngoài vòng if.
        console.log(name); // Java
    }
    console.log(name); // Java
}
foo(11);
```

Mặc dù ý đồ của chúng ta không muốn thay đổi biến name nhưng thật chất biến name đã được định nghĩa và được gán giá trị mới. Cho nên kết quả có thể không đúng như những gì ta mong đợi. Đó là lý do có sự xuất hiện của của const và let.

## let

### let là block scope

Từ khóa let tạo ra một biến chỉ có thể truy cập được trong block bao quanh nó. Một block được giới hạn bởi dấu {}.

```js
function foo(condition) {
    if (condition > 10) {
        let name = 'Javascript';
        console.log(name); // Javascript
    }
    console.log(name); // Uncaught ReferenceError: name is not defined
}
foo(11);
```

Ta thấy rằng không thể sử dụng biến name bên ngoài block scope.

### let bị hoisting, nhưng không được khởi tạo

Giống như var, let cũng bị hoisting, tuy nhiên var khởi tạo biến có giá trị undefined thì let không khởi tạo giá trị. Vì vậy nếu sử dụng biến let trước khi khởi tạo sẽ bị lỗi.

```js
hello = 'Hello';let hello = 'say hi !'; // Uncaught ReferenceError: Cannot access 'hello' before initialization
```

### let có thể định nghĩa trùng lặp, nhưng phải khác scope

Biến let có lẽ nghiêm ngặt hơn nhiều so với var. Nếu định nghĩa trùng lặp cùng scope sẽ bị lỗi.

```js
let hello = 'Hello';
let hello = 'say hi !'; // Uncaught SyntaxError: Identifier 'hello' has already been declared
```

Trong trường hợp khác scope. Lấy ví dụ bên trên để thấy được ưu điểm của từ khóa let hơn var

```js
function foo(condition) {
    let name = 'Javascript';
    if (condition > 10) {
        let name = 'Java';
        console.log(name); // Java
    }
    console.log(name); // Javascript
}
foo(11);
```

Ta thấy let được định nghĩa trùng lặp nhưng nằm ở 2 scope khác nhau nên hai biến này được coi là 2 biến khác nhau.

Có thể nói rằng, có 2 điều khiến let tuyệt vời hơn var:

- Biến let chỉ tồn tại trong scope của nó là một block

- Không thể định nghĩa biến trùng lặp trong cùng scope

## const

### const là block scope

Từ khóa const dùng để khai báo một hằng số là một giá trị không thay đổi được trong suốt quá trình chạy. Tương tự như let, biến const chỉ có thể truy cập được trong block bao quanh nó. Một block được giới hạn bởi dấu {}.

### const bị hoisting, nhưng không được khởi tạo

Giống như let, const cũng bị hoisting nhưng không khởi tạo giá trị. Vì vậy nếu sử dụng biến const trước khi khởi tạo sẽ bị lỗi.

### const không thể gán giá trị mới và không thể định nghĩa trùng lặp

Không giống như let, bản thân const đã có ý nghĩa dùng để khai báo một hằng số là một giá trị không thay đổi (constant). Vì vậy biến const không thể được cập nhật giá trị mới (đối với các giá trị nguyên thủy primitive)

```js
const hello = 'Hello';
hello = 'say hi !'; // Uncaught TypeError: Assignment to constant variable.
```

```js
const hello = 'Hello';
const hello = 'say hi !'; // Uncaught SyntaxError: Identifier 'hello' has already been declared.
```

Tuy nhiên đối, nếu định nghĩa biến const kiểu reference như object, array… Mặc dù không thể tái cập nhật giá trị mới cho biến nhưng ta vẫn có thể cập nhật giá trị cho thuộc tính của nó.

```js
const color = {
    red: 'Red',
    yellow: 'Yellow',
    green: 'Green'
}
color.red = 'Black';
console.log(color); // {red: "Black", yellow: "Yellow", green: "Green"}
```

Nếu muốn ngăn chặn việc object, array có thể bị sửa đổi, ta có thể sử dụng hàm Object.freeze như sau:

```js
const color = Object.freeze({
    red: 'Red',
    yellow: 'Yellow',
    green: 'Green'
})
color.red = 'Black';
console.log(color); // {red: "Red", yellow: "Yellow", green: "Green"}
```

## Tổng Kết

- Nên dùng let thay vì var để tạo biến.

- Nếu biến cần phải cập nhật giá trị mới thì thì dùng let.

- Sử dụng const cho hằng số không đổi
