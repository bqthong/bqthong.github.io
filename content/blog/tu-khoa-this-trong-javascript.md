---
title: "Từ khóa this trong Javascript"
date: "2020-04-02"
categories: 
  - "javascript"
tags: 
  - "tu-khoa-this"
---

Từ khóa this trong javascript là một khái niệm rất cơ bản tuy nhiên rất dễ nhầm lẫn. Các vấn đề thường gặp với từ khóa this là gì, ngữ cảnh thực thi khi gọi hàm thay đổi như thế nào, cùng tìm hiểu nhé.

## 'this' là gì?

Trong lập trình hướng đối tượng, từ khóa this (hay còn gọi là con trỏ this) dùng để trỏ đến object chứa phương thức đang thực thi. Thông qua con trỏ this này để lấy các giá trị thuộc tính khác nằm trong object đó.

Ví dụ 1 đoạn code trong Java

```java
public class Employee{
    String name;
    int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }

    String getName(){
        return this.name;
    }

    public static void main(String []args){
        Employee employee = new Employee("John", 24);
        String name = employee.getName();
        System.out.println(employee); // John
    }
}
```

Khi hàm getName() được gọi, thì con trỏ this trong hàm getName() sẽ trỏ đến đối tượng employee, vì vậy this.name chính là thuộc tính name của đối tượng employee.

## This trong Javascript

### This toàn cục (global)

Khi code được thực thi ở trình duyệt, mặc định this sẽ trỏ đến đối tượng window.

```js
function foo() {
    console.log(this) // This ở đây là Window object
}
foo();
```

### This trong Object

Có một điều cần lưu ý là hàm (function) ở trong Javascript cũng chính là một đối tượng (object). Chính vì vậy khi một hàm được gọi, thì nó sẽ có một thuộc tính là this và this này sẽ trỏ đến object chứa hàm đó.

Chúng ta cùng xem thử đoạn code sau:

```js
var employee = {
    name: 'John',
    age: 24,
    getName: function() {
        console.log('My name is ' + this.name); // This ở đây là object employee
    }
};
employee.getName(); //My name is John
```

Có thể thấy rằng khi gọi hàm getName(), this sẽ trỏ đến object employee.

### This trong Prototypes and Constructors

Tương tự như object, đối với Prototype và Constructor, this sẽ trỏ tới Constructor object.

```js
var myConstructor = function() {
    this.constructorMethod = function() {
        console.log(this); // This ở đây trỏ tới object myConstructor
    };
};

myConstructor.prototype = {
    prototypeMethod: function() {
        console.log(this); // This ở đây trỏ tới object myConstructor
    }
};

var test = new myConstructor();
test.constructorMethod();
test.prototypeMethod();
```

### This trong event DOM

Đối với event trong DOM, this sẽ trỏ tới đối tượng element được select. Ví dụ

```js
var element = document.querySelector('.my-element');
var clickeMethod = function(event) {
    console.log(this); // This ở đây chính là element <div class="my-element">
    console.log(event.currentTarget === this) // true
};
element.addEventListener('click', clickeMethod);
```

## Các rắc rối thường gặp với từ khóa this

### Sử dụng this trong hàm được truyền như một callback

Hàm callback là việc chúng ta truyền hàm A với vai trò như một tham số vào hàm B đến một thời điểm thì hàm A sẽ được hàm B gọi lại

Lấy ví dụ, ta truyền hàm getName() như một callback vào sự kiện click trong DOM.

```js
var employee = {
    name: 'John',
    age: 24,
    getName: function() {
        console.log('My name is ' + this.name);
    }
};
var buttonElm = document.getElementById("button");
buttonElm.addEventListener('click', employee.getName);

 // My name is undefined
```

Theo đoạn code trên có thể thấy kết quả in ra console giá trị name là undefined.

Nguyên nhân là do ta truyền employee.getName vào hàm click như một callback. Khi hàm click được gọi, sau đó hàm getName được kích hoạt. Tuy nhiên ngữ cảnh (context) lúc gọi hàm getName không còn là object employee nữa mà là chính là DOM mà ta click vào nên nên this không còn trỏ tới object employee.

Để giải quyết vấn đề này ta phải đảm bảo được ngữ cảnh của hàm callback là object employee. Để thay đổi ngữ cảnh ta có thể tạo ra lexical [scope](/scope-trong-javascript), hoặc dùng hàm bind trong Javascript như sau.

```js
// Thay vì viết
buttonElm.addEventListener('click', employee.getName);

// Cách 1: Sử dụng hàm bind để thay đổi context mình muốn
buttonElm.addEventListener('click', employee.getName.bind(employee));

// Cách 2: Sử dụng anonymous function tạo lexical scope.
var thiz = this;
buttonElm.addEventListener('click', function() {
    thiz.employee.getName();
});
// Hoặc
buttonElm.addEventListener('click', function(thiz) {
    return function() {
        thiz.employee.getName();
    }
}(this));
```

### Sử dụng this trong closure

Closure là một hàm được viết lồng vào bên trong một hàm khác (hàm cha) nó có thể sử dụng biến toàn cục, biến cục bộ của hàm cha và biến cục bộ của chính nó (lexical scope)

Theo như định nghĩa, closure thể sử dụng biến toàn cục, biến cục bộ của hàm cha. Vậy còn con trỏ this của hàm cha thì sao ?. Thử lấy ví dụ, giả sử trong object employee ta có thêm một list data như sau:

```js
var employee = {
    name: 'John',
    age: 24,
    skills: ['Java', 'Javascript', 'Angular'],
    getName: function() {
        console.log('My name is ' + this.name);
    },
    getSkills: function() {
        console.log(this); // This ở đây trỏ đên object employee
        this.skills.forEach(function(item) {
            console.log(this.name + '-' + item); // This ở đây trỏ đên object window
        });
    }
};
employee.getSkills();
// undefined - Java
// undefined - Javascript
// undefined - Angular
```

Theo đoạn code trên có thể thấy trong vòng lặp forEach ta không thể truy cập this.name. Vì sao vậy?

Đây là một trường hợp khá phổ biến: sử dụng this trong vòng lặp. Ở đây ta truyền một anonymous function cho hàm forEach và chúng nằm bên trong hàm cha là getSkills(). Có nghĩa là chúng ta đã tạo ta một closure. Nhưng không may closure chỉ có thể truy cập biến toàn cục và biến của cha nó, ngữ cảnh lúc này this không tham chiếu đến object employee nữa mà là object window.

Có nhiều cách giải quyết trong trường hợp này:

```js
// Cách 1: Sử dụng biến cục bộ
getSkills: function() {
    console.log(this); // This ở đây trỏ đên object chứa hàm là employee
    var thiz = this; // Gán this cho 1 biến cục bộ
    this.skills.forEach(function(item) {
        console.log(thiz.name + '-' + item); // Bây giờ ta có thể truy cập biến thiz cục bộ của hàm cha.
    });
}

// Cách 2: Sử dụng tham số thứ 2 của các hàm loop như forEach, map, filter, find,...
showSkills: function() {
    console.log(this); // This ở đây trỏ đên object employee
    this.skills.forEach(function(item) {
        console.log(this.name + '-' + item);
    }, this); // Truyền context vào tham số thứ hai của hàm forEach
}
```

### Sử dụng this khi hàm được gán cho biến

Trường hợp này ít gặp nhưng không phải không có, đó là trường hợp gán hàm cho một biến và gọi hàm đó.

```js
var name = 'Harry';
var employee = {
    name: 'John',
    age: 24,
    getName: function() {
        console.log('My name is ' + this.name);
    }
};
var getNameFn = employee.getName;
getNameFn();
// My name is Harry
```

Trường hợp này cũng tương tự ở chỗ là context của hàm đã thay đổi thành window object. Để thay đổi context, ta cũng có thể sử dụng hàm bind.

```js
var getNameFn = employee.getName.bind(employee);
getNameFn();
// My name is John
```

## Tổng kết

Có thể thấy hầu hết các rắc rối với con trỏ this là do sự thay đổi ngữ cảnh (context) khi gọi hàm. Các lưu ý về ngữ cảnh:

- Hàm toàn cục, this sẽ trỏ đến object window

- Hàm trong object , this sẽ trỏ đến chính object gọi hàm đó.

- Hàm trong event DOM, this trỏ đến chính element được chọn.

Do đó để biết this trỏ đến đối tượng nào phải chú ý để ngữ cảnh thực thi của hàm được gọi.
