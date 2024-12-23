---
title: "Scope trong Javascript"
date: "2020-03-30"
categories: 
  - "javascript"
tags: 
  - "closure"
  - "scope"
---

Scope trong javascript là gì? Có các loại scope nào? Closure là gì? scope và từ khóa this. Hiểu rõ scope của các biến/hàm giúp cho code của bạn rõ ràng và ít bug hơn. Cùng tìm hiểu nhé.

## Scope trong Javascript là gì ?

Scope là phạm vi mà các biến/hàm có thể truy xuất được. Và khi ra khỏi scope thì các biến/hàm đó không thể truy xuất được nữa

Khi trang ứng dụng chạy trên trình duyệt, mặc định trình duyệt sẽ tạo ra Global scope là window, và khi tạo các function thì sẽ các scope riêng nữa. Dưới đây mình sẽ liệt kê các loại scope trong javascript

## Các loại scope trong Javascript

### Global Scope

Khi trang web chạy, nó sẽ mặc định tạo ra một Global scope là window. Vì vậy, khi bắt đầu viết code thì chúng ta đã ở trong Global scope.

```js
var hello = 'Hello world!'; // Biến này là biến global
window.hello
// 'Hello world!
```

Biến ở Global scope khá tiện dụng, nhưng nếu kiểm soát không tốt thì nó sẽ trở nên tồi tệ hơn. Hãy xem một ví dụ:

```js
var a = 5;

function calculator(value) {
    a = 2;
    var square = a * value;
    return square;
}
console.log(calculator(10)); // 20
console.log(a); // 2
```

Giá trị biến a toàn cục đã bị thay đổi bên trong hàm calculator. Điều này rất nguy hiểm do chúng ta sử dụng từ khóa var. Tuy nhiên, từ ES6 trở đi, vấn đề này đã được giải quyết nhờ sự ra đời của từ khóa [const và let](/phan-biet-let-const-var).

### Local Scope

Có một lưu ý quan trọng là tất cả các scope mới đều được tạo ra bởi function(Function scope). Vì vậy nếu định nghĩa các biến bên trong function thì các biến này sẽ trở thành local scope. Nó không thể truy cập từ scope bên ngoài.

Ví dụ đơn giản:

```js
var name = 'Thong'; // Đây là Global scope
function myFunction() {
    var age = 19; // Đây là Local scope
    console.log(name); // Có thể truy cập biến global ở local scope.
};
console.log(age); // Uncaught ReferenceError: age is not defined
// Không thể truy cập do biến age thuộc local scope.
```

Có thể thấy:

- Có thể truy cập biến global ở local scope.

- Không thể truy cập do biến local scope ở global scope.

### Lexical Scope (Closure)

Lexical scope hay còn gọi là closure xảy ra khi một function được định nghĩa bên trong function khác. Nó có thể sử dụng biến toàn cục, biến cục bộ của hàm cha và biến cục bộ của chính nó

Một số lưu ý về lexical scope:

- Function bên trong có thể truy cập được biến của function bên ngoài

- Lexical scope không hoạt động ngược, function bên ngoài không thể truy cập biến của function bên trong.

- Thứ tự gọi hàm ảnh hưởng đến biến được gọi.

Ví dụ mô tả về lexical scope:

```js
var name = 'Global';

function parentFunction() {
    var parent = 'Parent';
    // Đinh nghĩa hàm bên trong một hàm
    function childFunction() {
        var child = 'Child';
        console.log(parent); // Có thể truy cập biến outer scope
        console.log(name); //  Có thể truy cập global scope
    };
    childFunction();
    console.log(child);  // Lỗi, không thể truy cập biến bên trong childFunction
 
};
parentFunction();
// Parent
// Global
// Uncaught ReferenceError: child is not defined
```

### Scope và từ khóa this

Scope và từ khóa this là 2 khái niệm rất cơ bản và quan trọng cần nắm trong Javascript, chúng có quan hệ mật thiết với nhau. Nếu không hiểu rõ, bạn sẽ vô tình tạo bug mà không hề biết đấy. Có 3 vấn đề cần lưu ý:

- Mặc định thì this sẽ thuộc global scope và sẽ trỏ đến object window.

- Mỗi scope sẽ hiểu với một biến this khác nhau.

- This có thể bị thay đổi nên this trỏ đến đối tượng nào tùy thuộc vào function được gọi ở ngữ cảnh nào.

## Tổng kết

- Các loại scope trong javascript: Global Scope, Local Scope và Lexical Scope (Closure)

- Cần nắm rõ scope và từ khóa this
