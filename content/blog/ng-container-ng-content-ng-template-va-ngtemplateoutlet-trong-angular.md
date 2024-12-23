---
title: "Phân biệt ng-container, ng-content, ng-template và ngTemplateOutlet trong Angular"
date: "2022-11-11"
categories: 
  - "angular"
tags: 
  - "ng-container"
  - "ng-content"
  - "ng-template"
  - "ngtemplateoutlet"
---

ng container, ng-content, ng-template, và ngTemplateOutlet là các khái niệm liên quan đến hiển thị content và template trong angular. Vậy khi nào thì dùng cái nào, cùng mình tìm hiểu trong bài viết dưới đây.

## ng-container

**ng-container** dùng để tạo một container ảo để bọc các phần tử khác trong template mà không thêm tag trong DOM.

Ví dụ, đoạn code dưới đây render cell template trong table:

```html
<div *ngTemplateOutlet="cellTemplate; context: { row }"></div>

// Kết quả
<div>
    <p>Cell template</p>    
</div>
```

Khi render template sẽ bị dư thẻ div. Mặc dù đây không phải là vấn đề lớn nhưng nếu bạn viết CSS selector chặt, thì thứ tự các thẻ tag sẽ ảnh hưởng đến UI của bạn. Đoạn code dưới đây sử dụng ng-container sẽ không tạo thêm tag trong DOM.

```html
<ng-container *ngTemplateOutlet="cellTemplate; context: { row }"></ng-container>

// Kết quả
<p>Cell template</p>   
```

## ng-template

**ng-template** dùng để định nghĩa template có thể tái sử dụng. ng-template chỉ được render trong các trường hợp dưới đây:

### Sử dụng trong các cấu trúc điều kiện như `*ngIf`

```html
<div *ngIf="isUserLoggedIn; then loggedInTpl else notLoggedInTpl"></div>
<ng-template #loggedInTpl>
    <p>Hello, logged in!</p>
    <button (click)="logout()">Logout</button>
</ng-template>
<ng-template #notLoggedInTpl>
    <p>Please login!</p>
    <button (click)="login()">Login</button>
</ng-template>
```

Với cấu trúc `if then else`, sử dụng `ng-template` có lợi ích trong các điều kiện và template phức tạp, tránh trùng lặp code.

### Sử dụng khi template bị lặp lại

Giả sử bạn có một template bị duplicate nhiều lần nhưng không quá nhiều, cách đơn giản là tạo một template dùng chung, sau đó dùng ngTemplateOutlet để render:

Tạo template dùng chung:

```html
<ng-template #reusableTpl>
    <span class="quantity">{{ number}} items</span>
</ng-template>
```

Bất kỳ vị trí nào cần tái sử dụng template bạn có thể dùng:

```html
<ng-container [ngTemplateOutlet]="reusableTpl"></ng-container>
```

### Sử dụng khi muốn pass template vào component khác

Giả sử bạn có một reusable table component. nhưng tùy feature khác nhau, header cần phải khác nhau. Lúc này bạn cần cho phép pass custom header template vào table component:

```html
<div class="table-header">
    <ng-container *ngTemplateOutlet="headerTpl || defaultHeaderTpl"></ng-container>
    <ng-template #defaultHeaderTpl>
        <div class="default-header">...</div>
    </ng-template>
</div>
<div class="table-wrapper">
    <table>...</table>
</div>
```

```js
export class TableComponent {
  @Input() headerTpl: TemplateRef<any>;
}
```

Pass `customHeaderTpl` vào table component qua Input property `headerTpl`:

```html
<app-table
       [columns]="columns"
       [data]="data"
       [headerTemplate]="customHeaderTpl">
</app-table>
<ng-template #customHeaderTpl>
      <div class="custom-header">
        <button class="btn" (click)="add()"></button>
        <button class="btn" (click)="refresh()"></button>
      </div>
</ng-template>
```

## ngTemplateOutlet

Trong các ví dụ trên mình cũng đã nhắc nhiều đến ngTemplateOutlet:

*ngTemplateOutlet* dùng để render template tạo ra bởi ng-template.

Có 2 cú pháp sử dụng ngTemplateOutlet:

```html
<ng-container *ngTemplateOutlet="cellTemplate; context: { row }"></ng-container>

<ng-container 
        [ngTemplateOutlet]="cellTemplate"
        [ngTemplateOutletContext]="{ row }">
</ng-container>
```

Khi dùng ngTemplateOutlet, bạn có thể truyền data ra template thông qua context. Trong ví dụ trên mình đã truyền biến row ra context. Để sử dụng context bạn làm như sau:

```html
<ng-template #nameTpl let-row="row">
        <b>{{row.name}}</b>
</ng-template>  
```

Trong đó cú pháp bên trái dấu = là **let-row** biểu diễn biến row bạn có thể truy cập ở ng-template. Bên phải dấu bằng là **"row"** biểu diễn biến row bạn truyền qua context. Hai name này có thể đặt tên khác nhau. Nếu không đặt tên biến thì ở context sẽ truyền vào là **$implicit**. Tuy nhiên theo mình nên đặt tên cho dễ hiểu.

## ng-content

**ng-content** (content projection) dùng để chèn content từ component cha đến component con qua các projection slots.

Ví dụ, trong component con mình đặt một slot:

```html
<div class="child">
    <ng-content></ng-content>
</div>
```

Trong template của component cha mình làm như sau:

```html
<app-child-component>
        <p>Content from parent component</p>
</app-child-component>
```

Mặc định chúng ta chỉ nên dùng 1 slot duy nhất, nếu cần multiple slots, chúng ta cần định nghĩa selector.

Các dạng selector gồm:

- Element or HTML Tag selector: `<ng-content select="[header]"></ng-content>`

- CSS Class selector: `<ng-content select=".custom-class"></ng-content>`

- Attribute selector: `<ng-content select="[data-role='admin']"></ng-content>`

- Multiple selectors: `<ng-content select="p, .custom-class"></ng-content>`

Một ví dụ sử dung Element name selector, mình có một slot name là header trong component con:

```html
<ng-content select="[header]"></ng-content>
```

Cách dùng trong component cha.

```html
<div header>Header from parent component</div>
```

Lưu ý khi sử dụng `ng-content` bạn không thể truyền context ra ngoài. Vì đúng như tên của nó là ng-content. nó chỉ truyền content. Nếu bạn muốn dynamic content và truyền context để xử lý nên dùng `ng-template` và `ngTemplateOutlet`

## Tổng kết

- **ng-container** dùng để tạo một container ảo để bọc các phần tử khác trong template mà không thêm tag trong DOM.

- **ng-template** dùng để định nghĩa template có thể tái sử dụng.

- **ngTemplateOutlet** dùng để render template tạo ra bởi ng-template (có thể truyền context variables)

- **ng-content** dùng để chèn content từ component cha đến component con qua các projection slots (không thể truyền context variables)
