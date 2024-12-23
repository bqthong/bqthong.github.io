---
title: "Hoisting trong Javascript"
date: "2020-03-18"
categories: 
  - "javascript"
tags: 
  - "hoisting"
---

Hoisting trong Javascript là gì?, tại sao có thể gán được biến, gọi được hàm trước khi định nghĩa nó. Hoisting hoạt động như thế nào? Cùng tìm hiểu trong bài dưới đây.

## Hoisting là gì?

Hoisting là cơ chế trong đó các khai báo biến và hàm sẽ được hoisted lên đầu phạm vi (scope) của chúng trước khi được thực thi.

### Phân biệt undefined và ReferenceError

- **undefined**: xảy ra khi truy xuất một biến đã được định nghĩa nhưng chưa gán giá trị cho nó.

- **ReferenceError**: xảy ra khi truy xuất một biến chưa được định nghĩa.

```js
var a;
console.log(a); // Output: undefined
console.log(b); // Output: ReferenceError: b is not defined
```

### Chỉ có phần định nghĩa được hoisted

Cho đoạn code sau:

```js
foo();
function foo() {
    console.log(a); // undefined
var a = 5;
}
```

Thứ tự hoisting được giải thích như sau:

```js
function foo() {
    var a;
    console.log(a);
    a = 5;
}
foo();
```

Kết quả undefined do biến a được hoisted lên đầu hàm và được thực thi trước khi gán giá trị a = 5.

### Chỉ Function Declaration được hoisted

Dưới đây là cách khai báo Function Declaration và Function Expression

```js
// Function Declaration:
function foo(){
    // code
}
// Function Expression:
var foo = function(){
    // code
};
```

```js
foo();
bar();

function foo() {
    console.log('Foo');
}
var bar = function() {
    console.log('Bar');
}
// Foo
// Uncaught TypeError: bar is not a function
```

Có thể thấy nếu bạn chạy đoạn code trên thì sẽ có lỗi runtime Uncaught TypeError: bar is not a function. Nguyên nhân do Function expression không được hoisted

### Hàm được định nghĩa cuối sẽ ghi đè các hàm cùng tên

Ví dụ mình có đoạn code sau định nghĩa 3 hàm cùng tên:

```js
showNumber();
function showNumber(){
    console.log('1');
}
function showNumber(){
    console.log('2');
}
function showNumber(){
    console.log('3');
}
// 3
```

Có thể thấy output sẽ in ra là 3 do hàm cuối cùng sẽ ghi đè lên các hàm được định nghĩa trước đó.

## Tổng kết

Cơ chế hoisting:

- Chỉ có phần định nghĩa được hoisted, không phải phần gán giá trị.

- Chỉ Function Declaration được hoisted, Function expression không được hoisted

- Hàm được định nghĩa cuối cùng sẽ ghi đè lên các hàm cùng tên

Theo mình không nên dựa dẫm vào hoisting. Điều này dễ gây nhầm lẫn và dễ gây bug trong code. Chúng ta nên từ bỏ việc dùng var để khai báo biến, thay vào đó ta nên sử dụng let và const (ES6)
