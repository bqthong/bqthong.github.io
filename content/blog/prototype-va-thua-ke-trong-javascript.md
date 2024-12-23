---
title: "Prototype và thừa kế trong Javascript"
date: "2020-04-29"
categories: 
  - "javascript"
tags: 
  - "es6"
  - "prototype"
---

  
Javascript có phải là ngôn ngữ lập trình hướng đối tượng? Prototype là gì? Làm sao để thừa kế trong Javascript, cùng tìm hiểu trong bài viết nhé.

## Prototype là gì ?

Trước hết, JavaScript không phải là một ngôn ngữ lập trình hướng đối tượng (OOP) thuần túy như Java hoặc C++. JavaScript sử dụng object và prototype để triển khai lập trình hướng đối tượng. Vậy prototype là gì?

> Prototypes are the mechanism by which JavaScript objects inherit features from one another.
>
> MDN

Prototype là cơ chế mà các đối tượng (object) kế thừa các tính năng của nhau. Ví dụ:

- String objects thừa kế từ String .prototype

- Array objects thừa kế từ Array.prototype

- Date objects thừa kế từ Date.prototype

- Person objects thừa kế từ Person.prototype

Object.prototype nằm ở đầu chuỗi kế thừa prototype. Có nghĩa là: String objects, Date objects, ... đều thừa kế từ Object.prototype

## Prototype Chain

Trong JavaScript, việc thừa kế được hiện thực thông qua prototype.

Cơ chế của Prototype: khi ta truy xuất thuộc tính hoặc hàm của một object, đầu tiên JavaScript sẽ tìm trong chính object đó, nếu không có nó sẽ tìm trong chuỗi \[\[Prototype\]\], đầu tiên là tìm lên prototype là cha của nó, cứ tiếp tục như thế cho đến khi nào nó gặp Object.prototype.

Đó là lý do ta có thể gọi các hàm slice, toUpperCase, trong String.prototype, forEach trong Array.prototype,….

```js
var name = ['javascript'];
name.toUpperCase(); // JAVASCRIPT
```

Cơ chế hoạt động của prorotype chain có thể được mô tả như sau:

```js
// Tạo obj có 2 giá trị a và b
var fn = function () {
    this.a = 1;
    this.b = 2;
}
var o = new fn(); // {a: 1, b: 2}

// thêm 2 property vào prototype obj
fn.prototype.b = 3;
fn.prototype.c = 4;

// Kiểm tra biến a trong object -> có tồn tại
console.log(o.a); // 1
// Kiểm tra biến b trong object -> có tồn tại
console.log(o.b); // 2
// Kiểm tra biến c trong object -> không tồn tại,
// Tiếp tục kiểm tra prototype của nó -> có tồn tại
console.log(o.c); // 4
// Kiểm tra biến d trong object -> không tồn tại,
// Tiếp tục kiểm tra prototype của nó -> không tồn tại
// Tiếp tục kiểm tra prototype của nó, lúc này gặp Object.prototype -> không tồn tại, dừng tìm kiếm và ra kết quả
console.log(o.d); // undefined
```

## Thừa kế trong Javascript

Như đã biết, Javascript (ES5) không có khái niệm class. Kể từ ES6 trở đi, javascript đã hỗ trợ mạnh mẽ hơn cho việc triển khai OOP như lớp class, constructor, extends, super ,.. Tuy nhiên về bản chất vẫn dựa trên thiết kế prototype.

Giả sử ta có một đối tượng Person có 3 thuộc tính và 1 phương thức sayHello trong prototype:

```js
// Định nghĩa Constructor function
function Person(name, age, gender) {
    this.name = name;
    this.age = age;
    this.gender = gender;
}
//  Person.prototype được khởi tạo
// Ta có thể định nghĩa thêm phương thức sayHello trong Person.prototype
Person.prototype.sayHello = function() {
    console.log('Hi, my name is ' + this.name + ', ' + this.age + ', gender ' + this.gender);
};
```

Bây giờ chúng ta muốn tạo một đối tượng Employee và ta muốn nó thừa kế các thuộc tính và phương thức của Person.

- Thừa kế thuộc tính: Sử dụng hàm call() trong javascript để thay đổi ngữ cảnh của [con trỏ this](/tu-khoa-this-trong-javascript).

- Thừa kế phương thức: Sử dụng Object.create() hoặc từ khóa new để thay đổi tham chiếu prototype.

```js
// Định nghĩa Constructor function
function Employee(name, age, gender, exp) {
    Person.call(this, name, age, gender);// Dùng hàm call để đổi context, this lúc này sẽ tham chiếu tới Person.
    this.exp = exp; // Đinh nghĩa thuộc tính tiêng cho Employee
}
// Thay đối tham chiếu prototype thừa kế phương thức của Person
Employee.prototype = Object.create(Person.prototype); // Hoặc viết Employee.prototype = new Person();
var employee = new Employee('Thong', 19, 'Male', 5);
console.log(employee);
```

Ta thấy đối tượng employee đã thừa kế đầy đủ các thuộc tính và phương thức của của Person.

## Tổng Kết

- Prototype là cơ chế mà các đối tượng (object) kế thừa các tính năng của nhau.

- Trong JavaScript, việc thừa kế được hiện thực thông qua prototype.

- Thừa kế thuộc tính: Sử dụng hàm call()

- Thừa kế phương thức: Sử dụng Object.create() hoặc từ khóa new
