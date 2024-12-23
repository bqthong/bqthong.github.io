---
title: "Vấn đề timezone trong dự án"
date: "2023-07-05"
categories: 
  - "javascript"
tags: 
  - "timzone"
---

Làm việc trong dự án cần hỗ trợ nhiều timezone thì việc xử lý date time theo múi giờ của client là điều bắt buộc. Một document được tạo ở Anh lúc 15h, thì ông nhân viên ở Los Angeles phải thấy tài liệu đó được tạo lúc 7h sáng, ông nhân viên khác ở Việt Nam phải thấy tại liệu đó được tạo lúc 22h.

## Luôn sử dụng date time UTC

Trường hợp dự án của bạn không có yêu cầu cấu hình timezone, chỉ cần hiển thị theo đúng với timezone của client thì bạn chỉ cần làm như sau:

Chuyển đổi thành giờ UTC trước khi gởi request xuống BE:

```js
// Sử dụng hàm toISOString()
request.dueDate = dueDate.toISOString();
```

Khi nhận data từ BE cần convert ngược lại thành giờ client:

```js
// Sử dụng Date object
new Date(utcDateFromBE);

// Trong Angular sử dụng DatePipe để format
import { DatePipe } from '@angular/common';
this.datePipe.transform(utcDateFromBE, 'dd-MMM-yyyy');
```

## Vấn đề filter dữ liệu với timezone

Nếu dự án yêu cầu filter trên client, client chọn từ date picker có 2 trường hợp xảy ra:

**Bộ lọc cho phép chọn date và time:** trường hợp này bạn giữ nguyên date và time đã chọn và chỉ cần convert thành UTC.

**Bộ lọc chỉ chọn date:** trường hợp này bạn cần reset ngày đã chọn về mid-night (00:00) trước khi convert thành UTC để tránh dữ liệu trả về không chính xác.

Ví dụ: user A ở Việt Nam cần lọc dữ liệu ngày 5/7/2023

User A chọn ngày 5/7/2023 từ datepicker (chỉ có date không có time):

```js
new Date('2023-07-05')
//Wed Jul 05 2023 07:00:00 GMT+0700 (Indochina Time)

new Date('2023-07-05').toISOString();
//2023-07-05T00:00:00.000Z
```

Có thể thấy user A gởi request từ 7h sáng nên dữ liệu trả về client sẽ bao gồm data từ 7h ngày 5-7 đến 7h ngày 6-7. Dữ liệu này là không chính xác, vừa thiếu vừa thừa.

Nguyên nhân do khi bạn khởi tạo new Date mà không chỉ định time và timezone, Javascript tự động chuyển về local timezone. cho nên trong trường hợp này bạn cần phải reset về 00:00 trước khi convert về UTC. Để đặt date time về 0, bạn có thể làm như sau:

```js
new Date('2023-07-05 00:00:00.000');
//Wed Jul 05 2023 00:00:00 GMT+0700 (Indochina Time)

new Date('2023-07-05 00:00:00.000').toISOString();
//2023-07-04T17:00:00.000Z
```

Một vấn đề bộ lọc với nhiều timezone là client có thể thấy dữ liệu khác nhau tùy theo múi giờ của client . Để tránh nhầm lẫn, chỉ có thể cung cấp thông tin cho client về dữ liệu khác nhau giữa các timezone đối với bộ lọc. Nếu họ không đồng ý, chúng ta cần một cách tiếp cận khác. Chẳng hạn sẽ hiển thị dữ liệu dưới dạng UTC thay vì timezone của client. Tuy nhiên cách tiếp cận này cũng có nhiều vấn đề cần xác nhận với client.

## Vấn đề cấu hình timezone

Nếu dự án cần thay đổi múi giờ mà người dùng chỉ định, vấn đề sẽ phức tạp hơn một chút, ý tưởng như sau:

- Lưu timezone mà người dùng chỉ định trong DB.

- Date time trong DB luôn là UTC, lúc hiển thị ra màn hình thì convert theo timezone người dùng đã chỉ định

Tuy nhiên việc chuyển đổi múi giờ sẽ có nhiều vấn đề phát sinh, trong trường hợp này khi khởi tạo new Date bạn phải chỉ định timezone. Bạn có thể dùng Javascipt Date hoặc nếu cần nhiều tính năng hơn cho xử lý date time, hãy sử dụng thư viện như momentjs hoặc date-fns (date-fns được khuyên dùng).

## Tổng kết

Tóm lại, khi làm việc với timezone:

- Luôn sử dụng date time UTC.

- Khi khởi tạo new Date, Javascript tự động chuyển về local timezone nếu không chỉ định time và timezone.

Xem thêm: [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
