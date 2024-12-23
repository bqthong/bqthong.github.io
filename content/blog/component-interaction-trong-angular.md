---
title: "Component interaction trong angular"
date: "2022-08-01"
categories: 
  - "angular"
tags: 
  - "angular-component"
  - "angular-service"
  - "viewchild"
---

Trong một ứng dụng, có nhiều component và chúng cần tương tác với nhau(component interaction). Trong bài viết này, chúng ta sẽ tìm hiểu cách để các component giao tiếp với nhau trong angular.

## Component Cha và component Con

Trong Angular, bạn có thể chia ứng dụng của mình thành nhiều component nhỏ. Các component có thể được lồng nhau, component chứa một component khác được gọi là component cha, component bên trong được gọi là component con.

Vì vậy việc tương tác giữa các component cha và con là một phần quan trọng, làm sao để cha truyền dữ liệu cho con, làm sao để con emit sự kiện cho cha, hoặc làm sao để cha truy cập được các thuộc tính của con.

## Truyền data từ Cha đến Con dùng Input property

Cách phổ biến nhất để truyền dữ liệu từ cha con là dùng input properties, sử dụng @Input decorator. Trong component con, bạn định nghĩa một input property:

```js
@Input() name: string;
// Hoặc set giá trị default
@Input() name: string = 'default name';
```

Trong template của component cha, bạn có thể truyền dữ liệu cho component con như sau:

```html
<app-con [name]="name"></app-con>
```

data đã được truyền vào component con, tuy nhiên trong thực tế có thể bạn cần validate kiểm soát dữ liệu đầu vào hoặc làm một action nào đó khi phát hiện input value bị thay đổi. Có 2 cách phổ biến để biết được input value thay đổi:

Sử dụng getter/setter:

```js
@Input()
get name(): string { return this._name; }
set name(name: string) {
    this._name = (name && name.trim()) || '';
    console.log(this._name);
    // doSomeThing()
}
private _name = '';
```

Sử dụng hàm ngOnChanges của hook OnChanges:

```js
ngOnChanges(changes: SimpleChanges): void {
    const {name} = changes;
    if (name) {
        console.log(name.currentValue);
       // doSomeThing()
    }
}
```

## Cha tương tác với Con dùng _template reference variable_

Mặc định component cha không thể sử dụng property/method của component con. Angular support cho bạn một số cách để làm điều đó, phổ biến nhất là dùng _template reference variable_ và _ViewChild_. Dưới đây là ví dụ về _template reference variable_:

Giả sử trong ví dụ trên bạn có 1 method là showName() trong component con:

```js
@Input() name: string;showName() {    alert(this.name);}
```

Để gọi được hàm showName từ component cha, trong template của component cha bạn tạo một local variable:

```html
<button type="button" (click)="appcon.showName()">Show Name</button>
<app-con #appcon [name]="name"></app-con>
```

Variable _#appcon_ cho phép bạn truy cập vào bất kỳ property/method của component con.

Tuy nhiên điểm hạn chế của kỹ thuật _template reference variable_ là cha chỉ tương tác với con trong template. không thể thực hiện trong class cha. Để tương tác với component con trong class cha, lúc này bạn cần dùng kỹ thuật inject ViewChild.

## Cha tương tác với Con dùng ViewChild

Như đã trình bày ở trên. trong một số trường hợp, bạn có thể cần truy cập property/method của component con trực tiếp từ component cha. Bạn cần sử dụng ViewChild, trong đó 2 cách thường sử dụng:

Truy cập component con qua template variable:

```js
@ViewChild('componentConTpl') componentConTpl: TemplateRef<any>;
```

Truy cập component con qua decorator @Component or @Directive

```js
@ViewChild(ComponentCon) componentCon: ComponentCon;
```

Lúc này bạn có thể truy cập tất các property/method của `componentCon` từ class cha.

Tuy nhiên cần lưu ý rằng ViewChild có 1 config mặc định là **static: false**. Có nghĩa rằng resolve query result sẽ chạy sau [change detection](/angular-lifecycle-va-change-detection). Bạn không thể truy cập component con trong ngOnInit mà truy cập ở `ngAfterViewInit`. Nếu bạn muốn truy cập component con trong ngOnInit, cần config thành **static: true** và đảm bảo rằng component con không thay đổi trong suốt vòng đời của nó (không nằm trong \*ngIf, ...).

```js
@ViewChild('componentConTpl', {static: true}) componentConTpl: TemplateRef<any>;
```

## Emit event từ Con đến Cha dùng Output property

Component có thể emit event thông qua Output property, và component cha có thể lắng nghe nó.

Trong component con bạn định nghĩa một output property:

```js
@Output() dataChanged: EventEmitter<any> = new EventEmitter();
```

Khi nào cần emit 1 event cho component cha, bạn dùng cú pháp:

```js
this.dataChanged.emit(newValue);
```

Component cha có thể lắng nghe sự kiện từ component con trong template của mình:

```html
<app-con (dataChanged)="dataChanged($event)"></app-con>
```

## Sử dụng service

Thử tưởng tượng bạn có nhiều component lồng nhau nhiều cấp từ A, C, D, ... Z, thật khó để bạn truyền data hay emit event giữa component A và Z. Chúng không có mối quan hệ cha con trực tiếp. Lúc này bạn có thể sử dụng service.

Ví dụ bạn có một button cart ở header, giỏ hàng cần tăng số lượng ngay lập tức mỗi khi bạn thêm/bớt sản phẩm bất kỳ

Tạo một singleton service với 1 propery event là _cartChange_ sử dụng BehaviorSubject.

```js
@Injectable({
    providedIn: 'root'
})
export class CartHeaderService {

    cartChange: BehaviorSubject<number> = new BehaviorSubject(0);

    constructor() { }
}
```

```js
export class CartHeaderComponent {

    constructor(private cartHeaderService: CartHeaderService) { }

    ngOnInit(): void {
        this.cartHeaderService.cartChange.subscribe((newValue: number) => {
            console.log(newValue);
        });
    }
}
```

Lúc này khi ProductComponent emit event update thêm/bớt sản phẩm:

```js
this.cartHeaderService.cartChange.next(newValue)
```

CartHeaderComponent sẽ subcribe và nhận được giá trị mới từ ProductComponent.

Một lưu ý về việc sử dụng service để share dữ liệu giữa các component có thể tác động đến cơ chế change detection gây vấn đề về hiệu suất nếu bạn không kiểu soát tốt ứng dụng của mình, gây bug rất khó debug hoặc như lỗi ExpressionChangedAfterItHasBeenCheckedError.

## Kết Luận

Có nhiều cách để các component giao tiếp với nhau trong angular:

- Truyền data từ Cha đến Con dùng Input property

- Cha tương tác với Con dùng _template reference variable_

- Cha tương tác với Con dùng ViewChild

- Emit event từ Con đến Cha dùng Output property

- Component có thể giao tiếp với nhau qua service
