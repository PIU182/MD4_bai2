# BÀI TẬP 2 – CÂN NHẮC BÀI TOÁN ĐÁNH ĐỔI VỚI MICROSERVICES

## 1. Khái quát Monolith và Microservices

**Monolith** là mô hình trong đó các chức năng chính của hệ thống thường được đóng gói và triển khai cùng nhau trong một ứng dụng. **Microservices** chia hệ thống thành nhiều dịch vụ nhỏ, mỗi dịch vụ tập trung vào một năng lực nghiệp vụ và có thể được phát triển, triển khai, mở rộng tương đối độc lập.

Ví dụ, một hệ thống thương mại điện tử có thể gồm các dịch vụ Quản lý sản phẩm, Quản lý đơn hàng, Thanh toán và Vận chuyển. Các dịch vụ trao đổi dữ liệu qua API hoặc sự kiện thay vì gọi trực tiếp hàm nội bộ như trong một ứng dụng Monolith.

## 2. Bảng so sánh Monolith và Microservices

| Tiêu chí | Monolith | Microservices |
|---|---|---|
| **Cách tổ chức** | Nhiều chức năng nằm trong một ứng dụng triển khai. | Chia theo các dịch vụ nghiệp vụ có ranh giới rõ ràng. |
| **Phát triển ban đầu** | Thường nhanh hơn với nhóm nhỏ. | Cần thiết kế ranh giới, giao tiếp và hạ tầng sớm hơn. |
| **Triển khai** | Thường triển khai cả ứng dụng. | Có thể triển khai riêng từng dịch vụ. |
| **Khả năng mở rộng** | Thường tăng tài nguyên cho toàn ứng dụng hoặc nhân bản toàn bộ. | Có thể tăng tài nguyên cho riêng dịch vụ có tải cao. |
| **Giao tiếp** | Chủ yếu là lời gọi trong cùng tiến trình. | Thường qua HTTP/gRPC hoặc message broker, có độ trễ và nguy cơ lỗi mạng. |
| **Dữ liệu** | Có thể dùng chung một database, giao dịch cục bộ dễ xử lý hơn. | Thường ưu tiên sở hữu dữ liệu riêng theo dịch vụ; giao dịch xuyên dịch vụ khó hơn. |
| **Kiểm thử** | Dễ bắt đầu kiểm thử tích hợp toàn hệ thống hơn. | Phải kiểm soát API contract, tích hợp đa dịch vụ và môi trường kiểm thử. |
| **Giám sát & xử lý sự cố** | Dễ theo dõi luồng yêu cầu trong cùng ứng dụng hơn. | Cần centralized logging, metrics và distributed tracing. |
| **Chi phí vận hành** | Thường thấp hơn ở quy mô nhỏ. | Có thể cao do nhiều tiến trình, mạng, công cụ và tự động hóa. |
| **Công nghệ** | Thường dùng một bộ công nghệ thống nhất. | Có thể chọn công nghệ khác nhau cho từng dịch vụ nhưng làm tăng độ phức tạp quản trị. |

## 3. Ưu điểm của Microservices

### 3.1. Triển khai độc lập

Nhóm phát triển có thể cập nhật dịch vụ Thanh toán mà không phải phát hành toàn bộ hệ thống, **nếu** hợp đồng giao tiếp vẫn tương thích. Điều này giúp tăng tốc độ phát hành khi có nhiều nhóm làm việc song song.

### 3.2. Mở rộng theo nhu cầu thực tế

Nếu dịch vụ Tìm kiếm có lượng truy cập lớn nhưng dịch vụ Quản lý tài khoản ít tải hơn, doanh nghiệp có thể bổ sung tài nguyên riêng cho Tìm kiếm. Cách làm này có thể tiết kiệm hơn so với mở rộng đồng loạt, tùy hạ tầng và tải thực tế.

### 3.3. Phân chia trách nhiệm rõ ràng

Mỗi đội có thể chịu trách nhiệm một hoặc một nhóm dịch vụ gắn với nghiệp vụ cụ thể. Ranh giới tốt giúp đội ngũ dễ sở hữu, kiểm thử và bảo trì code.

### 3.4. Linh hoạt về công nghệ

Một dịch vụ có thể dùng Java/Spring Boot, trong khi dịch vụ phù hợp với xử lý dữ liệu thời gian thực có thể dùng công nghệ khác. Tuy nhiên, lợi ích này chỉ có ý nghĩa khi doanh nghiệp đủ năng lực quản lý hệ sinh thái đa công nghệ.

### 3.5. Giới hạn phạm vi ảnh hưởng của một số lỗi

Nếu thiết kế tốt với timeout, circuit breaker, retry có kiểm soát và cơ chế dự phòng, lỗi ở một dịch vụ có thể được cô lập, tránh làm toàn hệ thống dừng theo. **Microservices không tự động đảm bảo khả năng chịu lỗi** nếu thiếu những cơ chế trên.

## 4. Nhược điểm và thách thức của Microservices

### 4.1. Độ trễ và lỗi mạng

Trong Monolith, các thành phần thường gọi nhau trực tiếp. Với Microservices, các lời gọi qua mạng mất thêm thời gian và có thể gặp timeout, kết nối gián đoạn hoặc dịch vụ đích tạm ngừng.

**Ví dụ:** Một thao tác đặt hàng phải gọi tuần tự dịch vụ Kho, Khuyến mãi, Thanh toán và Vận chuyển. Nếu không thiết kế hợp lý, tổng thời gian phản hồi có thể tăng đáng kể.

### 4.2. Khó bảo đảm tính nhất quán dữ liệu

Mỗi dịch vụ thường sở hữu dữ liệu riêng. Một giao dịch nghiệp vụ như “đặt hàng – trừ tồn kho – thanh toán” liên quan nhiều dịch vụ và không thể mặc nhiên dùng một giao dịch database chung. Hệ thống thường phải áp dụng **Saga, Outbox, idempotency** và các cơ chế xử lý bù để quản lý trạng thái nhất quán cuối cùng (*eventual consistency*) khi phù hợp.

**Ví dụ:** Khách đã thanh toán nhưng dịch vụ Kho phát hiện hết hàng. Hệ thống cần có quy trình hủy đơn, hoàn tiền hoặc xử lý ngoại lệ rõ ràng.

### 4.3. Vận hành và triển khai phức tạp

Số lượng dịch vụ tăng kéo theo nhu cầu CI/CD, quản lý cấu hình, service discovery (khi cần), kiểm soát secrets, container, monitoring và khả năng rollback. Đội ngũ nhỏ có thể mất nhiều thời gian vận hành hơn làm tính năng.

### 4.4. Khó tìm nguyên nhân khi xảy ra lỗi

Một yêu cầu có thể đi qua nhiều dịch vụ. Nếu không có **correlation ID, distributed tracing, log tập trung và metrics**, việc xác định dịch vụ nào gây chậm hoặc phát sinh lỗi sẽ khó khăn.

### 4.5. Tăng độ khó khi kiểm thử và thay đổi API

Một thay đổi về cấu trúc dữ liệu hoặc API có thể ảnh hưởng các dịch vụ khác. Cần quy ước versioning, kiểm thử hợp đồng (*contract testing*) và duy trì tính tương thích giữa bên cung cấp – bên sử dụng API.

### 4.6. Chi phí hạ tầng và nhân sự tăng

Mỗi dịch vụ có chi phí chạy, giám sát, bảo mật và bảo trì riêng. Nếu quy mô nhỏ, tổng chi phí có thể vượt lợi ích thu được.

### 4.7. Rủi ro phân tách sai nghiệp vụ

Nếu tách dịch vụ theo từng bảng dữ liệu hoặc chia quá nhỏ, các dịch vụ sẽ gọi nhau liên tục và phụ thuộc chặt. Khi đó hệ thống trở thành **distributed monolith**: mang phần lớn nhược điểm của hệ phân tán mà không có lợi ích độc lập thực sự.

## 5. Tư vấn cho doanh nghiệp muốn viết lại toàn bộ hệ thống

Theo em, **không nên đập bỏ toàn bộ Monolith chỉ vì Microservices đang phổ biến**. Trước tiên cần đánh giá vấn đề thực tế: hệ thống có thường xuyên quá tải không, triển khai có gây gián đoạn không, code có khó thay đổi không, có bao nhiêu nhóm phát triển và ngân sách vận hành là bao nhiêu?

Hướng tiếp cận phù hợp là:

1. **Đánh giá và đo lường:** Xác định điểm nghẽn cụ thể về hiệu năng, tốc độ phát hành, bảo trì và độ tin cậy.
2. **Cải thiện Monolith trước nếu khả thi:** Tách module nghiệp vụ, giảm phụ thuộc, nâng chất lượng kiểm thử và tự động hóa triển khai.
3. **Chọn một nghiệp vụ có ranh giới rõ để thử nghiệm:** Ví dụ tách dịch vụ Thông báo hoặc Tìm kiếm nếu chúng có nhu cầu vận hành/triển khai độc lập.
4. **Di chuyển từng bước:** Áp dụng cách tiếp cận *Strangler Fig*, chuyển dần chức năng sang dịch vụ mới thay vì viết lại toàn bộ trong một lần.
5. **Chuẩn bị năng lực vận hành:** Xây dựng CI/CD, giám sát, tracing, bảo mật, quản lý lỗi mạng và chiến lược dữ liệu trước khi mở rộng quy mô Microservices.

**Chỉ nên ưu tiên Microservices** khi lợi ích từ việc triển khai độc lập, mở rộng theo dịch vụ hoặc phân chia nhiều nhóm phát triển đủ lớn để bù đắp chi phí và độ phức tạp của hệ phân tán.

## Kết luận

**Microservices không phải “viên đạn bạc” giải quyết mọi vấn đề kiến trúc.** Kiến trúc này mang đến khả năng phân chia dịch vụ, mở rộng và phát hành linh hoạt, nhưng đánh đổi bằng độ trễ mạng, tính nhất quán dữ liệu phức tạp và chi phí vận hành cao. Với nhiều doanh nghiệp, xây dựng Monolith có module rõ ràng rồi tách dần những phần thật sự cần thiết là giải pháp an toàn và hiệu quả hơn việc viết lại toàn bộ hệ thống.
