---
title: "Angular lifecycle và Change Detection"
date: "2022-05-18"
categories: 
  - "angular"
tags: 
  - "angular-lifecycle"
  - "change-detection"
  - "ngzone"
---

Angular lifecycle và và cơ chế Change Detection - một trong những khái niệm cốt lõi của Angular. Nắm rõ vòng đời của Angular và và cơ chế Change Detection sẽ giúp chúng ta có cái nhìn tổng quan hơn về framework và dễ dàng xử lý lỗi phát sinh trong quá trình sử dụng.

## Angular lifecycle

Vòng đời của một component trãi qua các giai đoạn theo thứ tự sau:

constructor  
ngOnChanges  
ngOnInit  
ngDoCheck  
ngAfterContentInit  
ngAfterContentChecked  
ngAfterViewInit  
ngAfterViewChecked  
ngOnDestroy

Ngoại trừ constructor và hàm ngOnDestroy được gọi khi khởi tạo và destroy component, có 3 hàm được gọi 1 lần duy nhất là:

- ngOnInit

- ngAfterContentInit

- ngAfterViewInit

Các hàm được gọi rất thường xuyên mỗi khi Angular chạy Change Detection, cần lưu ý để tránh ảnh hưởng tới performance của ứng dụng.

- ngOnChanges

- ngDoCheck

- ngAfterContentChecked

- ngAfterViewChecked

## Change Detection

Change Detection là cơ chế trong Angular để theo dõi sự thay đổi trong ứng dụng và tự động cập nhật UI, đảm bảo dữ liệu được hiển thị trên UI luôn đồng bộ với dữ liệu thực tế trong ứng dụng. Điểm đáng chú ý là kể từ angular2+, Angular tuân thủ luồng dữ liệu 1 chiều (Unidirectional Data Flow) hay one-way data binding. Đó là một bước thay đổi lớn của Angular so với AngularJS để cải thiện performance.

### Change Detection hoạt động như thế nào?

Mô tả đơn giản cách change detection trong angular hoạt động:

- Model được cập nhật

- Angular khích hoạt change detection

- Angular kiểm tra mỗi component trong component tree trừ trên xuống dưới (Unidirectional Data Flow)

- Nếu có value mới thì update DOM

- gọi ngAfterViewInit lifecycle hook cho tất cả các component

Tuy nhiên, có những tác vụ bất đồng bộ như HTTP request, setTimeout(), setInterval(), Promise.then(),...Vậy làm cách nào Angular biết được data thay đổi để đồng bộ với view? Đó là nhờ thư viện Zone.js và wrapper của nó là ngZone.

### ngZone

Angular sử dụng thư viện zone.js để quan sát các sự kiện bất đồng bộ. Wrapper ngZone tạo một zone có tên là 'angular' để tự động kích hoạt Change Detection khi phát hiện các sự kiện đồng bộ hoặc bất đồng bộ được thực thi.

Có thể thấy Zone.js rất quan trọng, nếu thiếu zone.js, Angular sẽ không phát hiện các thay đổi từ các sự kiện bất đồng bộ như HTTP request, setTimeout(),…dẫn đên view sẽ không được cập nhật.

Vậy nếu không sử dụng ngZone thì angular có phát hiện được thay đổi không? Câu trả lời là có nhưng chúng ta phải notify cho angular để kích hoạt Change Detection thủ công, dùng hàm ApplicationRef.tick() hoặc ChangeDetectorRef.detectChanges().

### Change Detection strategy

Có 2 chiến lược phát hiện thay đổi:

- **Default**: CheckAlways, change detection sẽ tự động check mọi component từ trên xuống dưới. Chiến lược này còn được gọi là **dirty checking**. Nó có thể ảnh hưởng lớn đến performance nếu ứng dụng của bạn lớn bao gồm nhiều component.

- **OnPush**: checkOnce, change detection chỉ chạy khi component khởi tạo. Sau đó có các trường hợp được trigger Change Detection:
  - Giá trị Input thay đổi hoặc reference thay đổi
  
  - Event của template như onClick, onMousemove… được kích hoạt
  
  - trigger change detection thủ công
  
  - async pipe emit value mới

Chiến lược OnPush được khuyên dùng để tăng performance của ứng dụng. Tuy nhiên cần chú ý chủ động việc trigger detect change lên view khi dùng chiến lược OnPush.

### Lỗi ExpressionChangedAfterItHasBeenCheckedError

ExpressionChangedAfterItHasBeenCheckedError xảy ra khi angular phát hiện expression value thay đổi sau khi quá trình change detection hoàn thành. Lỗi này chỉ xảy ra ở development mode do ở dev mode angular run check bổ sung sau khi hoàn thành detect change để đảm bảo value nhất quán với view.

Lỗi này chỉ xảy ra ở development mode có thể angular muốn cảnh báo cho bạn rằng: đây không hẳn là một lỗi, nhưng bạn đang vi phạm quy tắc Unidirectional Data Flow, model và view của bạn có thể không nhất quán hoặc sai, hãy kiểm tra lại.

Angular tuân thủ luồng dữ liệu 1 chiều Unidirectional Data Flow. Có nghĩa rằng component con không được phép cập nhật các thuộc tính của component cha sau khi change detection hoàn thành. Tuy nhiên, developer vẫn có cách cập nhật dữ liệu của component cha như sử dụng dữ liệu input two-way binding, share service, .... Chúng ta hầu hết đều đã từng sử dụng những thứ này trong dự án và sử dụng không đúng cách, dẫn đến data hiển thị sai hoặc bug rất khó fix.

Có một số cách "chữa cháy" lỗi này tuy nhiên thường không được khuyến khích sử dụng:

- Asynchronous update: dùng hàm setTimeout()

- Manual Trigger change detection: dùng hàm _detectChanges_()

Quay lại với câu hỏi ở trên, tại sao không chạy change detection tiếp cho tới khi nào update view xong thì thôi mà lại báo lỗi? Câu trả lời là đến khi nào mới xong, chúng ta đang tạo ra một infinity loop vô tận: child-parent-child.

Tóm lại, cách tốt nhất để tránh lỗi này là chúng ta cần nắm rõ lifecycle hooks trong angular, hàm nào gọi trước DOM, hàm nào gọi sau DOM, hàm nào gọi 1 lần, hàm nào gọi nhiều lần. Ưu tiên sử dụng chiến lược OnPush. Luôn tuân theo quy tắc luồng dữ liệu 1 chiều Unidirectional Data Flow.

## Tổng kết

- Có 8 hàm life cycle hooks trong suốt vòng đời của một component

- Change Detection hoạt động dựa trên thư viện zone.js và wrapper ngZone

- Có hai chiến lược của Change Detection, trong đó OnPush được khuyên dùng

- Tuân thủ quy tắc 1 chiều để tránh lỗi ExpressionChangedAfterItHasBeenCheckedError
