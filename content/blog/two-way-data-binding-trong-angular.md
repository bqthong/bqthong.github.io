---
title: "Two-way data binding trong Angular"
date: "2022-10-04"
categories: 
  - "angular"
tags: 
  - "data-binding"
  - "ngmodel"
---

Angular tuân thủ luồng dữ liệu 1 chiều (unidirectional data flow), tuy vậy chúng ta vẫn có thể áp dụng Two-Way Data Binding. Trong bài viết này, chúng ta sẽ tìm hiểu về Two-Way Data Binding và cách tạo một custom Two-Way Data Binding.

## Two-way Binding với ngModel

Kể từ Angluar2+, Two-Way Data Binding không còn là mặc định trong Angular, thay vào đó là One-Way Data Binding. Thay đổi lớn trong cơ chế change detection đã giúp Angular cải thiện hiệu suất so với AngularJS.

Tuy nhiên trong Angular bạn vẫn có thể sử dụng directive `ngModel` để áp dụng Two-Way Data Binding.

Như bạn đã biết property binding sử dụng cặp dấu ngoặc vuông `[]` thể hiện one-way binding, dấu ngoặc tròn `()` thể hiện event binding.

Để sử dụng two-way binding, dùng cú pháp kết hợp cặp dấu ngoặc vuông và ngoặc tròn `[()]`.

```html
<input type="text" [(ngModel)]="name" /><p>Your name: {{ name }}</p>
```

## Custom Two-way Binding với Input Output property

Nhìn vào cú pháp cặp dấu ngoặc vuông và ngoặc tròn `[()]`, chúng ta có thể nhận ra đây là viết tắt của property binding và event binding:

```html
<input type="text" [ngModel]="name" (ngModelChange)="name = $event" />
```

Vậy để tạo custom Two-way binding thì bạn chỉ cần kết hợp `@Input` và `@Output` và thêm chữ `Change` cho event binding.

Ví dụ bạn có Input là `selected` thì output sẽ là `selectedChange`

```js
export class SelectComponent {
    @Input() selected: any;
    @Output() selectedChange = new EventEmitter<any>();

    constructor() {}

    toggleSelect(item: any) {
        this.selected= item;
        this.nameChange.emit(this.selected);
    }
}
```

Trong ví dụ trên khi bạn chọn value mới, giá trị mới sẽ được ánh xạ và hiển thị lên view ở parent component.

```html
<app-select [(selected)]="selected"></app-select>
<p>{{selected.name}}</p>
```

Một lưu ý về việc sử dụng two-way binding cập nhật giá trị của component cha có thể tác động đến cơ chế change detection. Nó có thể gây nhiều vấn đề về hiệu suất nếu bạn không kiểu soát tốt ứng dụng của mình, gây bug rất khó debug hoặc như lỗi ExpressionChangedAfterItHasBeenCheckedError.

## Tổng kết

- Có thể áp dụng Two-way Binding với ngModel directive.

- Tạo Custom Two-way Binding với Input Output property.

- Cân nhắc lý do quyết định sử dụng two-way binding trong dự án.
